![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5e79f2a3469442ba9945021d33bc792d~tplv-k3u1fbpfcp-watermark.image?)
从图片分析打包慢的原因

## 上下文关联目录文件
```
 => [internal] load build context 
 => => transferring context: 
 900s,346M
```
docker 是 c/s 的架构设计，当用户执行 docker build 时并不是在 client 直接进行构建，而是将 build 指定的目录作为上下文传递到 server 端，再执行上述提到的镜像构建的过程。如果执行镜像构建的上下文中关联大量不必要的文件，那可以使用 .dockerignore 来忽略这些文件。 

配置.dockerignore
```
node_modules
dist
```
结果：  


![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0ca85765f17d478ea30df20053fab52b~tplv-k3u1fbpfcp-watermark.image?)


## ADD与COPY的区别
```
[builder 4/6] ADD . .
366s
```
区别：ADD支持从远端拉取文件，对压缩文件会自动解压，官方推荐COPY


![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4d9aa15b5d1e470fa65406b730b666d9~tplv-k3u1fbpfcp-watermark.image?)

## node_modules 安装
```
[builder 5/6] RUN yarn --frozen-lockfile
1035s
```
1、制作基础镜像   
2、能不能缓存？  
Docker cache npm Dependencies
```
COPY package.json /tmp/package.json
RUN cd /tmp && npm install --prefer-offline --no-audit --progress=false
RUN ln -s /tmp/node_modules /workspace/node_modules
```
第一次打包，


![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ddd06a5e3db64df081398ebf766b3294~tplv-k3u1fbpfcp-watermark.image?)
不修改内容，第二次打包

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/beff8d6c94da4f4dab82f72be3b562fe~tplv-k3u1fbpfcp-watermark.image?)
修改内容，再次打包


![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0e27abcff56d4547b5598379d42b08ce~tplv-k3u1fbpfcp-watermark.image?)

## 是否还能继续优化

打包放到本地，COPY本地的dist目录，减少了安装、打包时间
```
git checkout master

git pull

echo "start build"

yarn run build

echo "build finish"


echo "Build, Push and Run Frontend docker image"

docker build -t fe:$1 .
docker push fe:$1
```



## 常用指令

FORM：指定基础镜像  
COPY：复制文件  
RUN：指定要运行的命令  
ENV：设置环境变量  
EXPOSE：指定容器要暴露的端口  
WORKDIR：设置当前工作目录  
CMD：容器启动时运行命令  


## 源

网易：http://hub-mirror.c.163.com  
中科大镜像地址：http://mirrors.ustc.edu.cn/  
中科大github地址：https://github.com/ustclug/mirrorrequest  
Azure中国镜像地址：http://mirror.azure.cn/  
Azure中国github地址：https://github.com/Azure/container-service-for-azure-china   
DockerHub镜像仓库: https://hub.docker.com/   
阿里云镜像仓库： https://cr.console.aliyun.com   
google镜像仓库： https://console.cloud.google.com/gcr/images/google-containers/GLOBAL （如果你本地可以翻墙的话是可以连上去的 ）   
coreos镜像仓库： https://quay.io/repository/   
RedHat镜像仓库： https://access.redhat.com/containers  

## Dockerfile

+ **减少层的数量、控制层的大小**  
dockerfile中每一个大写的指令代码一层，如COPY,RUN,FROM...等等，层级越多，体积越大，所以在编写dockerfile时，应避免写多个RUN 指令，使用&&代替。 dockerfile 在同一层中删除文件，才会真正的删除，从而减少体积，如果在下面的层级中删除上面层级中的node_modules是无用的，不会减少文件的体积。每当代码变化就会触发构建，在同一层级中安装依赖并且构建代码，之后删除无用代码，会减小代码体积，但是构架时间会加长，很慢，不推荐此方法。

+ **将不变层放到前面，可变层放到后面**  
当我们在同个时间内多次执行 docker build 可以发现，在构建完一次镜像后再次构建，Docker会利用缓存中的镜像数据直接进行复用。

