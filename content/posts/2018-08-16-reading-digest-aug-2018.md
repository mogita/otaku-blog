---
title: Reading Digest
slug: reading-digest-aug-2018
date: 2018-08-16 17:21:30
updated:
tags:
cover:
desc:
share_cover:
from:
---

# Go

Accessing the database with Go needs the `database/sql` package. Apart from reading the official docs, this web site makes it even clearer on "how to" and "what not to do" matters. Learn idiomatic operations on it as well.

- [Go database/sql Tutorial](http://go-database-sql.org/index.html)

While designing the inforcement for our data security, I came across an article which explained briefly and clearly on SQL injection and how to avoid it in Go.

- [What is SQL injection and how do I avoid it in Go?](https://www.calhoun.io/what-is-sql-injection-and-how-do-i-avoid-it-in-go/)

It's also worth a while reading this article, also on the topic of SQL injection in Go. In fact it belongs to a big collection of articles on Go application building.

- [9.4 SQL injection](https://astaxie.gitbooks.io/build-web-application-with-golang/en/09.4.html) (from the collection of [Build web application with Golang](https://astaxie.gitbooks.io/build-web-application-with-golang/en/) )

# WebAssembly

As [WebAssembly Specification](https://webassembly.github.io/spec/core/index.html) draft goes 1.0 yesterday, it's time to take a closer look at the essential ideas of WebAssembly. Currently it supports C, C++ and Rust. Which one to take? Hmm...

- [WebAssembly: How and why](https://blog.logrocket.com/webassembly-how-and-why-559b7f96cd71)

# Metabase

This isn't a reading item actually lol. The [Metabase](https://github.com/metabase/metabase) is a tool that is `The simplest, fastest way to get business intelligence and analytics to everyone in your company`. In short, it's an open-source BI software.

One of our QA team member showed me this the other day. It supports running as a Java jar package, a docker image, on AWS and some other options. The setup process was even easier than setting up a new WordPress instance.

The Metabase astonished me with its simplicity of design, both on the workflow and the UI. I "ask" it questions, it shows me the answers. That's it.

Digging deeper, I can gather the answers and make a dashboard to visualize the data. Or I can see the SQL it generates if I want to.

Here's a screenshot from its repo:

![metabase screenshot](/img/metabase-product-screenshot.png)
