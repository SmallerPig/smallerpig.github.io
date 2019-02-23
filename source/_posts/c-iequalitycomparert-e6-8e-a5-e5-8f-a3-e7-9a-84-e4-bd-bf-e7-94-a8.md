---
title: 'C# IEqualityComparer<T>接口的使用'
id: 577
categories:
  - 'C#'
date: 2015-04-21 13:30:45
tags:
---

如果我们使用的都是简单类型,例如int.string等等.那么我们在使用linq的Distinct的时候就可以直接使用`IEnumerable&lt;T&gt;`的`Distinct\(\)`方法了.可是如果我们要对复杂类型进行区分而且区分的逻辑本身需要我们自己完成的时候就需要使用`IEnumerable&lt;T&gt;`的`Distinct`的另一个重载了
```
public static IEnumerable Distinct(this IEnumerable source, IEqualityComparer comparer);


 ```
该重载需要传递一个实现了`IEqualityComparer&lt;T&gt;`的类型来告诉`Distinct`要进行怎样的区别,今天我就来分享下如果使用该接口:

该接口的定义如下:
```
    public interface IEqualityComparer
    {

        bool Equals(T x, T y);

        int GetHashCode(T obj);
    }


 ```
例如我们为了使用该接口定义了下面的类
```
class EqualityComparer : IEqualityComparer
{
    public bool Equals(DemoClass x, DemoClass y)
    {
        if (Object.ReferenceEquals(x, y)) return true;

        if (Object.ReferenceEquals(x, null) || Object.ReferenceEquals(y, null))
            return false;

        return x.Id == y.Id;
    }

    public int GetHashCode(DemoClass obj)
    {
        if (Object.ReferenceEquals(obj, null)) return 0;

        return obj.Id.GetHashCode\(\);
    }
}

public class DemoClass
{
    public int Id { get; set; }

    public string Action { get; set; }

}



 ```
上面的比较器实现了对`DemoClass`的比较,只要两个类的ID相同,不管`action`值是否相同都认为他们是一样的,这样在`distanct`的时候只返回一条数据,
```
static void Main(string[] args)
{

    IEnumerable demoClasses = new List
    {
        new DemoClass\(\) {Id = 1, Action = "edit"},
        new DemoClass\(\) {Id = 2, Action = "add"},
        new DemoClass\(\) {Id = 3, Action = "insert"},
        new DemoClass\(\) {Id = 1, Action = "2222"},

    };

    demoClasses = demoClasses.Distinct(new EqualityComparer\(\)).ToArray\(\);

    foreach (var demoClass in demoClasses)
    {
        Console.WriteLine(demoClass.Id + " " + demoClass.Action);
    }

    Console.ReadLine\(\);
}


 ```
上述代码的最终输出为:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb6.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image7.png)