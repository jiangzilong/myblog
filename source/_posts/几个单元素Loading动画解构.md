---
layout: post
title: "几个单元素Loading动画解构"
date: 2016-08-09 16:36:04
comments: true
tags: 
	- html5
	- css 
---

<span style="font-size: 18px;">这个账号建了也有1个多月，拖延症患者终于下定决心开始写博。做前端从前至后差不多1年时间，如果文中有什么纰漏欢迎指出，未来的路还很长~</span>

<img style="width:100px;" src="http://images2015.cnblogs.com/blog/984702/201608/984702-20160809163924356-1369553483.jpg" />

<span style="font-size: 18px;">第一篇文章用来解构几个挺不错的单元素Loading动画。效果图如下：</span>
<!-- more -->

<img style="width:90%;" src="http://images2015.cnblogs.com/blog/984702/201608/984702-20160809111108340-1113592585.gif" />

 Demo中每个动画不考虑容器都只使用一个div标签，通过添加阴影和伪元素再加以动画效果即可实现。下面我们来逐个对图中的动画进行分析。

<span style="font-size: 18px; color: #000000;">总体HTML解构：</span>

<span>&lt;div class="load-container load[index]"&gt;</span>
<span>　　&lt;div class="loader"&gt;Loading...&lt;/div&gt;</span>
<span>&lt;/div&gt;</span>

<span style="font-size: 18px;"><br>CSS分析：</span>

<span style="font-size: 18px; color: #339966;">Demo 1:</span>

