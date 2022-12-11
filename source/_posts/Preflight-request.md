---
title: Preflight request
date: 2022-12-02 17:18:24
update: 2022-12-02 17:18:24
tags:
  - HTTP
  - 浏览器
categories: HTTP
description: 'Preflight request'
keywords: 'Preflight request,预检请求,HTTP'
top_img: /assert/index-image.png
cover: false
---

## 预检请求

#### 1. 什么是预检请求？
>一个`CORS`预检请求是用于检查服务器是否支持`CORS`（跨域资源共享），由浏览器自动通过`OPTIONS`方法发起（**非简单请求**才会发起预检请求）。当预检请求完成，服务器确认允许后，才发起实际的HTTP请求。

#### 2. 简单请求与非简单请求
* 简单请求
  * 请求方法：
    * GET
    * HEAD
    * POST
  * 请求首部字段只包含：
    * Accept
    * Accept-Language
    * Content-Language
    * Content-Type
      * text/plain
      * multipart/form-data
      * application/x-www-form-urlencoded 
  * 如果请求是使用XMLHttpRequest对象发出的，在返回的XMLHttpRequest.upload对象属性上没有注册任何事件监听器；也就是说，给定一个XMLHttpRequest实例xhr，没有调用xhr.upload.addEventListener()，以监听该上传请求
  * 请求中没有使用ReadableStream对象

* 非简单请求
  * 不满足简单请求的都是非简单请求

#### 3. 预检请求中的请求首部字段
* Origin：请求源URL（不包含路径）
* Access-Control-Request-Method：实际请求所使用的HTTP方法
* Access-Control-Request-Headers：实际请求时携带的自定义首部字段

{% asset_img request-header.png Preflight request header %}

#### 4. 预见请求中的响应首部字段
* Access-Control-Allow-Origin：允许该源访问资源地址，可以使用`*`通配符，表示任意源（仅当不需要携带`Cookie`时，如果携带`Cookie`，必须指定具体的源地址，否则将会请求失败）
* Access-Control-Allow-Methods：允许使用的HTTP方法
* Access-Control-Allow-Headers：允许使用的请求首部字段
* Access-Control-Allow-Credentials：是否允许携带Cookie
* Access-Control-Expose-Headers：`XMLHttpRequest`对象的 `getResponseHeader()`方法只能拿到一些最基本的响应头，Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma，如果要访问其他头，则需要服务器设置本响应头
* Access-Control-Max-Age：指定预检请求缓存时间，单位为秒，默认5秒

{% asset_img response-header.png Preflight response header %}

#### 5. 完整请求流程图

{% asset_img request-flow.png request flow %}
