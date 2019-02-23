---
title: 小猪学设计模式——工厂模式之简单工厂(静态工厂)
id: 303
categories:
  - 设计模式
date: 2013-09-12 10:31:40
tags:
  - 设计模式
  - 工厂模式
---

**前言**

在我们写代码过程中，经常使用类似这样的代码
```


ClassA a = new ClassA\(\);

 ```

严格意义上来讲这段代码已经依赖具体的实现了。当使用&quot;new&quot;关键字创建一个对象时，此时该类就依赖与这个对象，也就是他们之间的耦合度高，当需求变化时，我们就不得不去修改此类的源码。这违反了编程的原则里的“依赖抽象”“开放—关闭”等等一系列原则。

**简单工厂**

此时我们可以运用面向对象（OO）的很重要的原则去解决这一的问题，该原则就是——封装改变。

针对接口编程，可以隔离掉以后系统可能发生的一大堆改变。入股代码是针对接口而写，那么可以通过多态，它可以与任何新类实现该接口。但是，当代码使用一大堆的具体类时，等于是自找麻烦，因为一旦加入新的具体类，就必须要改变代码。在这里我们希望能够调用一个简单的方法，我传递一个参数过去，就可以返回给我一个相应的具体对象。

**定义**

简单工厂模式又称之为静态工厂方法，属于创建型模式。在简单工厂模式中，可以根据传递的参数不同，返回不同类的实例。简单工厂模式定义了一个类，这个类专门用于创建其他类的实例，这些被创建的类都有一个共同的父类。

**分析分析**

 &nbsp; &nbsp; &nbsp; &nbsp;Factory：工厂角色。专门用于创建实例类的工厂，提供一个方法，该方法根据传递的参数不同返回不同类的具体实例。
 &nbsp; &nbsp; &nbsp; &nbsp;Product：抽象产品角色。为所有产品的父类。
 &nbsp; &nbsp; &nbsp; &nbsp;ConcreteProduct：具体的产品角色。

<span style="color:#002060;">简单工厂模式将对象的创建和对象本身业务处理分离了，可以降低系统的耦合度，使得两者修改起来都相对容易些。当以后实现改变时，只需要修改工厂类即可。</span>

<span style="color:#002060;"></span>

下面以计算器的例子来说明简单工厂方法

基类：操作符(product)
```


public class Operation
{
    private double _numberA = 0;
    private double _numberB = 0;

    /// &lt;summary&gt;
    /// 数字A
    /// &lt;/summary&gt;
    public double NumberA
    {
        get
        {
            return _numberA;
        }
        set
        {
            _numberA = value;
        }
    }

    /// &lt;summary&gt;
    /// 数字B
    /// &lt;/summary&gt;
    public double NumberB
    {
        get
        {
            return _numberB;
        }
        set
        {
            _numberB = value;
        }
    }

    /// &lt;summary&gt;
    /// 得到运算结果
    /// &lt;/summary&gt;
    /// &lt;returns&gt;&lt;/returns&gt;
    public virtual double GetResult\(\)
    {
        double result = 0;
        return result;
    }

    /// &lt;summary&gt;
    /// 检查输入的字符串是否准确
    /// &lt;/summary&gt;
    /// &lt;param name="currentNumber"&gt;&lt;/param&gt;
    /// &lt;param name="inputString"&gt;&lt;/param&gt;
    /// &lt;returns&gt;&lt;/returns&gt;
    public static string checkNumberInput(string currentNumber, string inputString)
    {
        string result = "";
        if (inputString == ".")
        {
            if (currentNumber.IndexOf(".") &lt; 0)
            {
                if (currentNumber.Length == 0)
                    result = "0" + inputString;
                else
                    result = currentNumber + inputString;
            }
        }
        else if (currentNumber == "0")
        {
            result = inputString;
        }
        else
        {
            result = currentNumber + inputString;
        }

        return result;
    }
}

 ```

具体操作(加减乘除)类:(ConcreteProduct)

