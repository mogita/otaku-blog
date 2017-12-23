---
title: 理解 JavaScript Promises：上、背景和基础
permalink: understanding-javascript-promise-part-1
date: 2016-07-18 15:34:24
updated:
tags: 译文
cover: 
from: https://scotch.io/tutorials/understanding-javascript-promises-pt-i-background-basics
share_cover:
---

{% asset_img js_promises.png %}

## 许诺之地

ES2015 带来的最大变化之一就是[原生的 Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)（即「许诺」，下同）特性，用来解决各种回调问题，让开发者写出同步风格的异步代码。

讲道理来说，Promise 和 Generator 代表着异步的新标准。无论你是否使用它们，都应该了解一下。

Promise 特性由很简单的 API 构成，但学习曲线稍显陡峭。初学者或许会感到它的概念十分艰涩，需要一个了解的过程和充分的示例。

读完本文后，你将能做到：

- 明白为什么会有 Promise，以及它们能解决什么问题；
- 解释什么是 Promise，包括它们的实现以及应用；
- 将常见的回调设计用 Promise 进行重写

注意，文中示例均以 Node 环境为前提，你可以手动拷贝代码，也可以 [clone 我的代码](https://github.com/Peleke/promises/)。

```
git clone https://github.com/Peleke/promises/
git checkout Part_1-Basics
```

下面是本文的整体框架：

- 回调的问题
- Promise：定义以及 A+ 规范
- Promise 和反控制反转
- 用 Promise 控制流程
- 理解 `then`、`reject` 和 `resolve`

## 异步式

任何写过一些 JavaScript 代码的开发者都知道，它是「非阻塞」或「异步执行」的。这究竟是什么意思？

#### 同步和异步

**同步代码**会逐行运行，前行执行完了才执行后行。同步代码的近义词是阻塞执行，因为一行代码的执行会阻塞下面代码的执行，直到这行代码执行完毕。

```js
// readfile_sync.js

"use strict";

// This example uses Node, and so won't run in the browser. 
const filename = 'text.txt', 
            fs = require('fs');

console.log('Reading file . . . ');

// readFileSync BLOCKS execution until it returns. 
//   The program will wait to execute anything else until this operation finishes. 
const file = fs.readFileSync(`${__dirname}/${filename}`); 

// This will ALWAYS print after readFileSync returns. . . 
console.log('Done reading file.');

// . . . And this will ALWAYS print the contents of 'file'.
console.log(`Contents: ${file.toString()}`); 
```

{% asset_img 1.png %}

**异步代码**正好相反：当前正在执行的这行代码不会阻止接下来的代码，就算它正在执行需要较长时间的操作，例如 I/O 或网络请求。这就是「非阻塞代码」。下面是用异步方式模拟上面的代码段：

```js
// readfile_async.js

"use strict";

// This example uses Node, so it won't run in the browser.
const filename      = 'text.txt', 
            fs      = require('fs'),
        getContents = function printContent (file) {
        try {
          return file.toString();
        } catch (TypeError) {
          return file; 
        } 
      }

console.log('Reading file . . . ');
console.log("=".repeat(76));

// readFile executes ASYNCHRONOUSLY. 
//   The program will continue to execute past LINE A while 
//   readFile does its business. We'll talk about callbacks in detail
//   soon -- for now, just pay mind to the the order of the log
//   statements.
let file;
fs.readFile(`${__dirname}/${filename}`, function (err, contents) {
  file = contents;
  console.log( `Uh, actually, now I'm done. Contents are: ${ getContents(file) }`);
}); // LINE A

// These will ALWAYS print BEFORE the file read is complete.

// Well, that's both misleading and useless.
console.log(`Done reading file. Contents are: ${getContents(file)}`); 
console.log("=".repeat(76));
```

{% asset_img 2.png %}

同步代码最大的好处在于可读，便于理解。同步代码自上而下顺序执行，与我们读书的方式相同。第 n 行的代码一定比第 n+1 行代码先执行完。

但同步代码的坏处在于执行慢。比如，用户点击按钮后，浏览器要在完成一个 2 秒的网络请求之后，再继续响应用户操作，这种体验是无法接受的。

这个例子也正好说明为什么 JavaScript 是非阻塞型的。

#### 异步式带来的挑战

异步代码带来了速度上的提升，但不再具有线性特征。即使上例中琐碎的代码段也是如此。请注意：

- 无法知道 `file` 什么时候可用，除非将控制权交给 `readFile` 并让它在准备好的时刻通知我们
- 我们的代码不再按照它的书写顺序来执行，理解起来会不那么直观

这几点问题将成为本文接下来讨论的中心。

## 回调和回退

我们来精简一下异步 `readFile` 代码：

```javascript
"use strict";

