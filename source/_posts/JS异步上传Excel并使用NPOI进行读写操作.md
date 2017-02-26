---
layout: post
title: "JS异步上传Excel 并使用NPOI进行读写操作"
date: 2016-10-18 10:26:00
comments: true
tags: 
	- js
	- ajax
	- Excel上传
	- NPOI
---

<span style="font-family: &quot;Microsoft YaHei&quot;; font-size: 14pt; color: #008000"><span style="font-family: &quot;Microsoft YaHei&quot;">实现功能</span></span>
<hr>
<span style="font-family: &quot;Microsoft YaHei&quot;"><span style="font-family: &quot;Microsoft YaHei&quot;"><span style="font-size: 16px"><span style="color: #993300">导入</span>——客户端使用 ajaxfileupload.js 插件实现Excel的异步上传，并在服务端解析成JSON字符串返回页面</span>
<span style="font-family: &quot;Microsoft YaHei&quot;"><span style="font-family: &quot;Microsoft YaHei&quot;"><span style="font-size: 16px"><span style="color: #993300">导出</span>——将页面中的grid表拼接成JSON串上传至服务器，在服务端新建Excel并将数据导入，随后返回url给用户下载</span>
<!-- more -->
<br/>
<span style="font-family: &quot;Microsoft YaHei&quot;; color: #008000; font-size: 14pt"><span style="font-family: &quot;Microsoft YaHei&quot;">客户端（Test.aspx）</span></span>
<hr>
<img style="display: block; margin-left: auto; margin-right: auto" src="http://images2015.cnblogs.com/blog/984702/201610/984702-20161018103854217-1144891908.png" alt="">
<p>页面上需要实现的效果是显示一个“导入”按钮，hover时会显示标准格式图片，点击触发上传事件，选择Excel文件后上传到服务器，HTML代码如下</p>
<p>PS：使用了Bootstrap样式，也可以自己定义。“loadingDiv”为加载控件，“downfile”存放返回的url，提供下载。附送<a href="http://files.cnblogs.com/files/jiangzilong/loading.gif" target="_blank">loading.gif</a>资源，也可以用CSS绘制，可参考我的第一篇博文<a id="cb_post_title_url" class="postTitle2" href="http://www.cnblogs.com/jiangzilong/p/5753759.html">几个单元素Loading动画解构</a>~</p>

```html
<div id="loadingDiv"><div id="loadingCont"><img src="assets/loading.gif"/><span style="">请稍等...</span></div></div>
<a id="downfile"><span></span></a>

<div class="col-md-2 col-xs-4">
    <button class="btn btn-info" id="tab2-btn-import"><img src="assets/计算四参数.png"/><i class="icon-reply"></i>导入</button>
    <input type="file" name="file" id="upload-file" accept="application/vnd.openxmlformats-officedocument.spreadsheetml.sheet, application/vnd.ms-excel"/>
</div>

<div class="col-md-2 col-xs-4"><button class="btn btn-primary" id="tab2-btn-export"><i class="icon-share-alt"></i>导出</button></div>
```
<br>
CSS代码如下
```css
#loadingDiv{
    width:100%;
    height:100%;
    position:absolute;
    z-index:999;
    display:none;
}
#loadingCont{
    width:130px;
    height:50px; 
    position:relative;
    padding-left:10px;
    left:calc(50% - 50px);
    top:40%;
    border-radius:3px;
    background-color:#fff;
}
#loadingCont img{
    width:50px; 
    height:50px;
}
#loadingCont span{
    position:relative;
    top:2px;
    color: #696969; 
    font-family:"Microsoft YaHei";
}


#tab2-btn-import{
    position:relative;
}
#tab2-btn-import img{
    position:absolute;
    top:-70px;
    left:-145px;
    border:1px solid #999;
    display:none;
}

#upload-file{
    width:80px;
    height:35px;
    position:absolute;
    top:0;
    left:calc(50% - 40px);
    cursor:pointer;
    opacity:0;
}
```
<br>
<p>实现hover效果和导入、导出的JS代码：</p>

