---
title: php开发-跨域问题总结
abbrlink: 54e89ac9
date: 2023-07-21 16:26:13
tags:
---


# 一、什么是跨域

    常见场景 

    前端页面访问地址abc.com  访问后台地址xyz.com  
    前端页面访问地址https://abc.com  访问后台地址http://abc.com 
    ...

    总结 前端访问地址 与 后端接口地址 协议、域名、端口均相同 为同源



# 二、解决方案

## 2.1jsonp 解决方案（只支持get请求）

        利用 <script>标签没有跨域限制的特点，动态创建一个 script标签，
        其 src指向一个后端接口，并提供一个回调函数名作为参数（如 ?callback=handleResponse）。
        服务器接收到请求后，将数据包装在该回调函数中返回，形如 handleResponse({data: ...})。当脚本加载到浏览器时，回调函数会自动执行。

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>JSONP 实例</title>
</head>
<body>
<div id="divCustomers"></div>
<script type="text/javascript">
function callbackFunction(result, methodName)
{
    var html = '<ul>';
    for(var i = 0; i < result.length; i++)
    {
        html += '<li>' + result[i] + '</li>';
    }
    html += '</ul>';
    document.getElementById('divCustomers').innerHTML = html;
}
</script>
<script type="text/javascript" src="https://www.runoob.com/try/ajax/jsonp.php?jsoncallback=callbackFunction"></script>
</body>
</html>
```


```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>JSONP 实例</title>
    <script src="https://cdn.static.runoob.com/libs/jquery/1.8.3/jquery.js"></script>    
</head>
<body>
<div id="divCustomers"></div>
<script>
$.getJSON("https://www.runoob.com/try/ajax/jsonp.php?jsoncallback=?", function(data) {
    
    var html = '<ul>';
    for(var i = 0; i < data.length; i++)
    {
        html += '<li>' + data[i] + '</li>';
    }
    html += '</ul>';
    
    $('#divCustomers').html(html); 
});
</script>
</body>
</html>
```


## 2.2 反向代理方案

    nginx 配置实现 

    中转方式（代码请求返回）

    前端代码自己代理



## 2.3 cors 方案 也就是 后端服务器允许跨域

    php代码方式

```php
header("Access-Control-Allow-Origin:*");
header("Access-Control-Allow-Methods:GET, POST, OPTIONS, DELETE");
header("Access-Control-Allow-Headers:x-requested-with, Referer,content-type,token,DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type, Accept-Language, Origin, Accept-Encoding");
```

```nginx
# 设置CORS头
add_header Access-Control-Allow-Origin "https://www.example.com" always;
add_header Access-Control-Allow-Methods "GET, POST, OPTIONS, PUT, DELETE" always;
add_header Access-Control-Allow-Headers "Origin, X-Requested-With, Content-Type, Accept, Authorization" always;
add_header Access-Control-Allow-Credentials "true" always;

# 处理预检请求
if ($request_method = 'OPTIONS') {
    return 204;
}
```
   
    