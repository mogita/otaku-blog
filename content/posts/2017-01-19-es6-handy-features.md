---
title: 8 个 ES6 实用特性
slug: es6-handy-features
category: tech
date: 2017-01-19 10:45:49
updated:
tags: [ES6, JavaScript]
cover:
isCJKLanguage: true
---

## 模板字符串（Template String）

```js
var block = `<div>
    <p>hello</p>
</div>`;

console.log(block);
```

输出

```html
<div>
    <p>hello</p>
</div>
```

------

## 箭头函数

解决父级作用域无法被回调函数访问的问题。例如下列 ES5 代码，必须在回调函数后添加 `.bind(this)` 才可以正确运行：

```js
var obj = {
    names: ['mogita', 'me', 'zhiyi'],
    domain: 'mogita.com',
    method: function() {
        var mapEmail = function(item) {
            return item + '@' + this.domain;
        }.bind(this)
        
        return this.names.map(mapEmail);
    }
};

console.log(obj.method());
```

输出

```js
[ 'mogita@mogita.com', 'me@mogita.com', 'zhiyi@mogita.com' ]
```

#### ES6 用箭头函数替代

```js
var obj = {
    names: ['mogita', 'me', 'zhiyi'],
    domain: 'mogita.com',
    method: function() {
        return this.names.map(item => {
            return `${item}@${this.domain}`;
        });
    }
};

console.log(obj.method());
```

输出

```js
[ 'mogita@mogita.com', 'me@mogita.com', 'zhiyi@mogita.com' ]
```

## Class 语法糖

Javascript 中并没有真正的「类」，一直以来多用原型扩展的方式来构建一个类，如下列 ES5 代码：

```js
function Obj() {
    console.log('Obj constructed');
}

Obj.prototype.method = function() {
    console.log('This is Obj\'s method');
};

console.log('About to construct');
var obj = new Obj();
obj.method();
```

输出

```
About to construct
Obj constructed
This is Obj's method
```

#### ES6 的 Class 语法糖

```js
class Obj {
    constructor() {
        console.log('Obj constructed');
    }
    
    method() {
        console.log('This is Obj\'s method');
    }
}

console.log('About to construct');
var obj = new Obj();
obj.method();
```

输出

```
About to construct
Obj constructed
This is Obj's method
```

## 模块

方便在不同 `js` 文件之间共用代码，使项目模块化。

#### 导入模块

```js
import something from 'framework';
import * as something from 'framework';
import {matchedProp} from 'framework';
```

#### 导出接口

```js
export default function something {}

export var value = 'value';
export var another = 'another';

export var matchedProp = 'matched';
```

## 反构造（Destructure）

Angular 等现代前端框架是用 TypeScript（ES6 的一个超集）编写的，反构造在导入语句中很常见，它可以用来选择所需的具体模块。

例如 ES6 代码，只引用对象的若干选定属性：

```js
var obj = {
    a: 1,
    b: 2,
    c: 3
};

var {a, c} = obj;
console.log(a, c);
```

输出

```
1 3
```

## 剩余参数

如果最后一个命名了的参数前面加了三个点，该参数将含有该位置以及向后的所有传入参数。例如：

```js
function hello(...numbers) {
    numbers.forEach(function (item) {
        console.log(item);
    });
}

hello(1, 2, 3, 4);
```

输出

```
1
2
3
4
```

## 铺展操作符

铺展操作符可以视为「剩余参数」的反向概念，用来动态扩展参数。例如：

```js
function print(x, y, z) {
    console.log(x + y + z);
}

var params = [1, 2, 3];

print(...params);
```

输出

```
6
```

## 对象字面量语法糖

可以让对象属性名由变量组成。例如：

```js
var obj = {
    ['var_' + 42]: 'life'
}

console.log(obj.var_42);
```

输出

```
life
```

