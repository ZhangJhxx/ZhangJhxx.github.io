---
title: hexo基础命令
---


## Quick Start
### 初始化站点 `hexo init`

### Create a new post

``` bash
$ hexo new "My New Post"
```

### Run server

``` bash
$ hexo server
$ hexo s
```
### cache clean
``` bash
$ hexo clean
```
### Generate static files

``` bash
$ hexo generate
$ hexo g
```

### Deploy to remote sites

``` bash
$ hexo deploy
$ hexo d
```

### 报错记录1
```
Failed to connect to github.com port 443 after 21061 ms: Timed out
FATAL {
  err: Error: Spawn failed
      at ChildProcess.<anonymous> (D:\hexo\blog\node_modules\hexo-util\lib\spawn.js:51:21)
      ...
  }
} Something's wrong. Maybe you can find the solution here: %s https://hexo.io/docs/troubleshooting.html
```

### 解决方法
#### 进入站点根目录
`cd /usr/local/src/hexo/hanyubolg/`

#### 删除git提交内容文件夹
`rm -rf .deploy_git/`

#### 执行
`git config --global core.autocrlf false`

#### 最后
`hexo clean && hexo g && hexo d`

### 报错记录2
```
OpenSSL SSL_read: Connection was reset, errno 10054
```
> 首先，造成这个错误很有可能是网络不稳定，连接超时导致的，
如果再次尝试后依然报错，可以执行下面的命令
`git config --global http.sslVerify "false"`