```


/// &lt;summary&gt;
/// 加法类
/// &lt;/summary&gt;
class OperationAdd : Operation
{
    public override double GetResult\(\)
    {
        double result = 0;
        result = NumberA + NumberB;
        return result;
    }
}

/// &lt;summary&gt;
/// 减法类
/// &lt;/summary&gt;
class OperationSub : Operation
{
    public override double GetResult\(\)
    {
        double result = 0;
        result = NumberA - NumberB;
        return result;
    }
}

/// &lt;summary&gt;
/// 乘法类
/// &lt;/summary&gt;
class OperationMul : Operation
{
    public override double GetResult\(\)
    {
        double result = 0;
        result = NumberA * NumberB;
        return result;
    }
}

/// &lt;summary&gt;
/// 除法类
/// &lt;/summary&gt;
class OperationDiv : Operation
{
    public override double GetResult\(\)
    {
        double result = 0;
        if (NumberB == 0)
            throw new Exception("除数不能为0。");
        result = NumberA / NumberB;
        return result;
    }
}

 ```

运算工厂类：(Factory)
```


    /// &lt;summary&gt;
    /// 运算类工厂
    /// &lt;/summary&gt;
public class OperationFactory
{
    public static Operation createOperate(string operate)
    {
        Operation oper = null;
        switch (operate)
        {
            case "+":
                {
                    oper = new OperationAdd\(\);
                    break;
                }
            case "-":
                {
                    oper = new OperationSub\(\);
                    break;
                }
            case "*":
                {
                    oper = new OperationMul\(\);
                    break;
                }
            case "/":
                {
                    oper = new OperationDiv\(\);
                    break;
                }
        }
        return oper;
    }
}

 ```

这样就创建了简单工厂里面的三个角色、

然后在客户端需要具体操作时使用下列代码：
```


static void Main(string[] args)
{
    try
    {
        Console.Write("请输入数字A：");
        string strNumberA = Console.ReadLine\(\);
        Console.Write("请选择运算符号(+、-、*、/)：");
        string strOperate = Console.ReadLine\(\);
        Console.Write("请输入数字B：");
        string strNumberB = Console.ReadLine\(\);
        string strResult = "";

        Operation oper;
        oper = OperationFactory.createOperate(strOperate);
        oper.NumberA = Convert.ToDouble(strNumberA);
        oper.NumberB = Convert.ToDouble(strNumberB);
        strResult = oper.GetResult\(\).ToString\(\);

        Console.WriteLine("结果是：" + strResult);

        Console.ReadLine\(\);

    }
    catch (Exception ex)
    {
        Console.WriteLine("您的输入有错：" + ex.Message);
    }
}

 ```

<span style="color:#002060;"></span>

<span style="color:#ff0000;">优点</span>
 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;1、简单工厂模式实现了对责任的分割，提供了专门的工厂类用于创建对象。
 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;2、客户端无须知道所创建的具体产品类的类名，只需要知道具体产品类所对应的参数即可，对于一些复杂的类名，通过简单工厂模式可以减少使用者的记忆量。
 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;3、通过引入配置文件，可以在不修改任何客户端代码的情况下更换和增加新的具体产品类，在一定程度上提高了系统的灵活性。

<span style="color:#ff0000;">缺点</span>
 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;1、由于工厂类集中了所有产品创建逻辑，一旦不能正常工作，整个系统都要受到影响。
 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;2、使用简单工厂模式将会增加系统中类的个数，在一定程序上增加了系统的复杂度和理解难度。
 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;3、系统扩展困难，一旦添加新产品就不得不修改工厂逻辑，在产品类型较多时，有可能造成工厂逻辑过于复杂，不利于系统的扩展和维护。
 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;4、简单工厂模式由于使用了静态工厂方法，造成工厂角色无法形成基于继承的等级结构。
**五、简单工厂模式的使用场景 ** &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 
 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;1、 &nbsp;工厂类负责创建的对象比较少。
 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;2、 &nbsp;客户端只知道传入工厂类的参数，对于如何创建对象不关心。

**六、总结 ** &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 
 &nbsp; &nbsp; &nbsp; &nbsp; 1、 &nbsp;简单工厂模式的要点就在于当你需要什么，只需要传入一个正确的参数，就可以获取你所需要的对象，而无须知道其创建细节。
 &nbsp; &nbsp; &nbsp; &nbsp; 2、 &nbsp;简单工厂模式最大的优点在于实现对象的创建和对象的使用分离，但是如果产品过多时，会导致工厂代码非常复杂。