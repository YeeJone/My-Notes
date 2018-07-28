# js-Base  
## 数组
1. Array.isArray(obj)判断传入的对象是否为数组。
	```js
		// 下面的函数调用都返回 true
		Array.isArray([]);
		Array.isArray([1]);
		Array.isArray(new Array());
		// 鲜为人知的事实：其实 Array.prototype 也是一个数组。
		Array.isArray(Array.prototype);
	```
	若Array.isArray()不存在，实现方法：
	```js
	Array.isArray = (arg) => Object.prototype.toString.call(arg) === '[object Array]'
	```
2. 由new Array(3)或Array(3)创建的数组，创建一个长度为3的空数组（注意：这是指一个有3个空位的数组，而不是由3个undefined组成的数组）
	```js
	console.log(Array(3) == [undefined,undefined,undefined])
	//输出false
	```
3. Array.slice(begin,end)从 begin 到 end 的所有元素（包含begin，但不包含end），返回值还是数组。begin默认是从0开始，若无end值就从begin位置开始一直取到结束，若begin，end值为负数值得是倒数第几个数；若绝对值end小于等于begin则返回空数组；  
4. ```Array.toString()```和```Array.toLocaleString()```是有区别的，后者是返回一个字符串表示数组中的元素。数组中的元素将使用各自的 toLocaleString 方法转成字符串，这些字符串将使用一个特定语言环境的字符串（例如一个逗号 ","）隔开。
	```
	[1, 'a', new Date('21 Dec 1997 14:12:00 UTC')].toLocaleString()
	//"1,a,1997/12/21 下午10:12:00"
	[1, 'a', new Date('21 Dec 1997 14:12:00 UTC')].toString()
	//"1,a,Sun Dec 21 1997 22:12:00 GMT+0800 (中国标准时间)"
	```
## 函数
1. 函数的上下文是指：