const filename = 'throwaway.txt',
      fs       = require('fs');

let file, useless;

useless = fs.readFile(`${__dirname}/${filename}`, function callback (error, contents) {
  file = contents;
  console.log( `Got it. Contents are: ${contents}`);
  console.log( `. . . But useless is still ${useless}.` );
});

// Thanks to Rava for catching an error in this line.
console.log(`File is ${useless}, but that'll change soon.`);
```

由于 `readFile` 是非阻塞的，它不得不立即返回，以便代码继续往下执行。而「立即返回」也就意味着它没有足够的时间来完成 I/O 操作，它除了返回 `undefined` 之外也别无他法。但我们需要的是在 `readFile` 方法真正地读完文件。

问题来了，我们怎么知道读取已经完成？

答案也很简单：无法预知。但是，`readFile` 方法可以。上面的代码段中，我们向`readFile` 方法传递了两个参数：文件名，以及一个函数，这个函数就叫**回调函数**。在文件读取完成的时刻，它就会被执行。

把这段代码换成白话就是：「`readFile`，请查看一下 `${__dirname}/${filename}`这个路径里面有什么内容，这个过程需要一点时间。你查看完之后，请调用 `callback` 方法，传入 `contents`，如果有错误请让我知道 `error` 是什么。」

其要点就是我们无法获知读取文件的完成时间，只有 readFile 方法自己知道。这就是为什么我们要给它传递一个回调函数，并且信任 `readFile` 会对这个函数做正确的事情。

这便是以往人们使用异步方法的基本方式：用参数调用它，并传递一个回调函数来处理结果。

回调是一种异步解决方案，但它们并不完美。两个比较大的问题在于：

1. 控制反转
2. 复杂的错误处理方式

#### 控制反转

首先是关于信任的问题。

当我们向 `readFile` 传递回调函数时，我们**相信**它会执行这个回调。然而，事实上它却从没保证过它会执行这个回调。就算它确实会被调用，也没人保证传递给这个回调函数的参数将一定正确，或者顺序一定正确，又或者执行的次数也一定正确。

现实当中，这倒不是什么致命的问题：我们已经写了 20 多年回调，从未让网络崩坏。在此基础之上，我们觉得，把控制权直接交给 Node 核心代码应该也是安全的。

但是，把一个应用中最关键的部分交给第三方去管理，总感觉有点冒险，过去已经造成了无数个难以捕捉的 [Heisenbug](https://en.wikipedia.org/wiki/Heisenbug)。

#### 显式错误处理

在同步式编程中，我们可以用 `try`/`catch`/`finally` 来把握错误处理。

```javascript
"use strict";

// This example uses Node, and so won't run in the browser. 
const filename = 'text.txt', 
       fs        = require('fs');

console.log('Reading file . . . ');

let file;
try {
  // Wrong filename. D'oh!
  file = fs.readFileSync(`${__dirname}/${filename + 'a'}`); 
  console.log( `Got it. Contents are: '${file}'` );
} catch (err) {
  console.log( `There was a/n ${err}: file is ${file}` );
}

console.log( 'Catching errors, like a bo$$.' );
```

而在异步式编程中，还想这么写的话，就走远了：

```javascript
"use strict";

// This example uses Node, and so won't run in the browser. 
const filename = 'throwaway.txt', 
        fs       = require('fs');

console.log('Reading file . . . ');

let file;
try {
  // Wrong filename. D'oh!
  fs.readFile(`${__dirname}/${filename + 'a'}`, function (err, contents) {
    file = contents;
  });

  // This shouldn't run if file is undefined
  console.log( `Got it. Contents are: '${file}'` );
} catch (err) {
  // In this case, catch should run, but it never will.
  //   This is because readFile passes errors to the callback -- it does /not/
  //   throw them.
  console.log( `There was a/n ${err}: file is ${file}` );
}
```

这段代码根本不会以设想的方式执行。因为 `try` 语句块包含 `readFile` 方法，后者永远会成功地返回一个 `undefined` 值。也就是说，`try` 永远会成功完成，而不会发生任何意外。

要捕获 `readFile` 产生的错误的方式只有一种，就是给它传递一个回调函数，并在这个回调中获得错误信息，然后再来处理错误。

```javascript
"use strict";

// This example uses Node, and so won't run in the browser. 
const filename = 'throwaway.txt',
        fs       = require('fs');

console.log('Reading file . . . ');

