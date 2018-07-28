# 你不知道的HTML
## GET新技能
	
## Attention Point
1. option中a标签是无效的，若要在select的选项中给相关的链接，通过option的value属性，与a标签效果是一样的，例:`<select><option value="http：//xxx">aaa</option></select>;`
2. localstorage的value只能为字符串，若要存取对象的话需要将对象转换成json字符串，若对象是自定义对象或Map之类的要先转成json对象再转成json字符串；