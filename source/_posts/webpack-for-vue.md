---
title: 如何在APS.NET_MVC3.0 中搭建一个Vue开发工程
date: 2018-06-29 15:12:01
tags: [Vue, 工程化]
categories: 工程化
---

# 开始
公司使用的是 APS.NET_MVC3.0 框架，并且之前写的都是基于Jquery来写的，为了赶上时代，同时，公司的项目确实是适合用Vue开发，于是，便在这个框架中搭建了一个vue开发的工程项目。

# 工具
这个工程主要是使用 webpack 来对对应的文件进行打包编译

# 项目结构
在Scripts中新建一个source文件夹，工程项目就放在这个文件夹下

![image](https://raw.githubusercontent.com/chuanqiangTsang/chuanqiangtsang.github.io/master/images/scripts.png)

每个文件夹各存放对应的文件

在Content中新建一个build文件夹，打包出来的文件就放在这个文件夹下


# webpack配置文件
这个配置文件，基本满足基本开发，代码如下：

    const webpack = require('webpack')
    const path = require('path')
    const VueLoaderPlugin = require('vue-loader/lib/plugin')

    module.exports = {
    entry: {
        a: 'a.js',
        b: 'b.js',
    },
    output: {
        path: path.resolve(__dirname, '../../Content/build/'),
        filename: '[name].js',
        publicPath: '../../Content/build/',  // 相对目录
    },
    module: {
        rules: [
        {
            test: /\.vue$/,
            loader: 'vue-loader'
        },
        // 它会应用到普通的 `.js` 文件
        // 以及 `.vue` 文件中的 `<script>` 块
        {
            test: /\.js$/,
            loader: 'babel-loader',
            exclude: file => (
            /node_modules/.test(file) &&
            !/\.vue\.js/.test(file)
            )
        },
        // 它会应用到普通的 `.css` 文件
        // 以及 `.vue` 文件中的 `<style>` 块
        {
            test: /\.css$/,
            use: [
            'vue-style-loader',
            'css-loader'
            ]
        },

        //图片文件
        {
            test: /\.(gif|jpg|png|woff|svg|eot|ttf)\??.*$/,
            loader: 'url-loader?limit=8192&name=[hash:8].[name].[ext]'
        },
        ]
    },
    plugins: [
        // 请确保引入这个插件！
        new VueLoaderPlugin()
    ]
    }

# package.json

    {
    "name": "webpack",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "devDependencies": {
            "babel-core": "^6.26.3",
            "babel-loader": "^7.1.4",
            "babel-plugin-transform-runtime": "^6.23.0",
            "babel-polyfill": "^6.26.0",
            "babel-preset-env": "^1.7.0",
            "babel-preset-es2015": "^6.24.1",
            "babel-runtime": "^6.26.0",
            "css-loader": "^0.28.11",
            "extract-text-webpack-plugin": "^3.0.2",
            "jquery": "^3.3.1",
            "style-loader": "^0.21.0",
            "uglifyjs-webpack-plugin": "^1.2.5",
            "url-loader": "^1.0.1",
            "validate": "^4.4.1",
            "vue": "^2.5.16",
            "vue-loader": "^15.2.4",
            "vue-style-loader": "^4.1.0",
            "vue-template-compiler": "^2.5.16",
            "webpack": "^4.11.1"
        }
    }

有了以上文件，就可以对相应的文件进行编译了

在CMD执行webpack --watch 就可以实现实时编译

# 结语
这算是一个简单的demo， 如果需要其他的loader，自行配置即可

搭建好这个工程，我们可以进行模块化开发，将可以公共出来的类，方法放入相应的模块中，在需要的地方引用即可

其实，关键的地方，就是配置 webpack打包文件，剩下的都是按需创建就行了。