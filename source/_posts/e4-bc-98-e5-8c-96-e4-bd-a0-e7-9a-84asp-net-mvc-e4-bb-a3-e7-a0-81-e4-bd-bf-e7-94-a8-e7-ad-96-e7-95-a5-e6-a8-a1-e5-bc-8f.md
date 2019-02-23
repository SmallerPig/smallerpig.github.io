---
title: 优化你的ASP.NET MVC代码--使用策略模式
tags:
  - 策略模式
id: 965
categories:
  - ASP.NET
  - MVC
  - WEB开发
  - 设计模式
date: 2016-01-22 11:25:40
---

### 策略模式

策略模式属于对象的行为模式。其用意是针对一组算法，将每一个算法封装到具有共同接口的独立的类中，从而使得它们可以相互替换。策略模式使得算法可以在不影响到客户端的情况下发生变化。

具体代码表示策略模式:

    /// &lt;summary&gt;
    /// 抽象策略
    /// &lt;/summary&gt;
    interface IStrategy
    {
        /// &lt;summary&gt;
        /// 策略方法
        /// &lt;/summary&gt;
        void DoSomething\(\);
    }

    /// &lt;summary&gt;
    /// 具体策略类A
    /// &lt;/summary&gt;
    class StrategyA : IStrategy
    {
        public void DoSomething\(\)
        {
            Console.WriteLine("A do something");
        }
    }

    /// &lt;summary&gt;
    /// 具体策略类B
    /// &lt;/summary&gt;
    class StrategyB : IStrategy
    {
        public void DoSomething\(\)
        {
            Console.WriteLine("B do something");
        }
    }

    /// &lt;summary&gt;
    /// 调用者
    /// &lt;/summary&gt;
    class Client
    {
        private IStrategy strategy;
        public Client(IStrategy s)
        {
            this.strategy = s;
        }

        public void SomeThing\(\)
        {
            strategy.DoSomething\(\);
        }
    }



 ```

    上述代码中我们定义了一个策略接口`IStrategy`,其中有一个方法是`DoSomething\(\)`.然后有两个具体的策略类实现了这个接口.然后我们定义了这么一个调用者,该调用者拥有一个**策略接口**的引用.该策略接口的实例化是通过调用者的构造函数来进行的,这样我们在调用者对象里面是不需要知道我所引用的策略接口到底是啥,也不需要知道.为了测试效果我们定义了调用者的`SomeThing\(\)`方法,调用策略接口的`DoSomeThing`方法.

```  

 static void Main(string[] args)
    {
        Client c1 = new Client(new StrategyA\(\));
        c1.SomeThing\(\);//A do something
        Client c2 = new Client(new StrategyB\(\));
        c2.SomeThing\(\);//B do something
        Console.ReadLine\(\);
    }


 ```

    运行上述代码我们可得到运行结果.
    以上为策略模式的基本概念.

    ### 在ASP.NET MVC中使用策略模式

    试想下我们的ASP.NET MVC程序中是不是经常出现这么一种情况:在控制器中需要调用逻辑层的代码访问数据库返回数据.而一般情况下我们为了开发效率会直接使用逻辑类.类型这样:

```  

 public class PostController : Controller
    {
        private PostServer server = new PostServer\(\);
        // GET: Post
        public ActionResult Index\(\)
        {
            var models = server.GetList\(\);
            return View(models);
        }
    }

    public class PostServer
    {
        public IEnumerable&lt;Article&gt; GetList\(\)
        {
            throw new NotImplementedException\(\);
        }
    }


 ```

    上述代码可以正常工作,也在一定程度上隔离了与数据库的直接操作.但是却忽略了另外的一个很重要的问题.就是该控制器是跟`PostServer`类是强耦合的.导致
    1\. 无法进行单元测试代码的编写,要测试该Action却要去连接数据库.
    2\. 没有使用依赖注入,导致该`PostServer`无法替代.也就无法使用类似unity这样的IOC工具.

    所以考虑使用策略模式对类似该代码进行重构.
    1\. 对`PostServer`抽象出接口`IPostServer`,并在控制器中用该接口作为成员变量.
    2\. 对控制器的构造函数进行重载,提供一个使用`IPostServer`的构造函数.
    3\. 在上述构造函数中对控制器的成员变量`IPostServer`进行赋值.
    4\. 提供控制器的空参数构造函数来使默认情况下使用原`PostServer`实例化`IPostServer`接口.

    具体代码可参考:

```  

 public class PostController : Controller
    {
        private IPostServer server;

        public PostController\(\)
        {
            server = new PostServer\(\);
        }

        public PostController(IPostServer postServer)
        {
            this.server = postServer;
        }

        // GET: Post
        public ActionResult Index\(\)
        {
            var models = server.GetList\(\);
            return View(models);
        }
    }

    public interface IPostServer
    {
            IEnumerable&lt;Article&gt; GetList\(\);
    }

    public class PostServer : IPostServer
    {
        public IEnumerable&lt;Article&gt; GetList\(\)
        {
            throw new NotImplementedException\(\);
        }
    }

上述代码中我们获取数据的策略在控制器中是不管的.也不强依赖于具体的类,只依赖了一个`IPostServer`的接口,只要有实现了`IPostServer`接口的类就可以使用该控制器.

这样我们在编写单元测试的时候就可以很方便的使用Mock工具来模拟实现`IPostServer`接口而不用具体真实的去访问数据库或者网络从而真正的做到单元测试只测试**单元**.

### 重新回顾认识策略模式

#### 策略模式的重心

　　策略模式的重心不是如何实现算法，而是如何组织、调用这些算法，从而让程序结构更灵活，具有更好的维护性和扩展性。

#### 算法的平等性

　　策略模式一个很大的特点就是各个策略算法的平等性。对于一系列具体的策略算法，大家的地位是完全一样的，正因为这个平等性，才能实现算法之间可以相互替换。所有的策略算法在实现上也是相互独立的，相互之间是没有依赖的。

　　所以可以这样描述这一系列策略算法：策略算法是相同行为的不同实现。

#### 运行时策略的唯一性

　　运行期间，策略模式在每一个时刻只能使用一个具体的策略实现对象，虽然可以动态地在不同的策略实现中切换，但是同时只能使用一个。

#### 公有的行为

　　经常见到的是，所有的具体策略类都有一些公有的行为。这时候，就应当把这些公有的行为放到共同的抽象策略角色Strategy类里面。当然这时候抽象策略角色必须要用抽象类实现，而不能使用接口。

　　这其实也是典型的将代码向继承等级结构的上方集中的标准做法。