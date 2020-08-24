# instanceof && typeof
## typeof
typeof的值：number,string,undefined,object,symbol,boolean,function  
typeof所描述的是基本数据类型的值；  
一些🌰：  
 >typeof 3.14 === 'number';  
 **typeof Math.LN2 === 'number';**  
 typeof Infinity === 'number';  
 typeof NaN === 'number'; // 尽管NaN是"Not-A-Number"的缩写  
 **typeof Number(1) === 'number'; // 但不要使用这种形式!**  
 typeof String("abc") === 'string'; // 但不要使用这种形式!  
 typeof Boolean(true) === 'boolean'; // 但不要使用这种形式!  
 typeof Symbol('foo') === 'symbol';  
 **typeof Symbol.iterator === 'symbol';**  
 typeof new Boolean(true) === 'object';  
 typeof new Number(1) === 'object';  
 **typeof new String("abc") === 'object';**  
 **typeof Math.sin === 'function';**
 **typeof new Function() === 'function';**  
 **typeof null === 'object'; // 从一开始出现JavaScript就是这样的**  
 typeof /s/ === 'function'; // Chrome 1-12 , 不符合 ECMAScript 5.1  
 **typeof /s/ === 'object'; // Firefox 5+ , 符合 ECMAScript 5.1**
  
## instanceof  
instanceof是用来判断对应对象其所属的**引用类型** (object instanceof constructor)是指对象A的原型链上是否有B构造函数所指向的原型(B.prototype)  
举个🌰 ：  
 ```javascript  
 [1,2,3,4] instanceof Array  //true;
 new String("Hello") instanceof String //true;  
 new String("Hello") instanceof Object //true;  
 new String() instanceof String //true;
 "hello world!" instanceof String //false  
 ```  
 上面的🌰前面的几个按照定义来理解很容易得出答案，最后一个按着定义来理解也可以得出false，因为“hello world”根本就不是一个引用类型。 
### Curiosity--延伸  
 ```js  
 "hello world".__proto__ //String {"", length: 0, constructor: ƒ, anchor: ƒ, big: ƒ, blink: ƒ, …};  
 (new String("hello world")).__proto__ //String {"", length: 0, constructor: ƒ, anchor: ƒ, big: ƒ, blink: ƒ, …};  
 "hello world".__proto__.constructor //ƒ String() { [native code] };  
 (new String("hello world")).__proto__.constructor // ƒ String() { [native code] };    

 // 另一些奇怪现象；  
 console.log(Object instanceof Object);//true 
 console.log(Function instanceof Function);//true 
 console.log(Number instanceof Number);//false 
 console.log(String instanceof String);//false
 ```  
 可以发现两种字符串的隐式原型和构造函数是一样的，那为什么instanceof的表现会不一样？  
 立马翻了翻红宝石书，原来**因为当我们尝试访问一个primitive值(基本数据类型，字面量)的属性时，JS引擎内部会调用一个内置[[toObject]] 方法，将字面量的"Hello World"转为一个[[PrimitiveValue]] (primitive类型：Undefined, Null, Boolean, String, Number)为”foo”的String对象，然后从其原型链中尝试查找需要访问的属性，使用结束后再释放掉这个String对象。**  
 ```js  
 "Hello world".__proto__//相当于toObject("Hello world").__proto__
 ```  
 这也是为什么我们使用字面量的字符串("Hello world")的时候也可以使用String对象的一些方法。再想一想为什么"Hello world" == new String("Hello world"),因为'=='运算符也会进行类型转换，所以等式成立。  
    
=================================================  
上面的“奇怪现象”，需要一张图：  
![原型链](./imgs/yuanxinglian.png "原型链")  
   
 其实在控制台看Object,Function,Number,String...打印出来的都是一个function，所以他们自然属于Function对象，根据原型链图及instanceof定义可以得出上面的“奇怪现象”的几个语句是成立的；因为Function对象的原型链上并没有String，Number...等构造函数指向的原型，但Function和Object有。  
 ```js    
 Number instanceof Function //true
 ```
 完结！

