---
title: 前端跨域
date: 2017-8-12 23:55:12
tags: 跨域
---

##### 由于浏览器同源策略的原因，只有具有相同源的脚本和文档才能相互访问。

##### 所谓的相同源，是指协议，域名，端口必须一致。

##### 而所谓的跨域，就是让不同源的脚本，文档能够相互访问的方法。

### 一.JSONP

jsonp包括两部分，回调函数和数据。

原理是使用script标签具有跨域的特性去获取数据。

<!--more-->

```
// 在http://blog.luojc.cn页面
<script>
	function getJsonp(response) {
      console.log(response.data);
	}
</script>
<script src="http://www.luojc.cn/getJs.js"></script>

// 在http://www.luojc.cn页面下
getJsonp({data:"我是服务器js文件，返回数据"});
```

客户端通过script去请求服务器下的js文件，服务器下的js文件获取服务器数据返回给客户端。

但jsonp也有很大的缺点：

1.只能发起get请求.

2.安全隐患，客户端不知道获得的数据是否存在安全问题，即服务器返回的数据在中途被恶意拦截修改再返回给

​	客户端，客户端是无法知晓的.

3.,不知道jsonp是否请求失败，无法及时处理error.

## 二.img跨域.

该方法跟jsonp类似，也是利用img标签具有跨域的特性去获取数据。

## 三.window.damain跨域.

window.domain适合只有子域名不同的交互，比如

http://www.luojc.cn和http://blog.luojc.cn

```
// www.luojc.cn
<script>
	window.domain = "luojc.cn";
</script>

// blog.luojc.cn
<script>
	window.domain = "luojc.cn";
	const ifr = document.creatElement('iframe');
	ifr.src = "http://www.luojc.cn";
	ifc.style.display = "none";
	document.body.appendChild(ifc);
	
	ifc.onload = function () {
      	const x = ifc.contentDocument;	// 此时即可操作www.luojc.cn
      	ifc.onload = null;
	}
</script>

// 在blog.luojc.cn加载iframe,iframe的src设为www.luojc.cn，由于这两个页面都通过
window.domain设为相同域，所以主页面和iframe不存在跨域问题.
```

## 四.window.name

window.name也是通过iframe操作:

```
// 同样是blog.luojc.cn 请求www.luojc.cn

// www.luojc.cn	

<script>
	window.name = "数据";
</script>

// blog.luojc.cm
<iframe id="iframe" src="http://www.luojc.cn"></iframe>
<script>
	document.getElementById('iframe').contentWindow.location = 'http://blog.luo.cn/text.html';
	console.log(document.getElementById('iframe').contentWindow.name);	//获取数据
<script>

//	由于页面加载新页面时，window.name的值是不变的，因此可将数据保存在window.name中,然后在blog页加载iframe,iframed的src设为www,但此时iframe和blog仍为不同域，所以必须给iframe加载与blog同域的父层，即可通信。
```

## 五.HTML5的postMessage

同样以blog页面请求www页面为例：

```
// blog.luojc.cn
<iframe src=" http://www.luojc.cn/test.html" id="ifr"></iframe>
<script>
	const ifr = document.getElementById('ifr');
	ifr.contentWindow.postMessage("数据", "http://www.luojc.cn");
	
	window.addEventListener('message', function(event){
        if (event.origin == 'http://www.domain.com') {
      		consoe.log(event.data)   
      	}
	}, false)
</script>

// www.luojc.cn
<script>
	 window.addEventListener('message', function(event){
     if (event.origin == 'http://www.test.com') {
          window.parent.postMessage("返回数据", "http://blog.luojc.cn");
       }
     }, false);
</script>
```

<font color=red>综上几种方法，无论script，img，window,domain，window.name,都是通过html标签自带的src属性跨域跨域的特性进行跨域。特别是通过iframe进行跨域的window.domain,window.name,h5的postMessage，本质都是通过先加载iframe，再为iframe设置父层或者子层获取数据,最后主页面获取iframe数据进行跨域的。</font>

## 六.CORS

CORS是目前使用比较广的跨域请求，原因是只需在服务端配置前端的域名，响应方法等即可，而且配置简单，唯一的缺点是不支持IE9及以下，所以若想兼容IE9，服务器得对IE9做额外的跨域兼容，比如采用window.domain.

```
// 同样是blog请求www，只需在www配置即可。
header("Content-Type:application/json;charset=utf-8");
header("Access-Control-Allow-Origin: http://blog.luojc.cn");
header("Access-Control-Allow-Method: GET, POST");
```

## 七.其它

其它代理方式还有flash代理，服务器代理，css方式等。

## 总结.

<font coloe=red>跨域方法多种多样，推荐目前常用的CORS,简单易用，服务端配置即可。</font>

