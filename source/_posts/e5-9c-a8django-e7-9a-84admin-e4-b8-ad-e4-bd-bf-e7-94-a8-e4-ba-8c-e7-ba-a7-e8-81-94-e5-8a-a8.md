---
title: 在django的admin中使用二级联动(附源码)
tags:
  - Django
  - 二级联动
id: 1125
comment: false
categories:
  - Django
  - WEB开发
date: 2016-11-18 17:26:17
---

## 前言

之前一直尝试寻找不用自定义的方法，例如看到mdoel的各个Field的属性里面有一个叫做`choices`的属性，眼前一亮。仔细看文档或者网上找了也不是这么个用法，看到有`limit_choices_to`这个属性心想这应该差不多了吧，结果使用起来也不是那么顺利。
仔细想想也不太可能提供好本身只能由ajax才能做的事情，这个确实是太为难人家Django了。

## 数据

首先要解决的就是二级联动到底是个什么样的过程，
一般在使用到的地方比较典型的是城市的选择：先选择省份、再根据身份选择市、再下面是县或者更详细的信息。
看看数据结构可能会发现我们最终想要的其实是一个省份+市+*的结果，也就是最终的地址。所以在数据库里面通常会有省份表(province)、市表(city)，再加上我们想要的address表。city表有一个外键是是province_id；address表有province表的外键，也有city表的外键。
小猪只是拿简单的数据结构来解释面临的问题，其实不仅仅是局限于省市的联动，我们的程序中有很多类似的场景。

## 问题

默认的django会自动根据我们定义的模型生成form给admin使用，使用到这个form的地方分别是change和add的时候。
最终生成的结果就是可以选择所有的省，也可以选择所有的市，你看到这篇文章就说明你也发现这并不合理，正确的应该是在选择某个省的时候在市的下拉列表里只有该省的城市。
恰好，django原生并不能做到这么智能。

## 用到的Django知识

### form表单

默认的django会自动根据我们定义的模型生成form提交给admin页面，我可以使用重载modeladmin的方法来更改这个表单。

### 自定义templates

我们可以在django的admin后台自定义显示的html。

### view &amp; ajax

当用户选择了一个省的时候需要通过ajax来请求该省下面有哪些城市，view的责任就是来选择有哪些城市。

## 实现

下面只是大概代码，具体的代码当然需要根据项目的实际数据结构来做想要的调整。
view部分代码：
```
def get_city(request, provience_id):
    # 根据传递的省的id 查找数据库中该省的城市
    citys = City.objects.filter(provience_id=provience_id)
    result = []
    for i in citys:
        # 对应的id和城市名称组成一个字典
        result.append({'id':i.id, 'name':i.name})
    # 返回json数据
    return HttpResponse(json.dumps(result), content_type="application/json")


```

有了view部分代码之后需要之后我们需要能够调用到该view的ajax，所以在项目的templates文件夹下的下列目录中加入对应的`change_form.html`(只有change,没有add)文件

> /templates/admin/appname/address/change_form.html

html代码：

```  
\{\% extends "admin/change_form.html" \%\}

{% block extrahead %}
{{ block.super }}
<script type="text/javascript" src="{% url 'admin:jsi18n' %}"></script>
    <script>
    django.jQuery(function\(\) {
        var select = django.jQuery("#id_provience");
        select.change(function\(\){
            console.log("value change"+django.jQuery(this).val\(\))
            var url = "/appname/get_city/"+django.jQuery(this).val\(\);//能够正确的访问到view的url
            console.log(url);
            django.jQuery.get(
                url,
                function(data){
                    var target = django.jQuery("#id_city");
                    target.empty\(\);//先要清空一下
                    data.forEach(function(e){
                        // 将从view得到的id和城市名称赋值给city的select
                        target.append("<option value='"+e.id+"'>"+e.name+"<option>");
                        target.eq(0).attr('selected', 'true');
                    });
            })
        });

    });
    </script>
{% endblock %}
```
文件位置放的正确以后在添加address时django会加载该html文件取代默认的add_form.html文件。
好了，核心代码就是上面的两段。我们就可以实现了对应的二级联动，如果想要实现三级联动，思路都是一样的。
done!!

项目源码：https://github.com/SmallerPig/django-demo