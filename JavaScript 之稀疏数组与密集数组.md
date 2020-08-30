# JavaScript 之稀疏数组与密集数组

### 背景 
在 code review 的时候，发现别人写了一段这样的代码：
```js
const list = new Array(5)
list.map(() => {
  ....
})  
```  
由于之前很少用这种方式去创建数组，而且印象中看过相关文章，说用 ```new Array()``` 来创建数组有坑。出于好奇，我将上面代码在控制台中运行，“出乎意料”的是 map 中的 callback 并没有执行，**为什么呢？**   
为了满足好奇心，以及避免在之后的代码中继续踩坑，决定探究一番～ 

### ```new Array(len)``` 做了什么  
Array 构造函数支持不传或传 1-n 个参数，我们这里仅讨论传一个类型为正整数参数时的情况。在探究 ```new Array(len)``` 做什么之前，我们先看看 ```new Array(len)``` 创建的数组是怎样的：  
```js  
let arr = new Array(5)
console.log(arr)
// 输出：[empty × 5]  
console.log(arr.map)
// 输出：ƒ map() { [native code] }
console.log(arr.length)
// 输出：5
```    
从上面的代码中可以发现，使用 ```new Array(len)``` 创建的数组中，Array 相应的方法、属性都存在，但数组中的元素是 「empty」。  
在 [ECMAScript](https://tc39.es/ecma262/#sec-array-len) 标准中介绍了 ```new Array(len)``` 做了什么，精简一下主要干了这几件事：  
1. 判断 len 是否为合法数字（小于 2^32 - 1 的正整数），如果不是则抛出错误；  
2. 创建一个 JavaScript Array 实例；  
3. 将这个实例对象的 length 属性设置为入参的值；  

上面的操作相当于:
 ```js
 let arr = [];
 arr.length = 5;
 ```
此时将返回一个 length 的值等于 len 的数组对象，但**该数组此时并没有包含任何实际的元素，而且不能理所当然地认为它包含 len 个值为 undefined 的元素**。

### ```empty``` vs ```undefined```  
上面的例子中可以看到，使用 ```new Array(len)``` 创建的数组，输出的是 len 个 empty 数组，在 JS 基础数据类型中并没有 empty，所以它到底是什么呢？我们尝试将它输出看看：
```js
console.log(new Array(5)[2])
// 输出：undefined
```
在访问「empty」元素时，输出了 undefined ，难道 empty 就是 undefined ? 答案是否定的，我们看下面这个例子：
```js
var arr = new Array(2);
var undefinedArr = [undefined, undefined];
console.log(undefinedArr);    // [undefined, undefined]
undefinedArr[0];              // undefined

arr.forEach(item => { console.log(item) });    // 无输出
undefinedArr.forEach(item => { console.log(item) });    // 输出两个 undefined  
console.log(arr.hasOwnProperty(0)) // false
console.log(undefinedArr.hasOwnProperty(0)) // true
```  
arr 和 undefinedArr **只有在访问单个具体元素时表现一致，在其他一些情况下大都不一样。**  
至于为什么 arr 在访问具体单个元素时返回 undefined，可以从 JS 引擎解析的角度去看。在 v8 源码上对于 empty 的描述是一个**空的对象引用**，但 JS 基本类型中并无这种类型，故用 undefined 来代替。这种表现就像我们在控制台访问一个不存在变量时，通常得到的就是 undefined。  
arr 由于没有任何实际的元素，所以 forEach 的 callback 并不会执行，而 undefinedArr 我们是有填充值的。

### 稀疏数组与密集数组  
其实 arr 和 undefinedArr 细分的话，应该是两类数组，arr 这类的称为稀疏数组 ，undefinedArr 称为密集数组。我们来介绍下这两类数组的定义：  
> **稀疏数组：**  索引不连续，数组长度大于元素个数的数组, 可以简单理解为有 ```empty``` 的数组；  
**密集数组：** 索引连续, 数组长度等于元素个数的数组；  

#### 稀疏数组、密集数组如何相互转换  
稀疏数组 => 密集数组：  
```js  
var arr = new Array(5); // 稀疏数组  
// ES5  
Array.apply(null, arr);  
// ES6  
Array.form(arr);  
[...arr];
```  
密集数组 => 稀疏数组：  
```js  
var arr = [1,3,4,5,6]; // 密集数组   
arr.length = 10 // [1, 3, 4, 5, 6, empty × 5]
```  
#### 稀疏数组特性  
+ **稀疏数组在大多数遍历数组的方法中，遇到「empty」元素的时候，callback 函数是不会执行的，如：map, forEach, filter 等, 而且这种现象在 for...in 语句中，同样适用。**  
  ```js
  const arr = [3,,4,,5] // 稀疏数组 
  arr.forEach(item => { console.log(item)}) // 输出：3，4，5  

  console.log(arr.map(item => {
    console.log(item)
    return item+1
  })) // 输出：3，4，5，[4, empty, 5, empty, 6]
  // 值得注意的是：稀疏数组中 「empty」元素在 map 后返回的数组中仍然为 「empty」  

  console.log(arr1.filter(item => item === undefined)) // 输出：[]  
  console.log(arr1.filter(item => item > 3 )) // 输出：[4,5] 

  for (var i in a) { console.log(a[i]) } // 输出：3，4，5
  for (var i of a) { console.log(i) } // 输出：3，undefined，4，undefined，5
  ```   

+ **稀疏数组在访问元素的速度上比密集数组慢。**  
  ```js
  const arr = new Array(200000)
  arr[19999] = 88
  console.time('using[]')
  arr[19999]
  console.timeEnd('using[]')
  // using[]: 0.031982421875ms

  const ddd = [...new Array(200000)]
  ddd[19999] = 88
  console.time('using[]')
  ddd[19999]
  console.timeEnd('using[]')
  // using[]: 0.010009765625ms  
  ```  
  具体原因是，对于稀疏数组 V8 引擎访问对象是使用 **散列表模式**的，该种模式在访问时需要计算一遍哈希值，所以会比较慢，但散列表对于空间利用来说，效率更高。而密集数组，它是申请一段连续的内存空间，访问时可以直接通过「索引」来访问，所以速度比较快；  

+ **稀疏数组在一些数组方法中与密集数组存在差异**
  ```js
  var a = [1,,,,]
  var b = new Array(5)
  var c = []  
  ```
  ```js
  // Array.prototype.every() 和 Array.prototype.some() 

  b.every(i => i === undefined); // true  
  a.some(i => i === undefined); // false
  ```
  前面说到遍历数组的方法会跳过「empty」元素。
  所以在排除后「empty」元素后，数组内找不到 undefined 元素， some 会返回 false。空数组使用 every 时，返回 true。

  ==================================
  ```js
  // Array.prototype.find() 和 Array.prototype.findIndex()
  a.findIndex(i => i === undefined) // 1
  a.find(i => i === undefined) //undefined
  ```  
  由于 ```find``` 和 ```findIndex``` 是使用 for 循环来实现的，与 ```forEach``` 有所不同,所以这两种方法能遍历到「empty」元素。  

  ===================================  
  ```js
  // Array.prototype.includes() 
  a.includes() // true
  b.includes() // true
  c.includes() // false
  a.includes(undefined) // true
  b.includes(undefined) // true  
  ```  
  includes() 方法表现较为特殊，大体可以总结为：  
  1. 当数组长度为 0 时，```include``` 一定返回 false；  
  2. 当数组为稀疏数组且长度不为 0 ，且入参为空或 undefined 时，```include``` 一定返回 true；  

  ==================================
  ```js
  // Array.prototype.sort()
  var sortArr = [5,,9,,1]
  sortArr.sort()
  console.log(sortArr) // 输出：[1, 5, 9, empty × 2]
  ```  
  sort 方法能够正常排序，且 **sort 方法不会遍历「empty」元素**，但 sort 后数组的长度并不会变化，这一点与 ```map``` 的表现一致，```map``` 得到的数组长度也不变。  
    
  ===================================  
  ```js
  // Array.prototype.join()  
  [undefined,undefined,1].join() // 输出：",,1"
  [5,,9,,1].join() // 输出："5,,9,,1"
  ```  
  「empty」元素仍然会被保留。  
  
```Array.prototype``` 上的其他方法，稀疏数组和密集数组表现基本一致。  
  
### 总结  
通过 ```new Array(len)``` 的方式创建的数组属于稀疏数组，稀疏数组在一些数组方法中，特别是遍历数组的方法，往往与我们预期的不太一样，如果对其不了解，容易导致问题，而且稀疏数组在创建和访问元素方面性能表现并不好，所以在平时代码中应该尽量避免使用稀疏数组。遇到了也不用太担心，只要我们了解它，就不可怕～
