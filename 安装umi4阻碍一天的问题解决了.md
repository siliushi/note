umi4已经发布一段时间了，现在项目中还是用的umi3版本，umi3最坑的就是mfsu不会热更新，而且umi4新增了一些功能，所以打算把umi版本升级下。
# 问题
通过 npm create umi，选择pnpm安装。  
报错：dyld: Symbol not found: _SecTrustEvaluateWithError  
>环境：
>mac os: 10.12.6
>node.js: v16.14.2

pnpm报错：
```
Packages are hard linked from the content-addressable store to the virtual store.
  Content-addressable store is at: /Users/keven/Library/pnpm/store/v3
  Virtual store is at:             node_modules/.pnpm
Progress: resolved 1192, reused 1063, downloaded 0, added 1112, done
node_modules/.pnpm/esbuild@0.14.36/node_modules/esbuild: Running postinstall script, failed in 568ms
.../esbuild@0.14.36/node_modules/esbuild postinstall$ node install.js
│ node:child_process:828
│     err = new Error(msg);
│           ^
│ Error: Command failed: /Users/keven/Documents/personal/code/umi/antd/node_modules/.pnpm/esbuild@0.14.36/node_m
│ dyld: Symbol not found: _SecTrustEvaluateWithError
│   Referenced from: /Users/keven/Documents/personal/code/umi/antd/node_modules/.pnpm/esbuild@0.14.36/node_modul
│   Expected in: flat namespace
│     at checkExecSyncError (node:child_process:828:11)
│     at Object.execFileSync (node:child_process:866:15)
│     at validateBinaryVersion (/Users/keven/Documents/personal/code/umi/antd/node_modules/.pnpm/esbuild@0.14.36
│     at /Users/keven/Documents/personal/code/umi/antd/node_modules/.pnpm/esbuild@0.14.36/node_modules/esbuild/i
│   status: null,
│   signal: 'SIGABRT',
│   output: [
│     null,
│     Buffer(0) [Uint8Array] [],
│     Buffer(215) [Uint8Array] [
│       100, 121, 108, 100,  58,  32,  83, 121, 109,  98, 111, 108,
│        32, 110, 111, 116,  32, 102, 111, 117, 110, 100,  58,  32,
│        95,  83, 101,  99,  84, 114, 117, 115, 116,  69, 118,  97,
│       108, 117,  97, 116, 101,  87, 105, 116, 104,  69, 114, 114,
│       111, 114,  10,  32,  32,  82, 101, 102, 101, 114, 101, 110,
│        99, 101, 100,  32, 102, 114, 111, 109,  58,  32,  47,  85,
│       115, 101, 114, 115,  47, 107, 101, 118, 101, 110,  47,  68,
│       111,  99, 117, 109, 101, 110, 116, 115,  47, 112, 101, 114,
│       115, 111, 110,  97,
│       ... 115 more items
│     ]
│   ],
│   pid: 15457,
│   stdout: Buffer(0) [Uint8Array] [],
│   stderr: Buffer(215) [Uint8Array] [
│     100, 121, 108, 100,  58,  32,  83, 121, 109,  98, 111, 108,
│      32, 110, 111, 116,  32, 102, 111, 117, 110, 100,  58,  32,
│      95,  83, 101,  99,  84, 114, 117, 115, 116,  69, 118,  97,
│     108, 117,  97, 116, 101,  87, 105, 116, 104,  69, 114, 114,
│     111, 114,  10,  32,  32,  82, 101, 102, 101, 114, 101, 110,
│      99, 101, 100,  32, 102, 114, 111, 109,  58,  32,  47,  85,
│     115, 101, 114, 115,  47, 107, 101, 118, 101, 110,  47,  68,
│     111,  99, 117, 109, 101, 110, 116, 115,  47, 112, 101, 114,
│     115, 111, 110,  97,
│     ... 115 more items
│   ]
│ }
└─ Failed in 569ms
 ELIFECYCLE  Command failed with exit code 1.
```

换yarn试试，同样报错


