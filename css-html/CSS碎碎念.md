# CSS碎碎念
## GET新技能
1. **什么是BFC**（Block Formating Context）：BFC相当于提供了一个独立布局的环境，里面的元素都遵循同一套布局规则，相邻的元素一个个挨着排列，可以用margin-left | right来控制其间距，并且在bfc中垂直方向的margin会重叠。而float和clear float也只对同一个BFC内的元素有效。  
   **什么情况下会产生BFC**： （官方）非块级盒子的浮动元素、绝对定位元素及块级容器(比如inline-blocks，table-cells和table-captions)，以及overflow属性是visible之外任意值的块级盒子，都会创建了一个BFC。所以只要css中设置了以下元素就会产生BFC：  
	+ float：left|right
	+ position：absolute|fixed
	+ display: table-cell|table-caption|inline-block
	+ overflow: hidden|scroll|auto
## Attention Point
1. 浏览器渲染页面的过程：[页面渲染](https://coolshell.cn/articles/9666.html);
   > 总结：从上面的页面渲染过程可以得出为什么css3的动画性能比jquery或js写的动画效果性能好----因为页面渲染的流程可简单概括为以下几步：1.根据html文件构建DOM tree；2.根据样式表、计算CSS样式构建CSS Rule Tree；3.由DOM tree和CSS Rule Tree构建render tree；4.最后通过调用操作系统Native GUI的API绘制；**那么如果使用js或jquery实现动画必然会涉及到动态修改了DOM属性或是CSS属性这样就会导致重新执行1或2,3步骤，这些过程很耗性能（构建render tree要比对dom节点并对相应的节点使用匹配的样式规则，所以DOM树要小，CSS尽量用id和class，千万不要过渡层叠下去）**

