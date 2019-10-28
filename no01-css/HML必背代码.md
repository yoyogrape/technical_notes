# HTML必须背会代码(1)
```
1.<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" 				 		
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
2.<html xmlns="http://www.w3.org/1999/xhtml">
3.<meta name="keywords" content="关键字">
4.<meta name="description" content="描述">
5.<meta http-equiv="content-type" content="text/html;charset=utf-8">
6.<meta http-equiv=''refresh''content=''时间秒数'';URL=''http://www.baidu.com''/>
7.<body>	<body  bgcolor="">	<body  background="">	<body  background="">			
<body  text="">		<body  align="">  <body  leftmargin="">  <body  rightmargin="">
	     <body  topmargin="">  <body  bottommargin="''>
8.	字符格式
		<b></b>	<i></i>  <strong></strong>  <em></em>  
		<u></u>  <s></s>  <sup></sup>  <sub>< /sub>
9.	段落标题
		<h1></h1>~~~<h6></h6>
10.	修饰标记
		<hr />  <hr  width="" />  <hr  size="" />  <hr  noshade="" />
11.	特殊标记
		<blockquote></blockquote>  <blockquote  cite="引用的网址">
12. 	预格式化
		<pre></pre>
13.	特殊代码
		&nbsp;空格  &copy;版权号  &reg;商标  &quot;""号  &amp;&号  &lt;<  &gt;>
14.	无序列表
		<ul><li> </li></ul>         <ul  type="disc   circle   square"></ul>
15.	有序列表
		<ol><li> </li></ol>  <ol  type="a为字母"></ol>	<ol  start="从第几个开始"></ol>
16.	上下层列表
		<dl>
			<dt>上层项目</dt>
			<dd>下层项目</dd>
		</dl>
17.	图片
		<img />  <img  src="路径" />  <img alt="无法显示图片显示的文字" />  
		<img  title="鼠标经过显示文字" />  <img  width="" />  
		<img height="" />  <img border="" />  <img align="" />
		<img vspace="" />  <img hspace="" />
18.	表格
		<table><tr><td></td></tr></table>
		<table  bgcolor=""></table>	<table  background=""></table>
		<table  border=""></table>		<table  cellpadding=""></table>
HTML必须背会代码(2)
		

<table  cellspacing=""></table>  <table  bordercolor=""></table>
<table  bordercolorlight=""></table>  <table  bordercolordayk=""></table>
<table  align=""></table>        <table  width=""></table> 
<table  valign=""></table>  <table  valse="none"></table>
<table>
		<caption  align="">表格标题</caption>
</table>
<td  width=""></td>  <td  height=""></td>  <td  bgcolor=""></td>  
<td  background=""></td> <td  align=""></td>  <td  valign=""></td> 
<td  rowspan=""></td>  <td  colspan=""></td>
<colgroup  align=""></colgroup>  <colgroup  valign=""></colgroup>
<colgroup  span=""></colgroup>  <colgroup  bgcolor=""></colgroup>
19.超链接
	  <a></a>  <a  href="http://www.baidu.com"></a>
			  <a  href="mailto:电子邮件地址"></a>
  <a  title="注释"></a> 
	  <a  target="打开窗口方式"></a>
	  值：_blank 新窗口 _self 自身窗口(默认) _parent 上一级窗口打开 _top浏览器整个窗口打开
20.表单
	  <form></form>
	<input  type="text" value="初始值" size="显示字符数" maxlength="限制内容数量">
<input  type="password" value="初始值" size="显示字符数" readonly=" readonly只读" disabled=
" disabled 设为不可操作">
<input  type="radio" value="提交值" checked="checked选中" / >
<input  type="checked" value="提交值" checked="checked选中" / >
<input  type="reset" value="按钮名字"/ >
<input  type="submit" value="按钮名字"/ >
<input  type="button" value="按钮名字"/ >
<input  type="image" src="路径"/ >
<input  type="hidden" value="提交值">
<input  type="fike" value="提交值">
<select  size="显示的行数"><option>这里是单行<option></select>
						<optgroup>组</optgroup>
<textarea  cols="行中字符数"  rows="显示的行数">多行文本框</textarea>
<fieldset></fieldset>表单外框
<legend></legend>外框标题
21.插入FLASH动画
<embed  src=""  width=""  height="" wmode="透明度"></embed>
22.内嵌式框架
<ifame  src="被嵌套的网页位置和全称"  width=""  height=""  frameborder="0  1显示边框"
scroling="yes滚动条 no  auto">
HTML必须背会代码(3)



23.框架
<frameset  cols="横向切割"  rows="竖向切割">
		<frame  src="文件地址"  name="名字"></frame>
		<frame  src="文件地址"  name="名字"></frame>
</frameset>
24.关联
<lablel  for="关联名">单选和复选框一般会用到这个<label>
```