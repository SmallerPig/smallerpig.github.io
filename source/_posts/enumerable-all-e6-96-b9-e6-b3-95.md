---
title: Enumerable.All方法
tags:
  - 'C#'
id: 986
categories:
  - 'C#'
date: 2016-04-10 16:15:31
---

### 定义为:
```
public static bool All&lt;TSource&gt;(    this IEnumerable&lt;TSource&gt; source,   Func&lt;TSource, bool&gt; predicate);


```

如果源序列中的每个元素都通过指定谓词中的测试，或者序列为空，则为 true；否则为 false。

### 用法举例:

```  

class Pet 
{ 
    public string Name { get; set; } 
    public int Age { get; set; } 
} 

public static void AllEx\(\) 
{ 
    // 创建pet数组
    Pet[] pets = { new Pet { Name="Barley", Age=10 }, 
                    new Pet { Name="Boots", Age=4 }, 
                    new Pet { Name="Whiskers", Age=6 } }; 

    // 判断是否所有pet的名字都以字符“B”开头 
    bool allStartWithB = pets.All(pet =&gt; 
                                        pet.Name.StartsWith("B")); 

    Console.WriteLine( 
        "{0} pet names start with 'B'.", 
        allStartWithB ? "All" : "Not all"); 
} 

// 上述代码将输出Not all ，并不是所有pet名字都已字符B开头


```

该方法也可用于linq查询的where子句中，例如:

```  

class Pet 
{ 
    public string Name { get; set; } 
    public int Age { get; set; } 
} 
class Person 
{ 
    public string LastName { get; set; } 
    public Pet[] Pets { get; set; } 
} 

public static void AllEx2\(\) 
{ 
    List&lt;Person&gt; people = new List&lt;Person&gt; 
        { new Person { LastName = "Haas", 
                        Pets = new Pet[] { new Pet { Name="Barley", Age=10 }, 
                                            new Pet { Name="Boots", Age=14 }, 
                                            new Pet { Name="Whiskers", Age=6 }}}, 
            new Person { LastName = "Fakhouri", 
                        Pets = new Pet[] { new Pet { Name = "Snowball", Age = 1}}}, 
            new Person { LastName = "Antebi", 
                        Pets = new Pet[] { new Pet { Name = "Belle", Age = 8} }}, 
            new Person { LastName = "Philips", 
                        Pets = new Pet[] { new Pet { Name = "Sweetie", Age = 2}, 
                                            new Pet { Name = "Rover", Age = 13}} } 
        }; 

    // 输出哪些用户的pet的Age都大于5
    IEnumerable&lt;string&gt; names = from person in people 
                                where person.Pets.All(pet =&gt; pet.Age &gt; 5) 
                                select person.LastName; 

    foreach (string name in names) 
    { 
        Console.WriteLine(name); 
    } 

    /* 上述代码将输出
        *  
        * Haas 
        * Antebi 
        */ 
}


```

需要特别注意的是如果集合为空，那么不管传入的predicate是什么都将返回true

### 与any函数的比较

同样的上述查询中，如果想要使用Any\(\)方法查询出同样的结果，则需要使用下述代码:

```  

    IEnumerable&lt;string&gt; names = from person in people 
                                where !person.Pets.Any(pet =&gt; pet.Age &lt;= 5) 
                                select person.LastName; 

    foreach (string name in names) 
    { 
        Console.WriteLine(name); 
    } 
```
可见Any\(\)方法主要用于判断某集合里**是否有**满足条件的元素。
而All\(\)方法用户判断某集合里**是否都**满足条件。