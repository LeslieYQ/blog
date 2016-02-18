title: "JS '严格模式'"
date: 2015.12.03 17:27:44
tags: js html web前端 严格模式
---

为什么想到写这么一篇文章呢，来源在于回答一个 SG上面的问题。那么问题是这样子的。

    var a = 2; 
    function foo(){ console.log(this.a);}
    foo();

以上代码，执行的结果是什么？？

如果你回答是2，那么对了多少？

只能说对了一半，为什么呢？  其实是题主的问法有问题，他没有规定环境，所以答案很多，
直接在浏览器里面如下写法执行
      
     <script>
        var a = 2; 
        function foo(){ console.log(this.a);}
        foo();
     </script>

确实会输出2，没有问题。

但是如果在 nodejs里面， 大家考虑过么？

结果是可能是2，可能是 undefined。  现在有人是不是蒙圈了，不知道为什么。那么解析的文章在

http://f2e.souche.com/blog/a-js-problem-about-global/

甚至还有后续，可以看 sg上的原问题，

http://segmentfault.com/q/1010000004053433?_ea=473968

在评论中讨论到了一个问题，如果在浏览器中使用 ‘严格模式’， 会怎么样？

     <script>
        "use strict"
        var a = 2; 
        function foo(){ console.log(this.a);}
        foo();
     </script>

结果就是会报错，错误如下，是不是出乎大家的意料，

      Uncaught TypeError: Cannot read property 'a' of undefined(…)

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/198062-2ebc5e6d2b03973d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那么为什么呢？ 我们打印下 this 关键字看看， 发现是 undefined。 我操，这跟我们的想法不符合啊，为何不是window这个全局变量了？？ 这一切的原因在于 "use strict"

那么什么是严格模式，mozile的官方文档解释在这里 
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode#Securing_JavaScript

主要注意点在 “Changes in strict mode” 这个栏目下，“ Securing JavaScript” 第一条

First, the value passed as this to a function in strict mode is not forced into being an object (a.k.a. "boxed"). For a normal function, this is always an object: either the provided object if called with an object-valued this; the value, boxed, if called with a Boolean, string, or number this; or the global object if called with an undefined or null this. (Use [call](https://developer.mozilla.org/en-US/Web/JavaScript/Reference/Global_Objects/Function/call), [apply
](https://developer.mozilla.org/en-US/Web/JavaScript/Reference/Global_Objects/Function/apply), or [bind
](https://developer.mozilla.org/en-US/Web/JavaScript/Reference/Global_Objects/Function/bind) to specify a particularthis
.) Not only is automatic boxing a performance cost, but exposing the global object in browsers is a security hazard, because the global object provides access to functionality that "secure" JavaScript environments must restrict. Thus for a strict mode function, the specified this is not boxed into an object, and if unspecified, this will be undefined:

      "use strict";function fun() { return this; }
      console.assert(fun() === undefined);
      console.assert(fun.call(2) === 2);
      console.assert(fun.apply(null) === null);
      console.assert(fun.call(undefined) === undefined);
      console.assert(fun.bind(true)() === true);

意思就是不会在默认的给function指定一个this对象了。 一般情况下，我们的function一定会有一个调用对象，不管是字符还是数组什么的，或者是全局变量，这种转换很损耗性能，而且危险，因为暴露了全局对象，所以严格模式下，不会再封装this对象了，如果没有，就是 undefined。

以上就是我们执行出来，报错的原因。至于这个模式到底好不好呢？ 其实有些争议的，很多人写js已经习惯了这些各种变化，或者说是为方便吧，但是很多刚开始写的人很不习惯，甚至搞不明白，仁者见仁智者见智吧。

一篇简单的探索，这个严格模式 出来很早了，很多人都有写过，而且官方文档很详细。当然更关键的是你不使用也没有任何问题，一旦使用就要注意了哦。