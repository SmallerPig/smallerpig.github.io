---
title: 小猪学设计模式——工厂模式之抽象工厂
id: 305
categories:
  - 设计模式
date: 2013-09-12 10:36:15
tags:
  - 设计模式
  - 工厂模式

---

这篇是工厂模式系列的最后一篇

在上一篇的工厂方法模式中，我们使用了汽车的例子来说明。其中我们为不同的品牌车新建了各自的类来实现创建车的过程。
工厂方法模式代码：

    interface IFactory {  
        ICar createCar\(\);  
    }  
    class AudiFactory implements IFactory {  
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
            IFactory factory = new AudiFactory\(\);  
            ICar car = factory.createCar\(\);  
            car.show\(\);  
        }  
    }  


 ```

    随着程序的演进，功能要求变的更加复杂，我们本来只是要创建车就可以了。但是现在我们需要根据车的类型不同来生成不同的车，例如SUV车系、运动车系、商务车系等。而不同的汽车厂商都会根据自己车的定位和品牌价值来生产这些车系。这时候如果我们使用工厂方法模式的话就需要为不同的车系、不同的厂商分别新建工厂类。这显然是不太合理的。
    这个场景就需要抽象工厂模式了。

    * * *

    ## 抽象工厂模式

    > 提供一个创建一系列相关或相互依赖对象的接口，而无需指定他们具体的类。

    根据上面关于车的描述，我们可以对车的概念进行一个概括：将车根据类型和厂商进行划分，也就是说如果我们知道了车的类型以及厂商，我们就能够确定车的型号。

    我们将原来工厂方法模式的接口进行升级。

```  

 interface IFactory {  
        ICar createSUVCar\(\);

        ICar CreateSportsCar\(\);

        ICar CreateBussinessCar\(\);        
    }  


 ```

    而我们的奥拓工厂就实现了这个接口。

```  

 public class AoTuoFactory implements IFactory{
        public ICar createSUVCar\(\){

        }

        public ICar CreateSportsCar\(\){

        }

        public ICar CreateBussinessCar\(\){

        }
    }


 ```

    在客户端，我们就可以这样来调用了：

```  

 public class Client {  
        public static void main(String[] args) {  
            IFactory factory = new AoTuoFactory\(\);  
            ICar car = factory.createSUVCar\(\);  
            car.show\(\);  
        }  

这样，我们创建car的时候，并不需要理会factory到底是哪个工厂。

## 几个概念

产品族与产品等级
所谓产品族，是指位于不同产品等级结构中，功能相关联的产品组成的家族，例如车的厂商。
而产品等级就可以理解成车的车系了。

显然，每一个产品族中含有产品的数目，与产品等级结构的数目是相等的。产品的等级结构与产品族将产品按照不同方向划分，形成一个二维的坐标系。只要指明一个产品所处的产品族以及它所属的等级结构，就可以唯一的确定这个产品。

## 适用场景

*   一个系统要独立于它的产品的创建、组合和表示时。
*   一个系统要由多个产品系列中的一个来配置时。
*   当你要强调一系列相关的产品对象的设计以便进行联合使用时。
*   当你提供一个产品类库，而只想显示他们的接口而不是具体实现时。

## 缺点

前面的例子当中，如果我们需要新增一个车系时，变动将会很大。也就是说产品等级一旦发生变化，那抽象工厂模式的使用会给程序代码的改动带来很大的影响。