---
title: 11 条提升工作效率的 npm 命令
permalink: npm-useful-quick-tips
date: 2016-09-04 09:55:11
updated:
tags: Npm
cover:
from: https://nodesource.com/blog/eleven-npm-tricks-that-will-knock-your-wombat-socks-off/
share_cover:
---

Npm 是个容易上手但不易深入的工具，因为内置了数不尽的功能。如果要挨个学习和尝试，不知何年何月能够全部学完。

以我自身为例，学会了一条 `npm prune`（即本文第四节）之后，才明白如果项目里某些模块不再需要了，根本不必手动删除 `node_modules` 目录再重新执行 `npm install`。这个过程着实令人头痛。

我们总结了 11 条易用的 npm 命令，能够帮你在任何工程中提升效率。

## 打开模块的主页

**执行：**`npm home <package>`

执行 `home` 命令会打开 `package` 指定包的主页。比如输入 `lodash` 会打开 Lodash 的网站首页。无论这个包是否已被全局安装或工程内安装，都可以直接使用。

## 打开模块的 GitHub 仓库

**执行：**`npm repo <package>`

和 `home` 命令类似，`repo` 命令会打开指定包的 GitHub 仓库页面。例如输入 `express` 将打开 Express 的官方仓库页面。和 `home` 一样，未安装的包也可以打开。

## 检查需更新的依赖

**执行：**`npm outdated`

你可以在工程里执行 `outdated` 命令，npm 会检查哪些包已陈旧，需要更新。结果会展现在一张表格里，显示当前版本、所需版本和最新版本。

```bash
$ chatroom git:(master) npm outdated
Package    Current  Wanted  Latest  Location
express     4.13.4  4.13.4  4.14.0  chatroom
socket.io    1.4.6   1.4.6   1.4.8  chatroom
```

## 检查未被 package.json 声明的包

**执行：**`npm prune`

当你执行 `prune` 时，npm 会检查 `package.json` 里的依赖列表，并对比工程的 `node_modules` 目录。它会打印出未被 `package.json` 声明却已存在的包。你可以评估一下哪些包是真正需要的（比如说通过 `npm install` 安装却未加 `--save` 参数的包），并可以将它们删除。

## 锁定依赖版本

**执行：**`npm shrinkwrap`

执行 `shrinkwrap` 命令将生成 `npm-shrinkwrap.json` 文件。它能将安装在 `node_modules` 目录中的依赖锁定在一个特定的版本。当 `npm-shrinkwrap.json` 文件存在时，`npm install` 将遵守其中的版本号，而不再使用 `package.json` 里的更高版本号。

如果你想检查 `package.json`、`npm-shrinkwrap.json` 以及 `node_modules` 中依赖版本的一致性，请尝试使用 [npm-shrinkwrap](https://github.com/uber/npm-shrinkwrap) 包。

## 使用 npm v3 和 Node.js v4 LTS

**执行：**`npm install -g npm@3`

用 npm 全局安装 `npm@3` 将把 v2 版的 npm 升级到 v3 版，甚至在 Node.js v4 LTS（Argon）上也可以这样做。这样，你的 v4 LTS 运行环境里就可以使用最新稳定版的 npm v3 了。

## 让 `npm install -g` 不再请求 `sudo`

**执行：**`npm config set prefix <dir>`

执行这个命令时，需要在 `<dir>` 参数指定新的全局安装模块的目录，这样你就可以避免全局安装时要输入密码的情况了。指定的目录将成为新的全局 bin 目录。需要注意的是，新指定的目录必须对你的当前用户提供写权限，可以使用例如 `chown -R <USER> <dir>` 的命令来修改权限。

## 修改工程里的保存前缀

**执行：**`npm config set save-prefix ~`

在安装新模块并用 `--save` 或 `--save-dev` 保存时，波浪号 `~` 比 npm 默认的 `^` 号保守。波浪号会把依赖锁定在当前小版本号，也就是说可以通过 `npm update` 来安装 patch 发行版。而 `^` 代表锁定在大版本号，通过 `npm update` 安装的是 minor 发行版。

## 上线前去掉工程里的 `devDependencies`

当你的项目准备上线进入生产环境时，请无比在安装模块时加上 `--production` 标记。这个标记将只安装 `dependencies`，忽略 `devDependencies`。这就保证了线上版本的代码库里不含有调试用的工具包等。

其实，你可以在 `NODE_ENV` 环境变量中设置 `production`，从而保障线上工程的 `devDependencies` 永远不被安装。

## 使用 `.npmignore` 时需谨慎

如果你尚未使用过 `.npmignore`，其默认值还是很安全的。

一旦你在工程里添加了 `.npmignore` 文件，那么 `.gitignore` 里的规则就会被无视了。结局就是你必须保持这两个文件内容的同步，否则有可能在发布时泄漏敏感信息。

## 设置 `npm init` 的默认值

当你在新工程里执行 `npm init` 时，会从头到尾走一遍设置流程，最后生成 `package.json`。如果你想要设定一些可以让 `npm init` 一直使用的默认值，可以执行 `config set` 命令，执行方法如下：

```bash
npm config set init.author.name <name>
npm config set init.author.email <email>
```

或者你想要直接修改初始化脚本文件，做一些自定的初始化逻辑，那么请执行下面的命令：

```bash
npm config set init-module ~/.npm-init.js`
```

下面是个脚本范例，它会提问一些私人问题，并在需要时创建 GitHub 仓库。请修改默认的 GitHub 用户名 `YOUR_GITHUB_USERNAME`，作为 GitHub 用户名的默认参数。

```js
var cp = require('child_process');  
var priv;

var USER = process.env.GITHUB_USERNAME || 'YOUR_GITHUB_USERNAME';

module.exports = {
  name: prompt('name', basename || package.name),
  version: '0.0.1',
  
  private: prompt('private', 'true', function(val){
    return priv = (typeof val === 'boolean') ? val : !!val.match('true')
  }),

  create: prompt('create github repo', 'yes', function(val){
    val = val.indexOf('y') !== -1 ? true : false;
    if(val){
      console.log('enter github password:');
      cp.execSync("curl -u '"+USER+"' https://api.github.com/user/repos -d " +
        "'{\"name\": \""+basename+"\", \"private\": "+ ((priv) ? 'true' : 'false')  +"}' ");
      cp.execSync('git remote add origin '+ 'https://github.com/'+USER+'/' + basename + '.git');
    }

    return undefined;
  }),

  main: prompt('entry point', 'index.js'),

  repository: {
    type: 'git',
    url: 'git://github.com/'+USER+'/' + basename + '.git' },
  
  bugs: { url: 'https://github.com/'+USER'/' + basename + '/issues' },
  homepage: "https://github.com/"+USER+"/" + basename,
  keywords: prompt(function (s) { return s.split(/\s+/) }),
  license: 'MIT',
  cleanup: function(cb){
    cb(null, undefined)
  }
}
```

## 最后的话

如果你还想了解更多关于 npm、Node.js、JavaScript、Docker、Kubernetes 和 Electron 等的话题，请关注 [@NodeSource](https://twitter.com/nodesource) 的 Twitter 账号。