---
title: JavaScript数组
date: 2018-08-11 10:27:03
tags: Array
category: Javascript
description: 数组是值的有序集合，每个值叫做一个元素，而每个元素在数组中有一个位置，以数字表示，称为索引。
---

JavaScript数组的索引是基于零的32位数值，第一个元素索引为0，数组最大能容纳4294967295（即2^32-1）个元素。

JavaScript数组是动态的，根据需要它们会增长或缩减，并且在创建数组时无需声明一个固定的大小或者在数组大小变化时无需重新分配空间。

JavaScript数组可能是稀疏的，数组元素的索引不一定要连续的，它们之间可以有空缺。

每个JavaScript数组都有一个length属性，针对非稀疏数组，该属性就是数组元素的个数。针对稀疏数组，length比所有元素的索引都要大。

### 创建数组
#### 1、最简单的方法是使用数组直接量(字面量)创建数组。

    var empty = [];     //没有元素的数组
        var arr = [1.1, true, "a",];    //3个不同类型的元素和结尾的逗号

数组直接量中的值也不一定必须是常量，它们可以是任意的表达式：

    var number = 1;
        var list = [number, number+1, number+2];

如果省略数组直接量中的某个值，省略的元素用empty表示（就是没有这个元素），访问的话会返回undefined。

    var count = [1,,3];     // 数组打印出来是(3) [1, empty, 3], count[1]sh是undefined
        var undefs = [,,];      //数组直接量语法允许有可选的结尾的逗号，undefs.length是2

#### 2、构造函数Array()创建数组
调用时没有参数，等同于[]，创建一个没有任何元素的空数组

    var arr = new Array();
调用时有一个数值参数，它指定长度

    var arr = new Array(10)     // (10) [empty × 10]
显式指定两个或者多个数组元素或者数组元素的一个非数值元素

    var arr = new Array(1,2,3,"one");

#### 3、ES6的一些方法
（1）Array.of() 返回由所有参数组成的数组，不考虑参数的数量或类型，如果没有参数就返回一个空数组 (ES6新增)
参数：elementN 任意个参数，将按顺序成为返回数组中的元素。
注意：of() 可以解决上述构造器因参数个数不同，导致的行为有差异的问题(参数只有一个数值时，构造函数会把它当成数组的长度)。

    Array.of(1,2,3); // [1,2,3]
        Array.of(1,{a:1},null,undefined) // [1, {a:1}, null, undefined]

        // 只有一个数值参数时
        let B = new Array(3);   // (3) [empty × 3]
        let C = Array.of(3);    // [3]
返回值： 新的 Array 实例。

（2）Array.from()从一个类数组或可迭代对象中创建一个新的数组 (ES6新增)

参数：
- 第一个参数：想要转换成数组的类数组或可迭代对象
- 第二个参数（可选）：回调函数，类似数组的map方法，对每个元素进行处理，将处理后的值放入返回的数组。
- 第三个参数（可选）：绑定回调函数的this对象

    // 有length属性的类数组
    Array.from({length：5},(v,i) => i)     //[0, 1, 2, 3, 4]

    // 部署了Iterator接口的数据结构 比如:字符串、Set、NodeList对象
    Array.from('hello')    // ['h','e','l','l','o']
    Array.from(new Set(['a','b']))   // ['a','b']

    // 传入一个数组生成的是一个新的数组，引用不同，修改新数组不会改变原数组
    let arr1 = [1,2,3]
    let arr2 = Array.from(arr);
    arr2[1] = 4;
    console.log(arr1,arr2)
    //[1, 2, 3] [1, 4, 3]

返回值： 新的 Array 实例。

**知识点**

    //数组合并去重
        function combine(){
             //没有去重复的新数组，之后用Set数据结构的特性来去重
            let arr = [].concat.apply([], arguments);  
            return Array.from(new Set(arr));
        }

        var m = [1, 2, 2], n = [2,3,3];
        console.log(combine(m,n));

