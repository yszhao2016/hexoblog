---
title: php开发-无法解析json
abbrlink: 151712b4
date: 2025-12-30 11:57:33
tags:
---


# php开发-无法解析json

   问题描述： 无法解析回调的json数据，json_decode() 函数返回null,接收的数据直接放postman 中解析正常
   file_get_contents('php://input')接收到就有乱码
```php
 $HEX="7b2273657269616c4e756d626572223a22323032353132323331373330333539333"
   ."6354646222c226f726465724964223a22464c4f5730303132303030313030323531323233"
   ."313733303436383134313439222c22737461747573223a2d312c226d657373616765223a22"
   ."453c3125de031ae51a453c3125286661696c656429222c22766f7563686572223a226661696c6564227d"
```
  
```json
   {"serialNumber":"2025122317303593751B","orderId":"FLOW0012000100251223173046814154","status":-1,"message":"E<1%?^C^Z?^ZE<1%(failed)","voucher":"failed"}
```
 

   解决方法：
      <font color="red">有换行符之类POSIX字符造成的</font>
      <font color="red">最终问题可能在php接收前，那边被转码造成的,因为php端接收到就是乱码了</font>

```php
    $converted = mb_convert_encoding($content, 'UTF-8', "UTF-8");
    $clean = preg_replace('/[[:cntrl:]]/', '', $converted);
    $data = json_decode($clean, true);
```



