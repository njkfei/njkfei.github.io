title: ffmpeg媒体编解码使用方法
date: 2015.12.04
categories: 编程开发
tags: [audio, video]
toc: true
---
  ffmpeg是主流的媒体编解码基本工具，市场上主流的播放器，都是基于ffmpeg开发的。

## ffmpeg用途
  通过ffmpeg，可以构建流媒体服务。基本思路是：通过ffmpeg，将原始的媒体文件转化为流文件，再通过segment工具，将流文件切分为小文件，用户通过边听边看边下载的方式，来
享受无缝无扰的流媒体服务。

## ffmpeg安装
　可以在[ffmpeg](www.ffmpeg.org)首页上直接下载编译好的二进制文件。如果需要特殊的插件，则需要进行编译安装。

### 音频转码
 将原始音频文件，转码为流媒体文件。
``` bash
$ ffmpeg -i demo.mp3 -acodec libfaac demo.ts
```

解释如下：
#### -i: 输入文件　demo.mp3输入文件名
#### -acodec: 编码格式,使用faac
#### demo.ts输出的流媒体文件，该文件后续将用于切分

### 视频转码
　将原始视频文件，转码为流媒体文件。

``` bash
$ ffmpeg -i demo.mp4 -acodec copy -vcodec libx264 demo.ts
```


### 流媒体切分
　转码后的流媒体文件一般比较大，因此，需要进行切分，这样用户可以边看边听边下载媒体文件，这当中的中介，就是一个索引文件，通过segmenter工具，可以对文件进行切分，并生成相应的索引文件。
　流媒体切分工具segment,不是ffmpeg的组件，需要gcc编译环境，进行编译安装。
``` bash
$ segmenter -i demo.ts -d 10 -p demo -m demo.m3u8 -u http://m3u8.s.cn/data/
```

解释如下：
#### -i: 输入的流媒体文件　 demo.ts
#### -d: 切分后的媒体文件时间单位(秒)
#### -p: 文件名前缀
#### -u: m3u8索引文件前缀


### 视频截图
　视频一般都会有一张封面，因此，可以将视频媒体文件里的图片截取出来。
``` bash
$ ffmpeg -i demo.mp4 -y -f mjpeg -ss 3 -t 1  demo.jpg  
```

### 添加水印
  加水印，有两大作用：
* 保护版权
* 广告
正中位置加水印
```
$ ./ffmpeg -i south_pacific.mp4 -i wm.png -filter_complex "overlay=(main_w-overlay_w)/2:(main_h-overlay_h)/2" -codec:a copy output.mp4
```

右下角位置加水印
```
$ ./ffmpeg -i south_pacific.mp4 -i wm.png -filter_complex "overlay=(main_w-overlay_w):(main_h-overlay_h)" -codec:a copy output.mp4
```

### 环境说明
　上述命令：在centos6.5环境下，测试通过。
