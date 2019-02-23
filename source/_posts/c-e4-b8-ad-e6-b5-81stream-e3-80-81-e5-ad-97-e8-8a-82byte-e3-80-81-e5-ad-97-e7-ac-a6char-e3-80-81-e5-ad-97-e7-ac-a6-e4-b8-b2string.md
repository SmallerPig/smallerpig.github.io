---
title: 'C#中流(stream)、字节(byte[])、字符(char[])、字符串string'
tags:
  - 'C#'
id: 220
categories:
  - 'C#'
date: 2013-07-15 15:40:47
---

```
首先要明白它们本身是由什么组成的：

流：二进制

字节：无符号整数

字符：Unicode编码字符

字符串：多个Unicode编码字符

那么在.net下它们之间如何转化呢？

一般是遵守以下规则：

流-&gt;字节数组-&gt;字符数组-&gt;字符串

下面就来具体谈谈转化的语法:

流-&gt;字节数组

MemoryStream ms = new MemoryStream\(\);

byte[] buffer = new byte[ms.Length];

ms.Read(buffer, 0, (int)ms.Length);

字节数组-&gt;流

byte[] buffer = new byte[10];

MemoryStream ms = new MemoryStream(buffer);

字节数组-&gt;字符数组

1.

byte[] buffer = new byte[10];

char[] ch = new ASCIIEncoding\(\).GetChars(buffer);

//或者：char[] ch = Encoding.UTF8.GetChars(buffer)

2.

byte[] buffer = new byte[10];

char[] ch = new char[10];

for(int i=0; i&lt;buffer.Length; i++)

{

    ch[i] = Convert.ToChar(buffer[i]);

}

字符数组-&gt;字节数组

1.

char[] ch = new char[10];

byte[] buffer = new ASCIIEncoding\(\).GetBytes(ch);

//或者：byte[] buffer = Encoding.UTF8.GetBytes(ch)

2.

char[] ch = new char[10];

byte[] buffer = new byte[10];

for(int i=0; i&lt;ch.Length; i++)

{

    buffer[i] = Convert.ToByte(ch[i]);

}

字符数组-&gt;字符串

char[] ch = new char[10];

string str = new string(ch);

字符串-&gt;字符数组

string str = "abcde";

char[] ch=str .ToCharArray\(\);

字节数组-&gt;字符串

byte[] buffer = new byte[10];

string str = System.Text.Encoding.UTF8.GetString(buffer);

//或者：string str = new ASCIIEncoding\(\).GetString(buffer);

字符串-&gt;字节数组

string str = "abcde";

byte[] buffer=System.Text.Encoding.UTF8.GetBytes(str);

//或者：byte[] buffer= new ASCIIEncoding\(\).GetBytes(str);

说明：主要就是用到了Convert类和System.Text命名空间下的类，Encoding是静态类,ASCIIEncoding是实体类，方法都是一样的！
```