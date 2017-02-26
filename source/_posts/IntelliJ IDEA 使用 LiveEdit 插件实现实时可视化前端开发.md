---
layout: post
title: "IntelliJ IDEA 使用 LiveEdit 插件实现实时可视化前端开发"
date: 2016-11-26 11:47:00
comments: true
tags: 
	- IntelliJ IDEA
	- LiveEdit
---

<p>之前因为公司很多都是C#后台项目，所以一直用的Visual Studio开发。而在VS里会自带实时刷新功能，即：在IDE中修改的CSS代码会同步反映在页面上，而不用我们手动F5刷新。</p>
<!-- more -->
<p>先在因为在考虑做自己的个站，所以打算转JSP模式，IDE选择了Jet Brains的IntelliJ IDEA，内置同步发布、数据库管理等等，功能确实很强大，但发现每次修改页面代码都得刷新才能看到效果，着实很麻烦。所以安装了LiveEdit插件来实现实时可视化开发，接下来分享一下安装步骤，非常简单。</p>
<span style="font-family: &quot;Microsoft YaHei&quot;; font-size: 14pt; color: #008000">第一步：下载LiveEdit插件</span>
<hr>
<p>在IDE中进入“文件（File）→设置（Settings）→插件（Plugins）”中查找LiveEdit。如果你用的IDE版本没有内置这一插件，可以点击<img src="http://images2015.cnblogs.com/blog/984702/201611/984702-20161126113502315-1055718248.png" alt="" width="114" height="19">，会自动加载插件列表，这个过程可能会等待一段时间，请保持耐心。然后找到插件，点击下载即可。随后根据提示重启IDE。</p>
<br>
<span style="font-family: &quot;Microsoft YaHei&quot;; font-size: 14pt; color: #008000">第二步：在Chrome中安装JetBrains IDE Support扩展程序</span>
<hr>
<p>仅仅是在IDE中安装好插件还不行，我们还需要得到浏览器的支持。可以点击<a href="http://www.cnplugins.com/devtool/jetbrains-ide-support/download.html" target="_blank">这里</a>下载。下载文件为一个100多K的crx文件，随后点击“更多工具→扩展程序”，如图。</p>
<p><img style="display: block; margin-left: auto; margin-right: auto" src="http://images2015.cnblogs.com/blog/984702/201611/984702-20161126114405221-50584083.png" alt="" width="419" height="331"></p>
<p>将下载的crx文件拖入窗口中即可安装。</p>