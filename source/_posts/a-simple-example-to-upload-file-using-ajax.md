---
title: 一个简单的ajax上传文件方案
date: 2020-05-08 17:48
tags:
- Javascript
---

一般在前端上传文件我习惯用百度的[WebUploader](https://fex.baidu.com/webuploader/)，不过有时候也会用到一些简单的方案。这里就记录一个比较简单的ajax上传文件方案。

### 前端
1. html部分
```html
<form enctype="multipart/form-data">
    <p>选择文件：<input type="file" id="upload_file" name="upload_file"/></p>
    <button id="submit">提交</button>
</form>
```
2. js部分
首先需要依赖`jquery`,请自行引入，这里不再描述。
```javascript
<script type="application/javascript">
$(function() {
    // 选择1：点击”提交“按钮时上传
    $('#submit').click(function() {
        ajaxUpload();
    });
    // 选择2：文件对象改变时触发上传
    $('#upload_file').change(function(){
        ajaxUpload();
    });
    function ajaxUpload() {
        var files = $('#upload_file').prop('files');
        var data = new FormData();
        data.append('file', files[0]);
        
        $.ajax({
            type: 'POST',
            url: 'upload.php',
            data: data,
            cache: false, // 兼容ie8，防止ie8之前版本缓存get请求的处理方式
            contentType: false, // 避免jquery误解
            processData: false, // 避免发送的数据被默认转为"application/x-www-form-urlencoded"
            success: function(data) {
                console.log(data);
                console.log(typeof data);
                // 拿到后端传回的响应信息后，做后续业务处理
            }
        })
    }
})
</script>
```

### 后端
1. 我这里在后端处理文件上传的语言是`upload.php`。它的主要内容如下：
```php
// in upload.php
header("Content-type: application/json");
try {
    $uploadFile = $_FILES['csv_file'];
    $originName = basename($uploadFile["name"]); //被上传文件的名称
    $tmpName = $uploadFile['tmp_name'];
    
    //检查或创建保存目录
    $savePath = '../upload/' . date("Y_m_d/");
    if(!file_exists($savePath)){
        mkdir($savePath);
    }
    
    //文件重命名
    $newName = $originName; // 为简单起见，这里在保存文件时未修改文件名
    
    //保存文件
		$saveFile = $savePath.$newName;
		if(move_uploaded_file($tmpName, $saveFile)){
		    die(json_encode('code'=>1, 'msg'=>$newName));//上传成功，返回文件名。
		} else {
			throw new Exception('文件写入失败，请检查上传目录是否可写', -1);
		}
} catch (Exception $e) {
    die(json_encode('code'=>-1, 'msg'=>$e->getMessage()));
}
```
