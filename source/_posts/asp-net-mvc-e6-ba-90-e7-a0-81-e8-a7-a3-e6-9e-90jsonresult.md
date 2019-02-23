---
title: '[ASP.NET MVC]源码解析:JsonResult'
id: 745
categories:
  - ASP.NET
  - MVC
  - WEB开发
date: 2015-06-05 11:21:30
tags:
---

JsonResult类主要用来向调用者返回json格式的数据,其响应头的ContentType值默认为application/json.

JsonResult其本身实现了抽象类ActionResult,定义为:
```
namespace System.Web.Mvc
{
    public abstract class ActionResult
    {
        public abstract void ExecuteResult(ControllerContext context);
    }
}
```
其只有一个抽象方法:ExecuteResult(context)!用来向浏览器输出!

JsonResult的完整代码为:
```
namespace System.Web.Mvc
{
    public class JsonResult : ActionResult
    {
        public JsonResult\(\)
        {
            JsonRequestBehavior = JsonRequestBehavior.DenyGet;
        }

        public Encoding ContentEncoding { get; set; }

        public string ContentType { get; set; }

        public object Data { get; set; }

        public JsonRequestBehavior JsonRequestBehavior { get; set; }

        /// 
        /// When set MaxJsonLength passed to the JavaScriptSerializer.
        /// 
        public int? MaxJsonLength { get; set; }

        /// 
        /// When set RecursionLimit passed to the JavaScriptSerializer.
        /// 
        public int? RecursionLimit { get; set; }

        public override void ExecuteResult(ControllerContext context)
        {
            if (context == null)
            {
                throw new ArgumentNullException("context");
            }
            if (JsonRequestBehavior == JsonRequestBehavior.DenyGet &amp;&amp;
                String.Equals(context.HttpContext.Request.HttpMethod, "GET", StringComparison.OrdinalIgnoreCase))
            {
                throw new InvalidOperationException(MvcResources.JsonRequest_GetNotAllowed);
            }

            HttpResponseBase response = context.HttpContext.Response;

            if (!String.IsNullOrEmpty(ContentType))
            {
                response.ContentType = ContentType;
            }
            else
            {
                response.ContentType = "application/json";
            }
            if (ContentEncoding != null)
            {
                response.ContentEncoding = ContentEncoding;
            }
            if (Data != null)
            {
                JavaScriptSerializer serializer = new JavaScriptSerializer\(\);
                if (MaxJsonLength.HasValue)
                {
                    serializer.MaxJsonLength = MaxJsonLength.Value;
                }
                if (RecursionLimit.HasValue)
                {
                    serializer.RecursionLimit = RecursionLimit.Value;
                }
                response.Write(serializer.Serialize(Data));
            }
        }
    }
}
```
重点为response.Write部分代码.其调用了一个JavaScriptSerializer对象的Serialize方法对Data数据进行序列化!

类内部本身又用到了枚举类型JsonRequestBehavior:以用来标示请求是否允许get的方式来请求Json.默认值为DenyGet,也就是说当在get请求中返回JsonResult结果系统将直接报错
```
namespace System.Web.Mvc
{
    public enum JsonRequestBehavior
    {
        AllowGet,
        DenyGet,
    }
}
```


可见JsonResult类提供了如下的属性供我们来设置:
```
    public object                 Data { get; set; }  
     public Encoding               ContentEncoding { get; set; }
     public string                 ContentType { get; set; }    
     public JsonRequestBehavior    JsonRequestBehavior { get; set; }    
     public int?                   MaxJsonLength { get; set; }
     public int?                   RecursionLimit { get; set; }
```
其中

*   object为返回内容,
*   ContentEncoding为返回请求头的ContentEncoding值,
*   ContentType为返回请求头的ContentType值 ,默认值为application/json.
*   JsonRequestBehavior枚举标示是否允许get请求. 默认为不允许get请求
*   RecursionLimit为递归限制值.限制json数据的层数(如果有值的话) ,默认为100
*   MaxJsonLength为Json数据的最大长度(如果有值的话),默认值位为2097152（0x200000，等同于 4 MB 的 Unicode 字符串数据）
在Controller类中,其为我们提供了一个简化的Json\(\)方法.该方法提供了6个重载方法根据指定的数据对象、编码方式为我们简化实例化JsonResult的过程:
```
protected internal JsonResult Json(object data)
        {
            return Json(data, null /* contentType */, null /* contentEncoding */, JsonRequestBehavior.DenyGet);
        }

        protected internal JsonResult Json(object data, string contentType)
        {
            return Json(data, contentType, null /* contentEncoding */, JsonRequestBehavior.DenyGet);
        }

        protected internal virtual JsonResult Json(object data, string contentType, Encoding contentEncoding)
        {
            return Json(data, contentType, contentEncoding, JsonRequestBehavior.DenyGet);
        }

        protected internal JsonResult Json(object data, JsonRequestBehavior behavior)
        {
            return Json(data, null /* contentType */, null /* contentEncoding */, behavior);
        }

        protected internal JsonResult Json(object data, string contentType, JsonRequestBehavior behavior)
        {
            return Json(data, contentType, null /* contentEncoding */, behavior);
        }

        protected internal virtual JsonResult Json(object data, string contentType, Encoding contentEncoding, JsonRequestBehavior behavior)
        {
            return new JsonResult
            {
                Data = data,
                ContentType = contentType,
                ContentEncoding = contentEncoding,
                JsonRequestBehavior = behavior
            };
        }
```
重点看最后一个重载的实现,其就是根据调用的参数来实例化一个JsonResult类的实例,但其默认是不适用JsonResult类的MaxJsonLength属性和RecursionLimit属性的.

如果我们想利用起来这两个参数的话就需要我们手动创建JsonResult类的实例了,如下面代码所示:
```
public ActionResult Test\(\)
{
    var data = new
    {
        Recursion1 = new
        {
            Recursion2 = new
            {
                Recursion3 = new
                {
                    Recursion4 = new
                    {
                        Recursion5 = 5
                    }
                }
            }
        }
    };//5级数据

    JsonResult result = new JsonResult\(\)
    {
        Data = data,
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        RecursionLimit = 3,//递归内容层级只允许三级!
        //其他参数省略
    };
    return result;
}

```
我们启动程序看看结果:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/06/image_thumb1.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/06/image1.png)

可见我们的RecursionLimit值产生了效果.

转载请注明出处:[小猪博客](http://www.smallerpig.com/745.html "[ASP.NET MVC]源码解析:JsonResult")

官方参考文档:https://msdn.microsoft.com/zh-cn/dd470569.aspx