---
title: JS字符串和Array方法
date: 2018-05-30 15:15:09
description: 字符串和Array的操作在js开发中使用非常频繁，也是非常重要的基础。这里对字符串和Array的一些常用操作做个整理，一方面可以加深印象，二来也方便自己今后查阅复习！
tags:
category: 
 - Javascript
---

## String对象属性
**length属性**
length属性是字符串中的一个基本属性了，可以获取字符串长度，要注意的是js的中文每个汉字也只代表一个字符。
```
var str1 = 'abc';
var str2 = '中国';
console.log(str1.length); //3
console.log(str2.length); //2
```
**prototype属性**
prototype属性用来给对象添加属性或方法，并且添加的方法或属性在所有的实例上共享。可以用来扩展js内置对象，如给字符串添加了一个去除两边空格的方法：
```
String.prototype.trim = function(){
    return this.replace(/^\s*|\s*$/g, '');
}```

```bash
封装成函数   

function trim(str,type) {  // [type]类型
    var type=type||"b";
    if(type=="b"){
        return str.replace(/^\s*|\s*$/g,""); // 去除两边的空白
    }else if(type=="l"){
        return str.replace(/^\s*/g,"");      // 去除左边空白
    }else if(type=="r"){
        return str.replace(/\s*$/g,"");      // 去除右边空白
    }else if(type=="a"){
        return str.replace(/\s*/g,"");       // 去除所有空白
    }
}
```

## String对象方法

### 1. 获取类方法
#### (1) charAt()
charAt()方法可用来获取指定位置的字符串，参数为字符串索引值，从0开始到string.length - 1，若不在这个范围将返回一个空字符串
```
var str = 'abcde';
console.log(str.charAt(2));        //返回c
console.log(str.charAt(8));        //返回空字符串
```
#### (2) charCodeAt()
charCodeAt()方法可返回指定位置的字符的Unicode编码。charCodeAt()方法与charAt()方法类似，都需要传入一个索引值作为参数，区别是前者返回指定位置的字符的编码，而后者返回的是字符子串
```
var str = 'abcde';
console.log(str.charCodeAt(0));      //返回97
```
### 2. 查找类方法
#### (1) indexOf()(数组中也有该方法)
<blockquote>stringObject.indexOf(searchvalue,fromindex)</blockquote>

indexOf()用来检索指定的字符串值在字符串中首次出现的位置。它可以接收两个参数，searchvalue表示要查找的子字符串，fromindex表示查找的开始位置，省略的话则从开始位置进行检索。
```
var str = 'abcdeabcde';
console.log(str.indexOf('a'));       // 返回0
console.log(str.indexOf('a', 3));    // 返回5
console.log(str.indexOf('bc'));      // 返回1
```
#### (2) lastIndexOf()(数组中也有该方法)
<blockquote>stringObject.lastIndexOf(searchvalue,fromindex)</blockquote>

lastIndexOf返回的是一个指定的子字符串值最后出现的位置，其检索顺序是从后向前。与indexOf()类似，这两个方法在搜索到第一个匹配的子字符串后就停止运行，所以如果想找到字符串中所有的子字符串出现的位置，可以循环调用。
```
var str = 'abcdeabcde';
console.log(str.lastIndexOf('a'));       // 返回5
console.log(str.lastIndexOf('a', 3));    // 返回0 从第索引3的位置往前检索
console.log(str.lastIndexOf('bc'));      // 返回6
```
#### (3) search()
<blockquote>stringObject.search(substr)
stringObject.search(regexp)</blockquote>

search()方法用于检索字符串中指定的子字符串，或检索与正则表达式相匹配的子字符串。它会返回第一个匹配的子字符串的起始位置，如果没有匹配的，则返回-1。
```
var str = 'abcDEF';
console.log(str.search('c'));     //返回2
console.log(str.search('d'));     //返回-1
console.log(str.search(/d/i));    //返回3
```
#### (4) match()
<blockquote>stringObject.match(substr)
stringObject.match(regexp)</blockquote>

match()方法可在字符串内检索指定的值，或找到一个或多个正则表达式的匹配。

