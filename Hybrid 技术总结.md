# Hybrid 技术总结

## 前言  
随着移动设备的普及，移动端应用在近几年「爆炸」式增长。目前大多数移动端应用，都会采用 WEB 技术与 Native 相结合的方式来形成整个应用的架构，这种组合式架构方案我们称之为「**Hybrid 技术**」。一个好的 Hybrid 架构方案，不仅能够让应用拥有极佳的使用体验，同时也能利用 WEB 技术灵活开发的特点，进行快速迭代。  
从实习开始到现在，写 "移动端" 已经一年多了，但一直没有很好的探究过我司的 Hybrid 技术方案，所以这篇文章一方面是介绍一些通用的 Hybrid 方案及其原理，另一方面会对我司现有的 Hybrid 方案进行一个总结。  

## 主流 Hybrid 方案  
所谓的 Hybrid 方案，即需要 Native 与 WEB 技术进行混合开发，不同的 Hybrid 方案其本质都一样：**用 WEB 技术来开发迭代较快的 UI 页面，而 Native 用来为 WEB 提供一些原生能力**。不同方案间主要的差别是：Native 与 WEB 的通信方式及 UI 渲染方式，目前主要有三种主流的 Hybrid 方案：  
  1. **WebView 方案**，这种方案很容易理解，也是最为常见的 Hybrid 方案，它的实现方式就是 Native 提供一个类似浏览器的 WebView 窗体，里面运行着前端页面，Native、WEB 之间通过 JSBridge 进行双向通信，它是 Hybrid 方案中最为基础的一种方案；
  2. **Native 渲染方案**，像 React-Native、Weex 等，它与「WebView 方案」的主要区别是：最终的 UI 界面是由原生渲染的。以 React-Native 为例，我们用一些 WEB 方式（JSX）去写 UI ，这些 js 经过 RN 解析后，被解析成一个虚拟节点树 JSON，然后将这个 JSON 通过 JSBridge 传递给 Native 进行渲染；
  3. **小程序方案**，这是一个近几年兴起的 Hybrid 方案，可以看作是更为精细的「WebView 方案」，它利用双线层将页面的逻辑层与视图层进行了隔离，视图层只负责渲染，逻辑层负责拉取、维护数据状态，最终通过渲染层将整个页面进行渲染。值得注意的是该方案中，最后渲染的内容，对于一些公用组件，是直接采用 Native 嵌入渲染的，即 WebView + Native 共同组合渲染出最后的页面；   
  <img src='./imgs/Hybrid 技术总结[0].png' />  
  [[图片来源](https://www.infoq.cn/article/X1uPp2GV7eTAlqpt_54S)]  
    
从上面三个方案中可以发现，虽然各种方案 'Hybrid' 的方式不同，但它们都需要面对同一个问题，即：**都需要解决 Native 与 WEB 通信问题**。  
在上面三种方案中，都是通过 JSBridge 来实现 Native 与 WEB 双向通信的，因此 JSBridge 是 Hybrid 技术的核心。那么 JSBridge 到底是什么呢？  
对于我们前端来说比较常见的，例如微信的 [JS-SDK](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/JS-SDK.html)，它提供了我们一个 `wx` 对象，这就是一个 JSBridge，我们利用它可以与 Native 进行通信，调用一些微信的原生能力，像分享、支付等。  
```js
wx.ready(function () {   //需在用户可能点击分享按钮前就先调用
  wx.updateAppMessageShareData({ 
    title: '', // 分享标题
    desc: '', // 分享描述
    link: '', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
    imgUrl: '', // 分享图标
    success: function () {
      // 设置成功
    }
  })
}); 
```  
## Hybrid 技术原理--JSBridge 

## 公司 Hybrid 总结





