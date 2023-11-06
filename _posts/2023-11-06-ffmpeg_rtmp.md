---
layout: post
title:  "FFMPEG 录制与发布 RTMP 流"
date:   2023-11-06 15:43:59 +0800
categories: ffmpeg rtmp
---

## 发布

```
ffmpeg -re -i input.mp4 -c copy -f flv -rtmp_app live -rtmp_playpath stream rtmp://127.0.0.1
```

## 播放

```
ffplay rtmp://127.0.0.1:1935/live/stream
```

![ffmpeg_rtmp](/assets/images/ffmpeg/ffmpeg_rtmp.png)

## 参考

1. [Windows搭建RTMP服务器+OBS推流+VLC拉流](https://blog.csdn.net/BigHorse110/article/details/119538683)
