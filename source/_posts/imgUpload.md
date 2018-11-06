---
title: 图片上传
categories: Little.Luo
tags: 图片上传
date: "2018-08-30"
---

#### 需求：实现一个图片上传功能.

涉及主要问题：

（1）图片的传输形式。

（2）图片的存储形式。

#### 1.base64存储.

**本质：<font color="red">数据库存储base 64, 图片即字符串.</font>**

##### 什么是base 64?

（1） 用<font color="red">**64**</font>个字符来表示任意<font color="red">**二进制**</font>的编码方法。

（2）把3个字节的二进制编码为4个字节的文本数据，<font color="red">**长度增加33%。**</font>

（3）常用于在URL、Cookie、网页中传输<font color="red">**少量二进制数据。**</font>

<!--more-->

![](/img/imgUpload/upload_1.png)

```
    submitImgBase (e) {
      const value = e.target.value
      if (!value) return
      const files = e.target.files[0]
      const reader = new FileReader()
      const that = this
      console.log('size', files.size / 1024 / 1024)
      reader.onload = function (e) {
        const base64 = e.target.result
        const obj = {
          base: base64
        }
        console.log('base64', base64.length)
        service.saveImgBase(obj)
          .then(res => {
            if (res.data.code === 0) {
              that.imgBase = base64
              that.$refs.formBase.reset()
            }
          })
          .catch(error => {
            that.$refs.formBase.reset()
            console.log(error)
          })
      }
      reader.readAsDataURL(files)
    }
```

优点：使用简单，便捷。

缺点：base 64字节过大造成css体积过大，渲染效率低，比较适用于小图片。



**<font color=red>改进方法：Canvas压缩</font>**

```
    getBase64Image (img, Orientation) {
      let canvas = document.createElement('canvas')
      // 图片原始尺寸
      let originWidth = img.width
      let originHeight = img.height
      // 最大尺寸限制，可通过设置宽高来实现图片压缩程度
      let maxWidth = 800
      let maxHeight = 800
      // 目标尺寸
      let targetWidth = originWidth
      let targetHeight = originHeight
      // 图片尺寸超过800x800的限制
      if (originWidth > maxWidth || originHeight > maxHeight) {
        if (originWidth / originHeight > maxWidth / maxHeight) {
          // 更宽，按照宽度限定尺寸
          targetWidth = maxWidth
          targetHeight = Math.round(maxWidth * (originHeight / originWidth))
        } else {
          targetHeight = maxHeight
          targetWidth = Math.round(maxHeight * (originWidth / originHeight))
        }
      }

      let height = targetHeight
      let width = targetWidth
      let ctx = canvas.getContext('2d')
      // 判断上传的图片是否旋转过
      switch (Orientation) {
        case 6:
          canvas.width = height
          canvas.height = width
          ctx.rotate(Math.PI / 2)
          ctx.drawImage(img, 0, -height, targetWidth, targetHeight)
          break
        case 3:
          canvas.width = width
          canvas.height = height
          ctx.rotate(Math.PI)
          ctx.drawImage(img, -width, -height, targetWidth, targetHeight)
          break
        case 8:
          canvas.width = height
          canvas.height = width
          ctx.rotate(Math.PI * 1.5)
          ctx.drawImage(img, -width, 0, targetWidth, targetHeight)
          break
        default:
          canvas.width = width
          canvas.height = height
          ctx.drawImage(img, 0, 0, targetWidth, targetHeight)
          break
      }
      let dataURL = canvas.toDataURL('image/jpeg')
      return dataURL
    }
```



#### 2.服务器存储.

**本质：<font color="red">图片存储在服务器，数据库存图片路径.</font>**

![](/img/imgUpload/upload_2.png)

```
  // 设置文件上传路径
  koaBodyConfig: {
    multipart:true, // 支持文件上传
    encoding:'gzip',
    formidable:{
      uploadDir:path.join('/data/upload/'), // 设置文件上传目录
      keepExtensions: true,    // 保持文件的后缀
      onFileBegin:(name, file) => { // 文件上传前的设置
        const dir = path.join('/data/upload');
        common.checkDirExist(dir);
        file.path = `${dir}/${file.name}`;
      },
    }
  }
  // 拼接得到图片路径
  saveImgServer (files) {
    const file = files.imgServer;
    const imgName = file.path.substring(file.path.lastIndexOf('/') + 1) ;
    const imgPath = 'https://luojc.cn/uploadImg/' + imgName;
    return imgPath;
  }  
```

优点：二进制传输速度较快。

缺点：服务器带宽过小时，获取图片过慢。

#### 3.对象存储.

**本质：<font color="red">图片存储在对象存储，数据库存图片路径.</font>**

对象存储是一个键值的海量存储空间，常用于存储互联网图片，短视频，音频等数据，可以使用服务器提交的API对存储文件进行访问和管理。

![](/img/imgUpload/upload_3.png)

```
  // 设置文件上传路径
  koaBodyConfigTmp: {
    multipart:true, // 支持文件上传
    encoding:'gzip',
    formidable:{
      uploadDir:path.join('/data/tmp/'), // 设置文件上传目录
      keepExtensions: true,    // 保持文件的后缀
      onFileBegin:(name, file) => { // 文件上传前的设置
        const dir = path.join('/data/tmp');
        common.checkDirExist(dir);
        file.path = `${dir}/${file.name}`;
      },
    }
  }
```

```
// 分片上传文件至对象存储
let cos = new COS({
    AppId: 'xxxxxxxxx',
    SecretId: 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx',
    SecretKey: 'xxxxxxxxxxxxxxxxxxxxxxxxxxxx',
});

const imp = {};

imp.uploadFile = (key, path) => {
  // 分片上传
  return new Promise(function (resolve, reject) { 
    cos.sliceUploadFile({
      Bucket: 'luojc-xxxxxxx',
      Region: 'ap-guangzhou',
      Key: key,
      FilePath: path
    }, (err, data) => {
      // 上传完成删除原服务器图片
      fs.unlink(path, error => {
        if (err || error) {
          return reject('error')
        }
        return resolve('//' + data.Location)
      })
    })
  });
}
```



### 总结：

|    方式    | 传输格式 | 有无压缩 |  适用大小  | 图片存储位置 | 图片存储形式 |
| :--------: | :------: | :------: | :--------: | :----------: | :----------: |
|  Base 64   | base 64  |    无    |     小     |    数据库    |   base 64    |
| canva 压缩 | base 64  |    有    | 小、中、大 |    数据库    |   base 64    |
| 服务器存储 |  binary  |    无    |   小图片   |    服务器    |   图片地址   |
|  对象存储  |  binary  |    无    | 小、中、大 |   对象存储   |   图片地址   |

