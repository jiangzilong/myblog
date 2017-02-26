---
layout: post
title: "CSS伪类:first-child与:first-of-type的异同"
date: 2016-08-09 16:36:04
comments: true
tags: 
	- html5
	- css 
---

<div id="cnblogs_post_body"><p><span style="font-size: 16px;">CSS里关于元素匹配里面有两个非常类似却又不尽相同的选择器，伪类&nbsp;</span><span style="font-size: 18px; color: #008000;">:first-child&nbsp;</span><span style="font-size: 16px;">和&nbsp;</span><span style="font-size: 18px; color: #008000;">:first-of-type</span><span style="font-size: 16px;"><br /></span></p>
<p><span style="font-size: 16px;">两者在匹配方式上有很大差异，其实在一开始自己也没去注意这个细节，直到上次一个同事遇到同级元素匹配失败的问题后才发现原来以前的理解都不正确。</span></p>
<!-- more -->
<span style="font-size: 16px;">先来看看可爱的同事遇到的问题：</span>
<span style="color: #ff6600;">&nbsp;HTML代码</span>
``` html
<div id="add_road">
     <span class="input-title">路段 I D</span>
     <input id="road_id" placeholder="road-id" value="S12"/>
     <span class="required">*</span>
     <span class="input-title">路段名称</span>
     <input id="road_name" placeholder="rode-name" value="苏州桥"/>
     <span class="required">*</span>
</div>
```
<span style="color: #ff6600;">&nbsp;CSS代码</span>
``` css
.add_input input:first-child{
    color:#3c0; 
}
.add_input input:last-child{
    color:#c30; 
}
```
<span style="font-size: 16px;">在此为了测试方便对代码做了一些调整。效果如图：</span>
<p><img src="http://images2015.cnblogs.com/blog/984702/201608/984702-20160816153143562-961845516.png" alt="" /></p>

<p><span style="font-size: 16px;">可是发现我们对字体颜色进行设置的CSS代码没有效果，这跟我之前理解的:first-child和:last-child就有些出入。</span></p>
<p><span style="font-size: 16px;">那么接下来我们把最后一个&lt;span&gt;去掉</span></p>
<p><span style="font-size: 16px;">即：</span></p>
<p><span style="color: #ff6600;">&nbsp;删减后的HTML代码</span></p>
``` html
<div id="add_road">
     <span class="input-title">路段 I D</span>
     <input id="road_id" placeholder="road-id" value="S12"/>
     <span class="required">*</span>
     <span class="input-title">路段名称</span>
     <input id="road_name" placeholder="rode-name" value="苏州桥"/>
</div>
```
<span style="font-size: 16px;">效果如图：</span>
<p><img src="http://images2015.cnblogs.com/blog/984702/201608/984702-20160816153525953-604074472.png" alt="" /></p>
<p><span style="font-size: 16px;">我们发现：咦~ :last-child生效了，可我们想要的效果是一个为绿色，一个为红色，这显然还有差距。</span></p>
<p><span style="font-size: 16px;">然后我们再把第一个span删掉：</span></p>
<p>&nbsp;&nbsp;<span style="color: #ff6600;">删减后的HTML代码</span></p>
``` html
<div id="add_road">
     <input id="road_id" placeholder="road-id" value="S12"/>
     <span class="required">*</span>
     <span class="input-title">路段名称</span>
     <input id="road_name" placeholder="rode-name" value="苏州桥"/>
</div>
```
<span style="font-size: 16px;">效果如图：</span>
<p><img src="http://images2015.cnblogs.com/blog/984702/201608/984702-20160816153917703-212440348.png" alt="" /></p>
<p><span style="font-size: 16px;">结果不错，虽然没有标题很丑，但是至少两个伪类都有效果了。</span></p>
<p><span style="font-size: 16px;">那现在我们需要分析一下为什么会出现这种结果。</span></p>
<p><span style="font-size: 16px;">首先对:first-child和:first-of-type用一句话做出总结：</span></p>
<p><span style="font-size: 16px; color: #008000;">A B:first-child &nbsp; &nbsp; <span style="color: #000000;">&mdash;&mdash;</span>&nbsp;<span style="color: #000000;">A元素的第一个子元素，且这个子元素为B，<span style="color: #993300;">如果不是则CSS失效</span><br /><span style="color: #008000;">A B:first-of-type</span>&nbsp; &mdash;&mdash;&nbsp;A元素的第一个为B的子元素</span></span></p>

<p><span style="font-size: 16px;">也就是说在使用:first-child 时需要注意元素之前不能有其他同级元素，不然会使得样式失效，而在不确定的情况下可使用first-of-type属性</span></p>
<p><span style="font-size: 16px;">在<a title="CSS参考手册" href="http://css.doyoe.com/" target="_blank">CSS参考手册</a>上有个关于:nth-child 的例子挺不错，可以帮助我们更有效地理解这个伪类</span></p>
<p>&nbsp;&nbsp;<span style="color: #ff6600;">示例代码</span></p>
``` html
<div>
    <p>第1个p</p>
    <p>第2个p</p>
    <span>第1个span</span>
    <p>第3个p</p>
    <span>第2个span</span>
    <p>第4个p</p>
    <p>第5个p</p>
</div>
```
<span style="font-size: 16px;">当我们使用&nbsp;p:nth-child(3){color:#f00;} 时发现并没有任何效果，而再尝试&nbsp;p:nth-child(4){color:#f00;} 可以看到&nbsp;<span style="color: #008000;">&lt;p&gt;第3个p&lt;/p&gt; <span style="color: #000000;">这一元素变为了红色而不是</span>&nbsp;&lt;p&gt;第4个p&lt;/p&gt;</span></span>
<p><span style="color: #000000; font-size: 16px;">在此对于 :nth-child(n) 的解释为：&nbsp;<span style="color: #008000;">E:nth-child(n)&nbsp;</span>会选择父元素的第n个子元素E，如果第n个子元素不是E，则是无效选择符，但n会递增</span></p>
<p><span style="color: #000000; font-size: 16px;">大家可以据此理解两个伪类之间的区别于联系。</span></p>

<p><span style="color: #000000; font-size: 16px;">祝生活愉快~</span></p>