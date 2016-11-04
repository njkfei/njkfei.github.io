title: grep使用
date: 2016.01.21
categories: linux #分类
tags: [linux, grep]
---
   grep文件内搜索之瑞士军刀。
## 忽略大小写匹配行
``` 
$ grep -n -i "duration" log.info
19:  Duration: 00:00:31.30, start: 0.000000, bitrate: 1685 kb/s
```

## 忽略大小写匹配数量
```
$ grep -c -i "duration" log.info 
1
```

## 正则匹配-匹配字符
```
$ egrep  -o  "[1-9][0-9]+x[1-9][0-9]+" log.info    
856x480
```

## 正则匹配-匹配行
```
$ egrep   "[1-9][0-9]+x[1-9][0-9]+" log.info    
 Stream #0:0(und): Video: mpeg4 (Simple Profile) (mp4v / 0x7634706D), yuv420p, 856x480 [SAR 1:1 DAR 107:60], 1562 kb/s, 29.97 fps, 29.97 tbr, 30k tbn, 2997 tbc (default)
```

## 附测试文件
log.info内容如下
```
ffmpeg version N-62058-gfd2bcfc Copyright (c) 2000-2014 the FFmpeg developers
  built on Dec 16 2015 14:44:57 with gcc 4.4.7 (GCC) 20120313 (Red Hat 4.4.7-16)
  configuration: --enable-version3 --enable-libvpx --enable-libfaac --enable-libmp3lame --enable-libvorbis --enable-libx264 --enable-libxvid --enable-shared --enable-gpl --enable-postproc --enable-nonfree --enable-avfilter --enable-pthr
eads --enable-libnut --enable-libtheora
  libavutil      52. 71.100 / 52. 71.100
  libavcodec     55. 56.107 / 55. 56.107
  libavformat    55. 36.101 / 55. 36.101
  libavdevice    55. 11.100 / 55. 11.100
  libavfilter     4.  3.100 /  4.  3.100
  libswscale      2.  6.100 /  2.  6.100
  libswresample   0. 18.100 /  0. 18.100
  libpostproc    52.  3.100 / 52.  3.100
Input #0, mov,mp4,m4a,3gp,3g2,mj2, from 'hechang.mp4':
  Metadata:
    major_brand     : isom
    minor_version   : 1
    compatible_brands: isom
    creation_time   : 2016-01-19 06:10:23
    encoder         : FormatFactory : www.pcfreetime.com
  Duration: 00:00:31.30, start: 0.000000, bitrate: 1685 kb/s
    Stream #0:0(und): Video: mpeg4 (Simple Profile) (mp4v / 0x7634706D), yuv420p, 856x480 [SAR 1:1 DAR 107:60], 1562 kb/s, 29.97 fps, 29.97 tbr, 30k tbn, 2997 tbc (default)
    Metadata:
      creation_time   : 2016-01-19 06:10:23
      handler_name    : video
    Stream #0:1(und): Audio: aac (mp4a / 0x6134706D), 44100 Hz, stereo, fltp, 125 kb/s (default)
    Metadata:
      creation_time   : 2016-01-19 06:10:23
      handler_name    : sound
[NULL @ 0x129dee0] Unable to find a suitable output format for '2'
2: Invalid argument
```