fs.readFile(`${__dirname}/${filename + 'a'}`, function (err, contents) {
  if (err) { // catch
    console.log( `There was a/n ${err}.` );
  } else   { // try
    console.log( `Got it. File contents are: '${file}'`);
  }
});
```

这个例子的代码还不算太糟糕，但是工程量足够大时，这些问题会后患无穷。

Promise 则一下解决了这两个问题等一系列不足之处，通过「反控制反转」，和「同步化」异步代码，来实现我们更熟悉的错误处理。

## Promise 机制

假设你从 O’Reilly 订购了全套「[You Don’t Know JS](https://github.com/getify/You-Dont-Know-JS/blob/master/README.md#you-dont-know-js-book-series)」丛书。你把辛苦搬砖挣来的钞票给了他们，他们发给你一张收据，声明你将在下周一收到一套崭新的图书。在那之前，你都并未拥有这套图书。但你可以相信，一定会收到，因为他们保证过（promised）一定会寄送。

基于这种 Promise，在图书送达之前，你就可以计划好每天要阅读多少本，计划借给朋友哪几本，甚至给老板请好一周的休假来奋力读书。在制定这些计划的时候，你其实都处于并未拥有这套图书的状态，你仅仅是知道你会拥有。

当然，O’Reilly 可能过几天之后告诉你，由于七七八八的原因，订单无法完成了。到那时，你可以取消每日阅读计划，告诉你朋友他们不用等了（因为你自己都拿不到书），然后告诉老板，下周你会照常上班。

**Promise**，就像这张收据一样，是替一个还未就绪的值占位的对象，但稍后一定会就绪。换句话说，一个未来的值。你可以把 Promise 当成一个你正在等待的值，并在假设你已获得它的前提下编写代码。

如果在事件过程中出现岔子，Promise 会在自身内部流程去处理中断的地方，并且让你用一个特殊的 `catch` 关键词来处理错误。这和同步式的代码有点不一样，但至少比异步处理一堆错误要舒服。

并且，由于 Promise 会在一个值准备就绪时将其传递给你，你就可以决定要如何使用这个值。这就解决了控制反转的问题：你自己来直接掌握应用的逻辑，无需把控制权转交给第三方。

#### Promise 的生命周期：状态（States）简介

假设你用 Promise 进行了一次 API 调用。

由于服务器不可能立即发回响应，Promise 是不可能立即获得最终值的，也不可能立即报告错误。这个状态下的 Promise 被称为 **Pending**（等候中），好比你在等待订购的那套新书。

一旦服务器发回了响应，就会产生两种可能：

1. Promise 得到了它期待的值，这个状态被称为 **Fulfilled**（已满足）。好比你收到了那套书。
2. 请求事件中出现了错误，这时 Promise 的状态被称为 **Rejected**（被拒绝）。这就好比你收到通知，购书订单无法完成了。

这三个状态就是 Promise 的所有可能状态。一旦 Promise 变成了 Fulfilled 或 Rejected，则无法再转化为任何其他状态了。

关于概念的阐释就说这么远，下面我们来看看究竟怎样应用这些东西。

## Promise 的基础方法

引用 [Promises/A+ 标准](https://promisesaplus.com/)的一段话：

> Promise 代表一个异步操作的最终结果。与 Promise 进行交互的主要方式，是使用它的 `then` 方法。该方法会注册回调函数，接收 Promise 的最终值，或 Promise 无法被满足的原因。

接下来的部分，我们将对 Promise 的基本用法进行详细介绍：

1. 用构造器创建 Promise
2. 用 `resolve` 处理成功事件
3. 用 `rejected` 处理错误事件
4. 用 `then` 和 `catch` 来建立控制流程

在这个例子中，我们将使用 Promise 来搞定上边 `fs.readFile` 的遗留代码。

## 创建 Promise

最基本的 Promise 创建方法就是通过构造器直接创建。

```javascript
'use strict';

const fs = require('fs');

const text = 
  new Promise(function (resolve, reject) {
      // Does nothing
  })
```

注意，我们向 Promise 构造器传递了一个函数作为参数。在这里我们将告诉 Promise 怎样执行异步操作、得到期待值后需要做什么，以及出错了怎么办。细说起来就是：

1. `Resolve` 参数也是一个函数，里面包含当获得**期待值**的时候我们想要做的事。当我们得到了期待值 `val` 的时候，我们这样调用：`resolve(val)`。
2. `Reject`参数也是一个函数，代表得到**错误**的时候我们要怎么做。如果有错误`err`，则要这样调用：`reject(err)`。
3. 最后，我们传递给 Promise 构造器的函数将处理异步代码本身。如果它按期待返回了，我们就调用 `resolve` 并传入获得的返回值。如果抛出错误，我们就调用`reject` 并传入错误。

我们的示例需要把 `fs.readFile` 封装成一个 Promise。那么，应该怎样写 `resolve` 和`reject` 呢？

1. 在成功的事件中，我们用 `console.log` 打印文件内容
2. 在错误的事件中，我们做一样的事：用 `console.log` 打印错误信息

也就是这样：

```javascript
// constructor.js

