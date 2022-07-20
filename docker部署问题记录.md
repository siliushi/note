# 前言

Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的镜像中，然后发布到任何流行的 Linux 或 Windows 操作系统的机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。
目前开发前后端分离模式非常流行，后端只需要提供 resetful api 接口，前端也是一个单独的工程应用，那么 NGINX 配置就必不可少。
下面列出了，前端项目 docker 部署所遇到的问题及解决办法，1 和 2 是内网部署问题，3 和 4 外网部署问题。

# 主要问题

## 1、宿主机不能上网

公司项目是打包到服务器一起发给客户，机器在公司上网都是正常的，邮件到客户那上不了网。主要是因为机器在公司注册了路由表，先接入的网络路由表没有更新，执行下面的命令清除路由表

```
sudo ip route flush table main
```

然后接入网络，重新获取

## 2、docker 与宿主机网络不通

### docker 网络模式

#### bridge：桥接模式

桥接模式是 docker 的默认网络设置，当 Docker 服务启动时，会在主机上创建一个名为 docker0 的虚拟网桥，并选择一个和宿主机不同的 IP 地址和子网分配给 docker0 网桥

#### host：主机模式

该模式下容器是不会拥有自己的 ip 地址，而是使用宿主机的 ip 地址和端口。这种模式的好处就是网络性能比桥接模式的好。缺点就是会占用宿主机的端口，网络的隔离性不太好

#### none：无网络模式

网络模式选择桥接模式的容器，就会连接上 docker0 这个网桥，在通过 nat 的转换，通过宿主机的网卡，连接外网，就能达到上外网的目的。

查看网络模式： docker network ls

### 问题解决

查看网桥 ip 为[172.17.0.1](http://172.17.0.1)，容器 ip 为[172.0.0.2](http://172.0.0.2)，发现宿主机能 ping 通网桥，但是无法连接容器，而容器无法连接网桥，无法连接宿主机，更别谈外网了，所以这里可以肯定是网桥出了问题

- 网桥工具

  ```
  yum install bridge-utils
  brctl show
  ```

- 这里`docker network`生成新的网桥不行，说明`docker`的`network`存在问题，我们利用刚才下载的`bridge-utils`来创建网桥。首先暂停`docker`服务，利用指令

  ```
  systemctl stop docker
  ```

- 添加网桥

  ```
  brctl addbr br0
  ```

- 设置网关

  ```
  ip addr add 172.16.0.1/24 dev br0
  ```

- 启动网桥 br0

  ```
  ip link set dev br0 up
  ```

- 查看网络 br0

  ```
  ifconfig br0
  ```

- 修改 docker 默认的网桥

  ```
  vi /etc/docker/daemon.json

  增加: "bridge":"br0"
  ```

- 重启 docker

  ```
  systemctl start docker
  ```

- 验证

  ```
  1. 宿主机ping br0网关
  ping 172.16.0.1

  2. 创建运行容器，进入容器，假设已经创建了容器 a
  docker exec -it a /bin/sh
  执行:
  ip a
  可以看到eth0虚拟网卡的ip地址为: 172.16.0.x
  ping www.baidu.com
  3. 宿主机ping容器ip
  ping 172.16.0.x
  完成

  ```

- 验证网桥重启后会不会失效，如果失效需要加到服务器的配置中

**参考:** [https://blog.csdn.net/qq_36059826/article/details/106550332](https://blog.csdn.net/qq_36059826/article/details/106550332)

### 脚本设置

服务器重启之后，上面的配置都失效，所以要设置永久桥接网络。可以将生成网桥的命令写在 rc.local 中，让服务启动时执行

    ```
    vi /etc/rc.d/rc.loal

    增加下面的内容:
    /usr/sbin/brctl addbr br0
    /usr/sbin/ip addr add 172.16.0.1.24 dev br0
    /usr/sbin/ip link set dev br0 up

    切记
    chmod +x rc.local

    重启后 docker0网桥则被删除
    ```

## 3、端口暴露

一个完整的项目有多个应用，前端、后端、后处理等等，这些应用部署在同一台机器，每个应用都有自己的服务端口。这些服务通信是在机器内部，按理说只要开放一个前端端口就可以访问，但是，关闭其他应用端口就访问失败。
这是因为 nginx 配置 proxy_pass 使用公网 ip，需要将公网 ip 改成网关 ip，其他服务也是对应的修改

## 4、nginx 配置支持 https 和 wss

nginx 配置如下

```
map $http_upgrade $connection_upgrade {
 default upgrade;
 '' close;
}

upstream wsbackend {
 server 172.18.0.1:8000;
}

server {
    listen       80;
    root /dist;
    server_name 域名;
    location ~* (.+)\.html {
      add_header Cache-Control no-cache;
    }

    location / {
        try_files $uri $uri/ /index.html;
    }
    # redirct
    return 301 域名$request_uri;

    location ~ ^/api {
            proxy_pass http://wsbackend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

            # Websocket代理配置
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "Upgrade";

            proxy_buffering off;
            proxy_read_timeout 3600;
        }


}

server {
     listen 443 ssl;
     server_name 域名;
     ssl_certificate     证书.crt;
     ssl_certificate_key 证书.key;
     ssl on;
     # ---
     ssl_prefer_server_ciphers on;
     ssl_verify_client off;
     ssl_session_cache shared:SSL:10m;
     ssl_protocols TLSv1 TLSv1.1 TLSv1.2 SSLv2 SSLv3;
     ssl_ciphers ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
     # ---

     root /dist;

     location ~* (.+)\.html {
           add_header Cache-Control no-cache;
         }

     location / {
             try_files $uri $uri/ /index.html;
         }

     location ~ ^/api {
                   proxy_pass http://wsbackend;
                   proxy_set_header Host $host;
                   proxy_set_header X-Real-IP $remote_addr;
                   proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

                   # Websocket代理配置
                   proxy_set_header Upgrade $http_upgrade;
                   proxy_set_header Connection "Upgrade";
                   proxy_buffering off;
                   proxy_read_timeout 3600;
              }


}
```

**注意：** WSS 连接只能用域名

# 多环境部署

```
-v /home/front/configs/nginx.conf:/etc/nginx/nginx.conf -v /home/front/configs/pro.conf:/etc/nginx/conf.d/default.conf
```

