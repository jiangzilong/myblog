---
layout: post
title: "树莓派3（Raspbain系统）安装.net环境"
date: 2016-09-27 15:41:00
comments: true
tags: 
	- 树莓派3
	- .net
---

<p>因为公司之前做的网站项目都是基于微软的.net平台，现在需要在树莓派3上测试它是否能负载起正常的访问请求。最开始直接的想到微软3月份刚发布针对于树莓派3的win10系统，其实说是win10,也就是一个超级精简的嵌入式系统，不过看支持列表里包括win10，所以就打算先试一试。</p>
<!-- more -->
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="http://images2015.cnblogs.com/blog/984702/201609/984702-20160927142910203-1868102429.png" alt="" /></p>
<br />
<p>结果发现兼容树莓派3的Windows 10 IoT系统只有预览版本，正式版只是针对于2代，3的正式版还未发布。而所谓的核心预览版。。。。。。对于没接触过底层开发的我来说就是一个带着图形界面的硬盘 [尴尬脸]</p>
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="http://images2015.cnblogs.com/blog/984702/201609/984702-20160927143106031-40165117.png" alt="" /></p>

<p>无奈之下重新安装了树莓派官方的 Raspbain 系统，linux 内核，自己之前也做过 ubuntu 系统的服务器搭建，好歹也会熟悉一些。下面进入正题。
总的来说在 linux 系统下搭建.net环境的组合是 mono+jexus ，mono主要作用是提供.net环境，使系统可以运行C#程序；而jexus则类似于window下的IIS，linux下的tomcat，作为网站的管理服务存在，提供一个可供外网访问的接口。jexus也存在不依赖于mono的独立版，在这儿没有采用这种方法，有兴趣可以<a title="jexus独立版" href="http://www.cnblogs.com/yunei/p/5452120.html" target="_blank">戳这里</a>。</p>
<br />
<span style="font-size: 18pt; font-family: 'Microsoft YaHei'; color: #339966;">第一步：安装mono</span>
<hr />
<p>&nbsp;搜集了很多网上的资料，根据文中提到的版本去下载安装发现在下一步安装jexus的时候会提示需要安装mono或.net环境，说明上一步没有安装成功，但仔细检查没有发现问题，看来还是文件包不行，最后直接从库里下载安装成功。</p>
<pre>sudo apt-get install mono-complete</pre>
<p>安装完成后输入</p>
<pre>mono -V</pre>
<p>会显示当前版本号和版本信息，随后用一句简单的C#语句用来测试</p>
<p>输入 csharp ，随后在命令中输入</p>
<pre>Console.WriteLine(DateTime.Now);</pre>
<p>可以得到系统时间。</p>
<p>&nbsp;<img style="display: block; margin-left: auto; margin-right: auto;" src="http://images2015.cnblogs.com/blog/984702/201609/984702-20160927145319750-496190871.png" alt="" width="567" height="312" /></p>
<p>PS:这里是服务器本地的时间，可以通过&nbsp;date -s "2016-09-27 00:00:00" 命令进行纠正。</p>
<br />
<span style="font-size: 18pt; font-family: 'Microsoft YaHei'; color: #339966;">第二步：安装jexus</span>
<hr />
<p>&nbsp;执行以下代码下载安装包。可以新建一个文件夹存放下载文件，我在这儿使用的是/root/software</p>
<p><strong><span style="color: #800000; font-size: 18px;">&nbsp;注意：</span></strong>因树莓派是arm处理器，所以必须下载jexus arm专用版，不然会出现安装后资源请求过慢的问题。自己之前没注意最后发现一个200k的css包请求时间就超过30s</p>

<pre><span style="color: #008080;">1</span> wget -c http://www.linuxdot.net/down/jexus-5.8.2-arm.tar.gz
<span style="color: #008080;">2</span> tar -zxvf jexus-5.8.2-arm.tar.gz
<span style="color: #008080;">3</span> cd jexus-5.8.2-arm</pre>

<p>也可以在官网上下载最新版本随后用FlashFXP和WinSCP之类的工具传过去，方法类似。</p>
<p>解压后执行安装命令：</p>

<pre>sudo ./install</pre>

<p>jexus会自动安装到/usr/jexus中，随后移动到该文件夹启动服务</p>

<pre><span style="color: #008080;">1</span> cd /usr/jexus
<span style="color: #008080;">2</span> sudo ./jws regsvr
<span style="color: #008080;">3</span> sudo ./jws start
<span style="color: #008080;">4</span> curl localhost/info</pre>

<p>最后一句为测试命令，输出结果：</p>
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="http://images2015.cnblogs.com/blog/984702/201609/984702-20160927150814016-537286363.png" alt="" /></p>
<p>或者直接通过其他电脑的浏览器输入 IP/info ，可看到如下欢迎界面</p>
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="http://images2015.cnblogs.com/blog/984702/201609/984702-20160927150926844-318764430.png" alt="" /></p>
<p>初步搞定！也可以通过运行 vim /etc/rc.local 或类似的开机启动脚本，在其中加入&nbsp;</p>
<pre>/usr/jexus/jws.start</pre>
<p>设置开机启动</p>

<span style="font-size: 18pt; font-family: 'Microsoft YaHei'; color: #339966;">第三步：配置与发布</span>
<hr />
<p>&nbsp;执行命令进入配置文件
</p>

<pre>vim /usr/jexus/siteconf/default</pre>

<p><img style="display: block; margin-left: auto; margin-right: auto;" src="http://images2015.cnblogs.com/blog/984702/201609/984702-20160927151913391-1464032230.png" alt="" width="528" height="314" /></p>
<p>可以看到默认路径为/var/www/default文件夹，及网站资源访问的默认路径，我们就可以把网站的UI文件夹内的内容复制进去，随后通过IP即可访问。</p>
<p>在这里我传了一个index.html文件作为测试<img src="http://images2015.cnblogs.com/blog/984702/201609/984702-20160927152713203-131199162.png" alt="" width="119" height="76" />，效果如下图</p>
<p>&nbsp;<img style="display: block; margin-left: auto; margin-right: auto;" src="http://images2015.cnblogs.com/blog/984702/201609/984702-20160927152618047-19726767.png" alt="" /></p>
<p>此外还可以通过jexus的配置文件配置端口，映射等内容，有兴趣的朋友可以再去尝试。</p>