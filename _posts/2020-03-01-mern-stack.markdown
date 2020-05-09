---
layout: post
title: M.E.R.N 全栈开发架构
date: 2020-03-01 22:23:45 +0800
categories: [Javascript, Fullstack]
tag: [Javascript, Fullstack, MongoDB, Express, React.js, Node.js]
---

![MERN](/assets/images/mern.jpg?style=centerme)

# 全栈开发：

<!--more-->

全栈的概念是随着 WEB 开发的复杂度上升而提出来的，即覆盖网络应用程序开发的前端、中间层、后台、数据库的各个层面。最初由于技术的限制，不同开发层面的开发语言、工具都不相同，如前端需要掌握 HTML、CSS、Javascript、ASP、PHP，后台需要掌握 Java、C#、.Net Framework，数据库需要掌握 SQL 以及关系型数据库的用法，数据库访问需要各种 ORM 做映射与隔离......总体而言学习难度很大。

随着 Google V8 引擎的出现，Node.js 让 Javascript 成为了后台应用程序的编程语言，前后端开发统一的趋势就逐步明确了，接下来我就对 M.E.R.N.这个 Javascript 全栈做一个笔记。

- V8 引擎：Google 用 C++写的 Javascript 引擎，采用的是 ECMA-262 标准。[github 源码镜像地址](https://github.com/v8/v8)
- Node.js：用 V8 引擎实现的异步事件驱动的 Javascript 运行时，让开发人员用 Javascript 构建网络应用程序后台应用成为可能。

# MERN 框架：

MERN 指的是：MongoDB、Express、React.js/React Native 和 Node.js 四个首字母的缩写，分别是数据库（NoSQL）
、Web 服务器、前端框架以及后台服务器运行时，通过该全栈架构，开发人员就可以用 Javascript 实现完整的 Web 开发以及移动端开发（通过 React Native）了。

1. Node.js

   [官方网站](https://nodejs.org/en/)

   | #   | 命令行                             | 说明                                  |
   | --- | ---------------------------------- | ------------------------------------- |
   | 0   | npm init -y                        | 初始化一个空项目，将生成 package.json |
   | 1   | node script.js                     | 运行 script.js                        |
   | 2   | Global Objects: global             | console,process                       |
   | 2.1 | console.log                        |                                       |
   | 2.2 | console.error                      |                                       |
   | 2.3 | console.time(label)/timeEnd(label) |                                       |
   | 2.4 | process.stdout                     |                                       |
   | 2.5 | process.argv                       |                                       |
   | 2.6 | process.env                        | PORT,                                 |
   | 2.7 | process.config                     |                                       |
   | 2.8 | process.version                    | node 版本                             |
   | 2.8 | process.platform                   | 运行操作系统平台信息                  |
   | 3   | const varName=require('module')    | 引入模块 module 并赋值给 varName 命令 |
   | 4   | os, fs, events, http               | 相关核心模块                          |

2. Express

   [官方网站](http://expressjs.com/)

   | #   | 对象\语句                          | 说明               |
   | --- | ---------------------------------- | ------------------ |
   | 0   | const express = require('express') | 导入 express       |
   | 1   | request                            | params, query,body |
   | 2   | response                           | send(),status()    |
   | 3   | get()                              | 处理 get           |
   | 3   | post()                             | 处理 post          |
   | 4   | listen()                           | 监听端口           |
   | 5   | use()                              | 加载中间件         |

3. React.js/React Native

   React.js 相关资料很多，[官方网站](https://reactjs.org/)

   | #   | 对象\语句 | 说明                                            |
   | --- | --------- | ----------------------------------------------- |
   | 0   | Hook      | 新引入的概念                                    |
   | 1   | Context   | 避免属性的层层传递                              |
   | 1   | JSX       | Javascript Syntax eXtension,javascript 语法扩展 |

4. MongoDB

   [官方网站](https://www.mongodb.com/)

   有用的相关软件及框架：

   - MongoDB Compass：MongoDB 官方客户端
   - Mongoose：简化了对 MongoDB 的相关操作，为编程实现提供了统一接口。
     相关概念：
     - 集合（Collection）：相当于关系数据库的库表（Table），是同类文档（Document）的集合。
     - 文档（Document）:相当于关系数据库的记录（Row），构成类似于 JSON 对象，用于存储键-值对（Key-Value Pair），值可以包含其他文档、数组。
     - 纲要（Schema）：Mongoose 的概念，每个 Schema 都对应到 MongoDB 的一个 Collection，是对应 Document 的模板。
     - 模型（Model）：Mongoose 的概念，实例化 Schema，并对应实现相关 document 的增删改查的各种操作。

# 有用的安装包：

| #   | 安装包               | 说明                                                  |
| --- | -------------------- | ----------------------------------------------------- |
| 0   | lodash               | 高性能的 Javascript 工具库                            |
| 1   | config               | 获取配置数据                                          |
| 2   | Joi -> @hapi/joi     | Schema 数据规范及验证工具                             |
| 3   | jsonwebtoken         | 生成 token                                            |
| 4   | express-async-errors | 用于 express，处理异步错误                            |
| 5   | winston              | logger 工具                                           |
| 6   | Jasmine              | 自动测试工具                                          |
| 7   | Jest                 | 专用于 React.js 的测试工具                            |
| 7.1 | Jest 相关            | describe, it, expect, fn(), beforeEach(), afterEach() |
| 8   | Postman              | 用于 API 测试，[官方网站](https://www.postman.com/)   |
