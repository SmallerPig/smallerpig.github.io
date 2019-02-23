---
title: 'C#字符串编码'
id: 241
categories:
  - 'C#'
  - WEB开发
date: 2013-08-06 12:04:09
tags:
---

在之前的使用习惯中，我们需要对URL Encode的时候通常只是调用系统的静态方法进行Encode下就可以了。今天大强问了个问题把我楞住了：你的Encode是使用什么编码？

所以小猪有了下面的测试代码：
```


class Program
{
    static void Main(string[] args)
    {
        string a = "Glory is fleeting, but obscurity is forever";
        string b = UrlEncode(a);
        Console.WriteLine(b);

    }

    public static string UrlEncode(string str)
    {
        StringBuilder sb = new StringBuilder\(\);
        byte[] byStr = System.Text.Encoding.Unicode.GetBytes(str); //默认是System.Text.Encoding.Default.GetBytes(str)
        for (int i = 0; i &lt; byStr.Length; i++)
        {
            sb.Append(@"%" + Convert.ToString(byStr[i], 16));
        }

        return (sb.ToString\(\));
    }

}

 ```