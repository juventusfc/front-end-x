# publish 阶段的工具

## 搭建 Linux 环境

一般的，服务都是架设在 Linux 个环境中。我们可以在 Ubuntu 中搭建 Node.js 环境：

1. 安装 virtual box 和 Ubuntu
2. 安装 nvm 用于控制各个 Node.js 版本
3. 通过 nvm 安装 Node.js

## 搭建发布系统

内网发布工具(`publish tool`) -> 网发布服务器 `publish server` 8081 -> 外网静态服务器 `server` 3000 <- `customer's computer`

### 搭建内网发布工具

publish-tool 作为内网发布工具，发送 http 请求至 publish-server。它以流式形式上传文件。很多时候我们都会将所有文件放到一个文件夹下，然后通过 archiver 包，将该文件夹下所有文件进行压缩传给 `server`。

[点击这里获取源码](https://github.com/juventusfc/tools-chain-publish-tool)

### 搭建内网发布服务器

内网发布服务器主要实现两个功能：

1. 对用户权限进行认证
2. 作为发布服务器，作为内外网之间的桥梁，接收来自内网发布工具的请求，更改外网服务器上的资源

我们可以执行 `npx express-generator --no-view` 产生模板代码并将默认的端口号更改为 8081。但是要注意，http 是流式传输的，但是 express 默认的处理方式是将流式全部接收完毕后再处理。所以，我们决定放弃 express，使用原生 http 包搭建一个 `publish-server-vanilla`。

由于从内网发布工具传过来的是压缩包，所以在这里要进行解压缩。然后，以 pipe 的方式将流传给静态服务器。

[点击这里获取 publish-server 源码](https://github.com/juventusfc/tools-chain-publish-server)

[点击这里获取 publish-server-vanilla 源码](https://github.com/juventusfc/tools-chain-publish-server-vanilla)

### 搭建静态服务器

`server` 作为静态服务器，接受来自 `publish-server` 的请求，将静态文件更改，从而让用户访问最新的资源。

执行 `npx express-generator` 产生模板代码。默认的端口号为 3000。有时候为了省力，可以直接将静态文件放在 public 文件夹下。

[点击这里获取源码](https://github.com/juventusfc/tools-chain-server)
