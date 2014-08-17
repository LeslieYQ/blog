title: "jquery的艺术 (二)"
date: 2014-07-28 23:51:42
tags: jquery Source Code
---

继续我们的jquery艺术之旅

	"./dimensions",
	"./deprecated",

今天分析这两个文件。其中deprecated比较简单：
		
	define([
		"./core",
		"./traversing"
	], function( jQuery ) {
	// The number of elements contained in the matched element set
	jQuery.fn.size = function() {
		return this.length;
	};

	jQuery.fn.andSelf = jQuery.fn.addBack;

	});

不管上面的引用，jQuery.fn.size 这个函数，正如注释所写的一样，返回元素的个数。例如可以这样子使用

	$('div').size(). return number;


接下来一句更简单了，就是新加了一个属性 andSelf。 并且赋值了 addBack。两个相同了。

来看看 dimensions这个这个文件吧。

	define([
	"./core",
	"./core/access",
	"./css"
	], function( jQuery, access ) {

	// Create innerHeight, innerWidth, height, width, outerHeight and outerWidth methods
	jQuery.each( { Height: "height", Width: "width" }, function( name, type ) {
		jQuery.each( { padding: "inner" + name, content: type, "": "outer" + name }, function( defaultExtra, funcName ) {
		// margin is only for outerHeight, outerWidth
			jQuery.fn[ funcName ] = function( margin, value ) {
				var chainable = arguments.length && ( defaultExtra || typeof margin !== "boolean" ),
					extra = defaultExtra || ( margin === true || value === true ? "margin" : "border" );

				return access( this, function( elem, type, value ) {
					var doc;

					if ( jQuery.isWindow( elem ) ) {
						// As of 5/8/2012 this will yield incorrect results for Mobile Safari, but there
						// isn't a whole lot we can do. See pull request at this URL for discussion:
						// https://github.com/jquery/jquery/pull/764
						return elem.document.documentElement[ "client" + name ];
					}

					// Get document width or height
					if ( elem.nodeType === 9 ) {
						doc = elem.documentElement;

						// Either scroll[Width/Height] or offset[Width/Height] or client[Width/Height], whichever is greatest
						// unfortunately, this causes bug #3838 in IE6/8 only, but there is currently no good, small way to fix it.
						return Math.max(
							elem.body[ "scroll" + name ], doc[ "scroll" + name ],
							elem.body[ "offset" + name ], doc[ "offset" + name ],
							doc[ "client" + name ]
						);
					}

					return value === undefined ?
						// Get width or height on the element, requesting but not forcing parseFloat
						jQuery.css( elem, type, extra ) :

						// Set width or height on the element
						jQuery.style( elem, type, value, extra );
				}, type, chainable ? margin : undefined, chainable, null );
			};
		});
	});

	return jQuery;
	});

跟注释的说明一样，这段代码建立了 innerHeight, innerWidth, height, width, outerHeight and outerWidth 6个jq的方法。

第一个each 用来匹配 height 和 width，第二个each 就是给 第一个each的对象的每个属性加上inner，outer以及不加。这个时候就就看出区别了。如果是我们来写的估计就会写成一个each，把这个6个都加上去，而不是分开成两个。（当然你可以说你高大上的不是，但是我比较低端，我是的。）

	jQuery.isWindow( elem )

这句话如果成立，也就是对象如果是窗口，那么返回 elem.document.documentElement[ "client" + name ]; 整个窗口的高或者宽。也可能是 iframe的。

如果 元素 是 document，elem.nodeType =9. 返回 document中 scroll。offset，client中最大值。

如果 参数中没有 value值，例如$('div').width(); 那么返回元素的 宽度，如果有，改变元素宽度为value。

以上就是本期的内容。