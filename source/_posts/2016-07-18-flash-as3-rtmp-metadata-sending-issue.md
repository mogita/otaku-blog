---
title: 解决 AS3 推流无法发送 MetaData 的问题
permalink: flash-as3-rtmp-metadata-sending-issue
date: 2016-07-18 13:55:08
updated:
tags: ActionScript3
cover:
share_cover:
---

在学习 ActionScript 3 推流的过程中遇到了一个棘手的问题，用 `ns.publish()` 方法可以正常推流，但无法发送任何 MetaData。

在 RTMP/FMS 服务器配置正确的情况下，可以检查一下是否有如下代码：

在初始化 NetConnection 实例后，添加一行 `nc.objectEncoding = 0`。

这样，就可以正常收发 onMetaData 事件了。