如果参数中传入的是子字符串或是没有进行全局匹配的正则表达式，那么match()方法会从开始位置执行一次匹配，如果没有匹配到结果，则返回null。否则则会返回一个数组，该数组的第0个元素存放的是匹配文本，除此之外，返回的数组还含有两个对象属性index和input，分别表示匹配文本的起始字符索引和stringObject 的引用(即原字符串)。
```
var str = '1a2b3c4d5e';
console.log(str.match('h'));    //返回null
console.log(str.match('b'));    //返回["b", index: 3, input: "1a2b3c4d5e"]
console.log(str.match(/b/));    //返回["b", index: 3, input: "1a2b3c4d5e"]
```
如果参数传入的是具有全局匹配的正则表达式，那么match()从开始位置进行多次匹配，直到最后。如果没有匹配到结果，则返回null。否则则会返回一个数组，数组中存放所有符合要求的子字符串，并且没有index和input属性。在字符串上调用这个方法本质上与调用RegExp的exec()方法相同。
```
var str = '1a2b3c4d5e';
console.log(str.match(/h/g));     //返回null
console.log(str.match(/\d/g));    //返回["1", "2", "3", "4", "5"]
```
#### ES6新增includes()、startsWith()、endsWith()
includes()：返回布尔值，表示是否找到了参数字符串
startsWith()：返回布尔值，表示参数字符串是否在源字符串的头部
endsWith()：返回布尔值，表示参数字符串是否在源字符串的尾部
这三个方法的参数与indexOf()，lastIndexOf()一样
```
var s = 'Hello world';
s.startsWith('world',6);    // true
s.endsWith('Hello',5);      // true
s.includes('Hello',6);      //false
```
**注意：**使用第2个参数n时，endsWith的行为与其他两个方法有所不同。它针对前面n个字符，而其他两个方法针对从第n个位置开始直到字符串结束的字符。

### 3. 截取类方法
#### (1)substring()
<blockquote>stringObject.substring(start,end)</blockquote>

substring()是最常用到的字符串截取方法，它可以接收两个参数(参数不能为负值)，分别是要截取的开始位置和结束位置，它将返回一个新的字符串，其内容是从start处到end-1处的所有字符。若结束参数(end)省略，则表示从start位置一直截取到最后。
```
var str = 'abcdefg';
console.log(str.substring(1, 4));    //返回bcd
console.log(str.substring(1));       //返回bcdefg
console.log(str.substring(-1));      //返回abcdefg，传入负值时会视为0
```
#### (2) slice()(数组也有)
<blockquote>stringObject.slice(start,end)</blockquote>

slice()方法与substring()方法非常类似，它传入的两个参数也分别对应着开始位置和结束位置。而区别在于，slice()中的参数可以为负值，如果参数是负数，则该参数规定的是从字符串的尾部开始算起的位置。也就是说，-1 指字符串的最后一个字符。
```
var str = 'abcdefg';
console.log(str.slice(1, 4));      //返回bcd
console.log(str.slice(-3, -1));    //返回ef
console.log(str.slice(1, -1));     //返回bcdef
console.log(str.slice(-1, -3));    //返回空字符串，若传入的参数有问题，则返回空
```
#### (3) substr()
<blockquote>stringObject.substr(start,length)</blockquote>

substr()方法可在字符串中抽取从start下标开始的指定数目的字符。其返回值为一个字符串，包含从 stringObject的start（包括start所指的字符）处开始的length个字符。如果没有指定 length，那么返回的字符串包含从start到stringObject的结尾的字符。另外如果start为负数，则表示从字符串尾部开始算起。
```
var str = 'abcdefg';
console.log(str.substr(1, 3))    //返回bcd
console.log(str.substr(2))       //返回cdefg
console.log(str.substr(-2, 4))   //返回fg，目标长度较大的话，以实际截取的长度为准
```
### 4. 其他方法
#### (1) replace()方法
> stringObject.replace(regexp/substr,replacement)

replace()方法用来进行字符串替换操作，它可以接收两个参数，前者为被替换的子字符串（可以是正则），后者为用来替换的文本。

如果第一个参数传入的是子字符串或是没有进行全局匹配的正则表达式，那么replace()方法将只进行一次替换（即替换最前面的），返回经过一次替换后的结果字符串。
```
var str = 'abcdeabcde';
console.log(str.replace('a', 'A'));
console.log(str.replace(/a/, 'A'));
```
如果第一个参数传入的全局匹配的正则表达式，那么replace()将会对符合条件的子字符串进行多次替换，最后返回经过多次替换的结果字符串。
```
var str = 'abcdeabcdeABCDE';
console.log(str.replace(/a/g, 'A'));    //返回AbcdeAbcdeABCDE
console.log(str.replace(/a/gi, '$'));    //返回$bcde$bcde$BCDE
```
#### (2) split()方法
> stringObject.split(separator,howmany)

