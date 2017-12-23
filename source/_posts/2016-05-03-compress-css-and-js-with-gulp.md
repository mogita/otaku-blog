---
title: 用 gulp 压缩 CSS 和 JavaScript
permalink: compress-css-and-js-with-gulp
date: 2016-05-03 19:44:10
updated:
tags: 前端
cover:
share_cover:
---

{% asset_img gulp.jpg %}

最近接触前端工作比较多，所以需要学习一些自动化的工作方式。今天的学习内容是 [gulp](http://gulpjs.com/)。本文直接以 CSS 和 JavaScript 文件压缩作为入门范例，这两种自动化处理也是最常见的处理之一，能对前端页面字节精简，缩短用户的页面载入等待，也能减少数据流量消耗。

## 安装 gulp

在安装 gulp 之前，本机必须已安装 [npm](https://nodejs.org/en/)，一个随 Node.js 安装包一起安装的包管理器。

首先，全局安装 gulp。在命令行中执行：

```
npm install --global gulp-cli
```

然后进入工程根目录，在工程中安装 gulp，执行：

```
npm install --save-dev gulp
```

注意：若工程未进行 npm 初始化，请在安装 gulp 之前执行 `npm init`。

接下来，创建 `gulpfile.js`：

```js
var gulp = require('gulp');

gulp.task('default', function() {
  // place code for your default task here
});
```

最后，执行 gulp：

```
gulp
```

我们会在终端看到「default」任务的执行结果。当然，目前是不会有任何动作，因为我们的「default」任务里没有代码。

## Sass 自动编译

当保存好一个 .scss 文件后，通常我们需要用 sass 命令来将其编译成 .css 文件。

通过 gulp，把这个处理放入自动流程就可以了。

进入工程根目录，安装 [gulp-sass](https://www.npmjs.com/package/gulp-sass) 包：

```
npm install gulp-sass --save-dev
```

注意：如果需要安装 sass，请根据[官方文档](http://sass-lang.com/install)进行安装。

编辑 `gulpfile.js` 文件，加入对 gulp-sass 的引用，并添加 styles 任务（任务名可以随意起）。完整代码如下：

```js
var gulp = require('gulp'),
    sass = require('gulp-sass');

gulp.task('styles', function() {
    return gulp.src('src/styles/*.scss')
        .pipe(sass.sync({
            outputStyle: 'expanded'
        }))
        .pipe(gulp.dest('dest/styles/'));
});
```

然后在工程根目录执行 `gulp styles`，即可看到生成的普通 css 文件。

## Autoprefixer 支持浏览器特定的 CSS 前缀

或许我们看到过（或者每天都要写）这样的 CSS 代码：

```css
a {
    -webkit-border-radius: 5px;
            border-radius: 5px;
}
```

可以看到，除了 `border-radius` 之外，还有一行 `-webkit-border-radius`，这个就是为 webkit 内核准备的 CSS 前缀。如果使用 PhpStorm 这样的 IDE，它或许会为我们自动补充所需的前缀。但只写不带前缀的语句，能够提高 CSS 代码的可读性，只需把添加前缀的工作交给 gulp 来执行即可。

首先，还是安装 gulp-autoprefixer 依赖：

```
npm install gulp-autoprefixer --save-dev
```

然后在 `gulpfile.js` 文件中引用它，并添加 pipe。完整代码如下：

```js
var gulp = require('gulp'),
    sass = require('gulp-sass'),
    autoprefixer = require('gulp-autoprefixer');

gulp.task('styles', function() {
    return gulp.src('src/styles/*.scss')
        .pipe(sass.sync({
            outputStyle: 'expanded'
        }))
        .pipe(autoprefixer('last 2 version'))
        .pipe(gulp.dest('dest/styles/'));
});
```

然后在工程根目录执行 `gulp styles` 即可。

## CSS 压缩

前端页面优化的重要一环就是文件压缩（minify），以达到节省用户的流量，加速页面载入的目的。下面我们对工程的 CSS 文件进行字节精简，也就是压缩。

第一步，仍然是安装依赖：

```
npm install gulp-minify-css --save-dev
```

同时，为了遵循压缩版文件在文件名中标明「.min」的惯例，我们需要一个文件改名包：

```
npm install gulp-rename --save-dev
```

接下来，仍然是编辑 `gulpfile.js` 文件，完整代码如下：

```js
var gulp = require('gulp'),
    sass = require('gulp-sass'),
    autoprefixer = require('gulp-autoprefixer'),
    minifycss = require('gulp-minify-css'),
    rename = require('gulp-rename');

gulp.task('styles', function() {
    return gulp.src('src/styles/*.scss')
        .pipe(sass.sync({
            outputStyle: 'expanded'
        }))
        .pipe(autoprefixer('last 2 version'))
        .pipe(gulp.dest('dest/styles/'))
        .pipe(rename({suffix: '.min'}))
        .pipe(minifycss())
        .pipe(gulp.dest('dest/styles/'));
});
```

在工程根目录执行一下 `gulp styles`，未压缩版和压缩版的 main.css 都已经转换完毕了。

## JavaScript 压缩

继续页面优化，我们需要把 JS 文件进行「合并（concatenate）」处理，并「丑化（uglify）」，形成一个几乎无法阅读但可以被浏览器执行的压缩版 JS 文件。

先安装依赖：

```
npm install gulp-uglify --save-dev
```

以及：

```
npm install gulp-concat --save-dev
```

然后编辑 `gulpfile.js` 文件，完整代码如下：

```js
var gulp = require('gulp'),
    sass = require('gulp-sass'),
    autoprefixer = require('gulp-autoprefixer'),
    minifycss = require('gulp-minify-css'),
    rename = require('gulp-rename'),
    concat = require('gulp-concat'),
    uglify = require('gulp-uglify');

gulp.task('styles', function() {
    return gulp.src('src/styles/*.scss')
        .pipe(sass.sync({
            outputStyle: 'expanded'
        }))
        .pipe(autoprefixer('last 2 version'))
        .pipe(gulp.dest('dest/styles/'))
        .pipe(rename({suffix: '.min'}))
        .pipe(minifycss())
        .pipe(gulp.dest('dest/styles/'));
});

gulp.task('scripts', function() {
    return gulp.src('src/scripts/*.js')
        .pipe(concat('main.js'))
        .pipe(gulp.dest('dest/scripts/'))
        .pipe(rename({suffix: '.min'}))
        .pipe(uglify())
        .pipe(gulp.dest('dest/scripts/'));
});
```

在工程根目录执行一下 `gulp scripts`，JS 压缩就完成了。

## 将「自动化」进行到底

我们可以利用 gulp 的 `watch` 方法，让 gulp 主动监控我们对文件所做的修改，并自动启动所需的处理任务。

在 `gulpfile.js` 中添加我们的两行 watch 语句，完整代码如下：

```js
var gulp = require('gulp'),
    sass = require('gulp-sass'),
    autoprefixer = require('gulp-autoprefixer'),
    minifycss = require('gulp-minify-css'),
    rename = require('gulp-rename'),
    concat = require('gulp-concat'),
    uglify = require('gulp-uglify');

gulp.task('styles', function() {
    return gulp.src('src/styles/*.scss')
        .pipe(sass.sync({
            outputStyle: 'expanded'
        }))
        .pipe(autoprefixer('last 2 version'))
        .pipe(gulp.dest('dest/styles/'))
        .pipe(rename({suffix: '.min'}))
        .pipe(minifycss())
        .pipe(gulp.dest('dest/styles/'));
});

gulp.task('scripts', function() {
    return gulp.src('src/scripts/*.js')
        .pipe(concat('main.js'))
        .pipe(gulp.dest('dest/scripts/'))
        .pipe(rename({suffix: '.min'}))
        .pipe(uglify())
        .pipe(gulp.dest('dest/scripts/'));
});

gulp.task('watch', function() {
    gulp.watch('src/scripts/*.js', ['scripts']);
    gulp.watch('src/styles/*.scss', ['styles']);
});
```

进入工程根目录，执行 `gulp watch`，我们会发现这次执行到「Finished…」之后并没有退出，而是一直挂在这一行。没错，gulp 已经在监视源文件目录了，这时候对`src/scripts/` 和 `src/styles/` 下的文件进行修改，gulp 就会自动执行处理。

## 结尾

以上处理已经能够为我们的工程提供一定的前端页面字节精简了，在 JS、CSS 代码量巨大的工程中，优化效果尤为明显。

参考：

- https://github.com/gulpjs/gulp/blob/master/docs/getting-started.md
- https://community.nitrous.io/tutorials/setting-up-gulp-with-livereload-sass-and-other-tasks
- http://tagtree.tv/gulp