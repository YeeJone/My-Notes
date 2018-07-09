# 计算机网络笔记  

1. 从输入url到页面加载结束所经历的过程：DNS域名解析 -> TCP连接（三次握手） -> 发送http请求 -> http响应 ->  （浏览器跟踪重定向地址） -> 服务器处理请求 -> 返回html资源(视情况决定释放TCP连接) -> 客户端解析HTML -> 获取嵌入在HTML中的对象重新发起http请求；

2. 什么是**同源策略**：同源是指*指协议相同*，*域名相同*，*端口相同*；
   非同源策略有什么限制:+ cookie,localStorage,indexDB无法获取；
						+ DOM无法获取；
						+ ajax请求无法发送；
						
   跨域的方式：
		a.*jsonp方式*：
	> JSONP实现跨域请求的原理简单的说，就是动态创建<script>标签，然后利用<script>的src 不受同源策略约束来跨域获取数据。
	```<!DOCTYPE html>
		<html lang="en">
		<head>
			<meta charset="UTF-8">
			<title>JSONP实现跨域2</title>
		</head>
		<body>
			<div id="mydiv">
				<button id="btn">点击</button>
			</div>
		</body>
		<script type="text/javascript">
			function handleResponse(response){
					console.log(response);
			}
		</script>
		<script type="text/javascript">
			window.onload = function() {

			var oBtn = document.getElementById('btn');

			oBtn.onclick = function() {     

				var script = document.createElement("script");
				script.src = "https://api.douban.com/v2/book/search?q=javascript&count=1&callback=handleResponse";
				document.body.insertBefore(script, document.body.firstChild);   
			};
		};
		</script>
		</html>
	```
	
	b.*图像ping的方式*
	> 这种方式是使用<img>标签，因为网页中加载图片不需要考虑跨步跨域的问题,使用Image对象的onload和onerror事件处理程序来确定是否接受到了响应，这个技术是在线广告跟踪浏览量的主要方式。限制：只能发送GET请求；无法访问服务器返回的响应文本；
	```var img = new Image();
	img.onload = img.onerror = function (){
		alert("Done!);
	}
	img.src = "http://www.example.com/test?name=yeejone";
	```
	
	c.*CORS(跨域资源共享)*
	> 实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。
	
	d.*webSocket*
	vue,react跨域：
	> 在vue项目和react项目中的config文件中，都有一个proxy代理设置，这个就是用来在开发环境下进行跨域的。对其进行设置就能实现跨域。
	```
		module.exports = {
		  dev: {
			env: {
			  NODE_ENV: '"development"'
			},
			//proxy

		   // 只能在开发环境中进行跨域，上线了要进行反向代理nginx设置
			 proxyTable: {
			   //这里理解成用‘/api’代替target里面的地址，后面组件中我们掉接口时直接用api代替 比如我要调用'http://40.00.100.100:3002/user/add'，直接写‘/api/user/add’即可
			  '/api': {
				 target: 'http://news.baidu.com',//你要跨域的网址  比如  'http://news.baidu.com',
				secure: true,  // 如果是https接口，需要配置这个参数
				changeOrigin: true,//这个参数是用来回避跨站问题的，配置完之后发请求时会自动修改http header里面的host，但是不会修改别的
				pathRewrite: {
				  '^/api': '/api'//路径的替换规则
				  //这里的配置是正则表达式，以/api开头的将会被用用‘/api’替换掉，假如后台文档的接口是 /api/list/xxx
				  //前端api接口写：axios.get('/api/list/xxx') ， 被处理之后实际访问的是：http://news.baidu.com/api/list/xxx
				}
			  }
			}
	```
   
3. http与https的区别：
> HTTP报文是包裹在TCP报文中发送的，服务器端收到TCP报文时会解包提取出HTTP报文。但是这个过程中存在一定的风险，HTTP报文是明文，如果中间被截取的话会存在一些信息泄露的风险。那么在进入TCP报文之前对HTTP做一次加密就可以解决这个问题了。HTTPS协议的本质就是HTTP + SSL(or TLS)。在HTTP报文进入TCP报文之前，先使用SSL对HTTP报文进行加密。从网络的层级结构看它位于HTTP协议与TCP协议之间。

4. cookie sessionStorage localStorage的区别：

5. TCP三次握手和四次挥手：

6. GET和POST的区别：

7. 需要了解的状态码：
   > 202
   > 204
   > 206
   > 301
   > 302
   > 303
   > 304
   > 307
   > 400
   > 401
   > 403
   > 404
   > 500
   > 503
