---
title: 构建 Vue.js 工程时 .vue 文件解析报错
slug: vue-js-doesn-t-export-content
date: 2017-04-10 00:00:51
updated:
tags: [Vue.js, node-sass, gcc, webpack]
cover:
share_cover:
---

在一台 CentOS 6 服务器上构建 Vue.js 项目时，`.vue` 文件大量报错：

```
xxx.vue doesn't export content
```

这个错误的原因之一可能是 `node-sass` 安装不正常。也就是说，在 `.vue` 文件报错日志的最上方，或许能够看到 `node-sass` 模块的相关出错信息。

如果你能看到这个出错信息，可以参考一下我的解决方法。

- 检查 `node-sass` 包是否正确安装和编译，以及在 `node-sass` 包目录下生成 `vendor` 文件夹。如果没有正确生成，需检查工程所在目录的读写或执行权限。
- 编译环境中 `gcc` 版本要在 4.9 或以上。我原先的版本是 4.8.8，它导致 `webpack` 在构建 Vue.js 工程时报错：`Error: /usr/lib64/libstdc++.so.6: version CXXABI_1.3.5' not found`。你需要更新 gcc 版本。

更新 `gcc` 方法参考：

1. 先安装 gcc 4.9

  ```bash
  cd /etc/yum.repos.d
  wget http://linuxsoft.cern.ch/cern/scl/slc6-scl.repo
  yum -y --nogpgcheck install devtoolset-3-gcc devtoolset-3-gcc-c++
  ```

2. 然后修改 `.bashrc` 设置 gcc 的执行路径

  ```bash
  vi ~/.bashrc
  ```

3. 修改或插入 PATH 变量

  ```
  PATH=/opt/rh/devtoolset-3/root/usr/bin/:$PATH
  ```

4. 进入工程目录，执行 `rm -rf node_modules/node-sass/ && yarn add node-sass` 再次安装并编译。如果安装过程无误，这次会由 gcc 4.9 进行编译。

编译完成后，执行 `yarn build` 或你的工程定义的构建命令来进行构建，应该不会再看到文章开头的错误了。

## 20170601 更新

CentOS 7 系统请参考这个 [gist](https://gist.github.com/mogita/c3528c5229479c8e02d85bc3f96010ef)