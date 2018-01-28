---
title: CSS 定位问题
date: 2017-05-04 22:11:53
tags: position
---

## 普通流（static）

w3c中指出，除非专门指定，否则所有框都在普通流中定位。也就是说，普通流中的元素的位置由元素在 (X)HTML 中的位置决定。意思即是普通流中的元素会按照其在html中结构布局。

## 相对定位（relative）

元素框偏移某个距离。元素仍保持其未定位前的形状，它原本所占的空间仍保留。

<!--more-->

如下代码：

```
<head>
	<title></title>
	<style>
    #div {
    	width: 200px;
    	height: 200px;
    	margin: 20px;
    	background-color: green;
    }
    #div1 {
    	width: 100px;
    	height: 100px;
    	background-color: red;
    }
    #div2 {
    	width: 100px;
    	height: 100px;
    	background-color: blue;
    }
	</style>
<body>
    <div id="div">
    	<div id="div1"></div>
    	<div id="div2"></div>
    </div>
</body>
```

效果图：![](/img/Position/demo1.png)

若在div1中添加relative，即改为

例1：

```
#div1 {
    	width: 100px;
    	height: 100px;
    	background-color: red;
    	position: relative;
    	bottom: 20px;
    }
```

![](/img/Position/demo2.png)

亦或div1改为

例2：

```
#div1 {
    	width: 100px;
    	height: 100px;
    	background-color: red;
    	position: relative;
    	top: 20px;
    }
```

![](/img/Position/demo3.png)

#### 由以上两种改法，可见，在使用相对定位时，无论是否进行移动，元素仍然占据原来的空间，元素在文档流中的位置依然保留，例1中div1上移20px之后，div2不会上移20px，原因就是文档仍保留着div1原来的空间，但例2中div1下移20px时覆盖了div2，可见相对定位的元素会覆盖普通流的元素。

## 绝对定位（absolute）

相对定位的位移是根据元素在文档流中的位置移动的，而绝对定位是相对父元素第一个不是static的元素移动的，多说无益，看代码

例3：

```
<head>
	<title></title>
	<style>
    #div {
    	width: 200px;
    	height: 200px;
    	margin: 30px;
    	background-color: green;
    }
    #div1 {
    	width: 100px;
    	height: 100px;
    	background-color: red;
    	position: absolute;
    	top: 10px;
    }
    #div2 {
    	width: 100px;
    	height: 100px;
    	background-color: blue;
    }
	</style>
<body>
    <div id="div">
    	<div id="div1"></div>
    	<div id="div2"></div>
    </div>
</body>
```

效果图：![](/img/Position/demo4.png)

讲解之前我们先忽略div2即蓝色元素，上面例子我们给div1添加了position: absolute;

    	position: absolute;
        top: 10px;

#### 首先，我们要明白此时div1是相对哪个元素移动，一开始也说了，绝对定位是相对父元素第一个不为static的元素移动，div1的父元素是div，但div没有定义position，没有定义即为static，所以div1不会相对div移动，所以div1会继续寻找上层父元素，而body是它的最上层父元素，所以，此时div1是相对body移动的。

若给div添加relative，使其不为static，则此时div1会相对div移动，如下

例:4：

```
#div {
    	width: 200px;
    	height: 200px;
    	margin: 30px;
    	background-color: green;
    	position: relative;
    }
```

![](/img/Position/demo5.png)

#### 最后我们看div2，无论是例3和还是例4，div1都覆盖了div2，并且div2紧贴div，这是绝对定位的另一个影响，绝对定位的元素会脱离文档流，文档流不在为它保留空间，同时会覆盖其它元素，所以，我们把div1设为absolute后，div1已经不存在于文档流，所以此时div1会上移。

## 固定定位（flxed）

   看完上面两张定位，最后一种固定定位就比较容易理解。

#### 固定定位跟绝对定位差不多，唯一不同的就是固定定位永远相对浏览器视角移动，而绝对定位会根据父元素移动，看代码：

```
<head>
	<title></title>
	<style>
    #div {
    	width: 200px;
    	height: 200px;
    	margin: 30px;
    	background-color: green;
    }
    #div1 {
    	width: 100px;
    	height: 100px;
    	background-color: red;
    	position: fixed;
    	top: 0;
    	left: 250px;
    }
    #div2 {
    	width: 100px;
    	height: 100px;
    	background-color: blue;
    }
	</style>
<body>
    <div id="div">
    	<div id="div1"></div>
    	<div id="div2"></div>
    </div>
</body>
```



效果图：![](/img/Position/demo6.png)

## 粘性定位（sticky）

##### 粘性定位是css3新增的属性，是相对定位和绝对定位的结合。

##### 当元素在窗口看得到时，元素为相对定位，当元素在窗口中看不到时，元素为固定定位。

##### 另外，设置为sticky的元素仍然存在于文档流中，因为其后续的元素会在syicky的初始位置

##### 定位，就跟相对定位一样。

##### 用处：比如说我们有一个导航条，一开始固定在页面中部，当我们把页面下滚动时，我们不想

##### 其滚出页面，而是固定在窗口顶部，这是sticky就可以派上用场了，当然有些浏览器还是不支持

##### sticky的，慎用。

## 浮动（float）

###### 元素可以通过float设置为浮动，浮动的元素脱离文档流，并且根据浮动特效向左或向右移动，如下图：



不浮动：![](/img/Position/demo7.png)





###### 当把框 1 向右浮动时，它脱离文档流并且向右移动，直到它的右边缘碰到包含框的右边缘

右浮动：![](/img/Position/demo8.png)



###### 当框 1 向左浮动时，它脱离文档流并且向左移动，直到它的左边缘碰到包含框的左边缘



左浮动：      ![](/img/Position/demo9.png)

###### 如果把所有三个框都向左移动，那么框 1 向左浮动直到碰到包含框，另外两个框向左浮动直到碰到前一个浮动框：![](/img/Position/demo10.png)

###### 如果包含框太窄，无法容纳水平排列的三个浮动元素，那么其它浮动块向下移动，直到有足够的空间

![](/img/Position/demo11.png)