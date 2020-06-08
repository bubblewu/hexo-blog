---
title: curl发送HTTP请求
tags: curl
categories: Linux
date: 2020-05-20 21:58:19
---
使用curl发送HTTP请求，用于简单验证接口连接性。
<!-- more -->

## GET 请求示例
```bash
# GET请求 -i
curl -i <url>
# 返回示例：
# HTTP/1.0 200 OK
# Content-Type: application/json
# Content-Length: 151
# Server: Werkzeug/0.8.3 Python/2.7.3
# Date: Mon, 20 May 2013 05:21:50 GMT
#
# {
#   "data": {
#     "description": "Need to find a good Python tutorial on the web",
#   },
#   "code": 200
# }
```

## POST 请求示例
```bash
# POST请求示例 -i
curl -i -H "Content-Type: application/json" -X POST -d '{"data":"data"}' <url>
# HTTP/1.0 201 Created
# Content-Type: application/json
# Content-Length: 104
# Server: Werkzeug/0.8.3 Python/2.7.3
# Date: Mon, 20 May 2013 05:56:21 GMT
#
# {
#   "data": {
#     "description": "Need to find a good Python tutorial on the web",
#   },
#   "code": 200
# }
```

## curl 带密码请求
```bash
# -u username:password -i url
curl -u miguel:python -i <url>

# HTTP/1.0 200 OK
# Content-Type: application/json
# Content-Length: 316
# Server: Werkzeug/0.8.3 Python/2.7.3
# Date: Mon, 20 May 2013 06:46:45 GMT
#
# {
#   "data": {
#     "description": "Need to find a good Python tutorial on the web",
#   },
#   "code": 200
# }
```

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}