```bash
//Uploadify
//导入
$("#upload-file").change(ImportExcel);
function ImportExcel() {
    $("#loadingDiv").show();   //显示加载控件;
    $.ajaxFileUpload(
        {
            url: 'Test.aspx', //用于文件上传的服务器端请求地址
            secureuri: false, //是否需要安全协议，一般设置为false
            fileElementId: 'upload-file', //文件上传域的ID
            dataType: 'JSON', //返回值类型 一般设置为json
            success: function (data, status)  //服务器成功响应处理函数
            {
                if (status == "success") {
                    try {
                        var jsondata = $.parseJSON(data);
                        if (jsondata[0]["源坐标X"] == undefined || jsondata[0]["源坐标Y"] == undefined || jsondata[0]["目标坐标X"] == undefined || jsondata[0]["目标坐标Y"] == undefined) {
                            alert("Excel格式有误，请按标准格式导入");
                        }
                        else {
                            var rows_be = grid.count();
                            for (var i = 1; i <= rows_be ; i++) {
                                grid.removeRow(i);   //grid.clear()不会清空，再使用addRow时仍会显示之前数据，暂时解决办法为暴力清空
                            }
                            for (var i = 0; i < jsondata.length; i++) {
                                grid.addRow({ '点名': jsondata[i]["点名"], "源坐标X": jsondata[i]["源坐标X"], "源坐标Y": jsondata[i]["源坐标Y"], "目标坐标X": jsondata[i]["目标坐标X"], "目标坐标Y": jsondata[i]["目标坐标Y"] });
                            }
                        }
                    }
                    catch (e) {
                        console.log(e);
                        alert("Excel格式有误，请按标准格式导入");
                    }
                    finally {
                        $("#upload-file").change(ImportExcel);    //ajaxFileUpload插件执行完成会新建，需要重新绑定事件
                        $("#loadingDiv").hide();   //数据准备完成，隐藏加载控件
                    }
                }
            },
            error: function (data, status, e)//服务器响应失败处理函数
            {
                console.log(e);
                $("#loadingDiv").hide();
            }
        });
}

//hover时显示提示
$("#upload-file").hover(function () { $("#tab2-btn-import img").show(); }, function () { $("#tab2-btn-import img").hide(); });

//导出
$("#tab2-btn-export").on("click", ExportExcel);
function ExportExcel() {
    $("#loadingDiv").show();
    $.ajax({
        type: 'post',
        url: 'Test.aspx/Export2Excel',
        data: "{'RowData':'" + JSON.stringify(grid.getAll()) + "'}",
        dataType: 'json',
        contentType: 'application/json;charset=utf-8',
        success: function (data) {
            console.log(data);
            if (data.d == "") {
                alert("该时段无历史数据");
            }
            else {
                var jsondata = $.parseJSON(data.d);
                if (jsondata["result"] != "fail") downloadFile(window.location.host + "\\文件路径，此处隐藏\\" + jsondata["result"]);
            }
            $("#loadingDiv").hide();
        },
        error: function (msg) {
            console.log(msg);
            $("#loadingDiv").hide();
        }
    });
}
});
//自动下载文件
function downloadFile(docurl) {
    $("#downfile").attr("href", "http://" + docurl);
    $("#downfile span").click();
}
```

<br>
<p>PS：对于ajaxFileUpload插件的使用可以看看这篇文章<a id="cb_post_title_url" href="http://www.cnblogs.com/kissdodog/archive/2012/12/15/2819025.html" target="_blank">jQuery插件之ajaxFileUpload</a>，因为插件代码很短所以在这里贴出来，各位新建一个js文件粘进去即可。</p>

