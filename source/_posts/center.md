---
title: 垂直居中问题
categories: CSS
date: 2017-06-24 22:22:15
tags: [水平居中, 垂直居中, center]
---

##### 在平常写网页时，总会遇到很多水平居中，垂直居中的问题，往往对很多居中属性半生不熟，或者没去好好总结，本文章整理了一些水平，垂直居中的方法，希望有用。

在列举之前，先熟悉一些css的两个属性，text-align和vertical-align。

 **text-align规定行内元素如何相对它的块级父元素对齐，注意：text-align并不规定父元素本身的对齐，而是规定其子元素（行内元素）的对齐。**

<!--more-->

适用条件：

1.父元素为块级元素

2.子元素为行内元素

3.父元素设置text-align.

代码：

```
<style type="text/css">
	.div1,.div2,.div3{width: 200px;height: 100px;border: 1px solid #000}
	.div1{text-align: left;}
	.div2{text-align: center;}
	.div3{text-align: right;}
</style>

<div class="div1">左对齐</div>
<div class="div2">居中对齐</div>
<div class="div3">右对齐</div>
```

 效果图：

![](/img/center/1.png)

**vertical-alingn属性规定行内元素或单元格元素的对齐方式。**

适用条件：

1.父元素为块级元素

2.子元素为行内元素

3.子元素设置vertical-align

代码：

```
img {
width: 50px;
}
.div1,.div2,.div3{width: 200px;border: 1px solid #000}
.div1 img{vertical-align: text-top;}
.div2 img{vertical-align: middle;}
.div3 img{vertical-align: text-bottom;}

<div class="div1">顶部<img src="logo.jpg">对齐</div>
<div class="div2">中部<img src="logo.jpg">对齐</div>
<div class="div3">底部<img src="logo.jpg">对齐</div>
```

效果图：

![](/img/center/2.png)

**以上是text-align和vertical-align的用法，大同小异，区别是text-align是设置在父元素，vertical-align是设置在子元素。**

## 一.水平居中

水平居中分为块级和行内元素的居中，行内元素最简单，直接父元素设置text-align:center即可，主要讲解块级元素。

块级元素又分为定宽和不定宽。

**定宽：**

可以设置<font color=red>**左右margin为auto**</font>来实现

代码：

```
<style type="text/css">
    .div {
    width: 300px;
    height: 300px;
    border: 1px solid #000;
    }
    .center {
    width: 100px;
    height: 100px;
    border: 1px solid #000;
    margin: 0 auto;
    }
</style>

<div class="div">
	<div class="center">div居中文字不居中</div>
</div>
```

效果图：

![](/img/center/3.png)

**不定宽：**

(1)<font color=red>**通过table标签的长度自适应性来实现，并为table设置左右margin为auto**</font>

代码:

```
.div {
    width: 300px;
    height: 300px;
    border: 1px solid #000;
    }
table {
    margin: 0 auto;
    border: solid 1px #000;
}

<div class="div">
  <table><tbody>
    <tr><td>
    	<div class="center">div居中文字不居中</div>
    </td></tr>
  </tbody></table>	
</div>
```

效果图：



![](/img/center/4.png)

(2)<font color=red>**块级元素设置display:inline将其改为行内元素，然后再设置text-align:center即可**</font>

(3)<font color=red>**通过给父元素position:relative，子元素设置position:relative,left:50%,transform:translateX（-50%来实现）**。</font>

代码：

```
<style type="text/css">
      .div {
      width: 300px;
      height: 300px;
      border: 1px solid #000;
      position: relative;
      }
      .center {
      border: solid 1px #000;
      position: absolute;
      left: 50%;
      transform: translateX(-50%);
      }
</style>

<div class="div">
	<div class="center">div居中</div>	
</div>
```

![](/img/center/5.png)

(4)<font color=red>通过给父元素设置float，然后给父元素设置 position:relative和 left:50%，子元素设置 position:relative 和 left: -50% 来实现水平居中。</font>这种方法就不举例了，可以自己尝试一下。

## 二.垂直居中

垂直居中也可分为定高和不定高

**定高：**

对于定高的单行文本可以简单的设置ling-height:height来实现垂直居中。

而对于多行文本，也有几种方法:

(1)<font color=red>类似水平居中，可以使用table标签的长度自适应性，设置vertical-align:middle</font>

代码：



	<style type="text/css">	
		table td {
			height: 200px;
			vertical-align: middle;
			border: 1px solid #000;
		}
	</style>
	<table><tbody>
		<tr><td>
			<div>
				<p>div居中</p>
				<p>div居中</p>
				<p>div居中</p>
			</div>
		</td></tr>
	</tbody></table>
效果图：

![](/img/center/6.png)

(1)<font color=red>可以设置display:table-cell激活vertical-align:middle属性，这种方法简单但兼容性差，所以不提倡</font>

```
<style type="text/css">
		.container {
			height: 200px;
			display: table-cell;
			vertical-align: middle;
			border: 1px solid #000;
		}
</style>

<div class="container">
	<div>
		<p>div居中</p>
		<p>div居中</p>
		<p>div居中</p>
	</div>
</div>
```

效果图：

![](/img/center/6.png)

**不定高：**

对于不定高的居中方法，比较少，但实用，项目中也会经常用到。

(1)<font color=red>类似水平居中，可以通过transform:translateY(-50%)实现</font>

代码：

```
<style type="text/css">	
      .div {
      width: 300px;
      height: 300px;
      border: 1px solid #000;
      position: relative;
      }
      .center {
      border: solid 1px #000;
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      }
</style>

<div class="div">
	<div class="center">div居中</div>	
</div>
```

效果图:

![](/img/center/7.png)

## 三.flex布局

对于元素的水平，垂直居中问题，还可以通过flex布局来实现，

方法：

父元素设置：

```
display: flex;
justify-content: center;
align-items: center;
```

直接上代码：

```
<style type="text/css">	
      .div {
      width: 300px;
      height: 300px;
      border: 1px solid #000;
      display: flex;
      justify-content: center;
      align-items: center;
      }
      .center {
      border: solid 1px #000;

      }
</style>

<div class="div">
	<div class="center">div居中</div>	
</div>
```

效果图：

![](/img/center/8.png)

#### <font color=red>差不多居中方法就这么几种，也比较实用，在项目更多运用到的是定宽不定高的居中，毕竟如果不设置元素宽度和高度，宽度会默认为100%，而高度会随着内容的变化而变化，当然要让宽度随着内容变化可以设置position:absolute.</font>