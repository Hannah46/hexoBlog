---
title: H5中的video标签
date: 2016-05-10 22:58:05
tags: HTML5 javascript
---

## 格式问题

video标签能够支持的格式有限，通常为mp4, webm, ogg这三种视频格式，可以通过列举多个视频格式预防某个视频文件出问题，但是这样同样也会出现视频多余加载的问题，推荐还是使用MP4，但需要注意的是，在大部分浏览器中（PC和移动端均存在），只能识别编码格式为H.264的编码格式，其他的编码格式浏览器不能正常解析，Chrome中会出现白屏（播放有声音但无图像），火狐上会提示“文件已损坏”。

![firefox error](http://ww4.sinaimg.cn/large/59967359gw1f3pcey8tagj20cn09w751.jpg)


## 封面问题

如果视频加载成功，video自带的封面图会根据视频的宽高进行缩放，如果视频加载失败，则会根据图片的尺寸显示出播放区域

<!-- more -->

![correct show](http://ww3.sinaimg.cn/large/59967359gw1f3pcdlp82bj20ae06amxq.jpg)

![wrong show](http://ww1.sinaimg.cn/large/59967359gw1f3pce5av9ej20an0agabf.jpg)

最好用一个统一的封面加一个播放按钮，将视频隐藏，这里最好能把video标签用1px * 1px来隐藏，而不要display:none，因此display:none不会将视频激活，后续加载可能会耗时过长。不过这个有一个问题就是必须规定好视频的宽高比例，封面图片和视频保持一致，最好是可以截取视频的第一帧图像作为视频的封面。
