title: Package.json 简介
date: 2015-11-25 15:26:40
tags: Package.json nodejs npm web前端
---

说道Node就不得不提NPM这个东东，npm这个东东到底是什么呢？npm--- Node Package Manager。 其实就是nodejs的包管理和分发工具。 这个东西基本可以伴随你的node生涯，所以要来介绍下它的一个重要文件： package.json.

那么package能做什么事情呢：

1、  相当于你本地项目的一个文档说明。

2、允许你指定你项目中所使用的node包的版本。

3、构建你的项目更加容易，便于给其他人共享。

创建一个json文件， 使用 如下命令，但是还需要添加devDependencies 和 dependencies 这两个特殊的类型。

	npm init --yes

一个典型的package.json文件如下所示。

	{

	    "name": "my_package",     //你的项目名称，全部小写，不能有空格，一个单词，允许-和_. 如果是要发布自己的node插件，一般用github上面项目名称。 下次有机会说明 npm 上面的发布流程。

	    "version": "1.0.0",  //你的项目版本号，最好遵守 GNU 版本号管理。 

	    "main": "index.js", //目录中启动文件名称。或者称之为入口文件，一般都是 index.js

	    "scripts": {

	        "test": "echo \"Error: no test specified\" && exit 1"    //一般默认一个test的空文件夹、用作写测试代码。

	    },

	    "keywords": [],  //项目的关键词。 一般用不到，发布npm才用得到。

	    "author": "ag_dubs", //作者名称

	    "license": "ISC", //协议 

	    "repository": {  // 用来存放到 版本管理远程服务。 发布npm才有用

	        "type": "git",

	        "url": "https://github.com/ashleygwilliams/my_package.git"

	    },

	    "dependencies": {   // 正式使用时，依赖的包

	        "my_dep": "^1.0.0"

	    },

	    "devDependencies" : {//开发或者测试时，依赖的包。

	        "my_test_framework": "^3.1.0"

	    }

	    "bugs": {  //同repository

	        "url": "https://github.com/ashleygwilliams/my_package/issues"

	    },

	    "homepage": "https://github.com/ashleygwilliams/my_package"  //项目主页、 发布才有用

	}

一般情况，如果是自己的项目，特别是前端的人，会使用到gulp或者grunt来打包自己的项目，并且一般不会吧node_modules上传到git上面，所以需要package来管理自己打包所需的插件，以便于项目中其他人员共享，这个文件的好处就是一个人添加某个插件后，更改了这个文件，其他人员只需要同步此文件，然后执行如下命令，即可安装同样的包。

	npm install

参考地址： https://docs.npmjs.com/getting-started/using-a-package.json.

转载请说明，有问题欢迎讨论。