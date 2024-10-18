---
title: php开发-fastadmin中搜索日期时间的处理
abbrlink: cb6a68f0
date: 2024-08-13 19:22:27
tags: fastadmin
categories:
  - PHP开发
---


# 问题

在使用FastAdmin时，遇到日期时间搜索的问题，JS中的RANGE或BETWEEN操作生成的SQL总是针对时间戳的区间查询。
通过分析代码发现，RANGE操作符会自动转为BETWEEN TIME，而BETWEEN操作符在含有datetimepicker样式的输入框中会被强制转换为RANGE。
为了解决这个问题，屏蔽了bootstrap-table-commonsearch.js中将operate置为RANGE的代码段，
实现了对日期时间的正确搜索



正常一般这么写
```js
  {field: 'notice_time',title: "通知时间",operate: 'Range',addclass: 'datetimerange',formatter: Table.api.formatter.datetime,data:'data-date-format="YYYY-MM-DD"'},
```

fastadmin 后端处理 BETWEEN TIME

也就是把搜索条件值转换为时间戳  进行between 搜索

当notice_time 存的是“xxxx-xx-xx”格式 就搜索不到



那就只能用
```js
  {field: 'notice_time',title: "通知时间",operate: 'BETWEEN',addclass: 'datetimepicker',formatter: Table.api.formatter.datetime,data:'data-date-format="YYYY-MM-DD"'},
```

但是bootstrap-table-commonsearch.js 中有将datetimepicker样式的operate字段  强制转为Range了
```js
  if ($("[name='" + name + "']:first", that.$commonsearch).hasClass("datetimepicker")) {
                       sym = 'RANGE';
  }
```


# 解决方案

## 方案一
bootstrap-table-commonsearch.js 中这段代码注释


重新生成bootstrap-table-commonsearch.min.js

 php think min -m backend -r all

 或者直接修改bootstrap-table-commonsearch.min.js

## 方案二

修改数据库字段类型 存储时间戳
