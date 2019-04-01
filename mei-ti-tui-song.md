---
description: 多才多艺的树莓派 ~
---

# 媒体推送

## 准备

* [ ] ffmpeg

```bash
sudo apt-get install libav-tools
sudo apt-get install ffmpeg
```

## 准备好了

开始 bilibili 直播 ~

```bash
ffmpeg -re -i  '视频地址' -vcodec copy -acodec aac -b:a 192k -f flv '直播地址和房间号' -r 5
```

参照

{% embed url="https://www.jianshu.com/p/0b1d03e2f0f4" %}



