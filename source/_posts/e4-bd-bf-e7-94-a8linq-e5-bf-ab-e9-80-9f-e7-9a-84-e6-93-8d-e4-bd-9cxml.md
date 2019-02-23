---
title: 使用Linq快速的操作XML
id: 425
categories:
  - 'C#'
date: 2014-02-21 16:06:32
tags:
---

### 开始内容之前先分享一段话

有时候，当你知道要做什么的时候就做的很快，比如你要实现个功能，码字的活儿不算很难，做个检索也不会有什么难倒你的。
但是，做着做着，你发现好像世界上的工作都在重复，于是你有种心要飞起来的感觉，但总觉得脚步速度太慢，你开始抱怨进度，公司也对你充满期待，于是会给你配备助手，一来二去，你成为了小领导，你不再自己编码了，而且要做什么也都告诉你的助手们了，这时，你陷入了沉思，我该干什么呢？
偶然的，你发现某些事情朝着错误的方向发展，用户开始抱怨了，你跳出来，指出助手们的错误，然后事情过去了，你却停不下来，你怕这样的事情再次发生，于是你开始更深的沉思，这次你学会了思考，我要做的就是保持优秀的团队力量，如何保持呢？古板不是个办法，因为古板会使你的团队成员走你的老路，他们会感到单一、重复、枯燥。。。
你不能再去写代码以逃避无休无止的思考，因为你写的那点代码也起不到作用，还冷不丁的出个意外需要你公关，你很想突破，突破思维的限制，你发现突破不仅是你的挑战，更是你唯一的出路。 

### 正文
```
<?xml version="1.0" encoding="utf-8" ?>
<Columns>
  <column id="序号">
    <name>序号</name>
  </column>
  <column id="检验项目">
    <name>检验项目</name>
  </column>
  <column id="单位">
    <name>单位</name>
  </column>
  <column id="标准要求">
    <name>标准要求</name>
  </column>
  <column id="检验结果">
    <name>检验结果</name>
  </column>
  <column id="结论">
    <name>结论</name>
  </column>
</Columns>

```

以上就是一个Xml文件，我们知道，Xml文件是用来存储数据的，那么我们如何遍历这些数据呢？
其实最简单的方法，就是使用Linq：
```
private void GetTestResultXml\(\)
  {
      List<string> iTestResultXml=new List<string>\(\);
      //定义并从xml文件中加载节点（根节点）
      XElement rootNode = XElement.Load(@"testResult.xml");   
      //查询语句: 获得根节点下name子节点（此时的子节点可以跨层次：孙节点、重孙节点......）
      IEnumerable<XElement> targetNodes = from target in rootNode.Descendants("column")
                                          select target;
      foreach (XElement node in targetNodes)
      {
          iTestResultXml.Add(node.Value);
      }
  }

 ```

这样我们就可以获得&lt;column/&gt;标签里所有的数据了，并把他们存储到列表iTestResultXml中。
在testResult.xml文件中，我们看到，&lt;column/&gt;标签设置了本身的id,而此id并不是他的数据，而是他的一个属性，那么如果我们想获得他的属性而不是他标签里的内容该如何获得呢？
```
private void GetTestResultXml\(\)
  {
      List<string> iXmlID = new List<string>\(\);
      //定义并从xml文件中加载节点（根节点）
      XElement rootNode = XElement.Load(@"....XmltestResult.xml");   
      //查询语句: 获得根节点下name子节点（此时的子节点可以跨层次：孙节点、重孙节点......）
      IEnumerable<XElement> targetNodes = from target in rootNode.Descendants("column")
                                          select target;
      foreach (XElement node in targetNodes)
      {
            iXmlID.Add(node.Attribute("id").Value);   //获取指定属性的方法
      }
  }
```

这样我们就获取了&lt;column/&gt;标签里id属性的列表iXmlID。

参考地址：[http://docs.google.com/fileview?id=0B9T0APtVi1fyNjNhM2I4NmEtMTdmOS00NTUzLTljZTUtMDdhZjM4NzIzMGEz&amp;hl=zh_CN](http://docs.google.com/fileview?id=0B9T0APtVi1fyNjNhM2I4NmEtMTdmOS00NTUzLTljZTUtMDdhZjM4NzIzMGEz&amp;hl=zh_CN "http://docs.google.com/fileview?id=0B9T0APtVi1fyNjNhM2I4NmEtMTdmOS00NTUzLTljZTUtMDdhZjM4NzIzMGEz&amp;hl=zh_CN")

[http://www.cnblogs.com/jiajiayuan/archive/2012/02/09/2343512.html](http://www.cnblogs.com/jiajiayuan/archive/2012/02/09/2343512.html "http://www.cnblogs.com/jiajiayuan/archive/2012/02/09/2343512.html")