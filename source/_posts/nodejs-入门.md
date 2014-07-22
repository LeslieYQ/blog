title: nodejs 入门
date: 2014-02-25 23:34:15
categories: node
tags: [blog,node]
---

话说这两天开始学习 nodejs，先从nodejs 入门手册一书练习，可能由于书籍和 node的版本差距太大的原因，而且我又是在windows下开发，出现几个错误，说说解决过程。

*****

一个就是 formidable 这个model的引用错误。走到formidable这块的时候，因为以开启的终端（CMD windows）在执行node服务，所以*需要再开启一个shell*来执行 `npm install formidable`， 然后在 js中 require ，重启 node 服务，我在这里就报错了，找不到formidable，仔细排除了安装原因之后，终于发现了问题。

问题就出现在*再开启shell*的时候没有到你的代码文件夹下，执行的`npm install formidable` 安装出来 npm_models/formiadable 并不在本文件夹下，而是在当时执行命令的目录下，于是引用失败，解决方案， cd 到你的代码目录，`npm install formiadable`，或者 `require` 的时候 加上路径。

*****

接着一个坑 就是上传图片，找不到路径，这里有两个问题，一个是如果你的路径用 默认路径，在windows 下 会放到 C盘去，操作权限的问题可能拦住你，所以 你应该 配置一下 file_upload_path 路径。可以在代码目录下加个 tmp文件夹。配置到此目录下。

*****

然后就是在 show 函数中，显示图片路径的时候，书中给的是 ‘/tmp’ 这个在 windows下回出错。在 linux下应该没有问题（我没有试过，猜测而已），所以 需要去掉/ 这样子 就默认在你的js目录了，也就是你的代码目录，这涉及到js默认路径的问题，写过前端的人应该都知道的。