---
title: Python super\(\)
tags:
  - python
  - super
id: 944
categories:
  - python
date: 2015-11-23 14:35:33
---

一般我们在使用类的继承时会用到super\(\)方法
```
class Animal(object):
    def eat(self,food):
        print('the animal is eating',food)

class Person(Animal):
    def eat(self):
        print('the person is eating now!')
        super(Person,self).eat('meal')

person = Person\(\)
person.eat\(\)
```
该代码在python3.4中输出结果为
```
> the person is eating now!
>   the animal is eating meal
>   [Finished in 0.1s]
```
可以看出其使用方法和C#的还是有点区别，C#使用关键字`base`来调用父类的方法，而在python中使用super。而且需要传递当前类名和self关键字然后再调用具体的方法。我在想这个过程是不是已经将父类给实例化出来了。