---
title: "浏览器中js操作文件的那些事"
date: 2024-10-15T12:09:41+08:00
draft: false
tags: ["File"]
summary: "本文介绍一下浏览器里如何通过js操作文件以及特点"
---

为了保护用户隐私和安全。浏览器中能够操作的文件通常是用户显式选择或授权的，JavaScript无法像后端那样直接读写用户的文件系统。

## 获取文件：通过`<input type="file">`或拖拽API

通过HTML的`<input type="file">`元素，用户可以手动选择本地文件，JavaScript代码随后可以访问这些文件的引用。
特点：
- 只能通过用户交互选择文件，无法自动访问文件。
- 可以获取文件的基本元数据（名称、大小、类型、修改时间等），但内容不会自动加载(内容未或未完全存在内存中，仍需用户主动通过API读取)，需手动读取。

```html
<input type="file" id="fileInput">
<script>
    const fileInput = document.getElementById('fileInput');
    fileInput.addEventListener('change', () => {
        const file = fileInput.files[0];
        console.log(file.name); // 文件名
        console.log(file.size); // 文件大小（字节）
        console.log(file.type); // MIME 类型
    });
</script>
```

除此之外，还可以通过拖放API来获取文件，用户可以通过拖放操作上传文件，文件会作为拖放事件的`DataTransfer`对象的一部分传递给JavaScript。

特点：
- 与`<input type="file">`类似，仍需用户交互。
- 适合构建更友好的用户体验，如通过拖拽上传文件。

```html
<div id="dropZone" style="width: 200px; height: 200px; border: 2px dashed #ccc;">
    拖拽文件到此处
</div>
```
```javascript
const dropZone = document.getElementById('dropZone');
dropZone.addEventListener('dragover', (event) => {
    event.preventDefault();  // 阻止默认行为
});

dropZone.addEventListener('drop', (event) => {
    event.preventDefault();
    const files = event.dataTransfer.files;  // 获取拖入的文件
    console.log(files[0].name);  // 打印文件名
});
```
## 读取文件内容：`FileReader`
描述：FileReader是一个内置的API，用于异步读取`File`或`Blob`对象的内容。
常用方法：
- `readAsText()`：将文件读取为文本字符串。
- `readAsDataURL()`：将文件读取为Base64编码的Data URL（通常用于显示图片）。
- `readAsArrayBuffer()`：将文件读取为ArrayBuffer，适用于二进制数据（如音频、视频）。
- `readAsBinaryString()`：已废弃，尽量不再使用。

特点：
- 异步操作，不会阻塞主线程。
- 只能读取文件内容，不能修改或写入文件。
- 只能读取用户通过`<input>`选择或通过拖放授权的文件。
```javascript
const fileInput = document.getElementById('fileInput');
fileInput.addEventListener('change', () => {
    const file = fileInput.files[0];
    const reader = new FileReader();

    reader.onload = function(event) {
        console.log('文件内容：', event.target.result);  // 文件的文本内容
    };

    reader.readAsText(file);  // 将文件读取为文本
});
```
## Blob 对象
描述：Blob（Binary Large Object）是一个表示二进制数据的对象，可以用来创建和处理文件数据。

特点：
- 可以使用JavaScript生成文件数据，并模拟文件对象。
- 常用于生成动态数据文件，如创建`CSV`、`PDF`等，并提供下载功能。

```javascript
const blob = new Blob(['Hello, World!'], { type: 'text/plain' });
const url = URL.createObjectURL(blob);
const a = document.createElement('a');
a.href = url;
a.download = 'example.txt';
a.click();  // 自动触发下载
URL.revokeObjectURL(url);  // 释放 URL
```
## 创建临时文件URL：`URL.createObjectURL()`

描述：URL.createObjectURL()可以为File或Blob对象生成一个临时的URL，该URL可以用于预览或直接访问文件数据（如在网页中显示图片、视频等）。

特点：
- 适合用于在**不完全加载**文件内容的情况下快速预览文件。
- 创建的URL只在当前会话有效，使用完后应调用`URL.revokeObjectURL()`释放资源。
- 不会将文件持久化，只有浏览器中临时存储。

```javascript
const fileInput = document.getElementById('fileInput');
fileInput.addEventListener('change', () => {
    const file = fileInput.files[0];
    const objectURL = URL.createObjectURL(file);
    const img = document.createElement('img');
    img.src = objectURL;
    document.body.appendChild(img);

    // 释放 URL
    setTimeout(() => {
        URL.revokeObjectURL(objectURL);  // 释放内存
    }, 10000);  // 10秒后释放
});
```

总结：
- 读取文件：通过`<input type="file"`>或拖拽API获取文件引用，这里的引用不同于服务端的**文件句柄**概念，程序不可以通过它对文件进行读写操作，而需要借助额外的API如`FileReader`来异步地读取文件内容。
- 预览文件：通过URL.createObjectURL()生成临时URL快速预览文件（这里也无需将文件完全加载到内存中）。
- 安全性：浏览器中的文件操作非常安全，用户必须手动选择文件，前端无法未经授权直接访问用户文件系统。