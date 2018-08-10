---
title: '从一个简易留言板体验全栈'
date: 2018-08-09 10:45:40
tags: [Node, MongoDB]
categories: 谁不想会全栈呢
---

# 开始

我想，每一个前端工程师都有一颗全栈的心吧。。。

很久之前，自己写过几个基于express + mongodb写的应用，出发点很简单，想体验全栈式开发，想学一下后台语言，但是后面由于很多原因，写了一些之后，很快就忘记了，加上工作原因，后面一直都没有去更新自己写的项目。所以，那些使用过的东西，基本都忘记了。

前几天在知乎听了几个live，有一个大神说到一点，就是他学习后台语言的时候，他都会用那个语言做一个最简单的留言板，去体验它的语言特性，增删改查的操作。

所以，我也写了一个简易的留言板，在这Mark一下所学所想。

# 技术栈
express Mongoose Vue Jquery

# 项目搭建
为了快速搭建一个基于express的应用，我使用了express-generator 来搭建自己的web应用，模板引擎用的是ejs。
[使用文档](http://expressjs.com/en/starter/generator.html)

# 开始思考

启动了项目之后，进入了首页。
这个时候，开始思考，只有当登录之后才能进来首页，所以，现在我需要做两件事情，一个是判断是否登录，一个是做一个登录注册的页面。

## 判断是否登录
怎么判断？ 记得之前跟后台同事配合的时候，听他们说过一些做法。就是登录之后在session里面存放用户信息， 每次进入页面之前，都判断一下是否有用户的某个信息，如果有，就代表登录，否则就是未登录。

所以最重要的是使用session去存储用户信息。因此，我需要使用session。
于是，我选择了[express-session](https://www.npmjs.com/package/express-session)

接下来，我就可以判断了，代码如下：

    router.get('/', function(req, res, next) {
        let isLogin = req.session.user;
        if(!isLogin){
            res.render('login');
        }else{
            res.render('index', {username: req.session.user.name});
        }
    });

大致就是：render之前，取session里面的user，有则render首页，否则render登录页面。

## 制作登录页面
因为使用的模板引擎是ejs，所以，页面的后缀名为.ejs，直接在views下面新建一个login.ejs即可。
因为仅仅是为了练习，所以在页面加了一个注册按钮，登录注册都在一个页面里面了。


# 开始写接口
当以上工作做好之后，接下来，需要去实现登录和注册的功能。

## 如何开始
此时，该如何开始呢？
其实有跟后台配合的经验都知道一个东西，那就是数据库。数据库简单说，就是存储数据的仓库。登录注册必然会产生用户的一些数据，所以此时我需要一个数据库。
我选择了mongoDB。
之前接触过php，所以知道，数据库都有类似的可视化工具，比如Navicat等等，mongodb的可视化工具，我选择是[Robomongo](https://robomongo.org/)。同时，我还选择了[mongoose](http://mongoosejs.com/docs/api.html)，作为操作数据库的对象模型工具，说白了，就像是封装好的数据库操作方法，方便操作数据库。

## 连接数据库
在成功安装好数据库之后，尝试使用mongoose连接数据库。代码如下：

    const mongoose = require('mongoose');
    mongoose.connect('mongodb://localhost/msgboard');
    const db = mongoose.connection;
    db.on('open', function(){
    console.log('数据库连接成功')
    })

当数据库连接成功之后，就能打印出 '数据库连接成功' 的字样，此时代表数据库连接成功。
为了检测错误，mongoose提供了error方法，跟open使用一样，可以在控制台输出错误以查看错误原因。
我这里使用的是5.0+的版本。

## 创建用户集合
用户注册登录所操作的集合都是同一个，所以此时需要建立一个用户集合。
MYSQL在建表的时候，都需要先设计表结构，同理，我们也可以按照这种流程去操作。

    const userSchema = new mongoose.Schema({
        name: String,
        password: String
    })

我把Schema理解为表结构。key代表的是字段名，value是字段类型。
然后再生成一个Model，模型。

    const userModel = db.model('users',userSchema);

users, 就是数据库中集合的名字。

此后，所有的增删改查都在这个生成的model上操作即可。

## 注册接口
注册其实就是往刚刚的集合里面添加一条数据。
Mongoose插入的方法很多，我为了练习，选择了insertMany。

    userModel.insertMany({
            name: name,
            password: password
          }, function(err, doc){
            // todo
          })

业务逻辑写在回调function里面。

## 前端如何访问接口
平时跟后台对接的时候，他们提供一个接口地址，然后根据接口文档，传对应参数，得到相应返回值。
那么我们需要一个前台可以访问的接口地址。

    app.get('/doregister', api.doregister)

前台可以用get方式去请求这个接口，为了练习，我所有的接口都是get方式。
所有的业务逻辑都写在api中的doregister方法里面。

    doregister: (req, res, next)=>{
        let name = req.query.name,
            password = req.query.password;
        userModel.insertMany({
        name: name,
        password: password
        }, function(err, doc){
        // todo
        })
    },

注册需要用户名以及密码作为参数传过去，于是前端请求为：

     $.ajax({
        url: '/doregister',
        type: 'GET',
        data: {name: $('#name').val().trim(), password: $('#password').val()}
    }).done(function(res){
        // todo
    })

到这里，第一个接口就写完了。

# 轻车熟路

当写完第一个接口的时候，会发现，其实并没有那么的复杂，就是定义接口，访问接口，在接口里面写业务逻辑，剩下的都可以根据文档去书写。

## mongoose的增删改查

上面的注册接口，实现了最基本的增加数据的操作。
接下来，说一下数据查询。

### 分页查询
在留言列表里面，我们需要去查到当前用户的、没有被删除的留言，并且，需要做分页查询。

同样的，留言列表所有的数据都来自数据库。根据上面的思路，需要建一个存放留言信息的集合，并生成一个model。

    const msgSchema = new mongoose.Schema({
        sender: String,
        nickname: String,
        title: String,
        content: String,
        addTime: Date,
        isDel: {
            type: Number,
            default: 0
        }
    })
    const msgModel = db.model('msgs', msgSchema)

然后提供一个供前端访问的接口地址：

    app.get('/doGetMsg', api.doGetMsg)

接下来，业务逻辑：

    doGetMsg: (req, res, next)=>{
      let pageIndex = parseInt(req.query.pageIndex),
          pageSize = parseInt(req.query.pageSize),
          skipNum = (pageIndex -1 ) *  pageSize;
      msgModel.find({sender: req.session.user.name,isDel: {$ne: 1}}, "nickname title content addTime", {skip: skipNum, limit: pageSize, sort: {addTime: -1}}, function(err, doc){
        if(!doc){
          res.json({state: false, data: null})
        }else{
          let resJson = doc;
            // 数据总条数
            msgModel.count({sender: req.session.user.name,isDel: {$ne: 1}}, function(err, num){
              if(num){
                res.json({state: true, data: resJson, totalCount: num, pageIndex: pageIndex})
              }
            })
        }
      })
    }

分页查询可以是用skip + limit来实现。 skip代表跳过几条数据，limit代表查询几条数据。
所有附加条件，都作为第三个参数传进去。
排序，使用sort，-1表示倒序，1代表正序。

### 删和改
在留言列表页面，需要对留言进行修改和删除，其实删除并不是真正的删除这条记录，而是修改这个记录标记删除状态的值。
因为操作都是一致的， 所以只展示修改的业务代码。

    doUpdateMsg: (req, res, next)=>{
      let msgId = req.query.msgId,
          nickname = req.query.nickname,
          title = req.query.title,
          content = req.query.content;

      msgModel.updateOne({_id: msgId, isDel: {$ne: 1}}, {nickname: nickname, title: title, content: content}, function(err, doc){
        if(!err && doc.ok >= 1){
          res.json({state: true, data: '修改成功'})
        }else{
          res.json({state: false, data: '修改失败'})
        }
      })
    }

利用updateOne方法，去修改集合中的某条记录。还有很多详细的参数解释，并且都非常好用，详情可以参考操作手册。

# 结语
从项目搭建，到接口书写，再到数据库的建立，整个流程下来，算是最最基本的全栈了。
前端路长，人生苦短，且行且学习。

[项目源码](https://github.com/chuanqiangTsang/Msgboard/)
