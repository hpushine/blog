---
title: Vue实现流程
date: 2018-10-10 14:07:53
tags: vue
category: 
description: vue实现数据绑定，模板编译及渲染流程。
---

### 1.解析模板成render函数

+ render函数返回的是vnode节点，解析的模板即是字符串，在开发环境下编译打包出js代码
+ 用到with（this），改变作用域
+ 模板中的所有信息都会被render函数所包含
+ 模板中用到的data的属性，都变成js变量
+ 模板中的v-model，v-if，v-for，v-on逻辑都变成js逻辑
+ render函数最后返回vnode

### 2.响应式监听  

- Object.defineProrerty(obj,name,{})进行数据劫持，使得属性变为可观察的对象
- data的属性代理到vm实例上
    
### 3.首次渲染，显示页面，且绑定依赖
```
    vm._update(vnode){
        Const preVonde = vm._vnode
        Vm._vnode = vnode
        If(!preVnode) {
            Vm.$el = vm._patch_(vm.$el, vnode)
            
        } else {
            Vm.$el = vm._patch_(preVnode, vnode)
        }
    }
    function updateComponent() {
        vm._update(vm.render())
    }
```
- 初次渲染执行updateComponent
- 执行render函数，访问到模板指令数据属性
- 会被响应式的get方法监听到（不监听set，get作为依赖收集，走get监听来收集data用到的属性，未走到get的属性，set时也无需关心以避免不必要的渲染）
- 执行updateComponent走到vdom的patch方法
- patch将vnode渲染成dom，初次渲染完成
    
### 4.data属性变化，触发rerender

- render函数执行及vdom的patch方法
- 修改data属性，会被响应式set监听到
- set中执行updateComponent
- update重新执行render函数
- 生成vnode和preVnode，通过patch进行对比变化
- 结果渲染到html中


### 基于es6实现简易mvvm框架

<h3>总体框架融合实现类</h3>
```
mvvm.js

class MVVM {
    constructor(options) {
        //属性挂载在实例
        this.$el = options.el;
        this.$data = options.data;

        if(this.$el) {
            //数据劫持，将对象所有属性 改为get和set方法
            new Observer(this.$data);

            //代理属性 vm.msg = vm.$data.msg
            this.proxyData(this.$data)

            //用数据和元素编译
            new Compile(this.$el, this);
        }
    }
    //跟mvvm融合没关系
    proxyData(data) {
        Object.keys(data).forEach((key)=>{
            Object.defineProperty(this,key,{

                get: function() {
                    return data[key];
                },
                set: function(newVal) {
                    data[key] = newVal;
                }
            })
        })
    }
}
```

<h3>实现数据劫持</h3>

```
observer.js文件

class Observer{
    constructor(data) {

        // this.$data = data;
        this.observe(data);
        
    }

    observe(data) {
        //将data属性改为get和set形式
        if(!data || typeof data !== 'object') {
            return
        }

        //数据属性一一劫持
        Object.keys(data).forEach(key=>{
            this.defineReactive(data,key,data[key]);
            this.observe(data[key]);// 递归劫持
        })

    }
    //定义响应式
    defineReactive(obj,key,value) {
        let that = this;

        let dep = new Dep();//每个变化数据对应一个数组  存放所有更新操作

        Object.defineProperty(obj, key, {
            enumerable:true,
            configurable:true,

            get: function() {

                Dep.target && dep.addSub(Dep.target);

                return value;
            },

            set: function(newVal) {
                if(newVal != value){
                    that.observe(newVal); // 如果是对象仍劫持
                    value = newVal;

                    dep.notify(); //通知所有人 数据更新
                }
            }
        });

    }
}

//发布-订阅 数组
class Dep{
    constructor() {
        this.subs = [];
    }

    addSub(watcher) {
        this.subs.push(watcher)
    }

    notify() {
        this.subs.forEach(watcher=>watcher.update());
    }
}


// 给需要变化的元素（input）增加观察者，数据变化后执行对应方法
class Watcher{
    constructor(vm, expr, fn) {
        this.vm = vm;
        this.expr = expr;
        this.cb = fn;
        //获取旧值
        this.oldvalue = this.getOld();      
    }

    getVal(vm,expr) {//获取实例上对应数据
        expr = expr.split('.'); // [a,b,c]
        return expr.reduce((prev,next)=>{ //vm.$data.a
            return prev[next];
        },vm.$data);
    }

    getOld() {
        Dep.target = this;

        let oldVal = this.getVal(this.vm,this.expr);

        Dep.target = null;

        return oldVal;
    }
    //对外暴露方法
    update() {
        let newVal = this.getVal(this.vm,this.expr);
        let oldVal = this.oldvalue;
        if(newVal != oldVal) {
            this.cb(newVal) //调用watch的callback
        } 
    }

//比对新值和旧值，发生变化则执行更新方法

}
```

