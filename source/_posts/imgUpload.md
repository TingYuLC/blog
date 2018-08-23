---
title: 图片上传
categories: Little.Luo
tags: 图片上传
date: "2018-08-23"
---

#### 需求：实现一个图片上传功能.

涉及主要问题：

（1）前端图片以什么形式传给后端？

（2）后端接受到图片后以什么形式存储图片？

（3）后端以什么形式返回图片给前端？

#### 1.base64存储.

**本质：<font color="red">数据库存储base 64, 图片即字符串.</font>**

![](/img/imgUpload/upload_1.png)

#### 2.服务器存储.

**本质：<font color="red">图片存储在服务器，数据库存图片路径.</font>**

![](/img/imgUpload/upload_2.png)

#### 3.对象存储.

**本质：<font color="red">图片存储在对象存储，数据库存图片路径.</font>**

![](/img/imgUpload/upload_3.png)

<!--more-->