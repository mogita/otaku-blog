---
title: CodeIgniter 的多版本 RESTful 接口
slug: build-versioned-restful-service-with-codeigniter
category: tech
date: 2016-01-17 19:55:34
updated:
tags: [PHP, CodeIgniter, Nginx]
cover:
isCJKLanguage: true
---

[CodeIgniter](https://www.codeigniter.com/) 是基于 PHP 的框架程序，采用 MVC 式设计。配合 [codeigniter-restserver](https://github.com/chriskacerguis/codeigniter-restserver) 能够快速搭建 RESTful 服务。

本文介绍的是在 Nginx 服务器上搭建多版本 RESTful 服务的例子。即创建 `http://api.example.com/v1/user` 这种在 URL 中包含版本号的接口。

## Nginx 配置

首先配置 CodeIgniter 的运行环境，在 Nginx 配置中添加如下代码：

```nginx
server {
    # api server definition

    server_name api.example.com;
    root /your/codeigniter/folder;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php($|/) {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_split_path_info ^(.+\.php)(.*)$;
        fastcgi_param PATH_INFO $fastcgi_path_info;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

其中使用 `try_files` 指令来重写 URL，去掉 CI 默认 URL 中的 `/index.php` 部分。

部署好 CI 的代码之后，访问域名，就能看到 Welcome 画面了。

## 配置 CodeIgniter

下面对 CI 进行以下简单的设置，配合去除 `/index.php` 美化 URL 的工作。

找到 `/application/config/config.php` 文件，将 38 行改成如下内容（行号可能因为具体的 CI 版本而不同，我的版本是 3.0.4）：

```php
$config['index_page'] = '';
```

然后找到同文件中的 55 行，确保内容与下面的相同：

```php
$config['uri_protocol']	= 'REQUEST_URI';
```

## 引入 CodeIgniter Rest Server

下载 [codeigniter-restserver](https://github.com/chriskacerguis/codeigniter-restserver) 后，将压缩包里的`application/libraries/Format.php`、`application/libraries/REST_Controller.php`和 `application/config/rest.php` 三个文件分别放进你自己的 CI 部署中的对应文件夹。

更多 CodeIgniter Rest Server 的使用说明请参考其 readme.md 文件。

## 创建 API 方法和转发规则

我们将通过在 `controllers` 目录下创建子目录的方式来实现多版本 API。在本文撰写之前，曾有 [issue](https://github.com/chriskacerguis/codeigniter-restserver/issues/272#issuecomment-33138531) 表示 CI 只支持在 `controllers` 中创建一层子目录，无法使用多层。而本人实测后，目前版本的 CI 已经支持多层子目录了。下面是创建方法。

在 `controllers` 目录下创建文件夹，命名为 `api`。然后在 `/controllers/api` 下再创建一个文件夹 `v1`。在里面创建一个 `Example.php` 文件，输入如下代码：

```php
defined('BASEPATH') OR exit('No direct script access allowed');
require_once APPPATH . '/libraries/REST_Controller.php';

class Example extends REST_Controller
{
    public function new_get()
    {
        $this->response([
            'error' => 0,
            'data' => 'user data from v1'
        ], REST_Controller::HTTP_OK);
    }
}
```

然后，再回到 `/controllers/api` 目录，创建文件夹 `v2`，在其中创建 `Example.php`文件，输入如下代码：

```php
defined('BASEPATH') OR exit('No direct script access allowed');
require_once APPPATH . '/libraries/REST_Controller.php';

class Example extends REST_Controller
{
    public function new_get()
    {
        $this->response([
            'error' => 0,
            'data' => 'user data from v2'
        ], REST_Controller::HTTP_OK);
    }
}
```

其实两个 `Example.php` 文件的内容大部分相同，唯一的区别是返回的字符串标识出它们来自哪个版本的 API，方便我们测试。

接下来，就是定义 `routes.php` 中的规则了。打开 `/config/routes.php` 文件，在最下边添加如下代码：

```php
$route['v(:num)/(:any)/(:any)'] = 'api/v$1/$2/$3';
```

这就是版本切换魔法的所在，这一行代码表示把任何形如 `http://api.example.com/v1/any_request` 的请求重定向到`/controllers/api/v1/any_request` 这个类中。其中，`v1` 的数字部分可以替换成任何数字，只要存在该数字对应的 API 版本即可访问。详细点说，等号左边的 `(:num)` 和两个 `(:any)` 分别对应右边的 $1、$2 和 $3，代表 URL 中的可变部分：$1 为 API 版本号，$2 为类名，$3 为方法名。也就是说，右边的层级实际上反映出文件系统中的目录和类下方法的层级。

现在，可以直接在浏览器中访问 `http://api.example.com/v1/example/new`，你会看到一串 json 内容，`data` 部分为「user data from v1」。接着，将 URL 中的 v1 换成 v2，`data` 部分就会显示出「user data from v2」。

这样，对 API 用户而言，切换版本号不会影响到已有的大部分代码。当然，前提是他们没有把版本号写死在自己的代码里。

## 总结

用 CodeIgniter 作为 API 后端的框架是个非常好的选择，官方文档和社区贡献内容都较丰富。除此之外，你也可以考虑使用 Slim 或者 Phalcon 等更加轻便的框架。