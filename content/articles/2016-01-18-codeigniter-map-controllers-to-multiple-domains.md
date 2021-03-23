---
title: 多个域名访问同一个 CodeIgniter 的不同 Controllers
slug: codeigniter-map-controllers-to-multiple-domains
date: 2016-01-18 23:03:17
updated:
tags: [PHP, CodeIgniter]
cover:
share_cover:
---

一套 CodeIgniter 部署，可以承载不同的业务：不同的 MVC 模块，用不同的域名访问。严格来讲，要让一个模块独立形成一套 MVC，需使用 wiredesignz 的 [codeigniter-modular-extensions-hmvc](https://bitbucket.org/wiredesignz/codeigniter-modular-extensions-hmvc) 扩展。后者则是 CI 自身的路由所提供的特性。

关于 HMVC 的讲解之后有时间再写。本文只讲如何用不同域名访问不同的 Controllers。

## 配置 Nginx

我的服务器环境基于 Nginx，所以这里贴出我的代码（需修改自定义部分后才可复用）。

```nginx
server {
  server_name     user.example.com admin.example.com;
  root            /path/to/code/base;
  index           index.php index.html index.htm;

  location / {
      try_files $uri $uri/ /index.php?$query_string;
  }

  location ~ \.php($|/) {
    fastcgi_pass   127.0.0.1:9000;
    fastcgi_index  index.php;
    fastcgi_split_path_info ^(.+\.php)(.*)$;
    fastcgi_param   PATH_INFO $fastcgi_path_info;
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    include        fastcgi_params;
  }

  location ~ /\.ht {
    deny  all;
  }
}
```

注意把所有域名放在 `server_name` 的同一行，中间用空格分开。

我们照例使用 `try_files` 去除 URL 中的 index.php。

## 部署 CodeIgniter 并配置 routes.php

部署过程就不细说了，若有问题直接甩评论上来吧。

仅以演示为目的，我们进入 `/application/controllers` 目录，把 CI 自带的`Welcome.php` 代码修改成如下内容：

```php
defined('BASEPATH') OR exit('No direct script access allowed');

class Welcome extends CI_Controller {

  public function index()
  {
    echo 'Hello user';
  }
}
```

然后，复制 `Welcome.php`，并命名为 `Welcome_admin.php`，并对文件内容也进行相应的修改：

```php
defined('BASEPATH') OR exit('No direct script access allowed');

class Welcome_admin extends CI_Controller {

  public function index()
  {
    echo 'Hello admin';
  }
}
```

接着，让魔法在 `routes.php` 文件里发生吧。打开 `/application/config` 目录中的`routes.php`。在已有代码的后面插入下列代码：

```php
if ($_SERVER['HTTP_HOST'] == 'user.example.com')
{
  $route['(:any)'] = 'welcome';
}
elseif ($_SERVER['HTTP_HOST'] == 'admin.example.com')
{
  $route['(:any)'] = 'welcome_admin';
}
```

现在，分别访问 `http://user.example.com` 和 `http://admin.example.com`，你将看到两个不同的控制器所生成的页面。即达到了多域名访问不同控制器的目的。

再配合 HMVC 扩展，我们可以将控制器分成一个个独立的模块，与 CI 的基础代码解耦，让开发时条理更加清晰。