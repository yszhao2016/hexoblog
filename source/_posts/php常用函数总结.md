---
title: php常用函数总结
date: 2023-07-25 15:54:20
tags: php开发
---


# 一、字符串处理函数

## 1.1 str_pad()
str_pad(【必填 需要处理的字符串】,【必填 填充的长度】，【 可选 填充的字符串，默认空白】，【可选 填充字符串的哪边】)

    规定填充字符串的哪边 可能的值：
    
    STR_PAD_BOTH - 填充字符串的两侧。如果不是偶数，则右侧获得额外的填充。
    STR_PAD_LEFT - 填充字符串的左侧。
    STR_PAD_RIGHT - 填充字符串的右侧。这是默认的。


示例 $str=10 ; echo str_pad($str,4,"0",STR_PAD_LEFT);  <font color="red">意思就4位长度 不够就往左边补0</font>

## 2.1 strpos、stripos 、strrpos



## 2.3 substr


## 2.4 strtolower strtouplower strlen mb_strlen trim ltrim rtrim explode implode常用

    比较常用，不在赘述
    
    

# 二、数组处理函数