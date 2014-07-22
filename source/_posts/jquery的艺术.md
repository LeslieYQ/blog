title: jquery的艺术 （一）
date: 2014-07-07 23:32:09
tags: jquery Source Code
---

今天起，开启一个系列，分析下前端用的最多的第三方类库，jQuery，这个库，相信大家都很熟知，以至于看见$，第一反应竟然不是美元，而是jQuery。虽然jQuery现在越来越臃肿，但是我仍然认为它是最好的DOM选择库，没有之一。

*************

我们开始从源码的包文件分析，在没有整合压缩之前，看看jquery的包文件的src文件夹里jquery.js里面的代码。

	define([
	"./core",
	"./selector",
	"./traversing",
	"./callbacks",
	"./deferred",
	"./core/ready",
	"./data",
	"./queue",
	"./queue/delay",
	"./attributes",
	"./event",
	"./event/alias",
	"./manipulation",
	"./manipulation/_evalUrl",
	"./wrap",
	"./css",
	"./css/hiddenVisibleSelectors",
	"./serialize",
	"./ajax",
	"./ajax/xhr",
	"./ajax/script",
	"./ajax/jsonp",
	"./ajax/load",
	"./effects",
	"./effects/animatedSelector",
	"./offset",
	"./dimensions",
	"./deprecated",
	"./exports/amd",
	"./exports/global"
	], function( jQuery ) {

	return jQuery;

	});

看完之后，就有了一个很清晰的思路来分析它的构成了。我决定从尾部朝前分析，嘿嘿，不走寻常路。那么今天看两个文件，都是exports下面的，这是干嘛的呢，是用来兼容现在非常流行的模块化，AMD 和 common-js的。

*********

amd文件里，很简单，读取了core文件，这是jquery的核心文件，其他都是从这扩展开来的，然后用下面的代码，输出。

	if ( typeof define === "function" && define.amd ) {
		define( "jquery", [], function() {
			return jQuery;
		});
	}

这段代码，相信用过requirejs的人都应该嫩明白，就是判断当前window环境，defind 是否是 function，并且define.amd 存在，如果符合，那么就用amd的方式，return jQuery，输出到外部。

global文件里。其实也很简单，这个代码就是在没有amd之前，放在合成版本最后的，输出jQuery 和 $ 到整个环境，也就是window。但是多做了点其他的判断。
	var
	// Map over jQuery in case of overwrite
	_jQuery = window.jQuery,

	// Map over the $ in case of overwrite
	_$ = window.$;

	jQuery.noConflict = function( deep ) {
		if ( window.$ === jQuery ) {
			window.$ = _$;
		}

		if ( deep && window.jQuery === jQuery ) {
			window.jQuery = _jQuery;
		}

		return jQuery;
	};

	if ( typeof noGlobal === strundefined ) {
		window.jQuery = window.$ = jQuery;
	}
	

注释都解释过了，先声明了另外一个_jQuery变量，保存了现在window下存在的jQuery属性，一个_$保存了window.$。然后给jQuery添加了一个属性方法，noConflict，用来重新定义jQuery的别名，让出$和jQuery简写。这个主要解决和其他类库的冲突问题，很多库都爱用$符号。最后假如没有定义过noGlobal,那么输出jQuery为window的属性，同时为$简写。

以上是第一期的 jQuery的艺术解析。
