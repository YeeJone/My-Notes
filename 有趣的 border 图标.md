# 有趣的border图标  
> 写于 2018.04, 秋招经历  

前段时间做了 360 的校招笔试，被一道 UI 题卡住了。题目要求用纯 html 和 css 绘制如下的图案，当时没有很好的想法，最后用 canvas 做了一个，然后就挂了...   

<img src='./imgs/有趣的 border 图标[0].jpg' />    

360 给出的标准答案是用 border 结合 css3 旋转来构成上面的图案（相关代码：[warning 图案代码](https://github.com/YeeJone/Fill-the-pit-do-again-/blob/master/warning.html)）   

border 属性我们在平时的项目中经常会用到，但大多是作为一个 div 边框的形式来呈现，很少用它来绘制相关图案，所以这里我们主要讲一些 border 有趣的用法，然后再看看它的「构图原理」是怎样的。首先我们使用 border 来绘制**三角形**  
<img src='./imgs/有趣的 border 图标[1].jpg' />   
实现代码：  
```css  
/* css */
#container {
  width: 0;
  height: 0;
  border-top: 100px solid rgb(66,133,244);
  border-left: 100px solid transparent;
  border-right: 100px solid transparent;
  border-bottom: 100px solid transparent;
}
/* html
<div id="container"></div> */
```  
可以看到，要想实现上面这个三角形，就是通过设置四边的 border-width 值，并给其中一个方向的边框设定一个颜色，其他边框颜色设为透明即可。看到这里，你可能会想为什么它会构成三角形呢？我们通过一些  🌰  看看浏览器到底是如何解析 border 的：   
```css
/* css */
#container {
  width: 100px;
  height: 100px;
  border-top: 50px solid rgb(66,133,244);
  border-left: 50px solid rgb(255,206,68);
  border-right: 50px solid rgb(255,206,68);
  border-bottom: 50px solid rgb(66,133,244);
}
/* html
<div id="container"></div> */
```  
上面这段代码，浏览器渲染的结果是这样的：   
 
<img src='./imgs/有趣的 border 图标[2].jpg' />   

可以发现交叉区域的方形中浏览器是以对角线平分的方式呈现的，那么我们很容易想到，当 div 的宽高为 0px 时它将会变成一个由四个三角形组成的正方形。这样我们就可以很清楚的理解 border 是如何构成的三角形了。   

在了解这些之后，我们就可以构造一些有趣的图案了！再讲图案之前，我先把 360 那道 UI 题思路讲讲，它主要是通过拼接而成（结合源代码和下面这张图理解）：  

<img src='./imgs/有趣的 border 图标[3].jpg' />   

### border 构成的有趣图案---**箭头**  
用处很多，比如置顶箭头，指向箭头等等，我们先制作几个使用频率较高的箭头图案：  

<img src='./imgs/有趣的 border 图标[4].jpg' />    

```css
/* css */
#container, #container:after {
  width:20px;
  height:20px;
  border-top:5px solid #09c;
  border-right:5px solid #09c;
  transform:rotate(-45deg);
  position:relative;
}
#container:after {
  content:"";
  transform:rotate(0);
  position:absolute;
  left:-15px;
  top:10px;
}
/* html */
/* <div id="container"></div>   */
```  
这个图案就是将 border 构成的直角进行旋转，使其箭头朝上即可，需要注意的是其伪元素 :after 定位的问题，千万不要忽略其父 div 是经过旋转的。    

<img src='./imgs/有趣的 border 图标[5].jpg' />    

```css  
/* css */
#container {
  position:relative;
  width:20px;
  height:20px;
  border-top:10px solid #09c;
  border-right:10px solid #09c;
  transform:rotate(-45deg);
}
#container:after {
  content:" ";
  position:absolute;
  margin: auto;
  left:-5px;
  top:-5px;
  right: -5px;
  bottom: -5px;
  width: 10px;
  height: 30px;
  background:#09c; 
  transform:rotate(45deg);
}
/* html
<div id="container"></div> */
```  
这个图案原理和上一个基本一致，需要注意的地方就是在伪元素 :after 中要加个 margin: auto 使其好定位。    

<img src='./imgs/有趣的 border 图标[6].jpg' />     

```css
/* css */
#container {
  position:relative;
  width:0;
  height:0;
  border-bottom:30px solid #09c;
  border-left:15px solid transparent;
  border-right:15px solid transparent;
}
#container:after {
  content:"";
  position:absolute;
  bottom:-30px;
  left:-15px;
  width:0;
  height:0;
  border-bottom:10px solid #fff;
  border-left:15px solid transparent;
  border-right:15px solid transparent;
}
/* html
<div id="container"></div> */
```  
实现这个图案有两个方案，一种是如我代码给出的这样，通过两个三角形的重叠覆盖来实现；另一种是通过两个等腰三角形通过旋转拼接而成，有兴趣的同学可以试试~   

### border 构成的有趣图案---**「小飘带」**    

<img src='./imgs/有趣的 border 图标[7].png' /> 

```css  
/* css */
#container {
  position:relative;
  width:0;
  height:0;
  border-top: 40px solid #09c;
  border-bottom:20px solid transparent;
  border-left:20px solid #09c;
  border-right:20px solid #09c;
}
#container:after {
  content: '特价';
  position: absolute;
  top: -30px;
  left: -16px;
  width: 40px;
  color: #fff;
  font-weight: 600;
}
/* html
<div id="container"></div> */
```  
如果前面都理解了，这个图案一眼就能看出来它是怎么实现的...   

### border 构成的有趣图案---**圆形图案** 

前面的一些图案，都是由方形和三角形的组合而成的，可能你会想，平时我们使用的图案中，有很大一部分是与圆有关的，border 能不能画圆呢？答案是肯定的，自从 border-radius 属性的出现，用 border 画圆就不再是问题了，下面是两个用 border 实现的圆形图案：  

<img src='./imgs/有趣的 border 图标[8].png' />    

```css  
/* css */
#container {
  position:relative;
  width:50px;
  height:50px;
  border: 10px solid #09c;
  border-radius: 35px;
}
#container:after {
  content: '';
  position: absolute;
  top: 40px;
  left: 50px;
  width: 10px;
  height: 50px;
  transform: rotate(-35deg);
  background: #09c;
}
```  
<img src='./imgs/有趣的 border 图标[9].png' /> 
  
```css
#container {
  width:100px;  
  height: 100px;  
  border:20px solid #D62427;  
  border-width: 10px 25px;     
  border-radius: 50px;  
  box-sizing: border-box;   
}  
```  
### 小结  
CSS 很强大，只要你有足够的想象力，border 也能画出很多有趣的图标～