---
title: 使用window.open打开新窗口被浏览器拦截解决方案
date: 2018-07-05 14:10:01
tags: [Javascript]
categories: Javascript
---

# 发生的原因
由于浏览器的安全机制，当window.open是用户触发的或者刚加载时触发的，不会拦截窗口，当window.open放在异步ajax请求或者某段异步代码中触发，该窗口就会被拦截，这类窗口浏览器会认为是广告，所以拦截。

# 通过浏览器设置拦截
发生这种问题，可以通过在浏览器设置允许打开新窗口即可

# 通过代码解决
当产品是面向大用户的，很有可能有用户不知道怎么在浏览器中设置，导致网站的用户体验不好，其实可以通过代码去解决。

通过原因分析， 我们知道，只要是用户主动触发的，浏览器都不会进行拦截，于是，我们可以，

    $('#selector').click(function(){
        var newWindow = window.open()
        $.ajax({
            url: '/api',
            type: 'GET'
        }).done(function(res){
            ...
            newWindow.location.href = res.Data.url
        })
    })

大致流程就是： 点击 -> 打开新窗口 -> 设置窗口href

当用户点击之后，立即打开一个新窗口，当请求完成之后，设置新窗口的href

这个过程可以在控制台进行 '慢动作回放'

输入 var newWindow = window.open() 

Enter

浏览器立即打开新窗口

再输入 newWindow.location.href = 'http://www.baidu.com'

会发现新窗口打开百度首页

# 结语
经测试，IE9及以上均可支持以上方法。

之前在网上看到一些其他的方法，动态创建a标签，再click，多次尝试皆被拦截，故没有将这种方法作为一种可靠的解决方案记录。