split()方法用于把一个字符串分割成字符串数组。第一个参数separator表示分割位置(参考符)，第二个参数howmany表示返回数组的允许最大长度(一般情况下不设置)。
```
var str = 'a|b|c|d|e';
console.log(str.split('|'));    //返回["a", "b", "c", "d", "e"]
console.log(str.split('|', 3));    //返回["a", "b", "c"]
console.log(str.split(''));    //返回["a", "|", "b", "|", "c", "|", "d", "|", "e"]
也可以用正则来进行分割

var str = 'a1b2c3d4e';
console.log(str.split(/\d/)); //返回["a", "b", "c", "d", "e"]
```
#### (3) toLowerCase()和toUpperCase()
toLowerCase()方法可以把字符串中的大写字母转换为小写，toUpperCase()方法可以把字符串中的小写字母转换为大写
```
var str = 'JavaScript';
console.log(str.toLowerCase());    //返回javascript
console.log(str.toUpperCase());    //返回JAVASCRIPT
```
## Array常用方法（主要是遍历方法）

**forEach**
forEach() 方法指定数组的每项元素都执行一次传入的函数，返回值为undefined。
语法：arr.forEach(fn, thisArg)
fn 表示在数组每一项上执行的函数，接受三个参数：

value 当前正在被处理的元素的值
index 当前元素的数组索引
array 数组本身

thisArg 可选，用来当做fn函数内的this对象。
forEach 将为数组中每一项执行一次fn函数，那些已删除，新增或者从未赋值的项将被跳过（但不包括值为 undefined 的项）。遍历过程中，fn会被传入上述三个参数。

**every**
every() 方法使用传入的函数测试所有元素，只要其中有一个函数返回值为 false，那么该方法的结果为 false；如果全部返回 true，那么该方法的结果才为 true。因此 every 方法存在如下规律：

若需检测数组中存在元素大于100 （即 one > 100），那么我们需要在传入的函数中构造 "false" 返回值 （即返回 item <= 100），同时整个方法结果为 false 才表示数组存在元素满足条件；（简单理解为：若是单项判断，可用 one false ===> false）


