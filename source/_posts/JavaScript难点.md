---
title: Javascript难点
date: 2018-05-15 09:28:32
description: JS三大难点分别指：原型与原型链，作用域及闭包，异步和单线程。Javascript引擎是单线程运行的，浏览器无论在什么时候都有且只有一个线程在运行JavaScript程序。

categories:
 - Javascript
tags:
 - grammer
---

JavaScript引擎是单线程运行的，浏览器无论在什么时候都有且只有一个线程在运行JavaScript程序，浏览器内核实现允许多个线程异步执行，这些线程在内核制控下相互配合以保持同步。浏览器内核的实现至少有三个常驻线程：javascript引擎线程，界面渲染线程，浏览器事件触发线程，也有一些执行完就终止的线程，如Http请求线程，这些异步线程都会产生不同的异步事件，单线程的JavaScript引擎与另外那些线程进行互动通信，虽然每个浏览器内核实现细节不同，但这其中的调用原理都是大同小异。

### 原型与原型链
说到原型，就得提一下构造函数，首先看下面一个简单的例子：
```bash
function Dog(name,age){   
    this.name = name;
    this.age = age;
}

let dog1 = new Dog("哈士奇",3);
let dog2 = new Dog("泰迪",2);
```
首先创造空的对象，再让this指向这个对象，通过this.name进行赋值，最终返回this，这其实也是new 一个对象的过程。

其实： let obj = {} 是 let obj = new Object()的语法糖； let arr = [] 是 let arr = new Array()的语法糖； function Dog(){} 是 let Dog = new Fucntion()的语法糖。

在js中，所有对象都是Object的实例，并继承Object.prototype的属性和方法，但是有一些是隐性的。

**看一下原型的规则：**

1.所有的引用类型（包括数组，对象，函数）都具有对象特性；可自由扩展属性。
```bash
var obj = {};
obj.attribute = "原型";
var arr = [];
arr.attribute = "作用域";
function fn1 () {}
fn1.attribute = "闭包";
```
2.所有的引用类型（包括数组，对象，函数）都有隐性原型属性（__proto__）,值也是一个普通的对象。
```bash
console.log(obj.__proto__);
```
3.所有的函数，都有一个prototype属性，值也是一个普通的对象。
```bash
console.log(obj.prototype);
```
4.所有的引用类型的__proto__属性值都指向构造函数的prototype属性值。
```bash
console.log(obj.__proto__ === Object.prototype); // true
```
5.当试图获取对象属性时，如果对象本身没有这个属性，那就会去他的__proto__（prototype）中去寻找。

