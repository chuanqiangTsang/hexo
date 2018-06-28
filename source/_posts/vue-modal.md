---
title: 基于vue做一个模态框
date: 2018-06-27 09:50:45
tags: Vue
categories: 每天撸会儿Vue
---
# 背景

这算是真正的第一篇*技术*博客了。。。

姑且说是技术博客吧

本来写了很多的铺垫，后面想想，装逼的话就不多说了，纯属为了把这个只是点Mark一下，开始吧

# 先写一个vue模板

先写好vue模态框模板， 并把方法，数据以及一些props定义好。
如下: 

    <template>
        <transition name="modal-fade">
            <div id="modal" class="jfl-modal_wrapper" v-show="visible">
                <!-- 自定义的内容 -->
            </div>
        </transition>
    </template>
    <script>
	    export default {
            name: 'jModal',
            data(){
                return {
                    ...
                }
            },
            props: {
                ...

                // 是否显示
			    visible: Boolean,

                ... 
            },
            methods: {
                ...
                closeModal(){
                    // props的双向绑定
                    this.$emit('update:visible', false)
                },
                
                ...
            }
        }
    </script>

# props的双向绑定

我们知道子组件无法直接取修改父组件的数据，因此，Vue提供了<font style="color: red">.sync</font> 修饰符, 用update:my-prop-name的模式触发事件。
假设我想在子组件关闭modal，并且把改变后的visible传递到父组件，如下：

    this.$emit('update:visible', false)

父组件绑定只需要在用 sync 修饰visible即可， 如下：

    <j-modal :visible.sync="isVisible"></j-modal>


<font style="color: red; font-size: 16px;">到这里，模态框组件就算是完成了，接下来是把这个组件注册到全局的Vue对象上</font>
<br />

# 把组件注册到全局Vue对象上

这里我写了一个比较通用的方式，方便后续添加

    import jModal from './modal.vue'
    const Components = [
        jflModal
    ]

    const install = (Vue) =>{
        // 注册组件	
        Components.map( comp =>{
            Vue.component(comp.name, comp)
        })
    }

    if( window.Vue !== 'undefined'){
        install(window.Vue)
    }
    export default {
        install
    }

解释一下，大致流程就是：引入组件 -> 存入数组 -> 循环注册

存入数组是为了方便后续添加组件，我只需要往 Components 数组里面添加就行了，剩下的都交给 install 方法。

最后暴露出一个 install 方法， 接下来只需要在引用的时候, 引入一下这个 install 方法就行了

    import Install from 'path-to-file'

因为是这个组件是全局的，所以直接使用就可以了。



# 结语

本以为可以写的简洁一点，没想到，还是写的比较乱，单纯的Mark知识点，关键的点就是 <font style="color: red">props'双向数据绑定'</font>以及<font style="color: red">全局注册组件</font>，掌握这两点，就能很简单实现自定义全局组件了。

至于一些回调函数，就根据需求，自行去设计就行了。

每天撸会儿Vue， 加油吧。。。