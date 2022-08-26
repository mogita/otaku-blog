---
title: 如何在自建 GitLab 中启用 CI Runner
slug: how-to-setup-gitlab-ci-runner
category: tech
date: 2017-10-23 11:43:50
updated:
tags:
isCJKLanguage: true
---

GitLab 内置了 CI/CD 功能，在任何自建的 [GitLab CE](https://about.gitlab.com/installation/) 服务器上都可以启用。对于中小型项目而言，这套内置的 CI/CD 工具足够用来完成测试、构建和部署自动化工作。

本文以前一个端开发项目为例，简单介绍 GitLab Runner 的搭建和使用流程。

> 什么是 GitLab Runner？
>
> GitLab Runner 是独立于 GitLab 的开源项目（采用 Go 语言编写），用来执行自动化任务，并能够与 GitLab 互传数据。它是 GitLab 官方采用的 CI 工具。

## 安装和启用

你可以直接查阅 [GitLab Runner 入门页面](https://docs.gitlab.com/runner/)来了解完整的安装步骤，这里仅作概括。

建议使用独立于 GitLab 所在的服务器来安装 GitLab Runner，我所在的公司目前使用一台 2 核 4GB 的主机来运行所有项目的 CI 任务。同时，建议使用 Docker 作为任务运行环境，确保每个任务的执行环境相对干净独立。

登录到要安装 GitLab Runner 的服务器，执行下列操作：

1. [安装 Docker](https://docs.docker.com/engine/installation/)（Docker 版本不低于 `1.5.0`）

   ```bash
   sudo curl -sSL https://get.docker.com/ | sh
   ```

2. 查看 GitLab 版本

   ```bash
   cat /opt/gitlab/version-manifest.txt |grep gitlab-ce|awk '{print $2}'
   ```

3. 安装 GitLab Runner 软件源

  ```bash
  # RHEL/CentOS/Fedora 系统
  curl -s https://packages.gitlab.com/install/repositories/runner/gitlab-ci-multi-runner/script.rpm.sh | sudo bash

  # Debian/Ubuntu/Mint 系统
  curl -s https://packages.gitlab.com/install/repositories/runner/gitlab-ci-multi-runner/script.deb.sh | sudo bash
  ```

3. 安装 GitLab Runner

   若 GitLab 版本低于或等于 8，要安装 GitLab Runner 1.11 版，新旧版 GitLab Runner 的 API 互不兼容

   ```bash
   # RHEL/CentOS/Fedora 系统
   sudo yum install gitlab-ci-multi-runner-1.11.2-1

   # Debian/Ubuntu/Mint 系统
   sudo apt-get install gitlab-ci-multi-runner-1.11.2-1
   ```

   GitLab 9 或以上版本，直接安装 GitLab Runner 包即可

   ```bash
   # RHEL/CentOS/Fedora 系统
   sudo yum install gitlab-ci-multi-runner

   # Debian/Ubuntu/Mint 系统
   sudo apt-get install gitlab-ci-multi-runner
   ```

4. 注册 GitLab Runner

   ```bash
   gitlab-runner register
   ```

   跟着提示填写所需信息，其中要填写和 GitLab 对应的 Token 和 URL，用于连接 GitLab 和 GitLab Runner

   > 在哪里查找 Token 和 URL？
   >
   > 如果你有 GitLab 管理员权限：
   > 进入 Admin Area，打开 Overview - Runners 页面，可以找到 Token 和 URL。
   >
   > 如果你没有 GitLab 管理员权限：
   > 进入 GitLab 的任一项目，点击右上角的功能菜单选择 Runners（GitLab 8），或左侧的 Settings 菜单（GitLab > 8）选择 CI/CD，可以找到 Token 和 URL。

## 在项目中使用 GitLab CI

假设我们要启用 CI 的项目叫做 `fe-project`，在项目根目录创建一个 `.gitlab-ci.yml` 文件，注意文件名开头的点字符。

GitLab CI 包含三个常用的步骤（`stage`），分别是 `test`、`build` 和 `deploy`，用于执行测试、构建和部署任务。

首先添加一段测试脚本：

```yml
the_unit_test:
  stage: test
  script:
    - npm install
    - npm run test
```

第一行是这段任务的名称，用于查阅和区分。第二行指定这段脚本要在 `test` 阶段执行。第三行是脚本数组，即这个任务具体执行的命令。具体测试命令可根据你的项目实际情况修改，以上给出的是比较常见的测试命令。

然后，提交代码并推到远程仓库。现在进入 `fe-project` 项目的 Pipelines 页面，即可看到当前正在运行刚刚添加的测试任务。

> 任务未运行或显示 Pending？
>
> 进入项目的 Runners 页面，检查是否已启用 Runner

如果运行过程中有报错（或任务本身执行失败、不通过等），可以点击任务 ID 查看详细的运行日志，查看命令行回显的错误内容并进行修正。

接下来在测试脚本下方添加构建脚本：

```yml
build_the_project:
  stage: build
  script:
    - npm install
    - npm build
  artifacts:
    expire_in: 1 week
    paths:
      - dist
```

这段脚本与测试脚本结构相似，其中新增了一段 `artifacts` 配置，用来指定构建出来的文件存放位置、过期时间等，供稍后的部署脚本使用。

提交代码并推到远程仓库，进入 Pipelines 页面查看一下日志，如果没有问题则可以进入下一步部署。通常构建阶段比较耗时，根据 `script` 执行的时长可能需要 3-5 分钟，具体耗时受项目本身的影响。

最后，当测试和构建都通过时，就可以执行部署任务了，新代码将被上传到部署服务器。

#### 初次执行部署任务前的准备工作

初次部署之前，你需要做这样几件事：

- 在生产服务器上配置好项目所需的服务，例如 Nginx、数据库等
- 在生产服务器上创建一个 `gitlab` 用户（用户名可以自拟）用于远程连接，并让项目所在的目录对这个用户可读写
- 为这个用户创建一对 SSH 密钥，存放在此用户的 `.ssh` 目录下，并拷贝私钥
  - 注意：确保 `authorized_keys` 文件权限 `chmod 600 authorized_keys`
  - 注意：确保 `.ssh` 目录的权限 `chmod 700 ~/.ssh`
- 进入项目的 GitLab 页面，并进入 Variables 页面（GitLab > 9 版本在 `Settings - CI/CD - Secret Variables` 下），添加一个名为 `CI_PRIVATE_KEY` 的变量，并将上一步拷贝的私钥粘贴在 Value 框内

#### 提交部署脚本

在构建脚本的下方添加部署脚本，关键信息已用注释标出：

```yml
deploy_to_server:
  # 使用最小 Linux 发行版 Alpine 作为部署执行环境的镜像
  image: alpine:3.6
  stage: deploy
  script:
    # 使用教育网 apk 镜像
    - echo "http://mirrors.ustc.edu.cn/alpine/v3.6/main/" > /etc/apk/repositories
    # 安装 rsync 和 openssh 包
    - apk add --no-cache rsync openssh
    # 创建 ssh 密钥，用来登录到生产服务器
    - mkdir -p ~/.ssh
    - echo "$CI_PRIVATE_KEY" >> ~/.ssh/id_dsa
    - chmod 600 ~/.ssh/id_dsa
    - echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config
    # 用准备工作中创建的用户登录到生产服务器，指定在 Nginx 中配置的项目路径
    # 注意请将 0.0.0.0 换成实际的 IP 地址
    - rsync -av --progress --delete dist/ gitlab@0.0.0.0:/var/www/fe-project/dist/
    # 在生产服务器进行一次代码备份，其中 CI_BUILD_REF 是 GitLab CI 的内置变量，表示构建任务的 ID 哈希值，用来区分不同构建的产出物，便于部署发现问题时立即将线上代码切换为任一可用版本
    - rsync -av --progress --delete dist/ gitlab@0.0.0.0:/var/www/fe-project/dist-$CI_BUILD_REF/
```

提交代码并推送到远程仓库，并观察 Pipelines 页面的日志。若测试、构建和部署都通过，再次访问网站的线上地址时，就可以看到最新的改动了。

## 结束

本文以最简洁的方式介绍了 GitLab CI 的配置和使用，在现实开发中你可能需要根据项目性质的不同来编排[更加复杂的 CI 脚本](https://docs.gitlab.com/runner/configuration/advanced-configuration.html)。此外，你还可以使用 GitLab 自带的 Webhooks、Services 来连接 Slack、[Telegram](https://integram.org/) 等外部服务，实时知晓 CI 的运行状况和结果。