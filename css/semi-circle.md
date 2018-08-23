# 使用CSS 画半圆 等
```
// 基础样式
div {
	width: 100px;
	height: 100px;
	background-color: lightblue;
}
```
## 圆形
```
<div class="circle">圆形</div>
.circle {
	border-radius: 50%;
}
```
## 半圆(上半边)
```
<div class="semi-circle">半圆</div>
.semi-circle {
	border-radius: 100px 100px 0 0;
	height: 50px;
}
```
## 半圆 （左半边—）
```
<div class="semi-circle">半圆</div>
.semi-circle {
	border-radius: 100px 0 0 100px;
	width: 50px;
}
```
## 扇形 (左上角)
```
<div class="sector">扇形</div>
.sector {
	border-radius: 100px 0 0;
}
```
## 小三角
```
<div class="triangle"></div>
<div class="arrow"></div>
.triangle{
		border: 50px solid green;
		width: 0;
		height: 0;
		border-top-color: yellow;
		border-right-color: blue;
		border-bottom-color: pink;
		border-left-color: orange;
	}
.arrow{
	background: none;	/*为了清除前面div设置的背景颜色*/
	border: 50px solid red;
	width: 0;
	height: 0;
	border-color: red transparent transparent transparent;
}
```
## 疑问框
```
<div class="rectangle">疑问框</div>
/*圆角矩形*/
.rectangle{
	width: 200px;
	border-radius: 15px;
	position: relative;
}
/*小三角*/
.rectangle::before{
	content: "";
	width: 0;
	height: 0;
	border: 15px solid red;
	border-color: red transparent transparent transparent;
	position:absolute;
	bottom: -30px;
	left: 40px;
}
```
