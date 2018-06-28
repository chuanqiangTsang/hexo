---
title: 基于vue的Messager提示
date: 2018-06-28 14:41:10
tags: Vue
categories: 每天撸会儿Vue
---

# 开始

昨天整理了一下 [基于vue做一个模态框的方法](https://chuanqiangtsang.github.io/chuanqiangtsang.github.io/2018/06/27/vue-modal/#more)，今天整理一下如何写一个全局Messager提示方法。
调用：

    this.$messager({
        type: 'success',
        msg: '操作成功',
        duration: 3000
    })

# 先写一个vue模板

先写好Messager组件模板， 并把方法，数据以及一些props定义好。
如下:

    <template>
        <transition name="messager-fade">
            <div id="messager" class="jfl-messager" v-show="visible">
                <!-- 自定义的内容 -->
            </div>
        </transition>
    </template>
    <script>
	    export default {
            name: 'jMessager',
            data(){
                return {
                    ...
                }
            },
            props: {
                ...

                // 是否显示
			    visible: Boolean,

                // 延时关闭
                duration: {
                    type: String,
                    default: '2000'
			    },
                ... 
            },
            methods: {
                ...
                close(){
				    this.visible = false
			    },

                setStartTime(){
                    let _duration = parseInt(this.duration)
                    setTimeout(()=>{
                        this.close();
                    }, _duration)
			    }
                ...
            },

            mounted(){
			    this.setStartTime();
		    }
        }
    </script>

# 一些关键点
脑子里回忆一下， 当我调用 <font style="color: red">Messager方法</font> 的时候, 页面顶部出现提示框，间隔一段时间之后消失。

在这个过程中，我们可以整理出以下几点：

1. Messager是一个全局方法
2. 调用这个方法的时候，出现提示框，即往body插入一个节点并显示之
3. 间隔一段时间之后消失

第3点，我们很容易去实现。只需要在组件挂载完成之后，执行一个定时器就可以了

     methods: {
        ...
        close(){
            this.visible = false
        },

        setStartTime(){
            let _duration = parseInt(this.duration)
            setTimeout(()=>{
                this.close();
            }, _duration)
        }
        ...
    },
    mounted(){
        this.setStartTime();
    }



接下来， 我们要做的，就是 <font style="color: red">把Messager组件变成全局的方法</font>

# 把组件插入到body中
为了使的代码更加容易管理， 我们另起一个js文件来处理这个过程。

    import Messager from './messager.vue'
    let MessagerConstructor = Vue.extend(Messager)

 Vue.extend 官方文档的解释是 *允许声明扩展另一个组件(可以是一个简单的选项对象或构造函数)，而无需使用 Vue.extend。这主要是为了便于扩展单文件组件。*

 也就是说， 使用 Vue.extend 我就得到了一个<font style="color: red">新的Messager组件</font>，我们所有操作，都在这个新的Messager组件上执行。

接下来， 定义一个函数，这个函数将被写在全局Vue对象上。

    const Messager = function(opts){
        // 参数处理
        if(typeof opts === 'string'){
            opts = {
                msg: opts
            }
        }

        // 注入到body
        let MessagerInstance = new MessagerConstructor({data: opts});
        MessagerInstance.vm = MessagerInstance.$mount();
        document.body.appendChild(MessagerInstance.vm.$el)
        
        // 参数赋值
        MessagerInstance.vm.visible = true;
        if(opts.type){
            MessagerInstance.vm.type = opts.type
        }
        MessagerInstance.vm.msg = opts.msg
        
        return MessagerInstance.vm
    }

分析一下这段代码：

1. 参数处理， 就是当这个函数的参数是一个字符串类型的时候，就把这个参数赋值给msg属性
2. 注入到body，上面我们提及过，当调用Messager方法的时候，会在body里面注入一个Dom节点，这个Dom节点，就是我们刚刚写好的Messager组件，那么要如何来获取这个Dom节点并把它注入到body呢？
vue提供了一个 <font style="color: red">$mount</font> 方法。通过这个方法，可以手动挂载这个组件，挂载完成之后，我们可以通过 $el 来获取Dom节点。

    手动挂载
        
        MessagerInstance.vm = MessagerInstance.$mount();

    获取到这个Dom节点，<font style="color: red">$el</font>

        MessagerInstance.vm.$el

    注入到body

        document.body.appendChild(MessagerInstance.vm.$el)

3. 参数赋值，我们知道，当一个实例 vm 挂载完成之后，我们就能获取到这个实例的data，methods，props等数据。因此，我们可以通过设置组件中控制显示隐藏的参数 visible，来让这个组件显示出来。

    MessagerInstance.vm.visible = true;

再将参数对应地赋给Messager，比如type，msg等。

    if(opts.type){
        MessagerInstance.vm.type = opts.type
    }
    MessagerInstance.vm.msg = opts.msg

<font style="color: red">参数赋值，就相当于是父子通讯，父组件传数据给子组件的过程， 只不过是通过直接赋值才实现的。</font>

最后把定义的Messager方法暴露出来

    export default Messager

# 定义全局方法

在定义全局方法的时候，我们会把这个方法写在 Vue.prototype 上。

    import Messager from './messager.js'
    Vue.prototype.$messager = Messager

大功告成！接下来我们就可以在所有组件中调用这个方法了。

# 结语
写的比较简单，大致的过程就是：建立组件 -> 将组件变成Dom插入body -> 定义全局方法

其中， 在 将组件变成Dom插入body 这一步的时候，有较多的知识点，比如 Vue.extend、手动挂载等等，如果疑问， 可以参考 ElementUI 源码。

每天撸会儿Vue， 加油吧。。。
