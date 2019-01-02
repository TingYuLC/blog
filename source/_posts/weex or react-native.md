---
title: weex or react-native
categories: webApp
tags: [weex, react-native]
date: "2018-12-27"
---



## 概述

|   维度   |                             weex                             |                         react-native                         |
| :------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|   思想   |                   write once, run anywhere                   |                  learn once, write anywhere                  |
|   框架   |                             Vue                              |                            React                             |
| 入门难度 |                             简单                             |                             困难                             |
|   异步   |                           Promise                            |                           callback                           |
|   平台   |                       android,ios,web                        |                         android,ios                          |
|   组件   |                        基本靠自身平台                        |                       靠自身平台和社区                       |
|   社区   |                            基本无                            |                             活跃                             |
|   打包   | 默认打的js bundle只包含业务js代码，体积小很多，基础js库包含在Weex sdk中 | 只能将ReactNative基础js库和业务js一起打成一个js bundle，没有提供分包的功能，需要制作分包打包工具 |
|   调试   |                          weex debug                          |                    react-native-debugger                     |

<!--more-->

## 思想

**weex:  一套代码同时跑web，android，ios三端。**

**react-native:  学习一套jsx语法，同时可编写app和web端。** 

**亲身实践：react-native只支持app端，对于web端，还需额外开发或者采用其他方式扩展，目前官网提供了react-native-web支持，但不全面，目前尚无较完美的方案。weex虽然支持跑三端，但对于很多三端兼容问题仍没有考虑完全，比如：字体，vue-router，navigator等。**

## 框架

**weex采用vue框架，由于vue本身的简洁性，入门简单，单文件组件特性，使得weex入门成本也是较低，而相对的，react-native由于使用了react独特的jsx语法，具有的一定的学习成本，上手难度较大。**

**亲身实践：问题大都出现在初始环境的搭建。。。**

## 分包

**weex采用webpack配置，分包简单，react-native需要额外制作分包打包工具，麻烦。**

## 调试

**weex采用weex debug和weex playgroud，react-native采用react-native-debuger**

**亲身体验：weex和react-native调试是大坑，weex 调试的安装会报更种莫名其妙的错误，而且新版weex playground无法调试js，network也无法调试post请求，而react-native-debuger有很多第三方库没支持，比如安装relam数据库，会导致debuger无法使用等等。**

## 综述

**weex兼容三端，基于Vue，采用webpack打包配置，分包简单，适用于替换hybird，适用于制作单个页面，在单独制作app方面，不建议。weex最大的缺点就是坑多，社区基本无，使用人数少，遇到坑得自己想办法解决**

**react-native基于react，只兼容两端，分包麻烦，对于提供单个页面，不推荐，但在单独制作app方面，远远优于weex，react-native还有最大的优点，社区活跃，开源组件，demo多，基本上遇到的坑都是有办法解决的。**

| 主要对比 |                     weex                      |                react-native                 |
| :------: | :-------------------------------------------: | :-----------------------------------------: |
|   支持   |  <font color="red">Android，IOS，Web</font>   |                Android，IOS                 |
|   特点   |      <font color="red">适合单页面</font>      |               适合开发整体App               |
|  bundle  | <font color="red">较小，可多页面多文件</font> |               较大，默认单一                |
|   社区   |                 残缺，apache                  |   <font color="red">丰富，facebook</font>   |
|   bug    |                 多，自身解决                  | <font color="red">少，基本有解决方案</font> |
|   文档   |                     凌乱                      |        <font color="red">完整</font>        |

**<font color='red'>总结：如果选择weex，得做好文档乱，社区少，坑多的准备，选择react-native，得考虑好web端的问题，以及项目是否需要分包或者安装包过大的问题。</font>**

## weex踩坑

**1.weex Android页面跳转问题，需在android目录下新增Activity进行file://拦截，采用file:// 名跳转，但由于android 7开始不支持file://，所以还需额外配置参数支持（整体麻烦。。。）。**

**2.router在app端无法手势返回（比如android端跟web不一样，采用的是堆栈，即activity）。**

**3.weex debug调试混乱，经常报错，network无法调试post请求，js debug无法使用等。**

**4.weex 没有暴露status的api，导致无法在每个页面更改status，只能在app配置里初始化配置一次。**

**5.weex 字体问题。**

**6.weex无法在app端调试。**

**7.weex全局变量Vue经常找不到。。。**

**8.如果你的手机装了两个或者多个weex app，当你进行navigator跳转的时候，会弹出应用选择框，用户体验极差。。。**



#### weex页面跳转三端（android， ios， web）适配方案：

**（1）思路：web端采用vue-router单页面应用的方式实现。但weex在使用router的情况下，app端的文件会被打包成一个index.js文件，无法实现跳转，并且vue-router在app端无法实现手势返回，所以综合考虑，app端采用多页面应用的形式开发，即一个navigator一个入口。**

**（2）页面参数传递：web使用vue-router传参，app为多页面应用，采用url传参，如file://assets/dist/DragBall.js?title=小球?id=10。**

##### 三端跳转方法：

```
    getBaseUrl () {
      let bundleUrl = weex.config.bundleUrl
      bundleUrl = String(bundleUrl)
      let nativeBase
      let isAndroidAssets = bundleUrl.indexOf('file://assets/') >= 0

      let isiOSAssets = bundleUrl.indexOf('file:///') >= 0 && bundleUrl.indexOf('WeexDemo.app') > 0
      if (isAndroidAssets) {
        // android
        nativeBase = 'file://assets/dist/'
      } else if (isiOSAssets) {
        // ios
        nativeBase = bundleUrl.substring(0, bundleUrl.lastIndexOf('/') + 1)
      } else {
        // weex playground
        let host = 'localhost:8080'
        let matches = /\/\/([^]+?)\//.exec(bundleUrl)
        if (matches && matches.length >= 2) {
          host = matches[1]
        }
        nativeBase = 'http://' + host + '/dist/'
      }
      return nativeBase
    },
    jumpWithParams (to, params) {
      if (weex.config.env.platform === 'Web') {
        // web
        if (this.$router) {
          this.$router.push({name: to, params: params})
        }
      } else {
        // 非web
        let path = this.getBaseUrl()
        let q = this.createQuery(params)
        const navigator = weex.requireModule('navigator')
        console.log(path + to + '.js' + q)
        navigator.push({
          url: path + to + '.js' + q,
          animated: 'true'
        }, event => {
          // const modal = weex.requireModule('modal')
          // modal.toast({ message: 'callback: ' + event })
        })
      }
    },
    // object 转 URL 参数
    createQuery (obj) {
      if (obj === null || obj === '' || obj.length === 0) {
        return ''
      }
      let url = '?'
      for (let key in obj) {
        if (obj[key] !== null) {
          url += (key + '=' + encodeURIComponent(obj[key]) + '&')
        }
      }
      return url.substring(0, url.lastIndexOf('&'))
    }
```

