---
title: 'C#拉姆达(=>)表达式'
id: 352
categories:
  - 'C#'
  - WEB开发
date: 2013-11-28 11:49:08
tags:
---

# 
	前言：

	之前小猪曾经分享过自己对[C#委托的一点理解](http://www.smallerpig.com/archives/206)&nbsp;其实在使用委托的过程中我们会大量的使用拉姆达(=&gt;)表达式

# 
	介绍：

	&quot;Lambda表达式&quot;是一个匿名函数，是一种高效的类似于函数式编程的表达式，Lambda简化了开发中需要编写的代码量。它可以包含表达式和语句，并且可用于创建委托或表达式目录树类型，支持带有可绑定到委托或表达式树的输入参数的内联表达式。所有Lambda表达式都使用Lambda运算符=&gt;，该运算符读作&quot;goes to&quot;。Lambda运算符的左边是输入参数(如果有)，右边是表达式或语句块。Lambda表达式x =&gt; x * x读作&quot;x goes to x times x&quot;。

	自C#3.0开始，就可以使用这种代码赋予委托。只要有委托参数类型的地方，就可以使用Lambda表达式。

	ps：lambda表达式的语法比匿名方法简单。如果所调用的方法有参数，且不需要参数，匿名方法的语法就比较简单，因为这样不需要提供参数。

```
namespace TimeBookMVC4.TEST
{
    class Program
    {
        static void Main(string[] args)
        {
            string mid = &quot;, middle part,&quot;;

            Func&lt;string, string&gt; lambda = param =&gt;
            {
                param += mid;
                param += &quot; and this was added to thes string.&quot;;
                return param;
            };
            Console.WriteLine(lambda(&quot;Start of string&quot;));
        }
    }
}

 ```

	lambda运算符&quot;=&gt;&quot;的左边列出了需要的参数，右边定义了赋予lambda变量的方法的实现代码。

```
## 

参数

	Lambda表达式有集中定义参数的方式。如果只有一个参数，只写出参数名就足够了。下面的Lambda表达式使用了参数s。因为委托类型顶了一个string参数，所以s的类型就是string。实现代码调用Strign.Format\(\)方法来返回一个字符串，在调用该委托时，就把字符串写到控制台上：

```
            Func&lt;string, string&gt; oneparam = s =&gt; String.Format(
                    &quot;change uppercase {0}&quot;,s.ToUpper\(\));
            Console.WriteLine(oneparam(&quot;test&quot;));

 ```

	如果委托使用多个参数，就把参数名放在花括号中。这里参数x和y的类型是double，由Func&lt;double,double,double&gt;委托定义：

```
            Func&lt;double, double, double&gt; twoparam = (x, y) =&gt; x * y;
            Console.WriteLine(twoparam(3,2));

 ```

	为了方便，可以在花括号中给变量名添加参数类型：

```
            Func&lt;double, double, double&gt; twoparam = (double x,double y) =&gt; x * y;
            Console.WriteLine(twoparam(3,2));


 ```

```
## 

多行代码

	如果Lambda表达式只有一条语句，在方法块内就不需要花括号和return语句，因为编译器会添加一条隐式的return语句。

```
            Func&lt;double, double&gt; square = x =&gt; x * x;


 ```

	添加花括号、return语句和分号是完全合法的，通常这比不添加这些括号更容易阅读:

```
            Func&lt;double, double&gt; square = x =&gt; {
                     return x*x;
             }


 ```

	但是，如果在Lambda表达式的实现代码中需要多条语句，就必须添加花括号和return语句：

```
            Func&lt;string, string&gt; lambda = param =&gt;
            {
                param += mid;
                param += &quot; and this was added to thes string.&quot;;
                return param;
            };

 ```

	&nbsp;

```
## 

Lambda表达式外部的变量

	通过Lambda表达式可以访问Lambda表达式块外部的变量。这是一个非常好的功能，但如果未正确使用，也会非常危险。

	在下面的示例中，Fun&lt;int,int&gt;类型的Lambda表达式需要一个int参数，返回一个int。该Lambda表达式的参数用变量x定义。实现代码还访问了Lambda表达式外部的变量someVal。只要不认为在调用f时，Lambda表达式创建了一个以后使用的新方法，这似乎么有什么问题。看看这个代码块，调用f的返回值应是x加5的结果，但似乎不是这样：

```
            int someVal = 5;
            Func&lt;int, int&gt; f = x =&gt; x + someVal;


 ```

	假定以后要修改变量someVal，于是调用Lambda表达式时，会使用someVal的新值。调用f(3)的结果是10：

	someVal =7;

	Console.WriteLine(f(3));

	特别是，通过另一个线程调用Lambda表达式时，我们可能不知道进行了这个调用，也不知道外部变量的当前值是什么。

	现在我们也许会奇怪，如何在Lambda表达式的内部访问Lambda表达式外部的变量。为了理解这一点，看看编译器在定义Lambda表达式时做了什么。对于Lambda表达式x=&gt;x+someVal,编译器会创建一个匿名类，他有一个构造函数来传递外部变量。该构造函数取决于从外部传递进来的变量个数。对于这个简单的例子，构造函数接收一个int。匿名类包涵一个匿名方法，其实现代码、参数和返回类型有Lambda变大事定义：

```
        public class AnonymousClass
        {
            private int someVal;
            public AnonymousClass(int someVal)
            {
                this.someVal = someVal;
            }
            public int AnonymousMethod(int x)
            {
                return x + someVal;
            }
        }


 ```

使用Lambda表达式并调用该方法，会创建匿名类的一个实例，并传递调用该方法时变量的值。

	&nbsp;

	参考：《C#高级编程(第七版)》

	&nbsp;