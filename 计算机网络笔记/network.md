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
	> cookie 它很小，最大限制为4kb左右（每个域名下的cookie 的大小最大为4KB，每个域名下的cookie数量最多为20个（但很多浏览器厂商在具体实现时支持大于20个）。），它的主要用途有保存登录信息，比如你登录某个网站市场可以看到“记住密码”，这通常就是通过在 Cookie 中存入一段辨别用户身份的数据来实现的。
	```
		document.cookie = "test1=myCookie1;"
		document.cookie = "test2=myCookie2; domain=.google.com.hk; path=/webhp"
		document.cookie = "test3=myCookie3; domain=.google.com.hk; expires=Sat, 04 Nov 2017 16:00:00 GMT; secure"
		document.cookie = "test4=myCookie4; domain=.google.com.hk; max-age=10800;"
	```
	> sessionStorage 严格用于一个浏览器会话中存储数据，因为数据在浏览器关闭后会立即删除
	> localStorage 则用于跨会话持久化地存储数据。
	

5. TCP三次握手和四次挥手：
	> 三次握手发生在建立tcp连接的时候：a.客户端发送连接请求报文（SYN）b. 服务端接受连接后回复ACK报文，并为这次连接分配资源 c. 客户端接收到ACK报文后也向Server段发生ACK报文，并分配资源，这样TCP连接就建立了；
	> 四次挥手发生在断开tcp连接的时候：假设Client端发起中断连接请求，也就是发送FIN报文。Server端接到FIN报文后，意思是说"我Client端没有数据要发给你了"，但是如果你还有数据没有发送完成，则不必急着关闭Socket，可以继续发送数据。所以你先发送ACK，"告诉Client端，你的请求我收到了，但是我还没准备好，请继续你等我的消息"。这个时候Client端就进入FIN_WAIT状态，继续等待Server端的FIN报文。当Server端确定数据已发送完成，则向Client端发送FIN报文，"告诉Client端，好了，我这边数据发完了，准备好关闭连接了"。Client端收到FIN报文后，"就知道可以关闭连接了，但是他还是不相信网络，怕Server端不知道要关闭，所以发送ACK后进入TIME_WAIT状态，如果Server端没有收到ACK则可以重传。“，Server端收到ACK后，"就知道可以断开连接了"。Client端等待了2MSL后依然没有收到回复，则证明Server端已正常关闭，那好，我Client端也可以关闭连接了。Ok，TCP连接就这样关闭了！
6. GET和POST的区别：
	> 1.对参数的数据类型，GET只接受ASCII字符，而POST没有限制，允许二进制。
	  2.GET请求数据有大小限制，最大是2048个字符，POST参数数据是没有限制的。
	  3.GET在浏览器回退/刷新时是无害的，而POST会再次提交请求。
	  4.POST 比 GET 更安全，因为GET参数直接暴露在URL上，POST参数在HTTP消息主体中，而且不会被保存在浏览器历史或 web 服务器日志中。
	  5.GET请求会被浏览器自动缓存，而post不会；
	  6.GET请求的参数会保留在历史记录中，而post不会；
	  7.GET请求可被收藏为书签，POST不能。
7. 需要了解的状态码：
   > 304 相当于可以从缓存中读取请求的资源。自从上次请求后，请求的网页未修改过。服务器返回此响应时，不会返回网页内容。如果网页自请求者上次请求后再也没有更改过，您应将服务器配置为返回此响应（称为 If-Modified-Since HTTP 标头）。服务器可以告诉 Googlebot 自从上次抓取后网页没有变更，进而节省带宽和开销。  
   > 301 请求的网页已永久移动到新位置.  
   > 302 (临时移动)服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来响应以后的请求。  
   > 503 服务器目前无法使用（由于超载或停机维护）。通常，这只是暂时状态。
   > 403 可以简单的理解为没有权限访问此站，服务器收到请求但拒绝提供服务。   
   > 100(继续) 请求者应当继续提出请求。服务器返回此代码则意味着，服务器已收到了请求的第一部分，现正在等待接收其余部分。  
   > 101(切换协议) 请求者已要求服务器切换协议，服务器已确认并准备进行切换。
8. TCP和IP的区别：TCP负责数据的传输，而IP负责逻辑地址。换句话说，IP包含了地址，而TCP保证了数据传输到该地址。  
9. 网络分层：（1）应用层（2）传输层（3）网络层（4）数据链路层（5）物理层  
	     OSI类型的分层：（1）物理层（功能：传输信息的介质规格、将数据以实体呈现并传输的规格、接头规格）  
	     （2）数据链路层（功能：同步、查错、制定MAC方法）  
	     （3）网络层（功能：定址、选择传送路径、路由选择）  
	     （4）传输层（功能：编定序号、控制数据流量、查错与错误处理，确保数据可靠、顺序、无错地从A点到传输到B 点）  
	     （5）会话层（功能：负责在网络中的两节点之间建立和维持通信。）  
	     （6）表示层（功能：内码转换、压缩与解压缩、加密与解密,充当应用程序和网络之间的“翻译官”角色。）  
	     （7）应用层（功能：指网络操作系统和具体的应用程序，对应WWW服务器、FTP服务器等应用软件　）  
10. 几种应用层协议：SMTP：简单邮件传输协议  
		   FTP：文件传输协议  
		   SNMP：简单网络管理协议  
		   TELNET：远程登录协议
11. web端缓存：[web缓存](http://www.alloyteam.com/2016/03/discussion-on-web-caching/)  
		[强缓存&&协商缓存](https://segmentfault.com/a/1190000008956069)  
		[细节分析版](https://mp.weixin.qq.com/s/qOMO0LIdA47j3RjhbCWUEQ?utm_source=caibaojian.com)	