```
error /Users/keven/Documents/personal/code/umi/antd/node_modules/esbuild: Command failed.
Exit code: 1
Command: node install.js
Arguments: 
Directory: /Users/keven/Documents/personal/code/umi/antd/node_modules/esbuild
Output:
node:child_process:828
    err = new Error(msg);
          ^

Error: Command failed: node /Users/keven/Documents/personal/code/umi/antd/node_modules/esbuild/bin/esbuild --version
dyld: Symbol not found: _SecTrustEvaluateWithError
  Referenced from: /Users/keven/Documents/personal/code/umi/antd/node_modules/esbuild-darwin-64/bin/esbuild
  Expected in: flat namespace

node:child_process:869
    throw err;
    ^

Error: Command failed: /Users/keven/Documents/personal/code/umi/antd/node_modules/esbuild-darwin-64/bin/esbuild --version
    at checkExecSyncError (node:child_process:828:11)
    at Object.execFileSync (node:child_process:866:15)
    at Object.<anonymous> (/Users/keven/Documents/personal/code/umi/antd/node_modules/esbuild/bin/esbuild:172:28)
    at Module._compile (node:internal/modules/cjs/loader:1103:14)
    at Object.Module._extensions..js (node:internal/modules/cjs/loader:1157:10)
    at Module.load (node:internal/modules/cjs/loader:981:32)
    at Function.Module._load (node:internal/modules/cjs/loader:822:12)
    at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:77:12)
    at node:internal/main/run_main_module:17:47 {
  status: null,
  signal: 'SIGABRT',
  output: [ null, null, null ],
  pid: 15677,
  stdout: null,
  stderr: null
}

    at checkExecSyncError (node:child_process:828:11)
    at Object.execFileSync (node:child_process:866:15)
    at validateBinaryVersion (/Users/keven/Documents/personal/code/umi/antd/node_modules/esbuild/install.js:101:32)
    at /Users/keven/Documents/personal/code/umi/antd/node_modules/esbuild/install.js:249:5 {
  status: 1,
  signal: null,
  output: [
    null,
    Buffer(0) [Uint8Array] [],
    Buffer(1114) [Uint8Array] [
      100, 121, 108, 100,  58,  32,  83, 121, 109,  98, 111, 108,
       32, 110, 111, 116,  32, 102, 111, 117, 110, 100,  58,  32,
       95,  83, 101,  99,  84, 114, 117, 115, 116,  69, 118,  97,
      108, 117,  97, 116, 101,  87, 105, 116, 104,  69, 114, 114,
      111, 114,  10,  32,  32,  82, 101, 102, 101, 114, 101, 110,
       99, 101, 100,  32, 102, 114, 111, 109,  58,  32,  47,  85,
      115, 101, 114, 115,  47, 107, 101, 118, 101, 110,  47,  68,
      111,  99, 117, 109, 101, 110, 116, 115,  47, 112, 101, 114,
      115, 111, 110,  97,
      ... 1014 more items
    ]
  ],
  pid: 15676,
  stdout: Buffer(0) [Uint8Array] [],
  stderr: Buffer(1114) [Uint8Array] [
    100, 121, 108, 100,  58,  32,  83, 121, 109,  98, 111, 108,
     32, 110, 111, 116,  32, 102, 111, 117, 110, 100,  58,  32,
     95,  83, 101,  99,  84, 114, 117, 115, 116,  69, 118,  97,
    108, 117,  97, 116, 101,  87, 105, 116, 104,  69, 114, 114,
    111, 114,  10,  32,  32,  82, 101, 102, 101, 114, 101, 110,
     99, 101, 100,  32, 102, 114, 111, 109,  58,  32,  47,  85,
    115, 101, 114, 115,  47, 107, 101, 118, 101, 110,  47,  68,
    111,  99, 117, 109, 101, 110, 116, 115,  47, 112, 101, 114,
    115, 111, 110,  97,
```



# 解决办法

官方也有人提同样的问题，但是没有解决办法，[https://github.com/evanw/esbuild/issues/2183](https://github.com/evanw/esbuild/issues/2183)   

有的文章说是go版本的问题，和go没关系，主要是因为esbuild版本问题。   

最终解决办法，在package.json加上下面的配置，主要是通过npm override来改变esbuild版本信息
```
"devDependencies": {
  "esbuild-wasm":"latest"
},
"overrides": {
  "esbuild":"npm:esbuild-wasm@latest"
}
```

执行**npm install**就安装好了