若需检测数组中是否所有元素都大于100 （即all > 100）那么我们需要在传入的函数中构造 "true" 返回值 （即返回 item > 100），同时整个方法结果为 true 才表示数组所有元素均满足条件。(简单理解为：若是全部判断，可用 all true ===> true）

以下是鸭式辨型的写法：
```
var o = {0:10, 1:8, 2:25, length:3};
var bool = Array.prototype.every.call(o,function(value, index, obj){
  return value >= 8;
},o);
console.log(bool);
```
**some**
some() 方法刚好同 every() 方法相反，some测试数组元素时，只要有一个函数返回值为 true，则该方法返回true，若全部返回 false，则该方法返回false。some方法存在如下规律：

若需检测数组中存在元素大于100 (即 one > 100)，那么我们需要在传入的函数中构造 "true" 返回值 (即返回 item > 100)，同时整个方法结果为 true 才表示数组存在元素满足条件；（简单理解为：若是单项判断，可用 one true ===> true）


若需检测数组中是否所有元素都大于100（即 all > 100），那么我们需要在传入的函数中构造 "false" 返回值 （即返回 item <= 100），同时整个方法结果为 false 才表示数组所有元素均满足条件。（简单理解为：若是全部判断，可用 all false ===> false）

你注意到没有，some方法与includes方法有着异曲同工之妙，他们都是探测数组中是否拥有满足条件的元素，一旦找到，便返回true。多观察和总结这种微妙的关联关系，能够帮助我们深入理解它们的原理。

**filter**
filter() 方法使用传入的函数测试所有元素，并返回所有通过测试的元素组成的新数组。它就好比一个过滤器，筛掉不符合条件的元素。
```
语法：arr.filter(fn, thisArg)
var array = [18, 9, 10, 35, 80];
var array2 = array.filter(function(value, index, array){
  return value > 20;
});
console.log(array2); // [35, 80]
```

**map**
map()方法遍历数组，使用传入函数处理数组每个元素，并返回函数的返回值组成的新数组。
语法：arr.map(fn, thisArg)

参数：
第一个参数：函数
    函数接收三个参数
    1、数组的项
    2、该项在数组中的位置
    3、数组对象本身
第二个参数：第一个参数的执行环境(this指向)

返回值：
    forEach() 无返回值
    every() 对数组运行给定函数，如果该函数对每一项都返回true，则返回true
    some() 对数组运行给定函数，如果该函数对任意一项返回true，则返回true
    filter() 对数组执行给定函数，返回该函数返回true的项组成的数组
    map() 对数组执行给定函数，返回每次函数调用结果组成的数组

**reduce**
reduce() 方法接收一个方法作为累加器，数组中的每个值(从左至右) 开始合并，最终为一个值。
语法：arr.reduce(fn, initialValue)
fn 表示在数组每一项上执行的函数，接受四个参数：

previousValue 上一次调用回调返回的值，或者是提供的初始值
value 数组中当前被处理元素的值
index 当前元素在数组中的索引
array 数组自身

initialValue 指定第一次调用 fn 的第一个参数。
当 fn 第一次执行时：

如果 initialValue 在调用 reduce 时被提供，那么第一个 previousValue 将等于 initialValue，此时 item 等于数组中的第一个值；
如果 initialValue 未被提供，那么 previousVaule 等于数组中的第一个值，item 等于数组中的第二个值。此时如果数组为空，那么将抛出 TypeError。
如果数组仅有一个元素，并且没有提供 initialValue，或提供了 initialValue 但数组为空，那么fn不会被执行，数组的唯一值将被返回。

【ES6】Array.of()

用于创建数组，用法和 new Array() 一样。弥补 Array() 构造函数的不足（即参数不同，行为不同），Array.of() 的行为始终一致，将传入的值作为数组的项，产生数组

参数：任意数量任意值
返回值：创建的数组

【ES6】Array.from(obj, func, context)

用于将 类数组对象(拥有length属性的对象) 和 可遍历对象(部署iterable接口的对象，包括 Set/Map) 转为真正的数组

参数：
{Object} obj 要转为数组的对象
{Function} func 一个函数，功能类似于数组的map方法，对每一个对象属性执行该函数，并返回由该函数的返回值组成的数组
{Object} context 第二个函数参数的执行环境(this指向)
返回值：生成的数组

【ES6】entries()，keys()和values()
描述：entries()，keys() 和 values() 都用于遍历数组。它们都返回一个遍历器对象（详见《Iterator》一章），可以用 for...of 循环进行遍历，唯一的区别是 keys() 是对键名的遍历、values() 是对键值的遍历，entries() 是对键值对的遍历。

【ES7】includes()
查找数组中是否包含给定值

参数：
第一个参数：要查找的值
第二个参数：查找的起始位置，默认是0，负数表示倒数，查出范围会重置为0
返回值：true包含， false不包含includes

相比于indexOf的优势有两点：1、更加语义化，不需要判断返回值是否为 -1。2、由于 indexOf 底层在判断是否相等时使用的是全等操作符 ===，这会导致使用 indexOf 查找 NaN 时查不到，而 includes 则不存在这样的问题

### 总结
**pop,push,reverse,shift,sort,splice,unshift,fill(ES6),copyWithin(ES6) 会改变原数组
join,concat,indexOf,lastIndexOf,slice,toString,includes(ES7) 不会改变原数组
map,filter,some,every,reduce,forEach
和ES6新增的方法entries、find、findIndex、keys、values这些迭代方法不会改变原数组
**

数组的这些方法之间存在很多共性，比如：
**所有插入元素的方法, 比如 push、unshift，一律返回数组新的长度；
所有删除元素的方法,比如 pop、shift、splice 一律返回删除的元素,者返回删除的多个元素组成的数组；
部分遍历方法,比如forEach、every、some、filter、map、find、findIndex，它们都包含function(value,index,array){} 和thisArg这样两个形参。
**
Array.prototype的所有方法均具有鸭式辨型这种神奇的特性。它们不止可以用来处理数组对象，还可以处理类数组对象。例如javascript中一个纯天然的类数组对象字符串（String），像join方法（不改变当前对象自身）就完全适用，可惜的是Array.prototype中很多方法均会去试图修改当前对象的length属性，比如说pop、push、shift, unshift方法，操作String对象时，由于String对象的长度本身不可更改，这将导致抛出TypeError错误，Array.prototype本身就是一个数组，并且它的长度为0。


**几个注意点：**
shift,pop会返回那个被删除的元素
splice 会返回被删除元素组成的数组，或者为空数组
push 会返回新数组长度
some 在有true的时候停止
every 在有false的时候停止
上述的迭代方法可以在最后追加一个参数thisArg，它是执行 callback 时的 this 值


参考链接：http://riny.net/2012/the-summary-of-javascript-string
