---
title: 小猪学设计模式——工厂模式之工厂方法模式
id: 304
categories:
  - 设计模式
date: 2013-09-12 10:35:20
tags:
  - 设计模式
  - 工厂模式

---

还记得上一篇的简单工厂模式吗？
我们举了个例子，使用加减乘除的方法来解释简单工厂，而如果我们有一天需要做科学计算器，那两个数之间的运算关系可不止加减乘除了，这时候如果我们的工厂类需要在不同的运算符之间做判断然后再给出具体的运算规则的话，那这些代码将非常的丑陋。

如果出现这种情况，就需要我们的工厂方法模式了：

## 代码

工厂方法模式代码：

    interface IProduct {  
        public void productMethod\(\);  
    }  

    class Product implements IProduct {  
        public void productMethod\(\) {  
            System.out.println("产品");  
        }  
    }  

    interface IFactory {  
        public IProduct createProduct\(\);  
    }  

    class Factory implements IFactory {  
        public IProduct createProduct\(\) {  
            return new Product\(\);  
        }  
    }  

    public class Client {  
        public static void main(String[] args) {  
            IFactory factory = new Factory\(\);  
            IProduct prodect = factory.createProduct\(\);  
            prodect.productMethod\(\);  
        }  
    }  


 ```

    从上述代码中我们可以看出，实现工厂方法模式需要4个元素

    * * *

    ## 四个要素

    **工厂接口**。工厂接口是工厂方法模式的核心，与调用者直接交互用来提供产品。在实际编程中，有时候也会使用一个抽象类来作为与调用者交互的接口，其本质上是一样的。
    **工厂实现**。在编程中，工厂实现决定如何实例化产品，是实现扩展的途径，需要有多少种产品，就需要有多少个具体的工厂实现。
    **产品接口**。产品接口的主要目的是定义产品的规范，所有的产品实现都必须遵循产品接口定义的规范。产品接口是调用者最为关心的，产品接口定义的优劣直接决定了调用者代码的稳定性。同样，产品接口也可以用抽象类来代替，但要注意最好不要违反里氏替换原则。
    **产品实现**。实现产品接口的具体类，决定了产品在客户端中的具体行为。
    上一篇提到的简单工厂模式跟工厂方法模式极为相似，区别是：简单工厂只有三个要素，他没有工厂接口，并且得到产品的方法一般是静态的。因为没有工厂接口，所以在工厂实现的扩展性方面稍弱，可以算所工厂方法模式的简化版，关于简单工厂模式，可参考上一篇:[小猪学设计模式——工厂模式之简单工厂(静态工厂)](http://www.smallerpig.com/303.html)。

    回到一开始提出的问题上来，我们需要在原来的简单工厂上面抽象出来一个抽象工厂的类：

```  

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


 ```

    抽象出来的类为：

```  

 public interface IOperationFactory{
        IOperation createOperation\(\);
    }

    //具体实现的工厂类
    public class AddOperationFactory  implements IOperationFactory{
        @Override
        public IOperation createOperation\(\) {
            return new AddOperation\(\);
        }
    }


 ```

    然后每一个操作符都新建一个实现了IOperationFactory接口的类。
    看上述代码修改的好像并没有太大的意义，那是因为我们的适用场景。

    想象下如果我们不是加减乘除，而是生产汽车的过程。一开始我们需要各种零件：轮胎，发动机，方向盘等等，我们使用了简单工厂模式直接给到调用者这些零件。这时候简单工厂模式是能够满足我们的需求的。
    但是我们的汽车品牌变了(之前可是只有零件可能发生变化的)，我们之前开的奥拓不满足需求了，我们需要奥迪宝马奔驰了，也就是工厂变了，这个时候就需要将原来的工厂进行抽象。调用者直接使用抽象工厂类来进行调用车，而将零件的具体组装过程放到不同的工厂类里面。客户端拿到的就是一辆实实在在的车，且客户端并不关心具体工厂内部是怎么组件车的，客户端只是希望能实现我开车的目的就可以了。

```  

 interface IFactory {  
        public ICar createCar\(\);  
    }  
    class Factory implements IFactory {  
        public ICar createCar\(\) {  
            Engine engine = new Engine\(\);  
            Underpan underpan = new Underpan\(\);  
            Wheel wheel = new Wheel\(\);  
            ICar car = new Car(underpan, wheel, engine);  
            return car;  
        }  
    }  
    public class Client {  
        public static void main(String[] args) {  
            IFactory factory = new Factory\(\);  
            ICar car = factory.createCar\(\);  
            car.show\(\);  
        }  
    }  

* * *

## 总结

简单工厂模式一般用在产品变化多而工厂变化少的情况。
工厂方法模式一般用在工厂也可能出现过多的变动的情况。这个时候我们就需要为每一个产品都创建一个工厂。而将具体需要实例化哪一个产品交给客户端来处理。也就是用官方的话来讲：**定义一个用于创建对象的接口，让子类决定实例化哪一个类。工厂方法使用一个类的实例化延迟到其子类。**。而这个也将导致另外一个问题。选择判断的问题还是存在的，也就是说，工厂方法把简单工厂的内部逻辑判断一到了客户端代码来进行。在新增功能的时候，本来是改工厂类，现在是修改客户端了。
当然，这只是个人拙见。可能随着个人代码量的增加，对工厂方法的认识也会逐渐的改变。

* * *

更新于2016年9月16日