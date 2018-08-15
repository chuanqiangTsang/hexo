---
title: 用JS判断是否触底
date: 2018-08-15 09:36:10
tags: Javascript 
categories: Javascript
---

# 开始
触底加载更多。

# 原理解释

上拉卷起部分(ScrollTop) + 浏览器视口的高度(windowHeight) = 文档高度(documentHeight)。

<br />
上拉卷起部分
    
    function getScrollTop(){
        var scrollTop = 0, bodyScrollTop = 0, documentScrollTop = 0;
        if(document.body){
            bodyScrollTop = document.body.scrollTop;
        }
        if(document.documentElement){
            documentScrollTop = document.documentElement.scrollTop;
        }
        scrollTop = (bodyScrollTop - documentScrollTop > 0) ? bodyScrollTop : documentScrollTop;
        return scrollTop;
    }

浏览器视口的高度

    function getWindowHeight(){
        var windowHeight = 0;
        if(document.compatMode == "CSS1Compat"){
            windowHeight = document.documentElement.clientHeight;
        }else{
            windowHeight = document.body.clientHeight;
        }
        return windowHeight;
    }
 

文档高度

    function getScrollHeight(){
        var scrollHeight = 0, bodyScrollHeight = 0, documentScrollHeight = 0;
        if(document.body){
            bodyScrollHeight = document.body.scrollHeight;
        }
        if(document.documentElement){
            documentScrollHeight = document.documentElement.scrollHeight;
        }
        scrollHeight = (bodyScrollHeight - documentScrollHeight > 0) ? bodyScrollHeight : documentScrollHeight;
        return scrollHeight;
    }



判断

    window.onscroll = function(){
        if(getScrollTop() + getWindowHeight() == getScrollHeight()){
            alert('触底了')
        }
    }

# 结语
方便使用，所以Mark一波。