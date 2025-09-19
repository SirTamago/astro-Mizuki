---
title: "把Mizuki主题默认的live2d模型更换为NagasakiSoyo"
published: 2025-09-20
pinned: false
description: "素世可爱捏"
tags: []
category: ""
licenseName: "CC BY 4.0"
author: "Rimiel"
sourceLink: ""
draft: false
date: 2025-09-20
image: './img.png'
pubDate: 2025-09-20
---

其实很简单，把原本的`public/pio`里的模型替换一下就可以了

关于模型及动作文件请参考这个视频：

<iframe width="100%" height="468" src="//player.bilibili.com/player.html?isOutside=true&aid=892317472&bvid=BV1hP4y1G7qt&cid=459545103&p=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" &#x26;autoplay="0"></iframe>

然后把 motions 和 textures 放到对应的文件夹里

再然后在 `public/pio` 这个目录下执行 

```powershell
tree /f > dir.txt
```

把`dir.txt`的内容交给AI，让它帮你写一个模型的json文件，比如这样：

> `soyo_other-26.json`

```json soyo_other-26.json
{
  "version": "1.0.0",
  "model": "soyo_other-26.moc",
  "textures": [
    "textures/texture_00.png",
    "textures/texture_01.png"
  ],
  "layout": {
    "center_x": -0.25,
    "center_y": -0.2,
    "width": 2.0
  },
  "motions": {
    "idle": [
      {"file": "motions/idle01.mtn"}
    ],
    "smile": [
      {"file": "motions/smile01.mtn"},
      {"file": "motions/smile02.mtn"},
      {"file": "motions/smile03.mtn"},
      {"file": "motions/smile04.mtn"},
      {"file": "motions/smile05.mtn"}
    ],
    "angry": [
      {"file": "motions/angry01.mtn"},
      {"file": "motions/angry02.mtn"},
      {"file": "motions/angry03.mtn"}
    ],
    "sad": [
      {"file": "motions/sad01.mtn"},
      {"file": "motions/sad02.mtn"},
      {"file": "motions/sad03.mtn"}
    ],
    "thinking": [
      {"file": "motions/thinking01.mtn"},
      {"file": "motions/thinking02.mtn"}
    ],
    "wink": [
      {"file": "motions/wink01.mtn"}
    ],
    "bye": [
      {"file": "motions/bye01.mtn"},
      {"file": "motions/bye02.mtn"}
    ],
    "serious": [
      {"file": "motions/serious01.mtn"},
      {"file": "motions/serious02.mtn"},
      {"file": "motions/serious03.mtn"},
      {"file": "motions/serious04.mtn"}
    ],
    "nf": [
      {"file": "motions/nf01.mtn"},
      {"file": "motions/nf02.mtn"},
      {"file": "motions/nf03.mtn"},
      {"file": "motions/nf04.mtn"},
      {"file": "motions/nf05.mtn"},
      {"file": "motions/nf_left01.mtn"},
      {"file": "motions/nf_right01.mtn"}
    ],
    "nnf": [
      {"file": "motions/nnf01.mtn"},
      {"file": "motions/nnf02.mtn"},
      {"file": "motions/nnf03.mtn"},
      {"file": "motions/nnf04.mtn"},
      {"file": "motions/nnf05.mtn"},
      {"file": "motions/nnf_left01.mtn"},
      {"file": "motions/nnf_right01.mtn"}
    ],
    "other": [
      {"file": "motions/ando01.mtn"},
      {"file": "motions/kandou01.mtn"},
      {"file": "motions/kime01.mtn"},
      {"file": "motions/odoodo01.mtn"},
      {"file": "motions/shame01.mtn"},
      {"file": "motions/shame02.mtn"},
      {"file": "motions/surprised01.mtn"}
    ]
  },
  "physics": "soyo_other-26.physics.json"
}
```

然后在`src/config.ts`里的`pioConfig`这一块把模型名字改掉就可以了

不过这里还有一个小问题，邦邦这个模型和Mizuki主题自带的默认模型分辨率会有差别，所以可以添加一个小设置来让你的模型更精细

在`public/pio/static/pio.js`里进行修改：

```js ins={8-52}
    // 运行
this.init = (noModel) => {
    // 未隐藏 + 非手机版，出现操作功能
    if (!(prop.hidden && tools.isMobile())) {
        if (!noModel) {
            action.welcome();

            // ====== 高 DPI / 保持视觉尺寸不变 的处理 ======
            try {
                const canvas = current.canvas;
                const container = current.body; // .pio-container
                // 优先使用 prop 提供的视觉尺寸，否则读取容器当前尺寸
                const cssWidth = (prop.width && +prop.width) || container.clientWidth || canvas.clientWidth || 300;
                const cssHeight = (prop.height && +prop.height) || container.clientHeight || canvas.clientHeight || 300;

                // 如果父容器没有显式设置尺寸，确保 container 跟 config 一致（可选）
                if (prop.width)  container.style.width  = cssWidth + "px";
                if (prop.height) container.style.height = cssHeight + "px";

                // 设备像素比（可被 prop.pixelRatio 覆盖）
                const dpr = (prop.pixelRatio && +prop.pixelRatio) || window.devicePixelRatio || 1;

                // 设置 canvas 的真实像素尺寸（高分渲染）
                const targetW = Math.max(1, Math.round(cssWidth * dpr));
                const targetH = Math.max(1, Math.round(cssHeight * dpr));
                canvas.width  = targetW;
                canvas.height = targetH;

                // 保持视觉大小不变（CSS 大小为用户期望的 px）
                canvas.style.width  = cssWidth + "px";
                canvas.style.height = cssHeight + "px";

                // 修正渲染上下文（WebGL 或 2D）
                const gl = canvas.getContext("webgl") || canvas.getContext("experimental-webgl");
                if (gl && typeof gl.viewport === "function") {
                    gl.viewport(0, 0, targetW, targetH);
                } else {
                    const ctx2d = canvas.getContext("2d");
                    if (ctx2d && typeof ctx2d.setTransform === "function") {
                        ctx2d.setTransform(dpr, 0, 0, dpr, 0, 0);
                    }
                }

                // 如果父容器被 CSS transform / zoom 缩放，会造成视觉大小异常，提示调试信息
                const cs = window.getComputedStyle(container);
                if (cs.transform && cs.transform !== "none") {
                    console.warn("pio container has CSS transform:", cs.transform, "this may affect visual size.");
                }
            } catch (e) {
                console.error("Pio HiDPI setup failed:", e);
            }
            // ==============================================

            loadlive2d("pio", prop.model[0]);
        }

        switch (prop.mode) {
            case "static": begin.static(); break;
            case "fixed":  begin.fixed(); break;
            case "draggable": begin.draggable(); break;
        }

        prop.content.custom && action.custom();
    }
};
```