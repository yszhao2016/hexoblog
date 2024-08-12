---
title: 商城开发-SPU和SKU数据结构设计
abbrlink: ce3caa74
date: 2024-08-12 14:24:31
tags:
---

# 一、SPU和SKU

SPU：Standard Product Unit （标准产品单位） ，一组具有共同属性的商品集

示例 如奶茶商品中的 容量  大 、中 、小 杯
     如奶茶商品中   甜度  少糖 标准  加糖


SKU：Stock Keeping Unit（库存量单位），SPU商品集因具体特性不同而细分的每个商品

示例 如奶茶商品中 【就是 容量 与 甜度 的排列组合】
        
        大杯  少糖
        中杯  少糖
        小杯  少糖
        大杯  标准
        中杯  标准
        小杯  标准
        。。。

# 二、数据库设计
    
## 2.1 spu表

| 字段 | 类型 |	描述 |
|:--------| :---------:|--------:|
| id | int | ID |
| goods_id | int| 商品ID |
| name | varchar | 规格名称 |
| item | text | 规格 |
| status | tinty | 状态 |
| createtime | int | 创建时间 |
| updatetime | int | 更新时间 |

6	2	口味	微辣,中辣,特辣	1723441719	1723441719		0


## 2.2sku 表

| 字段 | 类型 |	描述 |
|:--------| :---------:|--------:|
| id | int | ID |
| goods_id | int| 商品ID |
| thumbnail | text | 缩略图 |
| difference | text | 规格 |
| price | decimal(10,2) | 价格 |
| stock | int | 库存 |
| sn | varchar(200) | 商品标号 |   
| createtime | int | 创建时间 |
| updatetime | int | 更新时间 |


2	2		微辣,小份	1	11	11	1	11	0	1	1723441530	1723441600		normal