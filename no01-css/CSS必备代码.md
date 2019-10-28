# CSS必须背会代码
```
1.行内样式  <div  style="width:100px;  height:100px;"> </div>
2.内嵌式样式  <style  type="text/css">  <style>
3.外部链接式  <link  href="文件地址"  rel="stylesheet"  type="text/css">
4.导入式样式表  
  <style  type="text/css">
	   @import  url("1.css")
  </style>
5.CSS文字属性.									  
  color:#000000；   				颜色            list-style-type: lower-alpha;	小写英文字母
  font-family: 宋体，英文字体；		字体		       list-style-type: upper-alpha;	大写英文字母
  font-size: 9px;						字体大小		  list-style-type: disc;			实心圆形符号
  font-style: itelic   none;				文字斜体        list-style-type: circle;		空心圆形符号
  font-variant: small-caps;				小字体          list-style-type: square;		实心方块
  letter-spacing: 1px  none;			字符间距		  list-style-type-image: url(1.png);图片符号
  word-spacing: 1px 	none;			单词间距        list-style-position: outside;	凸排
  line-height: 200%;					设置行高        list-style-position: inside;	     缩进
  font-weight: bold;					文字粗体		8.外边界
  vertical-align: sub;					下标字		  margin-top:10px;			上边距
  vertical-align: super;					上标字		  margin-bottom:10px;		下边距
  text-decoration: line-through;			删除线		  margin-left:10px;			左边距
  text-decoration: overline;				顶线		  	  margin-right:10px;			右边距
  text-decoration: underline;			下划线		  margin:10px;				上下左右边距
  text-decoration: none;			     取消下划线    
  text-transform: capitalize;				首字母大写
  text-transform: uppercase;			英文大写
  text-transform: lowercase;			英文小写
  text-align: left  right  center  justify；	文字对齐方式
  vertical-align: top  bottom  middle  	垂直对齐方式
		    text-top  text-bottom;
6.内边距
  padding-top: 10px;					上内边距
  padding-bottom: 10px;				下内边距
  padding-left: 10px;					左内边距
  padding-right: 10px;					右内边距
  padding: 10px;						上下左右内边距
7.CSS符号属性
  list-style: none;					不使用符号
  list-style-type: decimal;				阿拉伯数字
  list-style-type: lower-roman;			小写罗马数字
  list-style-type: uppe-roman;			大写罗马数字
css必须背会代码(2)
9.背景样式
  background: transparent;			透视背景			对象定位 position: fixed		   固定定位
  background-image: url(1.png);		背景图片					positions: absolute       绝对定位
  background-attachment: fixed;		浮水印固定背景			positions: relative        相对定位
  background-repeat: repeat		重复排列(默认)				positions: static        默认静态位
  background-repeat:no- repeat		不重复排列		         outline: none;		取消超链接线
  background-repeat: repeat-x;		在横向重复排列			opacity: 0.5			半透明
  background-repeat: repeat-y;		在竖向重复排列			
  background-position: 90% 90%;		背景图片在X与Y轴的位置
  background-position: top;			上对齐
  background-position: bottom;		下对齐
  background-position: left;			左对齐
  background-position: right;		右对齐
  background-position: center;		居中对齐
10.伪类										13.text-indet: 2em;         文本缩进可为负值
  a:link		超链接基本样式					   white-space:norma;   	 自动换行
  a:visited		访问过的链接样式							    :pre ;  	 换行和空格受保护
  a:hover		鼠标移动样式								    :nowyap;    强制在同一行显示
  a:active		鼠标点击样式						   *选中所有元素
11.边框										   overflow: visible;         超出正常显示
  border-top :1px solid #222222;						   overflow: hidden ;        超出隐藏
  border-bottom :1px solid #222222;					   overflow: auto;          (默认)根据内容
  border-left :1px solid #222222;						   overflow: scroll;         总是加滚动条
  border-right :1px solid #222222;					   z-index:1;			  堆叠顺序
  border :1px solid #222222;						   display: block;		  块状元素
12.鼠标光标样式							        display: inline;		  内联元素
  cursor: hand;       链接手指						   display: inlineblock;		
  cursor: crosshair;    十字体						   visibility: hidden          隐藏元素
  cursor: move;       十字箭头					   float: left  right；	  	   浮动
  cursor: help;        加问号						   clear: both			   清除浮动
  cursor: w-resize;     左箭头						   
  cursor: e-resize;      右箭头				  		 .clearFix:after{  	解决浮动对父级的影响
  cursor: n-resize;      上箭头								clear: both;
  cursor: s-resize;      下箭头								display: block;
  cursor: ne-resize;     右上箭头							visibility: hidden;
  cursor: nw-resize;     左上箭头							height:0;
  cursor: se-resize;      右下箭头							line-height:0;
  cursor: sw-resize;      左下箭头							content:".";
  cursor: wait          漏斗						  celarFix{zoom:1;}	解决IE6/7兼用问题
 视频   <video class="1" id="1" autoplay="" loop="">
			<source src="1.webm" type="video/webm">
          	<source src="2.mp4" type="video/mp4">		</video>
          	
```