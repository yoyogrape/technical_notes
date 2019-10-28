# JS必须背会代码
```
1 . <script></script>		    42. var  oEvent=ev||event	  83. var arr=newArray(1,2)
2. window					43. oEvent.clientX			  84. var attr=[1,2,3,4]
3. onload						44. oEvent.clientY			  85. attr.unshift(  )
4. function (函数 ){内容}		45. oEvent.cancelBubble=true  86. attr.shift(  )
5. var						46. oEvent.keyCode			  87. attr.push(  )
6.index						47. oEvent.ctrlkey			  88. attr.pop(  )
7.charAt(在第几位置的字符)	48. oEvent.altkey			  89. arrt.sort(  )
8. onmouseover				49. oEvent.shiftkey			  90. arrt.concat(arrt2)
9. onmouseout				50. offsetLeft				  91.arr.join('-')
10.onclick					51. offsetWidth 			  92. arr.split('-')
11.onmousedown				52. offsetHeight			  93. arr.splice( 开始位置，删几个)
12.onmouseoup				53. offsetTop				  94. arr.splice( 0,3,'1','2','3')
13.onpress					54. oncontextmenu			  95. arr.splice( 0,3)
14.onkeydown					55. onsubmit				  96.document.createElement() 
15.onkeyup					56. onmousemove			  97.oul.appendChild('元素')                      16. if(条件)	                57. return false		      98.oul.insertBefore(元素,在谁前)
17.for(  )循环				58. true					  99. oul.removeChild)('节点')
18.length						59. false					  100. parentNode
19.this						60. null					  101. new Date().getTime()
20.innerHTML					61. Ecmascript				  102. parseint(  )
21.var oq1[1,2,3,4]				62. Dom					  103. parseFloat(  ) 
22.alert(  )					63. Bom					  104. isNaN
23.write(  )					64. number				  105. NaN
24.setinterval(  )				65. string					  106. getComputedStyle获取
25.setTimeout(  )				66. boolean				  107. currentStyle
26.clearinterval(  )				67. object				  108. arguments
27.clearTimeout(  )			68. undefined				  109. arguments.length
28.var oDate=new Date(  )		69. alert(typeof  a)		  110. arguments[0]
29. oDate.getHours(  )			70. (  )作用				   
30. oDate.getMinutes(  )		71. %  余数				  
31. oDate.getSeconds(  )		72.！=转类型比较不等于	  
32. oDate.getFullYear(  )		73.！==不转类型比较不等于  
33. oDate.getMonth(  )			74. =赋值右边装左边		  
34. oDate.getDate(  )			75. ==转换类型等于		  
35. oDate.getDay(  )			76. ===不转换类型等于		  
36.switch(变量)				77.？：if的简写			  
37.case  值:					78. for(){break}			 
38.return						79. for(){continue}			  
39.break						80. document.getElementsByTagName (' 元素名 ')
40.default					81. var j1=(a:1,b:2,c:3)
41. document.getElementById(' id ') 82. for(attr in j1)把J1所有参数装到attr


JS必须背会代码2

111. document.createDocumentFragment()   	文档碎片
112. 父级.childNodes					  	子节点会把空格和文字选中节点
113. nodeType                				节点类型  1元素节点   3文字节点
114. children							    子节点childNodes的兼容版
115. offsetParent							获取元素在页面上的实际位置
116. oul. firstElementChild || oul. firstChild		获取第一个子节点。用nodeType判断出来
117. oul. lastElementChild || oul. lastChild		获取最后一个节点。有兼容性问题
118. oul. nextElementSibling || oul. nextSibling	上兄弟节点
119. oul .previousElementSibling || oul .previousSibling	  下兄弟节点
120. otext.value='123'						设置元素的几种方式
121. otext['value']='abc'
122. otext.setAttribute('value','rtertw')
123. window.open('about:blank','_self  _blank  _top  _ parent') BOM打开网页
124.新建页面.document.write(要输出到新页面内容)
	window.open.()关闭当前网页  但是必须是open出来的
125. window.navigator.userAgent				提取浏览器信息
126. window.location						当前页面网址信息(位置)
127. document.documentElement.clientWidth  	可视区宽度
128. document.documentElement.clientHeight  可视区高度
129. document.documentElement.scrollTop		滚动距离FF兼容
130 .document.body.scrollTop					这是IE兼容
131 .confirm('今天下雨了吗？')				确定取消提示框确定是true  取消是float
132. prompt( '输入姓名'，'张三')				返回值字符串或者null
133.onscroll								滚动事件
134.onresize								改变页面事件
135.Math.ceil								数字向上取整
136.Math.floor							数字向下取整
```