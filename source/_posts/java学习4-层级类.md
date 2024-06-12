---
title: java学习4-层级类
abbrlink: 4a58ed40
date: 2024-06-03 14:16:17
tags:
---


# 数据层

## entity

  entity = 实体
  
  严格和数据库相对应，数据库中有什么字段，entity包中的类就有什么字段。因此当操作表时，操作这个类即可。
  
  比如user表中有name、id、age，则entity包中对应的同名类也只有这三个同名属性
  
  如果是严格对数据库表操作，就用entity
  
  

## model  （概念实体模型）实体类和模型

  当用model当包名时，一般里面存的是实体类的模型，是用来给后端用的。

  比如user表中有name、id、age，出于安全原因，我们需要把用户的密码定义在另一表中，即user_passwd表，
  但进行相关操作时，我们往往需要将两个表关联使用，每次定义都很麻烦。

  因此可以在model层中定义user_model类，将user表中的信息与user_passwd表中的信息整合成一张综合表，
  这样在进行操作时只需调用综合表，就可以完成对两个表的关联操作

  如果想显示某个几个表的综合信息，就用model，注意model包一般放在service层

## DTO （Data Transfer Object）数据传输对象

   Data Transfer Object数据传输对象的简称，多用于多个系统之间，网络通信，数据库存取等。
   将调用或返回的数据保存在DTO中从而减少调用负载。它不包含业务逻辑处理，但可以有数据整合性的校验。
   DTO需要注意的是必须序列化，即implements Serializable，
   一般用于接口间调用参数的传递，或访问DAO层传递条件参数使用。
   
   一般来说，DTO是用于在不同层之间传输数据的一个普通Java类。
   它通常用于封装从数据库查询结果中提取的数据，或者用于在应用程序的不同模块之间传递数据。
   

   
  <font color="red"> 

  一般在 前端（Web） 
   
   对控制层（Controller）进行数据传输时使用，说白了就是
   
   前端向后台
   提交数据。
   
   xxxDTO，xxx为业务领域相关的名称
   </font>

   

## VO （View Object）视图模型

   Value Object值对象的简称，主要体现在视图的对象，
   对于一个WEB页面将整个页面的属性封装成一个对象，然后用一个VO对象在控制层与视图层进行传输交换，
   比如页面要显示100个字段，而实体只有10个，就可以封装一个VO返回。
<font color="red">   
   一般用在业务逻辑层（Service） 
   
   对前端（Web） 的 视图模型效果控制的展示上，说白了就是
   
   后台向前端
   传输数据。
   
   xxxVO，xxx一般为网页名称
</font>   
   
## Form

   主要用于接收页面表单中的数据或者页面显示用数据的存储，
   包含校验Validate方法,一般用于controller层用来接收前端传过来的参数   
   
   
## domain
  domain = 域
  
  域是一个大范围，如简历域包括工作经验表、项目经验表、简历基本信息表。 
  在domain包中，就可以定义一个大的简历对象，将三个表的内容整合在一个对象中，作为整体操作。  
  
  如果想对几个表综合操作，就用domain
   
 
  
# 数据访问层  


## DAO （Data Access Object）数据访问对象
   
   DAO(Data Access Object)是一个数据访问接口，数据访问：顾名思义就是与数据库打交道。
   夹在业务逻辑与数据库资源中间。
<font color="red">  
   一般在 业务逻辑层（Service） 

  对

  数据库（SQL） 的访问时使用，一般能对SQL进行操作。

  xxxDAO，xxx即为实体类名（Entity实体）   
</font>    


## Mapper