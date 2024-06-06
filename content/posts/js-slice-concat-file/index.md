---
title: "javascript中文件的切片与合并"
date: 2024-01-08T09:26:31+08:00
draft: false
featuredImage: banner.jpeg
tags: ["File","分片上传","断点续传"]
summary: 文件切片在前端大文件上传时有很好的应用效果，可以将文件控制在更小的粒度，毕竟操作大文件困难，而操作小粒度的文件更简单高效。文本介绍前端和Nodejs中文件的切片与合并操作。
---

文件切片在前端大文件上传时有很好的应用效果，可以将文件控制在更小的粒度，毕竟操作大文件困难，而操作小粒度的文件更简单高效。文本介绍前端和Nodejs中文件的切片与合并操作。

## 前端中对文件切片、合并

先说切片，切片主要用到`Blob`接口的`slice()`方法。


```javascript
//用户通过<input type=file/> 选择文件后拿到到filelists
const file = filelists[0];
const partSize = 1024*1024;//每片2MB大小
const finalSegments = [];//切好的片按照顺序放到这里。

for(let i =0;i<file.size;i+=partSize){
    //此处的i+partSize超出文件的size不会报错而是当成文件的size值。
    finalSegments.push(file.slice(i,i+partSize));
}
```
切完片剩下的就是将小文件一个个的上传到后端即可，上传时也要把切片的序号一并带过去，方便后端在全部上传好后按照顺序组装合并成一个。


再来说前端如何将几个切好的小文件合并成一个完整文件。

```javascript
const file_segments = [];
Promise.all([
    fetch('/file1').then(res => res.blob()),
    fetch('/file2').then(res => res.blob()),
    fetch('/file3').then(res => res.blob()),
]).then(blobs => {
    const new_file = new File(blobs,'file.pdf');
})

```
如果涉及到下载，可以加上以下代码：
```javascript
const a = document.createElement('a')
a.download = new_file.name;
a.href = URL.createObjectURL(newFile);
a.click();
```
## Nodejs中对文件的切片与合并

nodejs中处理文件就相对简单多了，只用到了`buffer`的`subarray`和`Buffer.concat`方法。

```javascript
const fs = require('fs')
const buffer = fs.readFileSync('file.pdf')
//切割
const part1 = buffer.subarray(0,1024*1024);//1MB
const part2 = buffer.subarray(1024*1024,2*1024*1024);//2MB
//保存文件切片
fs.writeFileSync('./part1',part1);
fs.writeFileSync('./part2',part2);

//合并文件切片
const mergedFileBuffer = Buffer.concat([part1,part2]);
//保存到文件磁盘
fs.writeFileSync("./merged-file.pdf",mergedFileBuffer);
```

## 再说断点续传
断点续传本质就是先处理文件的切片，等到所有切片处理完之后再由程序进行合并。


我认为，断点续传可以用在上传中也可以用在下载的过程中，因为有了文件切片，
可以将尚未上传或下载的blob切片数据做存储（web端已经可以借助indexDB缓存blob数据了），同时计算上传或下载进度，下次重启设备时继续上传或下载，当所有切片处理完毕，再合并提示用户成功。

但是web应用不比原生应用，其存储空间有限，如果文件太大会导致缓存失败，这是web端受限的地方。



