---
title: Java 泛型
tags:
  - java
id: 1058
categories:
  - java
  - 程序语言
date: 2016-08-12 17:06:57
---

如果我们每次编写一个类时都只能为某一个特定的环境所服务，那是多么可惜的事情.

## 泛型类

### 代码1
``` java
class Demo{

    public Person getItem() {
        return item;
    }

    public void setItem(Person item) {
        this.item = item;
    }

    public LinkClass(Person item) {
        this.item = item;
    }

    private Person item;
}
class Person{
    public Person(){}
}


```

上述代码中定义了一个Demo类，其拥有一个类型为Person的item变量。这个类可以很好的工作。

### 代码2

``` java

class Demo<T>{

    public Demo(){

    }

    public T getItem() {
        return item;
    }

    public void setItem(T item) {
        this.item = item;
    }

    public Demo(T item) {
        this.item = item;
    }

    private T item;

}
```

拥有代码2之后我们可以使用Demo类来操作拥有任何一个类型的变量的类的（代码1只能为Person），只不过在声明的时候需要传递一个你希望使用的类

```  java

//代码1的使用方式
Demo demo = new Demo();
//代码2的使用方式
Demo<Person> demo = new Demo();


```

## 定义

泛型又被称为参数化类型，是含有一个或多个类型参数的类或接口类型。
为啥在某些时候需要使用泛型？小猪的理解就是
1. 方便我们在编译的时候检查错误（配合边界强类型检查），
2. 还有就是可以调用特定于泛型变量引用对象的方法或属性。
什么意思？当我们定义好了泛型变量的边界时，如下代码：

```  java
class Demo<T extends Parent>{}
```

我们就可以在该类的内部使用T。而且任何类型为T的变量都可以使用Parent中的方法。
如下代码：

```  java

class Parent{
    void doSth1(){}
    void doSth2(){}
}

class Demo<T extends Parent>{
    public void setItem(T item) {
        this.item = item;
        item.doSth1();
        item.doSth2();
    }

    T item;
}


```

上述代码的Demo类中，我们可以自由的使用类型为T的变量的soSth1方法和doSth2方法，因为我们已经告诉编译器T类型必须是Parent类型的子类。
这就是利用了所谓的泛型类型参数的边界。

上面的代码1和代码2只定义了为类的情况，当然我们也可以使用泛型接口。

## 泛型接口

通常我们定义接口是：

```  java
interface IEatable{
    void eating();
}


```

而使用泛型接口的定义我们可以将接口定义成：

```  java

interface IEatable<T>{
    T eating();
}


```

这样我们就可以像下面这样使用该接口:

```  java

class Meat{}

class Person implements IEatable<Meat>{

    @Override
    public Meat eating() {
        return null;
    }
}
```
我们定义了一个可以吃肉的人类。

## 总结

相比于.NET的泛型，个人觉得java的还是要比.NET的难用一点。但是概念上都是差不多的。
在前期的学习过程中，其实我们很少用到泛型，只有在有了一定代码量的积累之后才会渐渐的意识到这个概念设计者的良苦用心。而初学者只会把他当成单纯的当做难懂的概念。
还有就是，当我们学习某一个知识点的时候，尤其是编程语言的。我们一定要把运行环境打开，亲自把代码敲进去看看能不能通过编译、能不能正确输出。这很重要，不然我们总是会发现概念我们都懂，只是在真正写的时候还是不会写。
敲代码的过程是一个非常重要的思考的过程。