```bash
jQuery.extend({

    createUploadIframe: function(id, uri)
    {
        //create frame
        var frameId = 'jUploadFrame' + id;

        if(window.ActiveXObject) {
            var io = document.createElement('<iframe id="' + frameId + '" name="' + frameId + '" />');
            if(typeof uri== 'boolean'){
                io.src = 'javascript:false';
            }
            else if(typeof uri== 'string'){
                io.src = uri;
            }
        }
        else {
            var io = document.createElement('iframe');
            io.id = frameId;
            io.name = frameId;
        }
        io.style.position = 'absolute';
        io.style.top = '-1000px';
        io.style.left = '-1000px';

        document.body.appendChild(io);

        return io
    },
    createUploadForm: function(id, fileElementId)
    {
        //create form
        var formId = 'jUploadForm' + id;
        var fileId = 'jUploadFile' + id;
        var form = $('<form  action="" method="POST" name="' + formId + '" id="' + formId + '" enctype="multipart/form-data"></form>');
        var oldElement = $('#' + fileElementId);
        var newElement = $(oldElement).clone();
        $(oldElement).attr('id', fileId);
        $(oldElement).before(newElement);
        $(oldElement).appendTo(form);
        //set attributes
        $(form).css('position', 'absolute');
        $(form).css('top', '-1200px');
        $(form).css('left', '-1200px');
        $(form).appendTo('body');
        return form;
    },
    addOtherRequestsToForm: function(form,data)
    {
        // add extra parameter
        var originalElement = $('<input type="hidden" name="" value="">');
        for (var key in data) {
            name = key;
            value = data[key];
            var cloneElement = originalElement.clone();
            cloneElement.attr({'name':name,'value':value});
            $(cloneElement).appendTo(form);
        }
        return form;
    },

    ajaxFileUpload: function(s) {
        // TODO introduce global settings, allowing the client to modify them for all requests, not only timeout
        s = jQuery.extend({}, jQuery.ajaxSettings, s);
        var id = new Date().getTime()
        var form = jQuery.createUploadForm(id, s.fileElementId);
        if ( s.data ) form = jQuery.addOtherRequestsToForm(form,s.data);
        var io = jQuery.createUploadIframe(id, s.secureuri);
        var frameId = 'jUploadFrame' + id;
        var formId = 'jUploadForm' + id;
        // Watch for a new set of requests
        if ( s.global && ! jQuery.active++ )
        {
            jQuery.event.trigger( "ajaxStart" );
        }
        var requestDone = false;
        // Create the request object
        var xml = {}
        if ( s.global )
            jQuery.event.trigger("ajaxSend", [xml, s]);
        // Wait for a response to come back
        var uploadCallback = function(isTimeout)
        {
            var io = document.getElementById(frameId);
            try
            {
                if(io.contentWindow)
                {
                    xml.responseText = io.contentWindow.document.body?io.contentWindow.document.body.innerHTML:null;
                    xml.responseXML = io.contentWindow.document.XMLDocument?io.contentWindow.document.XMLDocument:io.contentWindow.document;

                }else if(io.contentDocument)
                {
                    xml.responseText = io.contentDocument.document.body?io.contentDocument.document.body.innerHTML:null;
                    xml.responseXML = io.contentDocument.document.XMLDocument?io.contentDocument.document.XMLDocument:io.contentDocument.document;
                }
            }catch(e)
            {
                jQuery.handleError(s, xml, null, e);
            }
            if ( xml || isTimeout == "timeout")
            {
                requestDone = true;
                var status;
                try {
                    status = isTimeout != "timeout" ? "success" : "error";
                    // Make sure that the request was successful or notmodified
                    if ( status != "error" )
                    {
                        // process the data (runs the xml through httpData regardless of callback)
                        var data = jQuery.uploadHttpData( xml, s.dataType );
                        // If a local callback was specified, fire it and pass it the data
                        if ( s.success )
                            s.success( data, status );

                        // Fire the global callback
                        if( s.global )
                            jQuery.event.trigger( "ajaxSuccess", [xml, s] );
                    } else
                        jQuery.handleError(s, xml, status);
                } catch(e)
                {
                    status = "error";
                    jQuery.handleError(s, xml, status, e);
                }

                // The request was completed
                if( s.global )
                    jQuery.event.trigger( "ajaxComplete", [xml, s] );

                // Handle the global AJAX counter
                if ( s.global && ! --jQuery.active )
                    jQuery.event.trigger( "ajaxStop" );

                // Process result
                if ( s.complete )
                    s.complete(xml, status);

                jQuery(io).unbind()

                setTimeout(function()
                {    try
                    {
                        $(io).remove();
                        $(form).remove();

                    } catch(e)
                    {
                        jQuery.handleError(s, xml, null, e);
                    }

                }, 100)

                xml = null

            }
        }
        // Timeout checker
        if ( s.timeout > 0 )
        {
            setTimeout(function(){
                // Check to see if the request is still happening
                if( !requestDone ) uploadCallback( "timeout" );
            }, s.timeout);
        }
        try
        {
            // var io = $('#' + frameId);
            var form = $('#' + formId);
            $(form).attr('action', s.url);
            $(form).attr('method', 'POST');
            $(form).attr('target', frameId);
            if(form.encoding)
            {
                form.encoding = 'multipart/form-data';
            }
            else
            {
                form.enctype = 'multipart/form-data';
            }
            $(form).submit();

        } catch(e)
        {
            jQuery.handleError(s, xml, null, e);
        }
        if(window.attachEvent){
            document.getElementById(frameId).attachEvent('onload', uploadCallback);
        }
        else{
            document.getElementById(frameId).addEventListener('load', uploadCallback, false);
        }
        return {abort: function () {}};

    },

    uploadHttpData: function( r, type ) {
        var data = !type;
        data = type == "xml" || data ? r.responseXML : r.responseText;
        // If the type is "script", eval it in global context
        if ( type == "script" )
            jQuery.globalEval( data );
        // Get the JavaScript object, if JSON is used.
        if ( type == "json" )
        {
            // If you add mimetype in your response,
            // you have to delete the '<pre></pre>' tag.
            // The pre tag in Chrome has attribute, so have to use regex to remove
            var data = r.responseText;
            var rx = new RegExp("<pre.*?>(.*?)</pre>","i");
            var am = rx.exec(data);
            //this is the desired data extracted
            var data = (am) ? am[1] : "";    //the only submatch or empty
            eval( "data = " + data );
        }
        // evaluate scripts within html
        if ( type == "html" )
            jQuery("<div>").html(data).evalScripts();
        //alert($('param', data).each(function(){alert($(this).attr('value'));}));
        return data;
    },

    handleError: function( s, xhr, status, e )      {  
        // If a local callback was specified, fire it  
    if ( s.error ) {  
        s.error.call( s.context || s, xhr, status, e );  
    }  
  
        // Fire the global callback  
    if ( s.global ) {  
        (s.context ? jQuery(s.context) : jQuery.event).trigger( "ajaxError", [xhr, s, e] );  
    }  
}  
})
``` 
<br>
<span style="font-family: &quot;Microsoft YaHei&quot;; font-size: 14pt; color: #008000">服务端（Test.aspx.cs）</span>
<hr>
<p>&nbsp;在服务端做Excel解析常用的有三种方法：</p>
<p>一是使用微软的开放式数据库接口技术OleDb，建立连接后可像数据库一样操作Excel，但是个人测试发现在网页文件上传成功后，OleDb接口无法读取文件，页面报跨域错误（本机调试通过，发布后报错），但路径却是同源的，一直不知道问题在哪儿，希望有了解的前辈指点指点。</p>
<p>二是使用Com组件方式读取，这种方式也尝试过，读取速度相对来说非常慢，故舍弃。</p>
<p>三是使用第三方的NPOI插件。因为前两种方法依赖于微软的Office环境，所以在使用时需要在服务器上安装Office，而这种方法只要下载并引用dll文件即可，优势明显。并且个人测试效率也很高，使用方便，故采用。</p>

