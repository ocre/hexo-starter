---
title: Python的WSGI是什么？
date: 2020-02-20 08:26:10
tags: 
- Python
---

## 什么是WSGI？

WSGI的全称是Web Server Gateway Interface，即Web服务器网管接口。注意，它不是一个服务器、不是Python模块、不是框架、也不是API程序，它不是任何一种软件，而仅仅是Python语言针对Web服务器和Web应用程序之间通用接口的规范（[PEP 3333](https://www.python.org/dev/peps/pep-3333)）。符合WSGI规范的应用程序可以运行在任何符合该规范的Web服务器上。

## WSGI规范
WSGI规范十分简单。下面这张时序图展示了WSGI所处的位置，以及调用规则。
```sequence
Client->Server: request
Note right of Server: WSGI
Server->Application: app_callable(environ, start_response)
Application->Server: start_response(status, response_headers, exc_info=None)
Application->Server: return iterator
Server->Client: response
```

从上图可见，WSGI处于`Server`和`Application`之间。`Server`端负责实现`start_response`这个`callback`，`Application`端负责实现`app_callable`这个`callable`对象。
其中，`app_callable`接受两个参数：
* `environ`: 包含有`Server`提供的所有请求信息的一个`dict`对象。
* `start_response`: `Server`端提供的回调方法，`Application`端可以通过它发送HTTP状态码和HTTP头部信息。
`app_callable`最后返回一个封装成可迭代对象的响应体字符串。

以下是一个简单的`app_callable`实现：
```python
def app_callable(environ, start_response):
    response_body = 'Request method: %s' % environ['REQUEST_METHOD']
    status = '200 OK'
    response_headers = [('Content-type', 'text/plain')]
    
    start_response(status, response_headers)
    
    return [response_body]
```

`WSGI`的`Application`端可以支持堆栈式调用。调用栈中间的`Application`又被称为`Middleware`。`Middleware`同时扮演`Server`和`Application`两种角色，因此需要同时实现`WSGI`两端的接口。
下图是对`Middleware`所处位置的一个简单表示：

```sequence
Note right of Server: WSGI
Server->Application1: 
Note right of Application1: WSGi
Middleware1->Application1: 
Note right of Middleware1: WSGI
Application1->Middleware2: 
Note right of Middleware2: WSGI
Middleware2->Middleware3: 
Note right of Middleware3: WSGI
Middleware3->Application2: 
Application2->Middleware3: 
Middleware3->Middleware2: 
Middleware2->Middleware1: 
Middleware1->Application1: 

```

## WSGI实现示例
在生产环境，一般用`Apache`+`mod_wsgi`来作为`Server`端的标准实现。这里我们使用Python内置的WSGI服务器`wsgiref`来实现一个简单示例。
编写一个`wsgi_test.py`文件，内容如下：
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
"""test wsgi
"""

# wsgi_test.py

from wsgiref.simple_server import make_server

def application (environ, start_response):
    status = '200 OK'
    response_headers = [('Content-Type', 'text/plain')]
    start_response(status, response_headers)

    return [f'Request {environ["REQUEST_METHOD"]}'
            f' {environ["PATH_INFO"]} has been'
            f' processed\r\n'.encode('utf-8')]

server = make_server('localhost', 8000, application)

# Wait for a single request, serve it and quit
print('Serving HTTP on port 8000...')
server.serve_forever()
```
接着在命令行运行`python wsgi_test.py`，启动WSGI服务器。然后打开浏览器，输入`http://localhost:8000/` 就可以看到效果了。