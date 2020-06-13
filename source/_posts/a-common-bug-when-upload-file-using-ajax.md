---
title: ajax上传文件时碰到的一个小坑
date: 2020-05-08 17:11
tags:
- Javascript
---

今天调试一个ajax文件上传接口，拿到的数据看起来是标准的json字符串，但是浏览器执行始终跟期望不一样。
```javascript
				$.ajax({
						type        : 'POST',
						data        : data,
						url :         'upload.php',
            cache:     false,
						processData: false,
						contentType: false,
						success :     function(data){
              console.log(data);
              console.log(data.code);
							var code = data.code;
							var msg  = data.msg;
							switch(code){
								case 1:
									$('#health_doc').val(msg);
									$('#show_plan_file').hide();
									break;
								default:
									layer.alert(msg, {icon: 5});
									break;
							}
						}
					});
```

上图代码中，`console.log(data)`打印正常,而`console.log(data.code)`打印结果为`undefined`。 于是，再追加一条打印语句`console.log(typeof data)`，发现结果竟然是`string`，看来json字符串没有被浏览器自动解析。经检查Response Header，找到问题了。原来是接口返回的`header`头不对，其他接口都返回`Content-Type: application/json`, 唯独这个接口返回的是`Content-Type: text/html`，导致返回的json字符串没有办法被浏览器自动解析。
再追究一层，发现Response Header的设置跟ajax请求时有关，我们为了实现文件上传，给ajax设置了`contentType`属性设置为`false`，这样后端就无法根据请求`contentType`自动给出正确的响应头`Content-Type`设置。

解决这个问题有两种方案：
一是在后端输出请求响应内容前，设置Http响应头信息为`application-json`。假设后端是php，可以如下设置：
```php
header("Content-type: application/json");
```
另一种方案是后端不处理，前端js拿到响应字符串后自己通过`JSON.parse(data)`手动转为json对象。