<h3>模板编译</h3>
```
compile.js文件

class Compile {
    constructor(el, vm) {
        this.el = this.isElementNode(el) ? el : document.querySelector(el);
        this.vm = vm;
        document.querySelector(el);

        if(this.el) {
            //获取到元素才开始编译
            //1.将dom加载到内存，fragment
            let fragment = this.nodetoFragment(this.el);

            //2.编译 查出v-model {{}}
            this.compile(fragment);

            //fragment加入页面
            this.el.appendChild(fragment)
        }

    }

    

    //辅助方法
    isElementNode(node) {
        return node.nodeType === 1;
    }


    //核心方法
    compile(fragment) {
        let childNodes = fragment.childNodes;
        
        Array.from(childNodes).forEach(node=>{
            if(this.isElementNode(node)) {
                //是元素节点，递归
                this.compile(node);
                this.compileElement(node);
                console.log('element:',node)
            } else{
                this.compileText(node);
                console.log('test:' +node)
            }
        })
    }
    //编译元素
    compileElement(node) {
        let attrs = node.attributes;
        Array.from(attrs).forEach(attr=>{
            console.log(attr.name)
            let attrName = attr.name;
            if(this.isDirective(attrName)) {
                //取到对应值放到节点
                let expr = attr.value;
                // let type = attrName.slice(2);
                let [,type] = attrName.split('-');
                //node this.vm.$data expr
                CompileUtil[type](node, this.vm, expr)

            }
        })

    }

    compileText(node) {
        let expr = node.textContent; //取文本内容
        let reg = /\{\{([^}]+)\}\}/g;
        if(reg.test(expr)) {
            //this.vm.$data text
            CompileUtil['text'](node, this.vm, expr)
        }
    }
    //判断是否指令
    isDirective(name) {
        return name.includes('v-');
    }

    nodetoFragment(el) {
        let fragment = document.createDocumentFragment();
        let firstChild;
        while (firstChild = el.firstChild){
            fragment.appendChild(firstChild);
        }

        return fragment; //内存中节点
    }

}

CompileUtil = {
    getVal(vm,expr) {//获取实例上对应数据
        expr = expr.split('.'); // [a,b,c]
        return expr.reduce((prev,next)=>{ //vm.$data.a
            return prev[next];
        },vm.$data);
    },

    getTextVal(vm,expr) {
        return expr.replace(/\{\{([^}]+)\}\}/g, (...arguments)=>{
            return this.getVal(vm,arguments[1]);
            // return arguments[1];
        });
    },
    text(node,vm,expr) {
        let updateFn = this.updater['textUpdate'];
        // console.log(expr)
        let value = this.getTextVal(vm,expr);
        /*let value = expr.replace(/\{\{([^}]+)\}\}/g, (...arguments)=>{
            return this.getVal(vm,arguments[1]);
            return arguments[1];
        });*/

        //加入watch后添加
        expr.replace(/\{\{([^}]+)\}\}/g, (...arguments)=>{
            // return arguments[1];
            new Watcher(vm,arguments[1],()=>{
                //文本节点数据变化，重新获取依赖属性更新文本内容
                updateFn && updateFn(node,this.getTextVal(vm,expr));
            });
        });
        // new Watcher(vm,expr);


        updateFn && updateFn(node,value);
    },

    setVal(vm,expr,value) {
        expr = expr.split('.'); // [a,b,c]
        return expr.reduce((prev,next,currentIndet)=>{ //vm.$data.a
            
            if(currentIndet == expr.length -1) {
                return prev[next] = value;
            }
            return prev[next]; //获取值

        },vm.$data);
    },
    model(node,vm,expr) {
        let updateFn = this.updater['modelUpdate'];

        //这里加一个监控 数据变化 调用watch的callback
        new Watcher(vm,expr,(newVal)=>{
            //值变化调用cb 新值传入 调用watch里updata时调用
            updateFn && updateFn(node,newVal);
        });

        //数据输入框绑定
        node.addEventListener('input',(e)=>{
            let newVal = e.target.value;
            this.setVal(vm,expr,newVal);
        })

        updateFn && updateFn(node,this.getVal(vm,expr));
    },

    updater:{
        textUpdate(node, value) {
            node.textContent = value;
        },
        modelUpdate(node, value) {
            node.value = value;
        }
    }
}
```

