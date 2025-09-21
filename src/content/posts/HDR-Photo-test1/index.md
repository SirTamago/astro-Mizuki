---
title: "关于HDR图片在不同设备和不同环境上的显示问题"
published: 2025-09-20
pinned: false
description: ""
tags: []
category: ""
licenseName: "CC BY 4.0"
author: "Rimiel"
sourceLink: ""
draft: false
date: 2025-09-20
image: ''
pubDate: 2025-09-20
---

> jpg
![](https://img.rimrose.work/_DSC9801-已增强-降噪.jpg)

> png
![](https://img.rimrose.work/_DSC9801-已增强-降噪.png)

> avif
![](https://img.rimrose.work/_DSC9801-已增强-降噪-1.avif)

png转avif用的脚本：

```bat filename={convert.bat}
@echo off
set input=%1
set output=%~n1.avif

ffmpeg -i "%input%" -pix_fmt yuv420p10le -color_primaries bt2020 -color_trc smpte2084 -colorspace bt2020nc -c:v libaom-av1 -still-picture 1 -crf 20 -b:v 0 "%output%"
```