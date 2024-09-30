---
title: "Promise.all的注意事项"
date: 2024-09-30T21:15:22+08:00
draft: false
summary: "今天测试提了一个bug，排查了很久发现是Promise.all用法的一个问题，颠覆了我对它的认知，这里记录一下。"
---
### Promise.all()的定义
- Promise.all() 接收一个可迭代对象（例如数组）作为参数，该对象包含多个 Promise。它会返回一个新的 Promise，当且仅当传入的所有 Promise 都成功时，返回的 Promise 才会被解析（fulfilled），并且返回一个包含每个 Promise 成功结果的数组。
- 如果其中有任何一个 Promise 失败（rejected），整个 Promise.all() 返回的 Promise 会立即失败，并抛出第一个失败 Promise 的错误。其他 Promise 即使还在进行中，也会**被忽略**。


### 注意点

当抛出第一个失败时，其他的promise中的代码是直接停止还是还会继续执行呢？答案是继续执行，只是它的promsie的值不会被promsie.all 所接受。
示例代码：
```javascript
const promise1 = new Promise((resolve, reject) => {
    setTimeout(() => {
        reject('failed on promise1');
    }, 1000);
});
const promise2 = new Promise((resolve, reject) => {
    setTimeout(() => {
        console.log('虽然被丢弃，依然执行1')
        resolve('succeed on promise2');
        console.log('虽然被丢弃，依然执行2')
    }, 2000);
});
Promise.all([promise1, promise2]).then((res) => {
    console.log('all promise.then', res);
}).catch((err) => {
    console.error('all promise.catch ', err);
}).finally(()=>{
    console.log('all promise.finally ');
});

```
上面的执行结果是：
```javascript
`all promise.catch  failed on promise1`
`all promise.finally `
`虽然被丢弃，依然执行1`
`虽然被丢弃，依然执行2`
```
有趣吧。

