---
title: 上手 Raspberry Pi
slug: hands-on-raspberry-pi
date: 2014-03-25T18:20:06+00:00
category: tech
isCJKLanguage: true
---

快递找到我时还正在忙着录音，拆开塑料包裹才想起这是朝思暮想的 Raspberry Pi 到了！加上一并购买的杂七杂八的元器件，一起拎了一纸袋的东西回家。

不知不觉已经玩了一个多星期了。RPi 的设计初衷是提起学生对电脑的兴趣，而我的 linux 技术倒是真的得到了一点长进。抽象一些的讲，一种表面上的「便利程度」很容易就成为了引起兴趣的缘由。回想起来，对 BASIC 的热情是被 PC1000 点燃的。对 linux 的兴趣，很大一部分也是基于 RPi 可以直接用电源插头控制开关机的工作方式吧。总之，开关机不是一件需要斟酌的事之后，就觉得是少了一个巨大的负担。

具体用 RPi 做的事情，倒也不多。熟悉了那些常用的 linux 命令，学会<a href="https://elinux.org/RPi_Adding_USB_Drives" target="_blank">挂载移动硬盘</a>，配置了 <a href="https://users.skynet.be/hupla/raspBrian/RaspiBlog/Artikelen/2013/3/25_make_your_RPI_show_up_in_Macs_Finder_as_a_shared_drive_using_Netatalk.html" target="_blank">AFP 共享</a>给 Mac 使用，架了个 web 服务器，绑定了一个<a href="https://xixitalk.github.io/blog/2013/05/29/update-ddns-with-api/" target="_blank">动态解析的域名</a>，配合之前购买的代理扩展出了一个 Wi-Fi 环境与 3G 环境都能使用的 APNP，装上了 aria2 和 yaax 实现<a href="https://blog.kuroy.me/aria2-download-in-centos.html" target="_blank">远程迅雷离线下载</a>。似乎说得上来的目前就是这些。

中间还重新刷过一次操作系统。RPi 刚到手时，在官网下载了最新版本的 Raspbian，内核为 3.10.xx。使用中发现这个版本的内核在蓝牙方面有些毛病（导致那几天心情很躁），降级内核后又出现了重启时总是内核崩溃。所以还是只能重新下载一个旧版本的 Raspbian（只会用，不懂编译内核啊）。但一直没有时间测试蓝牙。（**更新：**测试后发现蓝牙问题依然存在，并且从之前的 reboot 命令后崩溃，变成了一连接任何蓝牙设备就崩溃。<a href="https://elinux.org/RPi_USB_Bluetooth_adapters" target="_blank">elinux</a> 的解释是蓝牙棒本身的问题，芯片 bug 或伪劣芯片的原因。）

大概过两周就会搬家了。然后再去折腾那堆小器件吧。其实方案也挺傻瓜的，全都是兼容面包板的配件，玩起来大概和搭积木一样。总之这会是第一次感受用代码控制硬件嗷。

最近一股脑来了不少事，忙得连 instaFan 都没有时间修。很对不起依然在等待、向我抱怨的用户们。。这个周末也许会好一点，希望起码能让它像样工作先。

啊，说了这么多，不是应该摆上 RPi 的靓照一张嘛！

![Raspberry Pi](/img/rpi/raspberry-pi-1.jpg)

以及初次启动时忙碌地进行着 locale 设定（好像选太多了&#8230;

![Raspberry Pi](/img/rpi/raspberry-pi-2.jpg)
