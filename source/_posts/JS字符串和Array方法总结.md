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
## Array常用方法

**总结：**
pop,push,reverse,shift,sort,splice,unshift 会改变原数组
join,concat,indexOf,lastIndexOf,slice,toString 不会改变原数组
map,filter,some,every,reduce,forEach这些迭代方法不会改变原数组

**几个注意点：**
shift,pop会返回那个被删除的元素
splice 会返回被删除元素组成的数组，或者为空数组
push 会返回新数组长度
some 在有true的时候停止
every 在有false的时候停止
上述的迭代方法可以在最后追加一个参数thisArg，它是执行 callback 时的 this 值


参考链接：http://riny.net/2012/the-summary-of-javascript-string
