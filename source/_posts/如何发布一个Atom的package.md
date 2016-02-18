title: 如何发布一个Atom的package
date: 2015.11.26 19:11:00
tags: Atom package Github tools vue vue-format
---

上次为大家介绍了package.json的内容，文件在nodejs是非常有用的，发布npm是必须使用的，那么这次给大家说说如何给github的亲儿子编辑器Atom发布一个package，以便于共享你的好工具，造福大家。

简单介绍下Atom，是github用nodejs编写的一个编辑器，其实应该主要使用了node-webkit的技术，如果感兴趣，可以下次给大家详细分析。

此文章来源于官方文档的翻译和自己开发插件的一些心得 ---- https://atom.io/docs/v1.2.4/hacking-atom-package-word-count

##Package Generator

创建一个package最简单的方式就是使用atom里面的生成器，使用命令面板--command(window)+shift+P,输入generator，找到Package Generator:Generate Atom Package 这行命令，选择即可，这个时候输入你的package名字，可以起一个高大上有逼格的名字。


![屏幕快照 2015-11-26 下午5.47.50.png](http://upload-images.jianshu.io/upload_images/198062-b7aca444cf232e11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看见atom帮你创建了一大堆的文件夹和文件，一般来说，基础包如下

    my-package/
      keymaps/
      lib/
      menus/
      spec/
      styles/
      package.json
      README.md
      CHANGELOG.md
      LICENSE.md
      .gitignore

##package.json

同样，首先看 package.json  因为他涉及到你的发布和各种配置。跟npm的很像，但是有自己的东东

*  main ： 这个是你执行命令后进入的主文件，也就是入口文件，默认是寻找index的，同时atom支持 coffee 和 js

* styles ：一个字符串数组声明来让atom加载样式文件顺序，如果没有，就顺序的读取styles文件夹里面的文件。

* keymaps ： 一个字符串数组声明package需要加载的key map文件顺序，如果没有，按顺序读取keymaps文件夹的文件。

* activationCommands  ：一个对象来声明触发你的package的行为，key是css选择器形式，value是一个字符串数组声明命令。知道规定的css 选择器中一个触发了，才会载入你的package。

来看看下面的package.json长相：

    {
      "name": "my-package",
      "main": "./lib/my-package",
      "version": "0.0.0",
      "description": "A short description of your package",
      "keywords": [
        ],
      "activationCommands": {
        "atom-workspace": "my-package:toggle"
        },
        "repository": "https://github.com/atom/my-package",
        "license": "MIT",
        "engines": {
          "atom": ">=1.0.0 <2.0.0"
        },
        "dependencies": {
        }
    }  

你最先要确认的事情是就填写这些信息，包括：name，description, repository， license。 剩下的可以边做边写。

###Source Code

 我们来看看我们主文件，也就是刚刚创建的my-package.coffee,如果你在package.json中main的属性没有填写，就是用index文件了，这跟浏览器解析url很相似。

这跟最高级别的模块是一个单独对象，管理着你的package在atom的生命周期，无论你有其他多少的视图和模块，都被它管理。

看看这个模块有哪些重要的方法：

* activate(state): 这个必需的方法在你的package执行的时候被调用，如果你的模块使用了serialize()方法，那么这个方法会传递状态数据，在你最后一次窗口被序列化的时候。使用这个初始化工作流程在package开始时

* serialize() ：这个可选的方法在窗口被挂起的时候调用，允许你返回一个json表示你的组件状态。当窗口再次唤起的时候，这个数据会传递给activate方法，这样你就可以恢复你的视图。

* deactivate() ：这个可选的方法也是被挂起的时候调用，如果你的package有监听任何文件或者保持外部的资源，释放他们在这个时候。如果你只是订阅窗口内的，就不必关心了。

### Style Sheets

这个很简单，就是你的package执行的时候，用到的样式表而已，可以是css或者less。less更好，比较推荐。

其实你不需要太多的css，因为atom本身就提供了一个标准包给使用，你可以使用CMD+shift+P 然后 搜索 styleguide 或者 cmd+crtl+shift+G。

如果你有什么特别要求，可以使用这个style文件夹，记得添加package.json的路径。

###Keymaps

推荐使用常用的操作键来绑定扩展，特别是你也新加了一个命令的时候，在我们package里面，我们的keymaps/my-package.cson文件：

        'atom-workspace':
            'ctrl-alt-o': 'my-package:toggle'

这个意思就是你同时按下 crtl-alt-o的时候，我们的package就会执行toggle命令，你可以更改这些绑定在这个文件。

所有的快捷键都在keymaps的文件目录中，默认是所有的文件都被载入的，package.json文件中有个可选的keymaps选项可以指定哪些文件被载入

###Menus

这个文件主要是定义菜单栏的，包括顶部的package菜单里面的选项，和右键菜单选项。和keymaps一样可以在package.json里面配置指定文件。

然后就是开发我们的插件，这个比较复杂，主要是涉及到Atom内部的各种API和机制，这次先不讲，先说其他的。

### Basic Debugging

最简单的就是使用 console.log 来调试，跟我们在浏览器一样，因为node-webkit其实就一个浏览器。

使用alt-cmd-I 或者选择菜单 View>Developer> Toggle Developer Tools



![屏幕快照 2015-11-26 下午6.42.18.png](http://upload-images.jianshu.io/upload_images/198062-80aabce07a7ea550.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###Testing

当然我们还需要写测试功能，所有的测试代码在 spec目录下面，使用jasmine来测试，所以大家要熟悉下[jasmine](https://github.com/jasmine/jasmine)的语法，其实各种测试都差不多。 

使用cmd-alt-ctrl-p或者Developer>Run Package Spec菜单开始执行测试。一般来说都会有错误，如下所示：


![屏幕快照 2015-11-26 下午6.48.23.png](http://upload-images.jianshu.io/upload_images/198062-201c9308aeba1d9b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你可以自己慢慢的修改测试代码，保证通过，同时保证自己的package代码不错。

###Publishing

现在我们的package没问题了，也测试通过了，剩下的就是发布了，要注意一些小的细节，要不发布很可能失败。

#### Prepare Your Package

* package.json 一定要有 name， description 和 repository字段

* package.json 要有 version字段来表示版本号" 0.0.0"

* package.json 会有 engines 来表示工作环境，一般就是 {"Atom":">=1.0.0 <2.0.0"}

* 要有一个README.md文件在根目录

* 整个文件要在github上有自己的地址，并且已经提交

#### Publish Your Package

在你发布之前，最好去atom官网上找找会不会有跟你同名的package，如果有，你就只能修改名字，确保没有之后， 你就可以在你的目录下 使用 

        apm publish minor 

来发布你的package了

这个命令会做以下事情：

1. 注册一个package的名字在atom.io上，如果是第一次发布

2. 更新version从package.json文件中，然后提交

3. 创建一个新的 git tag 给这次提交

4. 推送这个tag和分支到 github上

5. 用新发布的version更新到 atom.io 上

如果你是第一次提交，可能会需要填写你github的用户名和密码，这个你跟着guide走就行了，注意别填错了，主要是数字别用小键盘。

term中显示成功之后，你就可以在
        
          https://atom.io/packages/my-package
看到你的package的页面了。记得编写README.md的内容给大家看。

说说 minor这个意思，其实publish后面跟的参数有三个选项的。

major： 表示给 apm 增加版本号的第一个数字，1.0.0 这样子 git tag 也会是 v1.0.0

minor： 第二个数字， 0.1.0的含义

patch： 最后一个数字， 0.0.1的意思

这个是跟版本号管理对应的，一般数来 major对应是往后兼容性的改变，例如 修改默认设置或者删除功能， minor 一般就是添加一个小功能或者优化。 patch 就是修复小的bug。

以上就一个完整的atom package开发和发布的过程，主要参考官网的文档，里面有我一定的心得。

同时 介绍我的新插件，

https://atom.io/packages/vue-format

主要是最近开始写 vue.js，然后webpack打包，使用.vue的文件方式，结果没有能格式化文件的插件，所以给atom写了一个，这是 0.1.0的版本，有问题很正常，我后面还会慢慢优化，如果大家有兴趣，可以一起来优化，或者给我提各种issue，欢迎交流