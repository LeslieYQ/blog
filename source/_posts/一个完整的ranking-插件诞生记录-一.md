title: 一个完整的ranking 插件诞生记录 一
date: 2014-03-16 15:24:52
tags: jquery plugin
---

先来说说 产生写这个插件的原因，因为公司做一个Web端类似App的页面，里面会展示各种运动数据，其中有一个需求是关于排行榜的。于是，整个排行榜就是个人写出来的。写出来的大家的感觉还不错。前几天聊到这个话题。我觉得这是一个不错的需求，于是想自己把整个代码实现。共享出来。

**************

先说说 这个插件的构想，首先第一步完成我已做的效果的抽出。也就是一个横向的排行榜和动效。然后添加各种参数选项。构思中，大概应该有 横竖的切换。大小的改变也就是css的定制。还有数据的获取方式的修改。大概就分这几个方向吧。

***********

先说说现在完成到哪里，现在只是把我在公司做的抽出来而已。把分散在各个js文件的函数，合成到一起了。还是一个横向的排行榜，而且耦合严重，对内部的图片的要求也比较高。css样式自己写的。还没能形成定制化。获取数据的参数接头也没有留出来，基本就是一个效果的Demo而已。

***********

不过大家也可以看看效果。这是一个记录，也是一个开发的完整过程。