const resolve = console.log, 
      reject = console.log;
```

接下来，我们需要把传递给构造器的函数补充完整。记住，我们的任务是：

1. 读取一个文件
2. 如果成功，`resolve`（解决）内容
3. 否则，`reject`（拒绝）并给出错误

因此：

```javascript
// constructor.js

const text = 
  new Promise(function (resolve, reject) {
    // Normal fs.readFile call, but inside Promise constructor . . . 
    fs.readFile('text.txt', function (err, text) {
      // . . . Call reject if there's an error . . . 
      if (err) 
        reject(err);
      // . . . And call resolve otherwise.
      else
    // We need toString() because fs.readFile returns a buffer.
        resolve(text.toString());
    })
  })
```

这样，我们就算完成工作了。这段代码会创建一个 Promise，它会严格执行我们想要的逻辑。但是，当你运行这段代码的时候，什么都不会打印出来。

## 她做出了承诺，然后…

原因是，我们写了 `resolve` 和 `reject` 方法，却并没有把它们传递给 Promise！怎么传？很简单，利用 Promise 的流程控制方法 `then` 来完成。

每个 Promise 对象都有一个叫做 `then` 的方法，它仅接受两个参数：`resolve` 和`reject`，且要保证顺序一致。调用 Promise 的 `then` 方法并传递这些函数，才能使构造器里的回调函数访问这两个函数。

```javascript
// constructor.js

const text = 
  new Promise(function (resolve, reject) {
    fs.readFile('text.txt', function (err, text) {
      if (err) 
        reject(err);
      else
        resolve(text.toString());
    })
  })
  .then(resolve, reject);
```

这样，我们的 Promise 会读取文件，在成功时调用 `resolve` 方法。

有个很关键的点值得注意，**then 方法永远会返回一个 Promise 对象**。这就是说，你可以用多个 `then` 来构造复杂的、同步风格的流程控制，以此控制一串异步操作。在下一篇文章中我们会深入这个话题，接下来我们先用 `catch` 范例来领略一下链式调用的风采。

## 捕获错误的语法糖

我们向 `then` 传递了两个方法：`resolve`，用于成功事件；`reject` 用于错误的事件。

Promise 还暴露了一个类似于 `then` 的方法，`catch`。它只接受一个参数，那就是**reject**。

由于 `then` 总是返回一个 Promise，在上面的例子中，我们可以只给 `then` 传递一个`resolve`，然后紧接着用 `catch` 来处理 reject。

```javascript
const text = 
  new Promise(function (resolve, reject) {
    fs.readFile('tex.txt', function (err, text) {
      if (err) 
        reject(err);
      else
        resolve(text.toString());
    })
  })
  .then(resolve)
  .catch(reject);
```

最后，必须要记住，`catch(reject)` 只是 `then(undefined, reject)` 的语法糖。即，我们也可以这样写：

```js
const text = 
  new Promise(function (resolve, reject) {
    fs.readFile('tex.txt', function (err, text) {
      if (err) 
        reject(err);
      else
        resolve(text.toString());
    })
  })
  .then(resolve)
  .then(undefined, reject);
```

但显然还是使用了语法糖的代码更易读。

## 总结

Promise 是异步编程中不可或缺的一个工具。初学时，它们显得有些可怕，但这仅仅是由于你对它还不够熟悉：多用几次，你就能像使用 `if`/`else` 一样使用它了。

下篇文章，我们来实践一下，把基于回调的传统代码用 Promise 进行重构。然后调研一个 Promise 库：[Q](https://github.com/kriskowal/q)。

你还可以阅读一下 Domenic Denicola 的「[States and Fates（状态与命运）](https://github.com/domenic/promises-unwrapping/blob/master/docs/states-and-fates.md)」来全面掌握术语，以及先前我们订购的丛书中 Kyle Simpson 关于 [Promises](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch3.md) 的章节。

照例，如果你有问题，请在下边进行评论，或者在 Twitter 上直接找我（[@PelekeS](http://www.twitter.com/PelekeS)）。我保证（Promise）会回复！