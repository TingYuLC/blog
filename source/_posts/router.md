# hash router && history router

### 1.hash模式在形式上比history模式多了个#符号。

**hash：https://douban.luojc.cn/#/movie**

**History: https://douban.luojc.cn/movie**

<!--more-->

### 2.url中，#符号后面的字段称为片段，在http跟服务器交互时，是不会把片段带给服务器的，即访问https://douban.luojc.cn/#/movie时，服务器只会拿到https://douban.luojc.cn，之后浏览器在拿到响应后，会自动根据#符号后面的片段自动跳转。

### 3.nginx配置默认只配置根路径，在hash模式下，永远都会访问根路径，但在history模式下，访问 https://douban.luojc.cn和 https://douban.luojc.cn/movie是不一样的，前者在nginx匹配到/，后者在nginx匹配到/movie，但由于nginx没有配置/movie，所以返回了404。

```
server {
  listen 80;
  server_name luojc.cn;
}
```

### 4.nginx配置404时，返回index.html。

```
server {
  listen 80;
  server_name luojc.cn;

  rewrite ^(.*)$  https://$host$1 permanent;
}
```

### 5.总结：无论是hash还是history，跳转原理都是浏览器和框架本身自己跳转，区别在于hash模式后面的参数属于片段，不会跟服务端交互，我们所要配置的只不过是为history模式404路径配置。