---
title: 'C# HTTP 断点续传'
id: 294
categories:
  - ASP.NET
  - 'C#'
  - MVC
  - WEBFORM
  - WEB开发
date: 2013-09-02 10:23:10
tags:
---

在IIS中，磁盘路径对应的文件是可以直接下载的，而原生的IIS并不需要额外的配置就可以进行断点续传。而在小猪的项目中使用到的文件下载地址不对应磁盘路径的文件地址，而是需要验证用户是否有权限进行下载然后使用[使用fileresult提供文件下载](http://www.smallerpig.com/archives/272)。这样整个下载过程都需要自己动手写代码完成。为了使客户端的体验更佳，所以必须要提供断点续传的功能。

**断点续传的原理**

其实断点续传的原理很简单，就是在 Http 的请求上和一般的下载有所不同而已。 

打个比方，浏览器请求服务器上的一个文时，所发出的请求如下： 
假设服务器域名为 wwww.smallerpig.com，文件名为 down.zip。
```
GET /down.zip HTTP/1.1  
Accept: image/gif, image/x-xbitmap, image/jpeg, image/pjpeg, application/vnd.ms-   
excel, application/msword, application/vnd.ms-powerpoint, */*   
Accept-Language: zh-cn   
Accept-Encoding: gzip, deflate   
User-Agent: Mozilla/4.0 (compatible; MSIE 5.01; Windows NT 5.0)   
Connection: Keep-Alive

 ```

服务器收到请求后，按要求寻找请求的文件，提取文件的信息，然后返回给浏览器，返回信息如下：

```
200  
Content-Length=106786028  
Accept-Ranges=bytes   
Date=Mon, 30 Apr 2001 12:56:11 GMT   
ETag=W/"02ca57e173c11:95b"  
Content-Type=application/octet-stream   
Server=Microsoft-IIS/5.0  
Last-Modified=Mon, 30 Apr 2001 12:56:11 GMT

 ```

所谓断点续传，也就是要从文件已经下载的地方开始继续下载。所以在客户端浏览器传给 Web 服务器的时候要多加一条信息 -- 从哪里开始。 
下面是用自己编的一个&quot;浏览器&quot;来传递请求信息给 Web 服务器，要求从 2000070 字节开始。
```
GET /down.zip HTTP/1.0  
User-Agent: NetFox   
RANGE: bytes=2000070-   
Accept: text/html, image/gif, image/jpeg, *; q=.2, */*; q=.2

 ```

仔细看一下就会发现多了一行 RANGE: bytes=2000070- 
这一行的意思就是告诉服务器 down.zip 这个文件从 2000070 字节开始传，前面的字节不用传了。 
服务器收到这个请求以后，返回的信息如下：
```
206  
Content-Length=106786028  
Content-Range=bytes 2000070-106786027/106786028  
Date=Mon, 30 Apr 2001 12:55:20 GMT   
ETag=W/"02ca57e173c11:95b"  
Content-Type=application/octet-stream   
Server=Microsoft-IIS/5.0  
Last-Modified=Mon, 30 Apr 2001 12:55:20 GMT

 ```

和前面服务器返回的信息比较一下，就会发现增加了一行：

```
Content-Range=bytes 2000070-106786027/106786028

 ```

返回的代码也改为 206 了，而不再是 200 了。

知道了以上原理，就可以进行断点续传的编程了。

**C#实现断点续传**

```


/// &lt;summary&gt;
/// 支持断点续传
/// &lt;/summary&gt;
/// &lt;param name="httpContext"&gt;&lt;/param&gt;
/// &lt;param name="filePath"&gt;&lt;/param&gt;
/// &lt;param name="speed"&gt;&lt;/param&gt;
/// &lt;returns&gt;&lt;/returns&gt;
public static bool DownloadFile(HttpContext httpContext, string filePath, long speed)
{
    bool ret = true;
    try
    {
        #region--验证：HttpMethod，请求的文件是否存在
        switch (httpContext.Request.HttpMethod.ToUpper\(\))
        { //目前只支持GET和HEAD方法   
            case "GET":
            case "HEAD":
                break;
            default:
                httpContext.Response.StatusCode = 501;
                return false;
        }
        if (!System.IO.File.Exists(filePath))
        {
            httpContext.Response.StatusCode = 404;
            return false;
        }
        #endregion

        #region 定义局部变量
        long startBytes = 0;
        int packSize = 1024 * 40; //分块读取，每块40K bytes   
        string fileName = Path.GetFileName(filePath);
        FileStream myFile = new FileStream(filePath, FileMode.Open, FileAccess.Read, FileShare.ReadWrite);
        BinaryReader br = new BinaryReader(myFile);
        long fileLength = myFile.Length;

        int sleep = (int)Math.Ceiling(1000.0 * packSize / speed);//毫秒数：读取下一数据块的时间间隔   
        string lastUpdateTiemStr = System.IO.File.GetLastWriteTimeUtc(filePath).ToString("r");
        string eTag = HttpUtility.UrlEncode(fileName, Encoding.UTF8) + lastUpdateTiemStr;//便于恢复下载时提取请求头;   
        #endregion

        #region--验证：文件是否太大，是否是续传，且在上次被请求的日期之后是否被修
        if (myFile.Length &gt; Int32.MaxValue)
        {//-------文件太大了-------   
            httpContext.Response.StatusCode = 413;//请求实体太大   
            return false;
        }

        if (httpContext.Request.Headers["If-Range"] != null)//对应响应头ETag：文件名+文件最后修改时间   
        {
            //----------上次被请求的日期之后被修改过--------------   
            if (httpContext.Request.Headers["If-Range"].Replace(""", "") != eTag)
            {//文件修改过   
                httpContext.Response.StatusCode = 412;//预处理失败   
                return false;
            }
        }
        #endregion

        try
        {
            #region -------添加重要响应头、解析请求头、相关验证-------------------
            httpContext.Response.Clear\(\);
            httpContext.Response.Buffer = false;
            httpContext.Response.AddHeader("Content-MD5",Common.ASE.GetMD5Hash(filePath));//用于验证文件
            httpContext.Response.AddHeader("Accept-Ranges", "bytes");//重要：续传必须   
            httpContext.Response.AppendHeader("ETag", """ + eTag + """);//重要：续传必须   
            httpContext.Response.AppendHeader("Last-Modified", lastUpdateTiemStr);//把最后修改日期写入响应                   
            httpContext.Response.ContentType = "application/octet-stream";//MIME类型：匹配任意文件类型   
            httpContext.Response.AddHeader("Content-Disposition", "attachment;filename=" + HttpUtility.UrlEncode(fileName, Encoding.UTF8).Replace("+", "%20"));
            httpContext.Response.AddHeader("Content-Length", (fileLength - startBytes).ToString\(\));
            httpContext.Response.AddHeader("Connection", "Keep-Alive");
            httpContext.Response.ContentEncoding = Encoding.UTF8;
            if (httpContext.Request.Headers["Range"] != null)
            {//------如果是续传请求，则获取续传的起始位置，即已经下载到客户端的字节数------   
                httpContext.Response.StatusCode = 206;//重要：续传必须，表示局部范围响应。初始下载时默认为200   
                string[] range = httpContext.Request.Headers["Range"].Split(new char[] { '=', '-' });//"bytes=1474560-"   
                startBytes = Convert.ToInt64(range[1]);//已经下载的字节数，即本次下载的开始位置     
                if (startBytes &lt; 0 || startBytes &gt;= fileLength)
                {//无效的起始位置   
                    return false;
                }
            }
            if (startBytes &gt; 0)
            {//------如果是续传请求，告诉客户端本次的开始字节数，总长度，以便客户端将续传数据追加到startBytes位置后----------   
                httpContext.Response.AddHeader("Content-Range", string.Format(" bytes {0}-{1}/{2}", startBytes, fileLength - 1, fileLength));
            }
            #endregion

            #region -------向客户端发送数据块-------------------
            br.BaseStream.Seek(startBytes, SeekOrigin.Begin);
            int maxCount = (int)Math.Ceiling((fileLength - startBytes + 0.0) / packSize);//分块下载，剩余部分可分成的块数   
            for (int i = 0; i &lt; maxCount &amp;&amp; httpContext.Response.IsClientConnected; i++)
            {//客户端中断连接，则暂停   
                httpContext.Response.BinaryWrite(br.ReadBytes(packSize));
                httpContext.Response.Flush\(\);
                if (sleep &gt; 1) Thread.Sleep(sleep);
            }
            #endregion
        }
        catch
        {
            ret = false;
        }
        finally
        {
            br.Close\(\);
            myFile.Close\(\);
        }
    }
    catch
    {
        ret = false;
    }
    return ret;
}

 ```