<p>以下为服务端代码：</p>

```bash
protected void Page_Load(object sender, EventArgs e)
    {
        HttpFileCollection files = Request.Files;
        string msg = string.Empty;
        string error = string.Empty;
        //string docurl;
        if (files.Count > 0)
        {
            string path = Server.MapPath("~/项目目录/uploadfile/") + Path.GetFileName(files[0].FileName);

            files[0].SaveAs(path);
            //msg = "成功！文件大小为:" + files[0].ContentLength;

            //解析Excel  
            string excelgrid = "";
            using (ExcelHelper excelHelper = new ExcelHelper(path))
            {
                DataTable dt = excelHelper.ExcelToDataTable("MySheet", true);
                excelgrid = JsonHepler.ToJson(dt);
            }
            //string res = "{ error:'" + error + "', msg:'" + msg + "',excelgrid:'" + excelgrid + "'}";
            Response.Write(excelgrid);
            Response.End();
        }
    }


    [WebMethod]
    public static string Export2Excel(string RowData)
    {
        try
        {
            DataTable dt = JsonHepler.JsonToDataTable(RowData);
            string docname = string.Format("{0:yyyyMMddHHmmssffff}", DateTime.Now) + ".xlsx";
            string docurl = AppDomain.CurrentDomain.BaseDirectory + "项目目录\\downloadfile\\" + docname;
            int count = 0;

            using (ExcelHelper excelHelper = new ExcelHelper(docurl))
            {
                count = excelHelper.DataTableToExcel(dt,"MySheet", true);
            }

            if (count >= 0) return "{\"result\":\""+ docname + "\"}";
            else return "{'result':'fail'}";
        }
        catch(Exception)
        {
            throw;
        }
    }
```
<br>
<p>NPOI为第三方插件，关于NPOI的使用可以参考<a id="cb_post_title_url" class="postTitle2" href="http://www.cnblogs.com/luxiaoxun/p/3374992.html">NPOI读写Excel</a>，文章中也提供了读取/写入的工具类。同时提供NPOI的<a href="http://npoi.codeplex.com/releases" target="_blank">下载地址</a>，下载后根据.net版本引入dll即可，在此我使用的是4.0，所以引用了 Net40 文件夹中的数个dll。</p>
<p>接下来我们就可以调试发布啦~</p>