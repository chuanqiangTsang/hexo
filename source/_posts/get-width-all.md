---
title: 原生JS获取窗口及元素宽高的方法
date: 2018-08-02 14:27:14
tags: Javascript 
categories: Javascript
---

获取宽高及各种位置信息的方法太多了，一直记不住那么多，于是，把一些常用的方法，做一下汇总，方便查阅。

窗口可视区域宽度： 

    document.documentElement.clientWidth || document.body.clientWidth;

窗口可视区域高度： 

    document.documentElement.clientHeight || document.body.clientHeight;

窗口可视区域宽度+边线和滚动条： 
    
    document.body.offsetWidth ;

窗口可视区域高度+边线和滚动条： 

    document.body.offsetHeight ;

实际内容的宽度： 

    document.body.scrollWidth;

实际内容的高度： 
    
    document.body.scrollHeight;

滚动条下拉被卷起来的距离：
    
    document.documentElement.scrollTop || window.pageYOffset || document.body.scrollTop;

滚动条侧拉被卷起来的距离：
    
    document.body.scrollLeft || document.documentElement.scrollLeft ;

网页正文部分上（窗口距离屏幕顶部）：
    
    window.screenTop;

网页正文部分左 （窗口距离屏幕左边）：
   
    window.screenLeft;

元素的宽度：
    
    obj.offsetWidth;

元素的高度：
    
    obj.offsetHeight;

相对于父元素的上位移（常用于获取定位时偏移量）：

    obj.offsetTop;

相对于父元素的左位移（常用于获取定位时偏移量）：

    obj.offsetLeft;