```bash
function Dog(name){ 
   this.name = name; 
} 
Dog.prototype.callName = function (){ 
   console.log(this.name,"wang wang"); 
}
let dog1 = new Dog("Three Mountain"); 
dog1.printName = function (){ 
   console.log(this.name);
}
dog1.callName();  // Three Mountain wang wang
dog1.printName(); // Three Mountain
```
原型链如下图：
找一个属性，首先会在f.__proto__中去找，因为属性值为一个对象，那么就会去f.__proto__.__proto__去找，同理如果还没找到，就会一直向上去查找，直到结果为null为止，这个串起来的链即为原型链。
![](http://jingchao.xyz/blog/images/proto.jpg)

### 作用域及闭包

说到作用域，肯定会想到是执行上下文。每个函数都有自己的excution context，和variable object。这些环境用于储存上下文中的变量，函数声明，参数等。只有函数才能制造作用域。
**PS：for  if  else 不能创造作用域**
```bash
console.log(a) ; // undefined
var a = 1;

//可理解为
var a;
console.log(a);  // undefined
a = 1;
```
执行console.log时，a只是被声明出来，并没有赋值；所以结果当然是undefined。

**this**

本质上来说，在js里this是一个指向函数执行环境的指针，this永远指向最后调用它的对象，并且在执行时才能获取值，定义是无法确认它的值。箭头函数没有它自己的this值，箭头函数内的this值继承自外围作用域。
```bash
var a = { 
    name : "A", 
    fn : function （）{
        console.log (this.name) 
    } 
} 
a.fn() // this === a 
a 调用了fn() 所以此时this为a

a.fn.call ({name : "B"}) // this === {name : "B"} 
使用call(),将this的值指定为{name:"B"}

var fn1 = a.fn 
fn1() // this === window
```
虽然指定fn1 = a.fn，但是调用是有window调用，所以 this 为window

this有多种使用场景，下面会主要介绍4个使用场景：

**1.作为构造函数执行**
```bash
function  Student(name,age) {
    this.name = name           // this === s
    this.age = age             // this === s
    //return  this
}
var s = new Student("hpu",22)
```
首先new 字段会创建一个空的对象，然后调用apply()函数，将this指向这个空对象。这样的话，函数内部的this就会被空对象代替。

**2.作为普通函数执行**
```bash
function  fn () {
    console.log (this)       // this === window
}
fn ()
```
**3.作为对象属性执行**
```bash
var obj = {
    name : "A",
    printName : function () {
        console.log (this.name)  // this === obj
    }
}
obj.printName ()
```
**4.call()，apply()，bind()**

三个函数可以修改this的指向，具体往下看：
```bash
var name = "小明" , age = "17"
var obj = { 
    name : "安妮", 
    objAge : this.age, 
    fun : function () { 
            console.log ( this.name + "今年" + this.age ) 
        } 
} 
console.log(obj.objAge) // 17 
obj.fun() // 安妮今年undefined
```
```
var name = "小明" , age = "17" 
var obj = { 
    name : "安妮", 
    objAge ：this.age, 
    fun : function (like,dislike) { 
              console.log (this.name + "今年" + this.age ，"喜欢吃" 
              + like + "不喜欢吃" + dislike) 
           } 
    } 
var a = { name : "Jay", age : 23 } 
obj.fun.call(a,"苹果","香蕉") // Jay今年23 喜欢吃苹果不喜欢吃香蕉 
obj.fun.apply(a,["苹果","香蕉"]) // Jay今年23 喜欢吃苹果不喜欢吃香蕉 
obj.fun.bind(a,"苹果","香蕉")() // Jay今年23 喜欢吃苹果不喜欢吃香蕉
```
首先call，apply，bind第一个参数都是this指向的对象，call和apply如果第一个参数指向null或undefined时，那么this会指向windows对象。

call，apply，bind的执行方式如上例所示。call，apply都是改变上下文中的this，并且是立即执行的。bind方法可以让对应的函数想什么时候调用就什么时候调用。

**闭包**

闭包的概念很抽象，看下面的例子理解什么叫闭包
```bash
function a(){
  var n = 0;
  this.fun = function () {
    n++; 
    console.log(n);
  };
}
var c = new a();
c.fun();  //1
c.fun();  //2
```
闭包就是能够读取其他函数内部变量的函数。在js中只有函数内部的子函数才能读取局部变量。所以可以简单的理解为：定义在内部函数的函数。

用途主要有两个：

1）前面提到的，读取函数内部的变量。

2）让变量值始终保持在内存中。

### 异步和单线程

先感受下异步
```bash
console.log("start");
setTimeout(function () {
    console.log("medium");
}, 1000);
console.log("end");
```
使用异步后，打印的顺序为 start-> end->medium，因为没有阻塞。

**异步产生原因**

首先因为js为单线程，也就是说CPU同一时间只能处理一个事务。得按顺序，一个一个处理。

如上例所示，第一步：执行第一行打印 “start”；第二步：执行setTimeout，将其中的函数分存起来，等待时间结束后执行；第三步：执行最后一行，打印“end”；第四步：处于空闲状态，查看暂存中，是否有可执行的函数；第五步：执行分存函数。

**js引擎单线程**

js的主要用途是与用户互动，以及操作DOM，这决定它只能是单线程。例：一个线程要添加DOM节点，一个线程要删减DOM节点，容易造成分歧。

为了更好使用多CPU，H5提供了web Worker 标准，允许js创建多线程，但是子线程受到主线程控制，而且不得操作DOM。

任务列队

单线程就意味着，所有的任务都要排队，前一个结束，才会执行后面的任务。如果列队是因为计算量大，CPU忙不过来，倒也算了。但是更多的时候，CPU是闲置的，因为IO设备处理得很慢，例如 ajax读取网络数据。js设计者便想到，主线程完全可以不管IO设备，将其挂起，然后执行后面的任务。等后面的任务结束掉，在反过头来处理挂起的任务。

梳理一下：

1）所有的同步任务都在主线程上执行，形成一个执行栈

2）除了主线程之外，还存在一个任务列队，只要一步任务有了运行结果，就在任务列队中植入一个时间

3）主线程完成所有任务，就会读取列队任务，并将其执行

4）重复上面三步

只要主线程空了，就会读取任务列队，这就是js的运行机制，也被称为 event loop（事件循环）。