第一步：首先通过对loader类添加::before和::after两个[伪元素](http://www.webhek.com/after-before/ "伪元素")副本，和主元素一样width为1em，height为4em。并设置position:absolute，通过调整TRBL到左右位置，实现如下图中的排列（注意需要在父元素上设置position:relative，这样才能根据父元素位置进行定位）。

![](http://images2015.cnblogs.com/blog/984702/201608/984702-20160809113720293-515839813.png)

第二步：设置动画关键帧（@keyframes），关于关键帧的介绍可以参考W3C的[Animation](http://www.w3cplus.com/content/css3-animation "CSS Animation")介绍。设置0%、80%、100%处为初<span style="font-size: 14px;">始状</span>态即 box-shadow: 0 0 #FFF; height: 4em; 在40%处设置为拉伸状态即：box-shadow: 0 -2em #ffffff; height: 5em; <span>其中使用box-shadow是为了实现沿水平轴线伸缩的效果。当然其中的长度我们可以自己设置，选择一个合适的伸缩尺度。考虑兼容性可加上内核前缀。</span>

<span>第三步：将动画添加至三个元素中，并使用animation-delay属性设置不同的时延以达到依次伸缩的效果。</span>

<span>以下是该Loading动画的CSS代码，总的来说还是很容易实现的。</span>
``` css
.load1 .loader,
.load1 .loader:before,
.load1 .loader:after {
  background: #FFF;
  -webkit-animation: load1 1s infinite ease-in-out;
  animation: load1 1s infinite ease-in-out;
  width: 1em;
  height: 4em;
}
.load1 .loader:before,
.load1 .loader:after {
  position: absolute;
  top: 0;
  content: '';
}
.load1 .loader:before {
  left: -1.5em;
  -webkit-animation-delay: -0.32s;
  animation-delay: -0.32s;
}
.load1 .loader {
  text-indent: -9999em;
  margin: 8em auto;
  position: relative;
  font-size: 11px;
  -webkit-animation-delay: -0.16s;
  animation-delay: -0.16s;
}
.load1 .loader:after {
  left: 1.5em;
}
@-webkit-keyframes load1 {
  0%,
  80%,
  100% {
    box-shadow: 0 0 #FFF;
    height: 4em;
  }
  40% {
    box-shadow: 0 -2em #ffffff;
    height: 5em;
  }
}
@keyframes load1 {
  0%,
  80%,
  100% {
    box-shadow: 0 0 #FFF;
    height: 4em;
  }
  40% {
    box-shadow: 0 -2em #ffffff;
    height: 5em;
  }
}
```

<span style="font-size: 18px; color: #008000;">Demo 2:</span>

第一步：首先同样新建两个::before和::after伪元素副本，设置形状：主元素为圆形，加1em的box-shadow，即外面的白色圆环。伪元素为半圆，背景色与容器背景色相同。为了更直观地查看总体结构我们给伪元素加上2px的boder，并将背景调为半透明，如下图所示。

![](http://images2015.cnblogs.com/blog/984702/201608/984702-20160809145643027-1400836186.gif)

第二步：设置关键帧，使用CSS Transform属性，具体介绍可参考[Transform](http://www.w3cplus.com/content/css3-transform "CSS3 Transform")。0%处设置为 transform: rotate(0deg); 100%处设置为 transform: rotate(360deg); 

第三步：将动画效果加入到两个伪元素中。在此一定要<span style="color: #000000;">注意</span>设置它们的<span style="color: #ff0000;">transform-origin</span>属性，即旋转中心点，这在上面的Transform参考文章中也有提到。因为CSS会默认绕着元素中心点进行旋转，而在此我们需要它实现绕主元素中心点进行旋转变换。

 以下为Demo2的CSS代码：
``` css
.load2 .loader,
.load2 .loader:before,
.load2 .loader:after {
  border-radius: 50%;
}
.load2 .loader:before,
.load2 .loader:after {
  position: absolute;
  content: '';
}
.load2 .loader:before {
  width: 5.2em;
  height: 10.2em;
  background: #0dcecb;
  border-radius: 10.2em 0 0 10.2em;
  top: -0.1em;
  left: -0.1em;
  -webkit-transform-origin: 5.2em 5.1em;
  transform-origin: 5.2em 5.1em;
  -webkit-animation: load2 2s infinite ease 1.5s;
  animation: load2 2s infinite ease 1.5s;
}
.load2 .loader {
  font-size: 11px;
  text-indent: -99999em;
  margin: 5em auto;
  position: relative;
  width: 10em;
  height: 10em;
  box-shadow: inset 0 0 0 1em #FFF;
}
.load2 .loader:after {
  width: 5.2em;
  height: 10.2em;
  background: #0dcecb;
  border-radius: 0 10.2em 10.2em 0;
  top: -0.1em;
  left: 5.1em;
  -webkit-transform-origin: 0px 5.1em;
  transform-origin: 0px 5.1em;
  -webkit-animation: load2 2s infinite ease;
  animation: load2 2s infinite ease;
}
@-webkit-keyframes load2 {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
@keyframes load2 {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
```

<span style="font-size: 18px; color: #008000;">Demo 3:</span>

第一步：主元素为一个从左至右渐变的圆形，如下图所示。在此使用了[linear-gradient](http://www.w3cplus.com/content/css3-gradient "gradient")，该属性已得到普遍兼容，如果需要兼容老版本的IE，可考虑使用滤镜（filter）实现。

![](http://images2015.cnblogs.com/blog/984702/201608/984702-20160809151458777-2107672049.png)

第二步：添加::before和::after伪元素副本，从下方左图中可以看出，伪元素一个为半径稍小的圆形，背景色与容器颜色相同，一个为1/4扇形，颜色为白色。最终效果如右图所示。

![](http://images2015.cnblogs.com/blog/984702/201608/984702-20160809152348184-224171678.png)              ![](http://images2015.cnblogs.com/blog/984702/201608/984702-20160809152717215-648508843.png)

第三步：接下来的事就顺理成章了，设置关键帧加入旋转效果即可。

CSS代码如下：
``` css
.load3 .loader {
  font-size: 10px;
  margin: 5em auto;
  text-indent: -9999em;
  width: 11em;
  height: 11em;
  border-radius: 50%;
  background: #ffffff;
  background: -moz-linear-gradient(left, #ffffff 10%, rgba(255, 255, 255, 0) 42%);
  background: -webkit-linear-gradient(left, #ffffff 10%, rgba(255, 255, 255, 0) 42%);
  background: -o-linear-gradient(left, #ffffff 10%, rgba(255, 255, 255, 0) 42%);
  background: -ms-linear-gradient(left, #ffffff 10%, rgba(255, 255, 255, 0) 42%);
  background: linear-gradient(to right, #ffffff 10%, rgba(255, 255, 255, 0) 42%);
  position: relative;
  -webkit-animation: load3 1.4s infinite linear;
  animation: load3 1.4s infinite linear;
}
.load3 .loader:before {
  width: 50%;
  height: 50%;
  background: #FFF;
  border-radius: 100% 0 0 0;
  position: absolute;
  top: 0;
  left: 0;
  content: '';
}
.load3 .loader:after {
  background: #0dcecb;
  width: 75%;
  height: 75%;
  border-radius: 50%;
  content: '';
  margin: auto;
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
}
@-webkit-keyframes load3 {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
@keyframes load3 {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
```

<span style="font-size: 18px; color: #008000;">Demo 4:</span>

第一步：此效果只需要一个主元素，随后在动画中加入数个box-shadow即可。从下图可以看到主元素为1个小圆形，将其周围分为8个区域，每个地方可存放一个box-shadow副本（我们完全可以把box-shadow创造出的阴影看做主元素的拷贝，就像text-shadow那样）。

![](http://images2015.cnblogs.com/blog/984702/201608/984702-20160809153431684-384651845.png)

第二步：定义关键帧，在0%、12.5%、25%、37.5%、50%、62.5%、75%、87.5%、100%处设置box-shadow的大小，让其顺时针或者逆时针逐个增大。

第三步：加入动画效果。

CSS代码如下：
``` css
.load4 .loader {
  font-size: 20px;
  margin: 5em auto;
  width: 1em;
  height: 1em;
  border-radius: 50%;
  position: relative;
  text-indent: -9999em;
  -webkit-animation: load4 1.3s infinite linear;
  animation: load4 1.3s infinite linear;
}
@-webkit-keyframes load4 {
  0%,
  100% {
    box-shadow: 0em -3em 0em 0.2em #ffffff, 2em -2em 0 0em #ffffff, 3em 0em 0 -0.5em #ffffff, 2em 2em 0 -0.5em #ffffff, 0em 3em 0 -0.5em #ffffff, -2em 2em 0 -0.5em #ffffff, -3em 0em 0 -0.5em #ffffff, -2em -2em 0 0em #ffffff;
  }
  12.5% {
    box-shadow: 0em -3em 0em 0em #ffffff, 2em -2em 0 0.2em #ffffff, 3em 0em 0 0em #ffffff, 2em 2em 0 -0.5em #ffffff, 0em 3em 0 -0.5em #ffffff, -2em 2em 0 -0.5em #ffffff, -3em 0em 0 -0.5em #ffffff, -2em -2em 0 -0.5em #ffffff;
  }
  25% {
    box-shadow: 0em -3em 0em -0.5em #ffffff, 2em -2em 0 0em #ffffff, 3em 0em 0 0.2em #ffffff, 2em 2em 0 0em #ffffff, 0em 3em 0 -0.5em #ffffff, -2em 2em 0 -0.5em #ffffff, -3em 0em 0 -0.5em #ffffff, -2em -2em 0 -0.5em #ffffff;
  }
  37.5% {
    box-shadow: 0em -3em 0em -0.5em #ffffff, 2em -2em 0 -0.5em #ffffff, 3em 0em 0 0em #ffffff, 2em 2em 0 0.2em #ffffff, 0em 3em 0 0em #ffffff, -2em 2em 0 -0.5em #ffffff, -3em 0em 0 -0.5em #ffffff, -2em -2em 0 -0.5em #ffffff;
  }
  50% {
    box-shadow: 0em -3em 0em -0.5em #ffffff, 2em -2em 0 -0.5em #ffffff, 3em 0em 0 -0.5em #ffffff, 2em 2em 0 0em #ffffff, 0em 3em 0 0.2em #ffffff, -2em 2em 0 0em #ffffff, -3em 0em 0 -0.5em #ffffff, -2em -2em 0 -0.5em #ffffff;
  }
  62.5% {
    box-shadow: 0em -3em 0em -0.5em #ffffff, 2em -2em 0 -0.5em #ffffff, 3em 0em 0 -0.5em #ffffff, 2em 2em 0 -0.5em #ffffff, 0em 3em 0 0em #ffffff, -2em 2em 0 0.2em #ffffff, -3em 0em 0 0em #ffffff, -2em -2em 0 -0.5em #ffffff;
  }
  75% {
    box-shadow: 0em -3em 0em -0.5em #ffffff, 2em -2em 0 -0.5em #ffffff, 3em 0em 0 -0.5em #ffffff, 2em 2em 0 -0.5em #ffffff, 0em 3em 0 -0.5em #ffffff, -2em 2em 0 0em #ffffff, -3em 0em 0 0.2em #ffffff, -2em -2em 0 0em #ffffff;
  }
  87.5% {
    box-shadow: 0em -3em 0em 0em #ffffff, 2em -2em 0 -0.5em #ffffff, 3em 0em 0 -0.5em #ffffff, 2em 2em 0 -0.5em #ffffff, 0em 3em 0 -0.5em #ffffff, -2em 2em 0 0em #ffffff, -3em 0em 0 0em #ffffff, -2em -2em 0 0.2em #ffffff;
  }
}
@keyframes load4 {
  0%,
  100% {
    box-shadow: 0em -3em 0em 0.2em #ffffff, 2em -2em 0 0em #ffffff, 3em 0em 0 -0.5em #ffffff, 2em 2em 0 -0.5em #ffffff, 0em 3em 0 -0.5em #ffffff, -2em 2em 0 -0.5em #ffffff, -3em 0em 0 -0.5em #ffffff, -2em -2em 0 0em #ffffff;
  }
  12.5% {
    box-shadow: 0em -3em 0em 0em #ffffff, 2em -2em 0 0.2em #ffffff, 3em 0em 0 0em #ffffff, 2em 2em 0 -0.5em #ffffff, 0em 3em 0 -0.5em #ffffff, -2em 2em 0 -0.5em #ffffff, -3em 0em 0 -0.5em #ffffff, -2em -2em 0 -0.5em #ffffff;
  }
  25% {
    box-shadow: 0em -3em 0em -0.5em #ffffff, 2em -2em 0 0em #ffffff, 3em 0em 0 0.2em #ffffff, 2em 2em 0 0em #ffffff, 0em 3em 0 -0.5em #ffffff, -2em 2em 0 -0.5em #ffffff, -3em 0em 0 -0.5em #ffffff, -2em -2em 0 -0.5em #ffffff;
  }
  37.5% {
    box-shadow: 0em -3em 0em -0.5em #ffffff, 2em -2em 0 -0.5em #ffffff, 3em 0em 0 0em #ffffff, 2em 2em 0 0.2em #ffffff, 0em 3em 0 0em #ffffff, -2em 2em 0 -0.5em #ffffff, -3em 0em 0 -0.5em #ffffff, -2em -2em 0 -0.5em #ffffff;
  }
  50% {
    box-shadow: 0em -3em 0em -0.5em #ffffff, 2em -2em 0 -0.5em #ffffff, 3em 0em 0 -0.5em #ffffff, 2em 2em 0 0em #ffffff, 0em 3em 0 0.2em #ffffff, -2em 2em 0 0em #ffffff, -3em 0em 0 -0.5em #ffffff, -2em -2em 0 -0.5em #ffffff;
  }
  62.5% {
    box-shadow: 0em -3em 0em -0.5em #ffffff, 2em -2em 0 -0.5em #ffffff, 3em 0em 0 -0.5em #ffffff, 2em 2em 0 -0.5em #ffffff, 0em 3em 0 0em #ffffff, -2em 2em 0 0.2em #ffffff, -3em 0em 0 0em #ffffff, -2em -2em 0 -0.5em #ffffff;
  }
  75% {
    box-shadow: 0em -3em 0em -0.5em #ffffff, 2em -2em 0 -0.5em #ffffff, 3em 0em 0 -0.5em #ffffff, 2em 2em 0 -0.5em #ffffff, 0em 3em 0 -0.5em #ffffff, -2em 2em 0 0em #ffffff, -3em 0em 0 0.2em #ffffff, -2em -2em 0 0em #ffffff;
  }
  87.5% {
    box-shadow: 0em -3em 0em 0em #ffffff, 2em -2em 0 -0.5em #ffffff, 3em 0em 0 -0.5em #ffffff, 2em 2em 0 -0.5em #ffffff, 0em 3em 0 -0.5em #ffffff, -2em 2em 0 0em #ffffff, -3em 0em 0 0em #ffffff, -2em -2em 0 0.2em #ffffff;
  }
}
```

<span style="font-size: 18px; color: #008000;">Demo 5:</span>

跟Demo 4非常类似，只是将设置box-shadow的大小改为设置其颜色透明度。

附上代码：
``` css
.load5 .loader {
  margin: 4em auto;
  font-size: 25px;
  width: 1em;
  height: 1em;
  border-radius: 50%;
  position: relative;
  text-indent: -9999em;
  -webkit-animation: load5 1.1s infinite ease;
  animation: load5 1.1s infinite ease;
}
@-webkit-keyframes load5 {
  0%,
  100% {
    box-shadow: 0em -2.6em 0em 0em #ffffff, 1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2), 2.5em 0em 0 0em rgba(255, 255, 255, 0.2), 1.75em 1.75em 0 0em rgba(255, 255, 255, 0.2), 0em 2.5em 0 0em rgba(255, 255, 255, 0.2), -1.8em 1.8em 0 0em rgba(255, 255, 255, 0.2), -2.6em 0em 0 0em rgba(255, 255, 255, 0.5), -1.8em -1.8em 0 0em rgba(255, 255, 255, 0.7);
  }
  12.5% {
    box-shadow: 0em -2.6em 0em 0em rgba(255, 255, 255, 0.7), 1.8em -1.8em 0 0em #ffffff, 2.5em 0em 0 0em rgba(255, 255, 255, 0.2), 1.75em 1.75em 0 0em rgba(255, 255, 255, 0.2), 0em 2.5em 0 0em rgba(255, 255, 255, 0.2), -1.8em 1.8em 0 0em rgba(255, 255, 255, 0.2), -2.6em 0em 0 0em rgba(255, 255, 255, 0.2), -1.8em -1.8em 0 0em rgba(255, 255, 255, 0.5);
  }
  25% {
    box-shadow: 0em -2.6em 0em 0em rgba(255, 255, 255, 0.5), 1.8em -1.8em 0 0em rgba(255, 255, 255, 0.7), 2.5em 0em 0 0em #ffffff, 1.75em 1.75em 0 0em rgba(255, 255, 255, 0.2), 0em 2.5em 0 0em rgba(255, 255, 255, 0.2), -1.8em 1.8em 0 0em rgba(255, 255, 255, 0.2), -2.6em 0em 0 0em rgba(255, 255, 255, 0.2), -1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2);
  }
  37.5% {
    box-shadow: 0em -2.6em 0em 0em rgba(255, 255, 255, 0.2), 1.8em -1.8em 0 0em rgba(255, 255, 255, 0.5), 2.5em 0em 0 0em rgba(255, 255, 255, 0.7), 1.75em 1.75em 0 0em rgba(255, 255, 255, 0.2), 0em 2.5em 0 0em rgba(255, 255, 255, 0.2), -1.8em 1.8em 0 0em rgba(255, 255, 255, 0.2), -2.6em 0em 0 0em rgba(255, 255, 255, 0.2), -1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2);
  }
  50% {
    box-shadow: 0em -2.6em 0em 0em rgba(255, 255, 255, 0.2), 1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2), 2.5em 0em 0 0em rgba(255, 255, 255, 0.5), 1.75em 1.75em 0 0em rgba(255, 255, 255, 0.7), 0em 2.5em 0 0em #ffffff, -1.8em 1.8em 0 0em rgba(255, 255, 255, 0.2), -2.6em 0em 0 0em rgba(255, 255, 255, 0.2), -1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2);
  }
  62.5% {
    box-shadow: 0em -2.6em 0em 0em rgba(255, 255, 255, 0.2), 1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2), 2.5em 0em 0 0em rgba(255, 255, 255, 0.2), 1.75em 1.75em 0 0em rgba(255, 255, 255, 0.5), 0em 2.5em 0 0em rgba(255, 255, 255, 0.7), -1.8em 1.8em 0 0em #ffffff, -2.6em 0em 0 0em rgba(255, 255, 255, 0.2), -1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2);
  }
  75% {
    box-shadow: 0em -2.6em 0em 0em rgba(255, 255, 255, 0.2), 1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2), 2.5em 0em 0 0em rgba(255, 255, 255, 0.2), 1.75em 1.75em 0 0em rgba(255, 255, 255, 0.2), 0em 2.5em 0 0em rgba(255, 255, 255, 0.5), -1.8em 1.8em 0 0em rgba(255, 255, 255, 0.7), -2.6em 0em 0 0em #ffffff, -1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2);
  }
  87.5% {
    box-shadow: 0em -2.6em 0em 0em rgba(255, 255, 255, 0.2), 1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2), 2.5em 0em 0 0em rgba(255, 255, 255, 0.2), 1.75em 1.75em 0 0em rgba(255, 255, 255, 0.2), 0em 2.5em 0 0em rgba(255, 255, 255, 0.2), -1.8em 1.8em 0 0em rgba(255, 255, 255, 0.5), -2.6em 0em 0 0em rgba(255, 255, 255, 0.7), -1.8em -1.8em 0 0em #ffffff;
  }
}
@keyframes load5 {
  0%,
  100% {
    box-shadow: 0em -2.6em 0em 0em #ffffff, 1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2), 2.5em 0em 0 0em rgba(255, 255, 255, 0.2), 1.75em 1.75em 0 0em rgba(255, 255, 255, 0.2), 0em 2.5em 0 0em rgba(255, 255, 255, 0.2), -1.8em 1.8em 0 0em rgba(255, 255, 255, 0.2), -2.6em 0em 0 0em rgba(255, 255, 255, 0.5), -1.8em -1.8em 0 0em rgba(255, 255, 255, 0.7);
  }
  12.5% {
    box-shadow: 0em -2.6em 0em 0em rgba(255, 255, 255, 0.7), 1.8em -1.8em 0 0em #ffffff, 2.5em 0em 0 0em rgba(255, 255, 255, 0.2), 1.75em 1.75em 0 0em rgba(255, 255, 255, 0.2), 0em 2.5em 0 0em rgba(255, 255, 255, 0.2), -1.8em 1.8em 0 0em rgba(255, 255, 255, 0.2), -2.6em 0em 0 0em rgba(255, 255, 255, 0.2), -1.8em -1.8em 0 0em rgba(255, 255, 255, 0.5);
  }
  25% {
    box-shadow: 0em -2.6em 0em 0em rgba(255, 255, 255, 0.5), 1.8em -1.8em 0 0em rgba(255, 255, 255, 0.7), 2.5em 0em 0 0em #ffffff, 1.75em 1.75em 0 0em rgba(255, 255, 255, 0.2), 0em 2.5em 0 0em rgba(255, 255, 255, 0.2), -1.8em 1.8em 0 0em rgba(255, 255, 255, 0.2), -2.6em 0em 0 0em rgba(255, 255, 255, 0.2), -1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2);
  }
  37.5% {
    box-shadow: 0em -2.6em 0em 0em rgba(255, 255, 255, 0.2), 1.8em -1.8em 0 0em rgba(255, 255, 255, 0.5), 2.5em 0em 0 0em rgba(255, 255, 255, 0.7), 1.75em 1.75em 0 0em rgba(255, 255, 255, 0.2), 0em 2.5em 0 0em rgba(255, 255, 255, 0.2), -1.8em 1.8em 0 0em rgba(255, 255, 255, 0.2), -2.6em 0em 0 0em rgba(255, 255, 255, 0.2), -1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2);
  }
  50% {
    box-shadow: 0em -2.6em 0em 0em rgba(255, 255, 255, 0.2), 1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2), 2.5em 0em 0 0em rgba(255, 255, 255, 0.5), 1.75em 1.75em 0 0em rgba(255, 255, 255, 0.7), 0em 2.5em 0 0em #ffffff, -1.8em 1.8em 0 0em rgba(255, 255, 255, 0.2), -2.6em 0em 0 0em rgba(255, 255, 255, 0.2), -1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2);
  }
  62.5% {
    box-shadow: 0em -2.6em 0em 0em rgba(255, 255, 255, 0.2), 1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2), 2.5em 0em 0 0em rgba(255, 255, 255, 0.2), 1.75em 1.75em 0 0em rgba(255, 255, 255, 0.5), 0em 2.5em 0 0em rgba(255, 255, 255, 0.7), -1.8em 1.8em 0 0em #ffffff, -2.6em 0em 0 0em rgba(255, 255, 255, 0.2), -1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2);
  }
  75% {
    box-shadow: 0em -2.6em 0em 0em rgba(255, 255, 255, 0.2), 1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2), 2.5em 0em 0 0em rgba(255, 255, 255, 0.2), 1.75em 1.75em 0 0em rgba(255, 255, 255, 0.2), 0em 2.5em 0 0em rgba(255, 255, 255, 0.5), -1.8em 1.8em 0 0em rgba(255, 255, 255, 0.7), -2.6em 0em 0 0em #ffffff, -1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2);
  }
  87.5% {
    box-shadow: 0em -2.6em 0em 0em rgba(255, 255, 255, 0.2), 1.8em -1.8em 0 0em rgba(255, 255, 255, 0.2), 2.5em 0em 0 0em rgba(255, 255, 255, 0.2), 1.75em 1.75em 0 0em rgba(255, 255, 255, 0.2), 0em 2.5em 0 0em rgba(255, 255, 255, 0.2), -1.8em 1.8em 0 0em rgba(255, 255, 255, 0.5), -2.6em 0em 0 0em rgba(255, 255, 255, 0.7), -1.8em -1.8em 0 0em #ffffff;
  }
}
```

<span style="font-size: 18px; color: #008000;">Demo 6:</span>

第一步：运用Demo 4、5的思路，在关键帧中给主元素创建5个box-shadow副本。

![](http://images2015.cnblogs.com/blog/984702/201608/984702-20160809155844496-1332090420.png)

第二步：在0%、5%、95%、100%处五个副本处于重叠状态，再在30%、55%处设置其分离状态，并加入transform的旋转效果即可实现小球的加速、减速模拟。我们不必在关键帧中定义太多节点，因为CSS会自动去处理中间的过渡状态。

第三步：将动画加入到主元素中。

CSS代码如下：
``` css
.load6 .loader {
  font-size: 90px;
  text-indent: -9999em;
  overflow: hidden;
  width: 1em;
  height: 1em;
  border-radius: 50%;
  margin: 0.8em auto;
  position: relative;
  -webkit-animation: load6 1.7s infinite ease;
  animation: load6 1.7s infinite ease;
}
@-webkit-keyframes load6 {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
    box-shadow: -0.11em -0.83em 0 -0.4em #ffffff, -0.11em -0.83em 0 -0.42em #ffffff, -0.11em -0.83em 0 -0.44em #ffffff, -0.11em -0.83em 0 -0.46em #ffffff, -0.11em -0.83em 0 -0.477em #ffffff;
  }
  5%,
  95% {
    box-shadow: -0.11em -0.83em 0 -0.4em #ffffff, -0.11em -0.83em 0 -0.42em #ffffff, -0.11em -0.83em 0 -0.44em #ffffff, -0.11em -0.83em 0 -0.46em #ffffff, -0.11em -0.83em 0 -0.477em #ffffff;
  }
  30% {
    box-shadow: -0.11em -0.83em 0 -0.4em #ffffff, -0.51em -0.66em 0 -0.42em #ffffff, -0.75em -0.36em 0 -0.44em #ffffff, -0.83em -0.03em 0 -0.46em #ffffff, -0.81em 0.21em 0 -0.477em #ffffff;
  }
  55% {
    box-shadow: -0.11em -0.83em 0 -0.4em #ffffff, -0.29em -0.78em 0 -0.42em #ffffff, -0.43em -0.72em 0 -0.44em #ffffff, -0.52em -0.65em 0 -0.46em #ffffff, -0.57em -0.61em 0 -0.477em #ffffff;
  }
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
    box-shadow: -0.11em -0.83em 0 -0.4em #ffffff, -0.11em -0.83em 0 -0.42em #ffffff, -0.11em -0.83em 0 -0.44em #ffffff, -0.11em -0.83em 0 -0.46em #ffffff, -0.11em -0.83em 0 -0.477em #ffffff;
  }
}
@keyframes load6 {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
    box-shadow: -0.11em -0.83em 0 -0.4em #ffffff, -0.11em -0.83em 0 -0.42em #ffffff, -0.11em -0.83em 0 -0.44em #ffffff, -0.11em -0.83em 0 -0.46em #ffffff, -0.11em -0.83em 0 -0.477em #ffffff;
  }
  5%,
  95% {
    box-shadow: -0.11em -0.83em 0 -0.4em #ffffff, -0.11em -0.83em 0 -0.42em #ffffff, -0.11em -0.83em 0 -0.44em #ffffff, -0.11em -0.83em 0 -0.46em #ffffff, -0.11em -0.83em 0 -0.477em #ffffff;
  }
  30% {
    box-shadow: -0.11em -0.83em 0 -0.4em #ffffff, -0.51em -0.66em 0 -0.42em #ffffff, -0.75em -0.36em 0 -0.44em #ffffff, -0.83em -0.03em 0 -0.46em #ffffff, -0.81em 0.21em 0 -0.477em #ffffff;
  }
  55% {
    box-shadow: -0.11em -0.83em 0 -0.4em #ffffff, -0.29em -0.78em 0 -0.42em #ffffff, -0.43em -0.72em 0 -0.44em #ffffff, -0.52em -0.65em 0 -0.46em #ffffff, -0.57em -0.61em 0 -0.477em #ffffff;
  }
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
    box-shadow: -0.11em -0.83em 0 -0.4em #ffffff, -0.11em -0.83em 0 -0.42em #ffffff, -0.11em -0.83em 0 -0.44em #ffffff, -0.11em -0.83em 0 -0.46em #ffffff, -0.11em -0.83em 0 -0.477em #ffffff;
  }
}
```

<span style="font-size: 18px; color: #008000;">Demo 7:</span>

第一眼看着类似于Demo 1和Demo 4的结合，实现方法其实不止一种。一种思路是创建两个伪元素然后给三个元素都创建box-shadow，控制阴影大小和动画时延即可；另一种思路是像前几个例子一样只需要对主元素创建两个box-shadow副本，并在关键帧中对各节点设置对应大小。Demo中使用的是前一种方法。

附上CSS代码：
``` css
.load7 .loader:before,
.load7 .loader:after,
.load7 .loader {
  border-radius: 50%;
  width: 2.5em;
  height: 2.5em;
  -webkit-animation-fill-mode: both;
  animation-fill-mode: both;
  -webkit-animation: load7 1.8s infinite ease-in-out;
  animation: load7 1.8s infinite ease-in-out;
}
.load7 .loader {
  margin: 8em auto;
  font-size: 10px;
  position: relative;
  text-indent: -9999em;
  -webkit-animation-delay: -0.16s;
  animation-delay: -0.16s;
}
.load7 .loader:before {
  left: -3.5em;
  -webkit-animation-delay: -0.32s;
  animation-delay: -0.32s;
}
.load7 .loader:after {
  left: 3.5em;
}
.load7 .loader:before,
.loader:after {
  content: '';
  position: absolute;
  top: 0;
}
@-webkit-keyframes load7 {
  0%,
  80%,
  100% {
    box-shadow: 0 2.5em 0 -1.3em #ffffff;
  }
  40% {
    box-shadow: 0 2.5em 0 0 #FFF;
  }
}
@keyframes load7 {
  0%,
  80%,
  100% {
    box-shadow: 0 2.5em 0 -1.3em #ffffff;
  }
  40% {
    box-shadow: 0 2.5em 0 0 #FFF;
  }
}
```

<span style="font-size: 18px; color: #008000;">Demo 8:</span>

相信有了Demo 2的例子很多朋友都会马上想到创建两个伪元素交替覆盖的方法，不过其实也不用那么复杂，我们只需要对主元素的border-top、border-right、border-bottom、border-left分别进行设置即可，类似于[CSS三角形](http://www.cnblogs.com/keepfool/p/5616326.html "CSS三角形")的制作原理。

![](http://images2015.cnblogs.com/blog/984702/201608/984702-20160809161932027-909167491.png)

附上CSS代码：
``` css
.load8 .loader {
  margin: 6em auto;
  font-size: 10px;
  position: relative;
  text-indent: -9999em;
  border-top: 1.1em solid rgba(255, 255, 255, 0.2);
  border-right: 1.1em solid rgba(255, 255, 255, 0.2);
  border-bottom: 1.1em solid rgba(255, 255, 255, 0.2);
  border-left: 1.1em solid #ffffff;
  -webkit-animation: load8 1.1s infinite linear;
  animation: load8 1.1s infinite linear;
}
.load8 .loader,
.load8 .loader:after {
  border-radius: 50%;
  width: 10em;
  height: 10em;
}
@-webkit-keyframes load8 {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
@keyframes load8 {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
```

<span style="font-size: 16px;"><span style="color: #ff4500; font-size: 14pt;"> 最后的最后，刚好今天是七夕，不管是人是犬都祝福大家节日快乐啦！</span>![](http://images2015.cnblogs.com/blog/984702/201608/984702-20160809162917121-449128484.png)</span>
