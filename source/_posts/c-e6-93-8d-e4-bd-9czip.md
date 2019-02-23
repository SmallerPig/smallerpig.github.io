---
title: 'C#操作Zip'
id: 227
categories:
  - 'C#'
date: 2013-07-24 09:29:23
tags:
---

<span style="color:#000000;font-family:宋体;font-size:16px;font-style:normal;font-variant:normal;font-weight:normal;letter-spacing:normal;line-height:normal;orphans:auto;text-align:start;text-indent:0px;text-transform:none;widows:auto;word-spacing:0px;-webkit-text-stroke-width:0px;display:inline !important;float:none;">Zip格式是开源的压缩格式，在.NET4.0下微软只提供了gzip的相关操作类，在.NET4.5之后才直接提供了操作Zip的类。</span>

<span style="color:#000000;font-family:宋体;font-size:16px;font-style:normal;font-variant:normal;font-weight:normal;letter-spacing:normal;line-height:normal;orphans:auto;text-align:start;text-indent:0px;text-transform:none;widows:auto;word-spacing:0px;-webkit-text-stroke-width:0px;display:inline !important;float:none;"></span>

在4.0之前想要操作Zip只有借鉴第三方的类库，比较著名的是：ShareZipLib。

直接解压与压缩Zip的操作比较简单，这里小猪分享的是在不解压所有文件的情况下只解压Zip包中的文件

情况一：知道Zip包中有某文件且知道在什么地方，解压Zip包中特定文件。

情况二：不知道文件在Zip包中的位置，需求搜索。

先在项目中引用第三方类库

在类代码前面插入对类库的引用：
```


using ICSharpCode.SharpZipLib.Zip;
using ICSharpCode.SharpZipLib.Core;

 ```

编写对应的代码：
```


            ZipInputStream zis = new ZipInputStream(System.IO.File.Open(integralName, FileMode.Open));

            ZipEntry entry = zis.GetNextEntry\(\);

            while (entry != null)
            {//这里是情况二
                #region 搜索 search.png
                if (entry.Name.Contains("search.png")) 
                {
                    FileStream writer = System.IO.File.Create(this.Request.PhysicalApplicationPath + "/Zips/" + datetime + "search.png"); 
//解压后的文件

                        int bufferSize = 1024 * 2; //缓冲区大小
                        int readCount = 0; //读入缓冲区的实际字节
                        byte[] buffer = new byte[bufferSize];
                        readCount = zis.Read(buffer, 0, bufferSize);
                        while (readCount &gt; 0)
                        {
                            writer.Write(buffer, 0, readCount);
                            readCount = zis.Read(buffer, 0, bufferSize);
                        }
                        writer.Close\(\);
                        writer.Dispose\(\);
                }
                #endregion
                entry = zis.GetNextEntry\(\);
            }
            zis.Close\(\);
            zis.Dispose\(\);

            using (FileStream fs = new FileStream(integralName, FileMode.Open, FileAccess.Read))
            using (ZipFile zf = new ZipFile(fs))
            {
                var ze = zf.GetEntry("face.png");//情况一
                if (ze == null)
                {
                }
                else
                {
                    #region 指定 face.png
                    using (var s = zf.GetInputStream(ze))
                    {
                        FileStream writer = System.IO.File.Create(this.Request.PhysicalApplicationPath + "/Zips/" + datetime + "face.png"); //解压后的文件

                        int bufferSize = 1024*2; //缓冲区大小
                        int readCount = 0; //读入缓冲区的实际字节
                        byte[] buffer = new byte[bufferSize];
                        readCount = s.Read(buffer, 0, bufferSize);
                        while (readCount &gt; 0)
                        {
                            writer.Write(buffer, 0, readCount);
                            readCount = s.Read(buffer, 0, bufferSize);
                        }

                        writer.Close\(\);
                        writer.Dispose\(\);

                        // do something with ZipInputStream
                    }
                    #endregion
                }
            }

 ```