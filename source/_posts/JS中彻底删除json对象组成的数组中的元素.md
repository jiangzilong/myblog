---
layout: post
title: "JS中彻底删除json对象组成的数组中的元素"
date: 2016-12-08 15:34:00
comments: true
tags: 
	- js
	- json
---

<p>只是分享一个小知识~</p>
<p>在JS中，对于某个由json对象组成的数组，例如：</p>

```js
var test = [{ "a": "1", "b": "2" }, { "a": "3", "b": "4" }, { "a": "5", "b": "6" }];
```

<p>如果我们想要删除其中的第二个json对象，应该怎么做呢？其实方法和操作数组完全相同。</p>
<!-- more -->
<p>在最开始的时候尝试使用了delete运算符，但在查询数组长度的时候发现其实这种方法并不是彻底删除元素，而是删除它的值，但仍会保留空间。</p>

```js
var test = [{ "a": "1", "b": "2" }, { "a": "3", "b": "4" }, { "a": "5", "b": "6" }];
test.length   //输出为 3
delete test[1];
test.length   //输出仍为 3
```

<p>查询运算符delete我们知道它只是将该值置为undefined，而不会影响数组长度，即将其变为稀疏数组（《JS权威指南》7.5节）。</p>

<p>了解及此，也许想着可以将删除点之后的元素各往前移动1个单位，实现彻底剔除该元素，但在JS方法中我们可以查到一种更加简便的方式：splice() 方法</p>

```js
var test = [{ "a": "1", "b": "2" }, { "a": "3", "b": "4" }, { "a": "5", "b": "6" }];
test.length   //输出为 3
test.splice(1, 1);
test.length   //输出为 2
```

<p>删除后test.length变为2，这正是我们想要的结果。</p>
<br>
<p>在W3C中我们可以查到有关于splice()的描述：
<span style="color: #008000">arrayObject.splice(index, howmany, item1, ....., itemX) </span>&nbsp;方法向/从数组中添加/删除项目，然后返回被删除的项目。
参数1：<span style="color: #008000">index</span> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 为插入添加或（和）删除的起始位置；
参数2：<span style="color: #008000">howmany</span> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 指定从数组中添加/删除的元素个数；
参数3：<span style="color: #008000">item1, ....., itemX</span> &nbsp; &nbsp; 可选，选择添加操作时填入，表示需要添加的元素。</p>