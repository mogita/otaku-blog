---
title: A casual instagram video
slug: a-casual-instagram-video
date: 2013-06-24T09:45:57+00:00
category: journal
isCJKLanguage: true
draft: true
---

<p id="custom-video" style="width: 100%;">
  <video src="/img/IMG_1612.mp4" poster="/img/IMG_1612_cover.jpg" autoplay="true" loop="true" controls style="height: 100%;width: 100%">There supposed to be a beautiful video right here, but your browser doesn&#8217;t seem to support this feature which every 21st-century-browser does. Why not download a Firefox for a start? 🙂</video>
</p>

<a href="https://instagram.com/p/a7MbCauYjp/" target="_blank">View on instagram</a>

更新：现在视频在桌面浏览器和 iOS 设备上都能正常显示、播放了。

```html
<p id="custom-video" style="width: 100%;">
  <video src="/img/IMG_1612.mp4" poster="/img/IMG_1612_cover.jpg" 
         autoplay="true" loop="true" controls 
         style="height: 100%;width: 100%">
    There supposed to be a beautiful video right here, but your browser 
    doesn&#8217;t seem to support this feature which every 
    21st-century-browser does. Why not download a Firefox for a start? 🙂
  </video>
</p>
```



就这么一小段代码折腾死我了。本以为用一个插件就能直接搞定，但是试了好几种当下热得不行的插件都不满意。需求诡异是我一惯的作风么。总之，仍然坚持无 Flash，视频只用 HTML5 来播放。如果浏览器不支持，（可能会有同学因为需要某些特定功能而使用不支持 HTML5 的浏览器）就会看到我预留的一句话。

唯一还剩一点小缺憾：因为本想着让视频自动播放、无限循环，就不显示控制栏了。这样虽然干净漂亮但有一个问题，iOS 6 禁止网页上的视频自动播放，苹果的考虑是避免用户流量的过量消耗。因此，在 iOS 设备上要播放这段视频，必须由用户去按播放按钮。但禁用控制栏以后，在 iOS 上竟然会连播放按钮都消失掉，视频压根没法开始播放。无奈之下还是暂时先留着控制栏吧。

这篇文章里的视频体积不到 3MB，不过对于普通流量套餐的爪机来说确实是笔大数。

目前就只能做到这里了。等到流量不再是问题的那一天，电脑就可以变成预言家日报了！
