---
layout: post
title: "CSS3实现鼠标悬停扩展效果"
date: 2016-08-23 15:47:00
comments: true
tags: 
	- html5
	- css 
---

<p><span style="font-size: 16px;">我们在做导航标签的时候，有时会出现空间过于拥挤需要隐藏部分内容的情况，所以在这里我自己写了一个鼠标悬停显示扩展内容的效果，如下图所示。</span></p>
<p><span style="font-size: 16px;"><img style="display: block; margin-left: auto; margin-right: auto;" src="http://images2015.cnblogs.com/blog/984702/201608/984702-20160823152911948-1947395348.gif" alt="" /></span></p>
<!-- more -->

<p><span style="font-size: 16px;">总的来说效果还是比较好实现，但是比较头疼的是三角部分使用了伪元素::after，而对父元素设置</span> <span style="color: #008000; font-size: 18px;">over-flow:hidden</span> <span style="font-size: 16px;">时也会把伪元素给隐藏掉。最后想的办法是把文字和图标用一个 &lt;span&gt; 包裹住然后对其设置over-flow属性。</span></p>

<p><span style="font-size: 16px;">HTML代码：</span></p>
``` html
<div id="nav">
  <a id="nav-main"><span><i class="icon-home"></i> 主界面</span></a>
  <a id="nav-sum"><span><i class="icon-laptop"></i> 统计界面</span></a>
</div>
```
<span style="font-size: 16px;">CSS代码：</span>
``` css
/*******************************************************************************/
/*********************************** nav **************************************/
/*******************************************************************************/
#nav{
    box-sizing:border-box;
    width:200px;
    height:100%;
    position:fixed;
    padding-top:80px;
}
#nav a{
    display:block;
    width:30px;
    height:52px;
    position:relative;
    margin-top:50px;
}
#nav a span{
    display:inline-block;
    width:46px;
    height:50px;
    font-size:1em;
    font-weight:600;
    color:rgba(255,255,255,0.9);
    text-indent:3px;
    line-height:52px;
    cursor:pointer;
    overflow:hidden;
}
#nav a span i{
    font-size:1.3em;
}
#nav a::after{
    content:'';
    display:block;
    width:0;
    height:0;
    position:absolute;
    right:-32px;
    bottom:0;
    border-top:26px solid transparent;
    border-right:16px solid transparent;
    border-bottom:26px solid transparent;
}
#nav-main{
    background-color:rgb(211,83,80);
}
#nav-sum{
    background-color:rgb(0,158,163);
}
#nav-main::after{
    border-left:16px solid rgb(211,83,80);
}
#nav-sum::after{
    border-left:16px solid rgb(0,158,163);
}
#nav a:hover{
    -webkit-animation:extend-a 0.5s;
    -moz-animation:extend-a 0.5s;
    animation:extend-a 0.5s;
    width:100px;
}
#nav a span:hover{
    -webkit-animation:extend-span 0.5s;
    -moz-animation:extend-span 0.5s;
    animation:extend-span 0.5s;
    width:116px;
}

/******************* a扩展效果 ******************/
@-webkit-keyframes extend-a{
    0% {
        width:30px;
    }
    100% {
        width:100px;
    }
}
@-moz-keyframes extend-a{
    0% {
        width:30px;
    }
    100% {
        width:100px;
    }
}
@keyframes extend-a{
    0% {
        width:30px;
    }
    100% {
        width:100px;
    }
}

/******************* span扩展效果 ******************/
@-webkit-keyframes extend-span{
    0% {
        width:46px;
    }
    100% {
        width:116px;
    }
}
@-moz-keyframes extend-span{
    0% {
        width:46px;
    }
    100% {
        width:116px;
    }
}
@keyframes extend-span{
    0% {
        width:46px;
    }
    100% {
        width:116px;
    }
}
```
<span style="font-size: 16px;">其中图标使用的是&nbsp;<a title="font awesome" href="http://www.bootcss.com/p/font-awesome/" target="_blank">font-awesome</a>&nbsp;提供的API，使用时引入它的css文件即可。</span>