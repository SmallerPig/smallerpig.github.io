---
title: Android基础教程——在TextView中显示Html 自定义标签，获取标签属性
id: 37
categories:
  - Android
  - Android基础
date: 2013-05-20 17:13:04
tags:
---

In my work, I have to add custom links (use custom tag) in each listview item. I met two problems and have searched by Google and stackoverflow, but no result...

Here is the code segment and I process the tag with TagHandler.

    TextView tv = (TextView)paramView.findViewById(R.id.tv_dynamics_desc);
    tv.setClickable(true);
    tv.setText(Html.fromHtml("&lt;uc id="133"&gt;This is a Uc link&lt;/uc&gt;", null, this));
    tv.setMovementMethod(LinkMovementMethod.getInstance\(\)); `

 ```
    **The  problem is how to get the attribute： id.**
```  

 @Override
    public void handleTag(boolean opening, String tag, Editable output, XMLReader xmlReader) {
        if(tag.toLowerCase\(\).startsWith("uc")) {
            if (opening) {
                //String id = xmlReader.getProperty( "id" ).toString\(\);
      //&lt;=== to get the attribute, but failed with exception of no such property.
                startClick(tag, output, xmlReader);  
            } else {  
                endClick(tag, output, xmlReader);  
            }
        }
    }

xmlReader.getProperty( "id" ).toString\(\); does not work here.

Is there any way to get the attribute directly?

the ansow is "yes". but I don't know now~!

I met the same problem and i will save it and record it at here!

&nbsp;