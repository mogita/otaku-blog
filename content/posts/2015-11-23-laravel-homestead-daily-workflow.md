---
title: Laravel 5 Homestead 日常工作流程
slug: laravel-homestead-daily-workflow
category: tech
date: 2015-11-23 10:51:15
updated:
tags: [PHP, Laravel, Homestead]
cover:
from: http://laravelcoding.com/blog/laravel-5-beauty-homestead-and-the-laravel-installer
isCJKLanguage: true
---

> 本文是教程的第三步，并假设你已安装好全部所需软件。

本章将探索两种 composer 工具：homestead 和 laravel。接下来讲解一种典型的日常工作流程，通过 6 个步骤，来建立一个新的 Laravel 5.1 工程。

## 目录

- [Homestead 工具](#56-homestead-tools)
- [常用 Homestead 命令概览](#56-homestead-overview)
- [了解 Homestead.yaml](#56-homestead-yaml)
- [向 Homestead 虚拟机添加软件](#56-homestead-software)
- [日常工作流程](#56-daily-workflow)
- [六个步骤开启一个全新 Laravel 5.1 工程](#56-6-steps)
  - [第一步 – 创建应用骨架](#56-step-1)
  - [第二步 – 配置 Web 服务器](56-step-2)
  - [第三步 – 添加主机记录到 Hosts 文件](56-step-3)
  - [第四步 – NPM 本地安装](#56-step-4)
  - [第五步 – 创建应用的数据库](#56-step-5)
  - [第六步 – 在浏览器中测试](#56-step-6)
- [其他 Homestead 小贴士](#56-homestead-tips)
- [本文回顾](#56-recap)

## Homestead 工具

> **什么是控制台**
>
> 每当你需要在控制台里进行一些操作时，你需要搞清楚是哪种控制台。
>
> **Homestead 控制台**是指通过 SSH 连接到 Homestead 虚拟机。在 Windows 系统中，可以是通过 PuTTY 建立的连接（详情请阅读在 Windows 系统建立虚拟机的章节）。在其他操作系统中，在终端执行`homestead ssh` 命令就可以进行连接了。在本文中，凡是跟在 `$` 后边的命令，都是在 Homestead 终端执行的。
>
> **OS 控制台**是指 Windows 的命令行提示符或者其他你正在使用的终端软件。（本文中 `%` 符号后边的命令都是在 OS 控制台中执行的）

在宿主机的控制台中，我们输入 homestead 命令（不带任何参数），就可以轻松查看所有可用的 homestead 参数和命令。

## Homestead 命令

```
% homestead
Laravel Homestead version 2.0.9

Usage:
[options] command [arguments]

Options:
--help           -h Display this help message.
--quiet          -q Do not output any message.
--verbose        -v|vv|vvv Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug.
--version        -V Display this application version.
--ansi           Force ANSI output.
--no-ansi        Disable ANSI output.
--no-interaction -n Do not ask any interactive question.

Available commands:
destroy   Destroy the Homestead machine
edit      Edit the Homestead.yaml file
halt      Halt the Homestead machine
help      Displays help for a command
init      Create a stub Homestead.yaml file
list      Lists commands
provision Re-provisions the Homestead machine
resume    Resume the suspended Homestead machine
run       Run commands through the Homestead machine via SSH
ssh       Login to the Homestead machine via SSH
status    Get the status of the Homestead machine
suspend   Suspend the Homestead machine
up        Start the Homestead machine
update    Update the Homestead machine image
```

我们每天接触最多的一个命令是 `homestead up`，用来启动 Homestead 虚拟机。

## 常用 Homestead 命令概览

这里是一系列常用的 Homestead 命令。

- `homestead up` 启动 Homestead 虚拟机，或者说「点亮」虚拟机的电源。如果你使用了 provision 选项（`homestead up --provision`），则 provision 配置将对所有新增加的站点都生效。
- `homestead halt` 停止 Homestead 虚拟机，或者说「关闭」虚拟机的电源。
- `homestead suspend` 暂停 Homestead 虚拟机，可以理解为休眠。
- `homestead resume` 将暂停的 Homestead 虚拟机恢复运行。
- `homestead edit` 编辑 Homestead.yaml 文件。这个命令将使用当前与 YAML 文件格式关联的编辑器软件。
- `homestead status` 查看 Homestead 虚拟机的当前状态

## 了解 Homestead.yaml

Laravel Homestead 的配置都在 Homestead.yaml 文件中。该文件位于你的宿主系统 home 目录中的 `.homestead` 目录下。

查看该文件的内容，大致如下。

#### Homestead.yaml 的内容

```yaml
---

ip: "192.168.10.10"
memory: 2048
cpus: 1

authorize: ~/.ssh/id_rsa.pub

keys:
- ~/.ssh/id_rsa

folders:
- map: ~/Code
to: /home/vagrant/Code

sites:
- map: homestead.app
to: /home/vagrant/Code/Laravel/public

databases:
- homestead

variables:
- key: APP_ENV
value: local
```

下面逐一解释各个配置部分：

- `ip` 访问虚拟机所用的内网 IP。
- `memory` 虚拟机的可用内存。
- `cpus` 虚拟机的可用 CPU 数目。
- `authorize` 此项应该指向你的 SSH 公钥。 
- `keys` 你的 SSH 私钥。
- `folders` 共享文件夹，即宿主机中的文件夹和对应在虚拟机中的文件夹。在 Windows 系统中，`~/Code` 目录大致上等同于 `C:\Users\YOU\Code` 文件夹。在 OS X 中为`/Users/YOU/Code`。在 Linux 中通常是 `/home/YOU/Code`。当你在宿主机的这个文件夹里进行文件操作时，虚拟机中的对应目录中也会立即发生改变。
- `sites` 站点列表（各个域名指向的路径），每当 Homestead 虚拟机启动时将生效。
- `databases` Homestead 应该能自动创建的数据库列表。
- `variables` 将在 Homestead 环境中生效的变量。

> **配置小技巧**
>
> 我一般只对一处进行修改，将数据库下边的 homestead 改名为 xhomestead。这样做的目的在于，如果我忘记为新应用创建数据库，就会报错。否则，会使用默认的 homestead 数据库，我却无法察觉。

那么现在，除了 **database** 之外，我们不修改任何配置。

#### Homestead 虚拟机详细配置

| **字段**           | **值**        |
| ----------------- | ------------- |
| Hostname          | homestead     |
| IP Address        | 192.168.10.10 |
| Username          | vagrant       |
| SU Password       | vagrant       |
| Database Host     | 127.0.0.1     |
| Database Port     | 33060         |
| Database Username | homestead     |
| Database Password | secret        |

## 向 Homestead 虚拟机添加软件

若你需要在 Homestead 虚拟机中安装新的软件，可以使用 Ubuntu 的 `apt-get` 命令。

这是非常简便的两个步骤：

- 升级 Ubuntu
- 用 apt-get 安装

下边举例说明，当我们需要 unzip 这个软件时，应该怎样进行安装。

#### 升级 Ubuntu

```bash
# 将 Ubuntu 软件升级到最新
vagrant@homestead:~$ sudo apt-get update
vagrant@homestead:~$ sudo apt-get upgrade
```

选择「Y」来继续安装。如果安装过程中出现什么提示，尽量选择已有的或默认配置。

升级完 Homestead 虚拟机里的 Ubuntu 系统之后，就可以安装 unzip 了。

#### 通过 apt-get 安装 unzip

```bash
# 在 Homestead 虚拟机中安装 unzip
vagrant@homestead:~$ sudo apt-get install unzip
```

## 日常工作流程

通常，使用 homestead 处理日常工作的流程主要分成三个步骤：

**第一步：**`homestead up` 启动 Homestead 虚拟机开始一天的工作。
**第二步：**`homestead ssh`、`PuTTY` 通过 SSH 连接到 Homestead 虚拟机，直接访问文件，并执行 artisan 命令。
**第三步：**编写优美的代码。在宿主系统中，打开你最爱的编辑器，开始写代码。
**可选的第四步：**`homestead halt` 一天的工作结束后，你可以选择关闭 Homestead 虚拟机的电源。

## 六个步骤开启一个全新 Laravel 5.1 工程

每次建立新的 Laravel 5.1 应用时，你都可以跟随下边的六大步骤来进行。

比方说，我们要创建一个名叫 `test.app` 的工程，项目文件夹的名字就叫 `test`。

#### 第一步 创建应用骨架

通过 Laravel Installer（前置章节中安装好的 `laravel` 命令），可以迅速创建一个工程骨架。

创建一个新的应用骨架

```bash
$ laravel new test
Crafting application...
Generating optimized class loader
Compiling common classes
Application key [rzUhyDksVxzTXFjzFYiOWToqpunI2m6X] set successfully.
Application ready! Build something amazing.
```

#### 第二步 配置 Web 服务器

安装好应用骨架后，就可以在 homestead 环境中启动 Nginx 服务器，来为应用的`public` 目录建立网页服务了。

Homestead 环境中的 `serve` 命令可以轻松完成这一任务。

在 Homestead 中启动一个新的虚拟主机

```bash
$ serve test.app ~/Code/test/public
dos2unix: converting file /vagrant/scripts/serve.sh to Unix format ...
* Restarting nginx nginx                                    [ OK ]
php5-fpm stop/waiting
php5-fpm start/running, process 2169
```

这条命令会在 `/etc/nginx/sites-available` 目录下，为 **test.app** 生成一个新的配置文件，并在 `/etc/nginx/sites-enabled` 目录下生成一个指向该配置文件的软连接。

即使虚拟机重启，该配置也会依然存在。

> **为什么不直接编辑 Homestead.yaml**
>
> 你问得对。另外一种建立 test.app 服务器的方式是 `homestead edit` 命令，并在 *sites:* 下边输入新的站点信息。但 serve 命令更简单，并且无需重新配置并启动 Homestead 虚拟机。
>
> 然而，有时你可能需要经常对某个应用进行配置，你完全可以去编辑 Homestead.yaml 文件。

#### 第三步 添加主机记录到 Hosts 文件

截至目前，test.app 的域名还无法解析（没有任何 DNS 记录），必须向宿主系统的 hosts 文件添加一条记录。编辑 Linux 或 OS X 中的 `/etc/hosts` 文件。Windows 系统中，该文件位于 `C:\Windows\System32\drivers\etc\hosts`。将 `test.app` 这个域名指向 `Homestead.yaml` 文件中指定的 IP 地址即可。

例如将下一行加入文件中。

**Test.app 的 Host 记录**

```
192.168.10.10  test.app
```

> **Windows 系统需要管理员权限来编辑 hosts 文件**
>
> 在 Windows 中，你必须以管理员身份运行编辑器软件（例如写字板或 Sublime Text 等）。在 Linux 或 OS X 中则可以使用 `sudo` 命令。

在 Linux 或 OS X 中编辑 hosts 文件

```bash
sudo nano /etc/hosts
// or
sudo vi /etc/hosts
```

#### 第四步 NPM 本地安装

为了稍后可以使用 `gulp`，我们需要将所需的 npm 模块都在本地安装好。

**如果你用不到 gulp，那么就跳过这一步吧。**

在宿主系统的控制台中进入工程所在的目录，并执行以下命令。

NPM 本地安装

```
$ cd Code/test
~/Code/test% npm install
npm WARN package.json @ No repository field.

v8flags@1.0.5 install /Users/chuck/Code/test/node_modules/gulp/\
node_modules/v8flags
> node fetch.js

flags for v8 3.14.5.9 cached.

[snip]
```

这样，gulp 所需的所有依赖都会安装到工程目录中的 `node_modules` 文件夹了。

#### 第五步 创建应用的数据库

如果应用需要数据库，进入 Homestead 虚拟机的 **mysql** 控制台就能轻松创建数据库了。

在 Homestead 虚拟机中创建数据库

```bash
$ mysql --user=homestead --password=secret
mysql> create database test;
mysql> exit;
```

数据库创建好之后，你需要在工程的根目录中找到 `.env` 文件，编辑其中的 `DB_NAME` 字段为适合的值。

修改 `.env` 中的 `DB_NAME`

```php
// Change the following line
DB_DATABASE=homestead

// To the correct value
DB_DATABASE=test
```

就是这样简单。现在，你可以对数据表进行迁移和创建等操作了。之后的章节将进一步介绍这些操作。

#### 第六步 在浏览器中测试

打开浏览器，输入 `http://test.app`，你就能看到下面的画面了。

默认的 Laravel 5.1 首页

![l5-welcome-page](https://leanpub.com/site_images/l5-beauty/l5-welcome-page.jpg)

如果你看到的不是这个画面，那么之前的步骤中或许有不正确的地方。

## 其他 Homestead 小贴士

#### 请在宿主系统中编写代码

之前的章节我们提到了这一点，这里再强调一次。请把代码编辑工作都放在宿主系统中进行。共享文件夹使用起来非常便捷，你在宿主系统的工程文件夹中进行的任何更改都会立即呈现在 Homestead 虚拟机中。

#### 使用 .homestead/aliases 文件

每当你需要使用 `homestead up --provision` 或 `homestead provision` 命令重新配置 Homestead 时，`.homestead/aliases` 文件都会更新 Homestead 虚拟机中的各个替身。

所以通过这个文件，我们可以轻松添加新的替身、函数甚至其他环境变量。

#### 保持 Homestead 虚拟机在最新状态

上文提到，只需两条命令就可以将 Homestead 虚拟机里的 Ubuntu 操作系统更新到最新状态。

保持 Ubuntu 最新

```bash
$ sudo apt-get update
$ sudo apt-get upgrade
```

## 本文回顾

本章详细讲解了 `homestead` 和 `laravel` 命令，以及创建一个 Laravel 5.1 工程的简单六步。
