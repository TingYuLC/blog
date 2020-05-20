---
title: weex半屏适配问题
categories: weex
date: 2020-05-20 17:11:53
tags: [weex]

---

**本文针对的问题是在使用ipad开发weex页面的情况，如果采用分屏的形式，比如左边一半是native页面，右边一半是weex页面，这时候怎么适配weex页面的解决方案。**

## 一.weex适配不同屏幕.

**weex默认是以750适配全屏，即内部会有一个换算方式，把750换算成屏幕宽度。**

**比如1024的屏幕，750px代表全屏宽度，375px代表512。**

[适应不同尺寸屏幕](https://weex.apache.org/zh/guide/advanced/multi-size-screen.html#%E7%AE%80%E4%BB%8B)

## 二.半屏问题.

**半屏模式下，weex还是以750跟屏幕做换算。**

**比如左屏320，右屏704，而native只会在右屏加载weex页面，但这时候的weex**

**页面如果要做到全屏，不能写750px，因为750px会被换算成1024，而native给我们**

**的空间只有704，所以就溢出了。**

<!--more-->

### 三.适配原理.

**750px对应1024，那么515px就适应704，所以如果我们要适配半屏，css只需要写**

**515px就可以。但问题是每个ipad的宽度是不一样的，如果是1222的ipad，我们需要**

**写525px，这就导致我们在开发的时候无法固定一个宽度去占全屏。**

[使用dom的viewport属性获取全屏需要的宽度](https://weex.apache.org/zh/docs/modules/dom.html#getcomponentrect)

**其实，归根到底就是weex没有对半屏做适配，如果在半屏下，weex是以750适配704，那**

**我们只需要写750px，无论什么设备都可以适配全屏。**

## 四.解决方案.

**在半屏下，我们面临的问题就是要用一个宽度去适应全屏。**

**但这个宽度是动态的，比如1024是515px，1366是525px。**

**（1）比较简单的解决方案是使用内嵌style的方式去动态绑定宽度，比如每次加载页面的时候**

**都去动态获取halfWidth，然后:style={width: halfWidth}，缺点是工作量巨大。**

**（2）重适配。比如我就想统一600px适配半屏，现在的情况是**

**1024 ipad： 515 / 750 = 半屏宽度 / 屏幕宽度**

**1366 ipad：525 / 750 = 半屏宽度 / 屏幕宽度**

**而对于weex而言，里面的750是一个基准，我们可以设置为其它值，也就是我们可以通过重设**

**基准值，达到改半屏动态为全屏动态的效果。**

**1024 ipad： 515 / 750 = 半屏宽度 / 屏幕宽度 = 600 / newViewWidth**

**1366 ipad：525 / 750 = 半屏宽度 / 屏幕宽度 = 600 / newViewWidth**

**而515 和 525 是可以通过dom获取的，记为halfWitdh，所以：**

**newViewWidth = 750 * 600 /  halfWidth**

**为了统一半屏和全屏，所以开发半屏还是按照750来，所以把600改为750，即**

**newViewWidth = 750 * 750 /  halfWidth**

**最后附上代码：**

```
/* global Vue */

/* weex initialized here, please do not move this line */
const Test = require('@/views/Test.vue')
// 配置 viewport 的宽度
const meta = weex.requireModule('meta')
const dom = weex.requireModule('dom')
dom.getComponentRect('viewport', option => {

    const halfWidth = option.size.width // 获取半屏占满的宽度
		
    // 自定义width，如果是750，css必须写750px才能占满，
    // 如果是600，css必须是600才能占满，与自己的css对应
    const defineWidth = 750 
		
    // 这里的750是weex默认设置的，对应屏幕宽度
    const width = 750 * defineWidth / halfWidth
    
    // 重设屏幕宽度的viewport
    meta.setViewport({
        width: width
    })
    
    // 最后初始化Vue页面
    /* eslint-disable no-new */
    new Vue(Vue.util.extend({
        el: '#root'
    }, Test))
})
```

