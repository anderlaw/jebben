---
title: "前后端如何实现流式传输数据"
date: 2024-06-06T08:27:49+08:00
draft: false
tags: ["http流式传输","耗时任务"]
summary: "做了一个分析视频链接并下载提供观看的网站，由于后端服务执行时间相对久一些，全部完成后前端才有反馈，体验很不好，于是改成了一边执行任务一边回传数据给前端提示任务进度的方式。"
---
## 要点
- 响应标头：`res.setHeader("Content-type", "application/octet-stream");`
- 后端需要回传数据的地方以异步的方式write数据`res.write(data)`， 最后`res.end(data)`
- 上一步的`data`内容要与前端达成标记共识，这样前端可以对数据进行判断并展示给用户。
- 前端通过`ajax`的`onprogress`事件（axios里的封装成了`onDownloadProgress`事件）来间断性获取数据。

## 后端
后端以`express`为例

```javascript
router.get("/download", async (req, res) => {
  //设置流式传输标头
  res.setHeader("Content-type", "application/octet-stream");
  //await异步函数：检查视频
  const stdout1 = await asyncExecCheckVideo(url);

  //省略代码................................

  //回传数据1   
  res.write("message:" + "视频查询完毕，准备下载...");
  const downloadOutput = await asyncExecDownloadVideo(
    videoId,
    (progressStr) => {
      //多次回传数据2:提示前端视频下载进度   
      res.write("progress:" + progressStr);
    }
  );
  //回传数据3:提示前端视频下载完成并转码  
  res.write("message:" + "视频下载完毕，转码中...");
  //转码为m3u8格式的文件
  const transcodeOutput = await asyncExec(videoId);
  
  //回传数据4:完成！
  res.end(
    "data:" +
      JSON.stringify({
        title: videoTitle,
        path: `/${videoId}`,
        filenames: fs.readdirSync(`./files/${videoId}`),
      })
  );
});
```
## 前端
```javascript
//由于回传的数据会渐进式的累加到一块，因此，要记录每次内容的长度len，下次从该处截取出当次的最新内容。
let offset = 0;
downloadVideoByURL(url, ({ event }) => {
    //取出data
    const { responseText } = event.target;

    const content = responseText.substring(offset);
    offset = responseText.length;
    if (content.indexOf("message:") === 0) {
        // message内容
        const uMessage = content.split("message:")[1];
    } else if (content.indexOf("progress:") === 0) {
        //进度内容
        const progress = content.split("progress:")[1];
    }
}).then((res) => {
    if (res.status === 200 && res.data) {
        //最终的data，截取并处理
        const rawSplitData = res.data.split("data:")[1];
        const videoInfo = JSON.parse(rawSplitData);
        console.log(`data:`, videoInfo);
    }
});
```
## 效果图
![效果图](progress.gif)
