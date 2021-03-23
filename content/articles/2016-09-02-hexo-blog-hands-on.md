---
title: Hexo 博客写作快速指南
date: 2016-09-02 19:57:24
updated: 2016-09-04 11:14:24
tags:
cover:
share_cover:
slug: hexo-blog-hands-on
---
今天把博客更新到了 [Hexo](https://hexo.io/)，取代了之前的 [WordPress](http://wordpress.org) 网站。WordPress 是个十足优秀的博客程序，但 [Markdown](https://daringfireball.net/projects/markdown/) 已经成为了我日常工作中的主要文档书写方式。

## 快速指南

因为是快速指南，所以操作细节会比较简略。如果你对任何细节有疑惑，请参考[官方文档](https://hexo.io/docs/index.html)，这里有详尽的讲解。

#### 什么是 Hexo？

Hexo 是一个基于 `Node.js` 的静态博客生成器。换成简单的说法就是：Hexo 是个命令行软件，可以把你的 `Markdown` 文件转换成一组漂亮的 `html` 页面构成的网站，把这个网站上传（部署）到 GitHub 或 FTP 服务器，就可以让读者访问你的博客了。

#### 如何使用？

既然 Hexo 是个软件，那么在使用之前就需要安装它。打开你的终端程序，执行下面的命令：

```bash
npm install -g hexo-cli
```

> 什么是 [npm](https://docs.npmjs.com/getting-started/installing-node)？

接下来，用 Hexo 命令来创建一个全新的博客吧。非常简单，执行下面的命令：

```bash
hexo init <folder>
```

用你想要的名称替换 `<folder>` 即可。这个命令会在当前所在的目录创建一个文件夹，里面装着一套 Hexo 博客的基本文件内容。当然现在它还是个尚未装修的毛坯房，你需要先对它进行一些敲敲打打。执行下面两条命令：

```bash
cd <folder>
npm install
```

上述命令将切换到博客所在的文件夹，并让 `npm` 自动安装所需的依赖。

然后，用你喜欢的文本编辑器打开 `_config.yml` 文件，这是 Hexo 博客的全部配置所在，比如网站域名、博客名称、作者昵称等。设置项的名称指明了它的作用，如果想要深入挖掘配置方法，请参考[此页文档](https://hexo.io/docs/configuration.html)。

## 写文章

首先，你需要知道文章都保存在哪里（事实上你不用手动创建 `.md` 文件，但仍需要知道在哪里能找到它们）。进入 `/<folder>/source/_posts` 文件夹，就能看到所有文章了，一篇文章对应一个 `.md` 文件。

怎样创建文章呢？执行下面的命令：

```bash
hexo new [layout] <name> 
```

其中 `<name>` 是文章的文件名，不用附加 `.md` 后缀。`layout` 参数可以填入 `post`（文章）或 `page`（页面）。忽略 `layout` 默认创建文章。如果创建的是文章，`.md` 文件位于 `/<folder>/source/_posts`，如果创建的是页面，则在 `/<folder>/source/<name>/index.md`。

然后，用你喜欢的 [Markdown 编辑器](http://www.williamlong.info/archives/4319.html)编辑文章吧。我正在使用的是 [Typora](http://typora.io/)。

## 生成网站

文章写完了，需要先生成网站。也就是把 `.md` 文件全部转换成 `html` 等静态 Web 文件，同时自动套用主题、插件等。注意，虽然这里也提到了「主题」、「插件」这样的字眼，但与 WordPress 的主题和插件有着本质的不同，静态 Web 站不需要像 WordPress 那样运行服务端脚本，插件等扩展功能都是在生成时一步到位，网站访问者请求的是一个货真价实的纯 `html` 网站。还有什么比直接访问 `html` 文件更快的呢？

执行下面的命令生成网站：

```bash
hexo generate
```

## 部署

所谓部署，对于静态 Web 站而言，就是「上传」了。无论是 GitHub 托管，还是通过 FTP 上传到自建网站，都需要把生成好的 `html` 文件等资源上传到服务端。

部署的配置方法已在官方文档中有详细的描述，比如 [Git](https://hexo.io/docs/deployment.html#Git) 方式，或 [FTP](https://hexo.io/docs/deployment.html#FTPSync) 方式。我的选择是 `FTP`，因为我已经有一台虚拟主机了。

执行下面的命令就可以开始部署了：

```bash
hexo deploy
```

如果没有出错，就可以去访问你的新博客了。

#### 一步部署命令

使用下面的命令，可以在执行完生成后，自动执行部署命令：

```
hexo generate --deploy

# 甚至更简略的命令，功能同上
hexo g -d
```

## 文章编辑小贴士

Hexo 文章除了支持基本的 `Markdown` 语法外，还额外支持一系列特殊标签，下面摘抄一些比较常用的标签。通过这些标签可以自动生成漂亮的排版元素。

#### 块引用

```
{% quote Seth Godin http://sethgodin.typepad.com/seths_blog/2009/07/welcome-to-island-marketing.html Welcome to Island Marketing %}
Every interaction is both precious and an opportunity to delight.
{% endquote %}
```

效果

{% quote Seth Godin http://sethgodin.typepad.com/seths_blog/2009/07/welcome-to-island-marketing.html Welcome to Island Marketing %}
Every interaction is both precious and an opportunity to delight.
{% endquote %}

#### 代码块

可以使用 `Markdown` 的 `Code fence` 语法，也可以使用下面的标签：

```
{% code Array.map %}
array.map(callback[, thisArg])
{% endcode %}
```

效果

{% code Array.map %}
array.map(callback[, thisArg])
{% endcode %}

### Gist

```
{% gist gist_id [filename] %}
```

#### 插入图片和附件

在 `Markdown` 文章里插入图片的方法十分简单，使用下面的语法就可以了：

```markdown
![Image of Yaktocat](https://octodex.github.com/images/yaktocat.png)
```

如果你想要有序地管理所有图片，可以使用 Hexo 的[资源文件夹](https://hexo.io/docs/asset-folders.html#Post-Asset-Folder)特性。首先需要在 `_config.yml` 文件中进行如下配置：

```
post_asset_folder: true
```

这样，每次执行 `hexo new [layout] <name>` 时会自动创建 `<name>` 同名的文件夹，把文章所需的图片放入该文件夹即可。用下面的语法即可插入图片：

```
{% asset_img slug [title] %}

// 举例
{% asset_img example.jpg This is an example image %}
{% asset_img "spaced asset.jpg" "spaced title" %}
```

其实，自动创建的 `<name>` 同名文件夹不仅可以用来存放图片，它本质上是一个「资源」文件夹，你还可以把其他类型的文件放在里面。比如，想要让读者下载一个 `PDF` 文件的话，你可以把文件放进资源文件夹，然后在文章中这样引用它：

```
{% asset_link file.pdf My PDF File %}
```

## 结尾

Hexo 是一款基于 `Node.js` 的静态博客生成器，你可以用 `Markdown` 语法和 Hexo 的扩展标签来编写文章内容和进行排版、插入图片或附件等。完成编写后，需要生成并部署网站资源，可以部署到 `GitHub` 通过 `GitHub Page` 来访问你的静态网站，也可以部署到 `FTP` 服务器。公开域名，就可以开始欢迎你的第一批访问者了！