### 数组方法
![](http://jingchao.xyz/blog/images/array.jpg)

#### 1、会改变原数组的方法

1. push() 方法在数组的尾部添加一个或多个元素，并返回数组的长度
参数: item1, item2, ..., itemX ,要添加到数组末尾的元素

    let arr = [1,2,3];
        let length = arr.push('末尾1','末尾2');     // 返回数组长度
        console.log(arr,length)
        // [1, 2, 3, "末尾1", "末尾2"] 5

    返回值： 数组的长度

2. pop() 方法删除数组的最后一个元素，减小数组长度并返回它删除的值。
参数：无

    //组合使用push()和pop()能够用JavaScript数组实现先进后出的栈
        let stack = [];
        stack.push(1,2) // 返回长度2，这时stack的值是[1,2]
        stack.pop()     // 返回删除的值2，这时stack的值是[1]

    返回值： 从数组中删除的元素(当数组为空时返回undefined)。

3. unshift() 方法在数组的头部添加一个或多个元素，并将已存在的元素移动到更高索引的位置来获得足够的空间，最后返回数组新的长度。
参数: item1, item2, ..., itemX ,要添加到数组开头的元素

        let arr = [3,4,5];
        let length = arr.unshift(1,2);  // 返回长度是5
        console.log(arr, length)
        //[1, 2, 3, 4, 5] 5

    注意： 当调用unshift()添加多个参数时，参数时一次性插入的，而非一次一个地插入。就像是上例添加1和2，他们插入到数组中的顺序跟参数列表中的顺序一致，而不是[2,1,3,4,5]。

    返回值： 返回数组新的长度。

4. shift() 方法删除数组的第一个元素并将其返回，然后把所有随后的元素下移一个位置来填补数组头部的空缺，返回值是删除的元素
参数: 无
        let arr = [1,2,3];
        let item = arr.shift(); // 返回删除的值1
        console.log(arr, item)
        // [2, 3] 1
    返回值： 从数组中删除的元素; 如果数组为空则返回undefined 。

5. splice() 方法是在数组中插入或删除元素的通用方法
语法
array.splice(start[, deleteCount[, item1[, item2[, ...]]]])
参数：
start
    指定修改的开始位置（从0计数）。如果超出了数组的长度，则从数组末尾开始添加内容；如果是负值，则表示从数组末位开始的第几位（从-1计数）；若只使用start参数而不使用deleteCount、item，如：array.splice(start) ，表示删除[start，end]的元素。
deleteCount (可选)
    整数，表示要移除的数组元素的个数。如果 deleteCount 是 0，则不移除元素。这种情况下，至少应添加一个新元素。如果 deleteCount 大于start 之后的元素的总数，则从 start 后面的元素都将被删除（含第 start 位）。
如果deleteCount被省略，则其相当于(arr.length - start)。
item1, item2, ... (可选)
要添加进数组的元素,从start 位置开始。如果不指定，则 splice() 将只删除数组元素。
**返回值**： 由被删除的元素组成的一个数组。如果只删除了一个元素，则返回只包含一个元素的数组。如果没有删除元素，则返回空数组。

        // start不超过数组长度(以下操作是连续的)
        let arr = [1,2,3,4,5];
        arr.splice(2)   // arr是[1,2]，返回值是[3,4,5]
        arr.splice(1,1) // arr是[1]，返回值是[2]
        arr.splice(0,3) // arr是[]，返回值是[1],因为此时数组从第0位开始不够3位，
        所以是删除从0开始到最后的所有元素。

        // start大于数组长度(以下操作是连续的)
        let arr = [1,2,3,4,5];
        arr.splice(5)   // arr是[1,2,3,4,5]，返回值是[]
        arr.splice(5,3,6) // arr是[1,2,3,4,5,6]，返回值是[]
        arr.splice(5,3,7) // arr是[1,2,3,4,5,7] 返回值是[6]

        // start是负数(以下操作是连续的)
        let arr = [1,2,3,4,5];
        arr.splice(-3,2); // arr是[1,2,5], 返回值是[3,4]
        arr.splice(-4); // arr是[],返回值是[1,2,5]

        // 插入数组时，是插入数组本身，而不是数组元素
        let arr = [1,4,5];
        arr.splice(1,0,[2,3])   // arr是[1,[2,3],4,5]，返回值是[]
6. sort() 方法将数组中的元素排序并返回排序后的数组
参数：
    compareFunction (可选) 
    用来指定按某种顺序进行排列的函数。如果省略，元素按照转换为的字符串的各个字符的Unicode位点进行排序。
    如果指明了 compareFunction ，那么数组会按照调用该函数的返回值排序。即 a 和 b 是两个将要被比较的元素：

    如果 compareFunction(a, b) 小于 0 ，那么 a 会被排列到 b 之前；
    如果 compareFunction(a, b) 等于 0 ， a 和 b 的相对位置不变。备注： ECMAScript 标准并不保证这一行为，而且也不是所有浏览器都会遵守（例如 Mozilla 在 2003 年之前的版本）；
    如果 compareFunction(a, b) 大于 0 ， b 会被排列到 a 之前。
    compareFunction(a, b) 必须总是对相同的输入返回相同的比较结果，否则排序的结果将是不确定的。

        var stringArray = ["Blue", "Humpback", "Beluga"];
        var numberArray = [40, 1, 5, 200];
        function compareNumbers(a, b){
          return a - b;
        }
        console.log('stringArray:' + stringArray.join());
        console.log('Sorted:' + stringArray.sort());

        console.log('numberArray:' + numberArray.join());
        // 没有使用比较函数时，数字并不会按照我们设想的那样排序
        console.log('Sorted without a compare function:'+ numberArray.sort());
        console.log('Sorted with compareNumbers:'+ numberArray.sort(compareNumbers));

        //打印如下
        // stringArray: Blue,Humpback,Beluga
        // Sorted: Beluga,Blue,Humpback

        // numberArray: 40,1,5,200
        // Sorted without a compare function: 1,200,40,5
        // Sorted with compareNumbers: 1,5,40,200

    返回值： 返回排序后的数组。原数组已经被排序后的数组代替。

7. reverse() 方法将数组中的元素颠倒顺序，返回逆序的数组。
参数: 无

        let arr = [1,2,3];
        arr.reverse()   // arr是[3,2,1]，返回值是[3,2,1]

    返回值： 返回顺序颠倒后的数组。原数组已经被排序后的数组代替。

8. copyWithin() 方法浅复制数组的一部分到同一数组中的另一个位置，并返回它，而不修改其大小。 (ES6新增)
语法：
arr.copyWithin(target[, start[, end]])
参数：
    target
    0 为基底的索引，复制序列到该位置。如果是负数，target 将从末尾开始计算。
    如果 target 大于等于 arr.length，将会不发生拷贝。如果 target 在 start 之后，复制的序列将被修改以符合 arr.length。
    start
    0 为基底的索引，开始复制元素的起始位置。如果是负数，start 将从末尾开始计算。
    如果 start 被忽略，copyWithin 将会从0开始复制。
    end
    0 为基底的索引，开始复制元素的结束位置。copyWithin 将会拷贝到该位置，但不包括 end 这个位置的元素。如果是负数， end 将从末尾开始计算。
    如果 end 被忽略，copyWithin 将会复制到 arr.length。
    返回值： 改变了的数组。
        [1, 2, 3, 4, 5].copyWithin(-2);
        // [1, 2, 3, 1, 2]

        [1, 2, 3, 4, 5].copyWithin(0, 3);
        // [4, 5, 3, 4, 5]

        [1, 2, 3, 4, 5].copyWithin(0, 3, 4);
        // [4, 2, 3, 4, 5]

        [1, 2, 3, 4, 5].copyWithin(-2, -3, -1);
        // [1, 2, 3, 3, 4]

        // copyWithin 函数是设计为通用的，其不要求其 this 值必须是一个数组对象。
        [].copyWithin.call({length: 5, 3: 1}, 0, 3);
        // {0: 1, 3: 1, length: 5}
9. fill() 方法用一个固定值填充一个数组中从起始索引到终止索引内的全部元素。 (ES6新增)
语法:
arr.fill(value[, start[, end]])
参数：
    value 用来填充数组元素的值。
    start (可选) 起始索引，默认值为0。
    end (可选) 终止索引，默认值为 this.length。
    如果 start 是个负数, 则开始索引会被自动计算成为 length+start, 其中 length 是 this 对象的 length 属性值. 如果 end 是个负数, 则结束索引会被自动计算成为 length+end。
    返回值： 修改后的数组
        [1, 2, 3].fill(4);               // [4, 4, 4]
        [1, 2, 3].fill(4, 1);            // [1, 4, 4]
        [1, 2, 3].fill(4, 1, 2);         // [1, 4, 3]
        [1, 2, 3].fill(4, 1, 1);         // [1, 2, 3]
        [1, 2, 3].fill(4, 3, 3);         // [1, 2, 3]
        [1, 2, 3].fill(4, -3, -2);       // [4, 2, 3]
        [1, 2, 3].fill(4, NaN, NaN);     // [1, 2, 3]
        [1, 2, 3].fill(4, 3, 5);         // [1, 2, 3]
        Array(3).fill(4);                // [4, 4, 4]

        //fill 方法故意被设计成通用方法, 该方法不要求 this 是数组对象。
        [].fill.call({ length: 3 }, 4);  // {0: 4, 1: 4, 2: 4, length: 3}

#### 2、不改变原数组的方法

1. slice() 方法返回一个从开始到结束（不包括结束）选择的数组的一部分浅拷贝到一个新数组对象。且原始数组不会被修改。
参数：
    begin (可选)
    从该索引处开始提取原数组中的元素（从0开始）。
    如果该参数为负数，则表示从原数组中的倒数第几个元素开始提取，slice(-2)表示提取原数组中的倒数第二个元素到最后一个元素（包含最后一个元素）。
    如果省略 begin，则 slice 从索引 0 开始。
    end (可选)
    在该索引处结束提取原数组元素（从0开始）。
    slice会提取原数组中索引从 begin 到 end 的所有元素（包含begin，但不包含end）。
    slice(1,4) 提取原数组中的第二个元素开始直到第四个元素的所有元素 （索引为 1, 2, 3的元素）。
    如果该参数为负数， 则它表示在原数组中的倒数第几个元素结束抽取。 slice(-2,-1)表示抽取了原数组中的倒数第二个元素到最后一个元素（不包含最后一个元素，也就是只有倒数第二个元素）。
    如果 end 被省略，则slice 会一直提取到原数组末尾。
    如果 end 大于数组长度，slice 也会一直提取到原数组末尾。
    返回值： 一个含有提取元素的新数组
        let arr = [1,2,3,4,5];
        let arr1 = arr.slice(1,3); // arr是[1,2,3,4,5]， arr1是[2,3]
        let arr2 = arr.slice(-2,-1);  // arr是[1,2,3,4,5], arr2是[4]
        // 开始位置在结束位置后面，得到的数组是空
        let arr3 = arr.slice(-2, -3); // arr是[1,2,3,4,5], arr3是[]
        let arr4 = arr.slice(2, 1); // arr是[1,2,3,4,5], arr4是[]

    //如果元素是个对象引用 （不是实际的对象），slice 会拷贝这个对象引用到新的数组里。两个对象引用都引用了同一个对象。如果被引用的对象发生改变，则新的和原来的数组中的这个元素也会发生改变。
        let arr = [{name: 'xiaoming'}];
        let arr1 = arr.slice(); // arr是[{name: xiaoming}]，arr1是[{name: 'xiaoming'}]
        arr1[0].name = 'xiaogang'; // arr是[{name: 'xiaogang'}]，arr1是[{name: 'xiaogang'}]

    // 对于字符串、数字及布尔值来说（不是 String、Number 或者 Boolean 对象），slice 会拷贝这些值到新的数组里。在别的数组里修改这些字符串或数字或是布尔值，将不会影响另一个数组。
        let arr = [1,2,3];
        let arr1 = arr.slice(); // arr是[1,2,3]，arr1是[1,2,3]
        arr1[1] = "two"; // arr是[1,2,3]，arr1是[1,"tow",3]

    // 当然，如果向两个数组任一中添加了新元素（简单或者引用类型），则另一个不会受到影响。

2. join() 方法将数组（或一个类数组对象）中所有元素都转化为字符串并连接在一起，返回最后生成的字符串。
参数：
    separator （可选）
    指定一个字符串来分隔数组的每个元素。
    如果有(separator)，将分隔符转换为字符串。
    如果省略()，数组元素用逗号分隔。默认为 ","。
    如果separator是空字符串("")，则所有元素之间都没有任何字符。

    返回值： 一个所有数组元素连接的字符串。如果 arr.length 为0，则返回空字符串

    知识点
        // 扁平化简单的二维数组
        const arr = [11, [22, 33], [44, 55], 66];
        const flatArr = arr.join().split(','); // ["11", "22", "33", "44", "55", "66"]
3. toString() 方法将数组的每个元素转化为字符串(如有必要将调用元素的toString()方法)并且输出用逗号分割的字符串列表。返回一个字符串表示数组中的元素
4. toLocaleString() 数组中的元素将使用各自的 toLocaleString 方法转成字符串，这些字符串将使用一个特定语言环境的字符串（例如一个逗号 ","）隔开。
5. concat() 方法用于合并两个或多个数组。此方法不会更改现有数组，而是返回一个新数组。
6. isArray() 用于确定传递的值是否是一个 Array。

### 3、数组遍历、映射、过滤、检测、简化等方法
介绍方法之前，先对这些数组方法做一个概述：
- 首先，大多数方法的第一个参数接收一个函数，并且对数组的每个元素（或一些元素）调用一次该函数。如果是稀疏数组，对不存在的元素不调用该函数。大多数情况下，调用提供的函数使用三个参数：数组元素、元素的索引和数组本身。通常，只需要第一个参数值，可以忽略后两个参数。
- 大多数方法，第二个参数是可选的。如果有第二个参数，则调用的第一个函数参数被看做是第二个参数的方法，即当执行第一个函数参数时用作this的值(参考对象)。
- 方法的返回值很重要，不同的方法处理返回值的方式也不一样。

下面这些方法运行时的规则：

- 对于空数组是不会执行回调函数的
- 对于已在迭代过程中删除的元素，或者空元素会跳过回调函数
- 遍历次数在第一次循环前就会确定，再添加到数组中的元素不会被遍历。
- 如果已经存在的值被改变，则传递给 callback 的值是遍历到他们那一刻的值。
- 已删除的项不会被遍历到。如果已访问的元素在迭代时被删除了(例如使用 shift()) ，之后的元素将被跳过

1. forEach() 方法从头到尾遍历数组，为每个元素调用指定的函数。
2. map() 方法创建一个新数组，其结果是该数组中的每个元素都调用一个callback函数后返回的结果。
3. filter() 方法返回的数组元素是调用的数组的一个子集。传入的函数时用来逻辑判定的，该函数返回 true 或 false,如果返回值为true或能转化为true的值，那么传递给判断函数的元素就是这个子集的成员，它将被添加倒一个作为返回值的数组中。
4. every() 方法测试数组的所有元素是否都通过了指定函数的测试。当且仅当针对数组中的所有元素调用判定函数都返回true，它才返回true。
5. some() 方法测试数组中的某些元素是否通过由提供的函数实现的测试。当数组中至少有一个元素调用判定函数返回true，它就返回true，当且仅当数组中的所有元素调用判定函数都返回false，它才返回false。
6. reduce() 和 reduceRight() 这两个方法使用指定的函数将数组元素进行组合，生成单个值。这在函数式编程中是常见的操作，也可以称为“注入”和“折叠”。reduceRight() 和 reduce() 工作原理是一样的，不同的是reduceRight() 按照数组索引从高到低（从右到左）处理数组，而不是从低到高。
参数：
    callback 执行数组中每个值的函数，包含四个参数：

    accumulator 累加器累加回调的返回值; 它是上一次调用回调时返回的累积值，或initialValue（如下所示）。
    currentValue 数组中正在处理的元素。
    currentIndex (可选) 数组中正在处理的当前元素的索引。 如果提供了initialValue，则索引号为0，否则为索引为1。
    array (可选) 调用reduce的数组

    initialValue (可选) 用作第一个调用 callback的第一个参数的值。 如果没有提供初始值，则将使用数组中的第一个元素。 在没有初始值的空数组上调用 reduce 将报错。

    注意：

    reduce为数组中的每一个元素依次执行callback函数，不包括数组中被删除或从未被赋值的元素，回调函数第一次执行时，accumulator 和currentValue的取值有两种情况：调用reduce时提供initialValue，accumulator取值为initialValue，currentValue取数组中的第一个值；没有提供 initialValue，accumulator取数组中的第一个值，currentValue取数组中的第二个值。即：如果没有提供initialValue，reduce 会从索引1的地方开始执行 callback 方法，跳过第一个索引。如果提供initialValue，从索引0开始。

    如果数组为空且没有提供initialValue，会抛出TypeError 。如果数组仅有一个元素（无论位置如何）并且没有提供initialValue， 或者有提供initialValue但是数组为空，那么此唯一值将被返回并且callback不会被执行。

7. indexof() 方法返回在数组中可以找到一个给定元素的第一个索引，如果不存在，则返回-1。
8. lastIndexOf() 跟indexOf()查找方向相反，方法返回指定元素在数组中的最后一个的索引，如果不存在则返回 -1。从数组的后面向前查找，从 fromIndex 处开始
9. includes() 方法用来判断一个数组是否包含一个指定的值，根据情况，如果包含则返回 true，否则返回false。 ES7新增
10. find() 和 findIndex() find 方法返回数组中满足提供的测试函数的第一个元素的值。否则返回 undefined。findIndex 方法返回数组中满足提供的测试函数的第一个元素的索引。否则返回-1。(ES6新增)
11. keys() 方法返回一个新的Array迭代器，它包含数组中每个索引的键。 (ES6新增)
12. values() 方法返回一个新的Array迭代器，它包含数组中每个索引的值。 (ES6新增)
13. @@iterator 属性和 values() 属性的初始值均为同一个函数对象。数组的 iterator 方法，默认情况下与 values() 返回值相同,调用语法是 arr[Symbol.iterator]()  (ES6新增)
14. entries() 方法返回一个新的Array迭代器，该对象包含数组中每个索引的键/值对。 (ES6新增)
11-14参数： 都是无。
11-14都是一个新的 Array 迭代器对象。

### 扩展几个概念
**1、数组的索引和对象key有什么关系？**

数组是对象的特殊形式，使用方括号访问数组元素和使用方括号访问对象属性一样。JavaScript将指定的数字索引值转换成字符串——索引1变成"1"——然后将其作为属性名来使用。数组的特别之处在于，当使用小于2^32的非负整数作为属性名时数组会自动维护其length属性。

    // 索引到属性名的转化
        let arr = [1,2,3];
        console.log(arr[1]) // 2
        console.log(arr["1"]) // 2

所有的数组都是对象，可以为其创建任意名字的属性，不过，只有在小于2^32的非负整数才是索引，数组才会根据需要更新length。事实上数组的索引仅仅是对象属性名的一种特殊类型，这意味着JavaScript数组没有“越界”错误的概念。当查询任何对象中不存在的属性时，不会报错，只会得到undefined

    let arr = [];
        arr["a"] = 1;
        console.log(arr,arr.length) // arr是[a:1] length是0

对于使用负数或非整数的情况，数值会转换为字符串，字符串作为属性名来用，当时只能当做常规的对象属性，而非数组的索引。

    let arr = [];
        arr[-1.23] = 0;
        console.log(arr,arr.length) // arr是[-1.23: 0] length是0

使用非负整数的字符串或者一个跟整数相等的浮点数时，它就当做数组的索引而非对象属性。

    let arr = [];
        arr["100"] = 'a';
        console.log(arr,arr.length) // arr 是[empty × 100, "a"]，length 是101

        let arr1 = [];
        arr1[1.0000] = 'b';
        console.log(arr1,arr1.length) // arr 是[empty, "b"]，length 是2

**2、稀疏数组**

稀疏数组就是包含从0开始的不连续索引的数组。通常数组的length属性值代表数组中元素的个数。如果数组是稀疏的，length属性值大于元素的个数

足够稀疏的数组通常在实现上比稠密的数组更慢，更耗内存，在这样的数组中查找元素所用的时间就变得跟常规对象的查找时间一样长了，失去了性能的优势。

**3、类数组对象**

拥有一个数值length属性和对应非负整数属性的对象看做一种类型的数组

数组跟类数组相比有以下不同：

    当有新元素添加到数组中时，自动更新length属性
        设置length为一个较小值将截断数组
        从Array.prototype中继承了一些方法
        其类属性为'Array'

JavaScript 数组有很多方法特意定义通用，因此他们不仅应用在真正的数组而且在类数组对象上都能正确工作，JavaScript权威指南一书说的是：ES5中所有的方法都是通用的，ES3中除了toString()和toLocaleString()意外所有方法也是通用的。
类数组对象显然没有继承自Array.prototype，所以它们不能直接调用数组方法，不过可以间接地使用Function.call方法调用。

**4、 JavaScript数组的进化——类型化数组的引入**

先说一下普遍意义上的Array,数组是一串 连续 的内存位置，用来保存某些值。JavaScript 中的数组是哈希映射，可以使用不同的数据结构来实现，如链表,上一个元素包含下一个元素的引用。这样其他语言中数组的取值是根据内存位置进行数学计算就能找到，而在JavaScript中就需要遍历链表之类的结构，数组越长，遍历链表跟数据计算相比就越慢。

现代 JavaScript 引擎是会给数组分配连续内存的 —— 如果数组是同质的（所有元素类型相同）。所以在写代码时保证数组同质，以便 JIT（即时编译器）能够使用 c 编译器式的计算方法读取元素是一种优雅的方式。
不过，一旦你想要在某个同质数组中插入一个其他类型的元素，JIT 将解构整个数组，并按照旧有的方式重新创建。

ES6 增加了 ArrayBuffer， 提供一块连续内存供我们随意操作。然而，直接操作内存还是太复杂、偏底层。于是便有了处理 ArrayBuffer 的视图（View）。

ArrayBuffer 对象用来表示通用的、固定长度的原始二进制数据缓冲区。ArrayBuffer 不能直接操作，而是要通过类型数组对象或 DataView 对象来操作，它们会将缓冲区中的数据表示为特定的格式，并通过这些格式来读写缓冲区的内容。
语法: new ArrayBuffer(length)

参数
length:要创建的 ArrayBuffer 的大小，单位为字节。

返回值:一个指定大小的 ArrayBuffer 对象，其内容被初始化为 0。

异常:如果 length 大于 Number.MAX_SAFE_INTEGER（>= 2 ** 53）或为负数，则抛出一个  RangeError  异常。
复制代码类型数组对象 一个TypedArray 对象描述一个底层的二进制数据缓存区的一个类似数组(array-like)视图。事实上，没有名为 TypedArray的全局对象，也没有一个名为的 TypedArray构造函数。相反，有许多不同的全局对象，下面会列出这些针对特定元素类型的类型化数组的构造函数。

    new TypedArray(); // ES2017中新增
        new TypedArray(length);
        new TypedArray(typedArray);
        new TypedArray(object);
        new TypedArray(buffer [, byteOffset [, length]]);

TypedArray()指的是以下的其中之一：

    Int8Array();//8位二进制带符号整数 -2^7~(2^7) - 1,大小1个字节
        Uint8Array();//8位无符号整数 0~(2^8) - 1,大小1个字节
        Int16Array();//16位二进制带符号整数 -2^15~(2^15)-1,大小2个字节
        Uint16Array();//16位无符号整数 0~(2^16) - 1,大小2个字节
        Int32Array();// 32位二进制带符号整数 -2^31~(2^31)-1,大小4个字节
        Uint32Array();//32位无符号整数 0~(2^32) - 1,大小4个字节
        Float32Array();//32位IEEE浮点数,大小4个字节
        Float64Array(); //64位IEEE浮点数,大小8个字节
应用：

    var buffer = new ArrayBuffer(8);
        var view   = new Int32Array(buffer);
        view[0] = 100;
        console.log(view)// [100,0],一个八个字节，Int32Array一个元素大小是
        4个字节，所以只能放下两个元素


参考和链接

《JavaScript权威指南》数组部分
详解JS遍历
一次掌握 JavaScript ES5 到 ES8 数组内容
【干货】js 数组详细操作方法及解析合集
【译】 深入 JavaScript 数组：进化与性能

原文链接：https://juejin.im/post/5b684ef9e51d451964629ba1