---
title: 'C#使用SharpZipLib编辑zip包中内容'
id: 246
categories:
  - 'C#'
  - WEB开发
date: 2013-08-07 13:53:49
tags:
---

小猪最近在使用SharpZipLib进行zip包的操作，编写了下列测试代码。
```
static void Main(string[] args)
{
    Console.WriteLine("---------------------Zip包中的文件并解压测试-------------------------");
    string content;

    string filename = @"E:wamp.zip";//需要测试的zip包地址
    string searchname = @"Web.config";
    using (FileStream fs = new FileStream(filename, FileMode.Open, FileAccess.ReadWrite))
    using (ZipFile zf = new ZipFile(fs))
    {
        Console.WriteLine("开始查找：" + searchname + System.DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss:fff"));
        Console.WriteLine("Zip包大小:" + fs.Length);
        var ze = zf.GetEntry(searchname);
        if (ze == null)
        {
        }
        else
        {
            Console.WriteLine("文件长度：" + ze.Size);
            Console.WriteLine("开始修改：" + System.DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss:fff"));
            using (Stream s = zf.GetInputStream(ze))
            {
                StreamReader inputReader = new StreamReader(s);
                content = inputReader.ReadToEnd\(\);
                content += "nthis is SmallerPig Test 测试下中文";//在原流中增加字符串
            }
            zf.BeginUpdate\(\);
            zf.Add(new StateDataSource(content), searchname);
            zf.CommitUpdate\(\);
            Console.WriteLine("结束：" + System.DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss:fff"));
        }
    }
}

class StateDataSource:IStaticDataSource//自定义实现IStaticDataSource接口的类
{
    string source;

    public StateDataSource(string source)
    {
        this.source = source;
    }

    public Stream GetSource\(\)
    {
        byte[] array = Encoding.ASCII.GetBytes(source);
        MemoryStream stream = new MemoryStream(array);
        return stream;
    }

}
```

由测试结果知道：编辑一个zip中文件所需时间主要是由zip的大小来决定的，整个提交过程需要重新打包。