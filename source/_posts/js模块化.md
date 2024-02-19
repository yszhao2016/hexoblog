---
title: JS模块化
abbrlink: b2e052be
categories:
  - 前端
date: 2022-02-26 10:48:58
tags:
---

## CommonJs规范简  
    前端模块化
    
    最初是服务于服务端的 但它的载体是前端语言 JavaScript
    commonjs 随着 nodejs 的诞生而面世，主要是用来解决服务端模块化的问题
    Node.js 应用由模块组成，每个文件就是一个模块，有自己的作用域。
    在一个文件里面定义的变量、函数、类，都是私有的，对其他文件不可见
    
     CommonJS 规范的模块时，无外乎就是使用了 require 、 exports 、 module 三个东西，
     然后一个 js 文件就是一个模块
     
     弊端require 多个文件  网络加载问题

### require

    用来加载某个模块


### module

    module 代表当前模块，是一个对象，保存了当前模块的信息
    exports 是 module 上的一个属性，保存了当前模块要导出的接口或者变量，
    使用 require 加载的某个模块获取到的值就是那个模块使用 exports 导出的值
    
```
    // a.js
    var name = 'morrain'
    var age = 18
    module.exports.name = name
    module.exports.getAge = function(){
        return age
    }
    
    //b.js
    var a = require('a.js')
    console.log(a.name) // 'morrain'
    console.log(a.getAge())// 18
```

### exports

    为了方便，Node.js 在实现 CommonJS 规范时，为每个模块提供一个 exports的私有变量
    为每个模块提供一个 exports的私有变量，指向 module.exports
    你可以理解为 Node.js 在每个模块开始的地方，添加了如下这行代码。
    
    exports 是模块内的私有局部变量，它只是指向了 module.exports
    所以直接对 exports 赋值是无效的
    
```
    //a.js
    var name = 'test'
    var age = 18
    exports.name = name
    exports.getAge = function(){
        return age
    }
    
    
    //b.js
    
    a = require('a.js')
    console.log(a.name)
    a.name
```
     
    
## AMD(Asynchronous Module Definition 异步模块定义)   require.js

    　　（1）实现js文件的异步加载，避免网页失去响应；
    
    　　（2）管理模块之间的依赖性，便于代码的编写和维护。

```
define(id?, dependencies?, factory)

id：可选参数，用来定义模块的标识，如果没有提供该参数，就使用 js 文件名（去掉拓展名）对于一个 js 文件只定义了一个模块时，这个参数是可以省略的

dependencies：可选参数，是一个数组，表示当前模块的依赖，如果没有依赖可以不传

factory：工厂方法，模块初始化要执行的函数或对象。如果为函数，它应该只被执行一次，返回值便是模块要导出的值。
如果是对象，此对象应该为模块的输出值

```


```
// a.js
define(function(){
    var name = 'morrain'
    var age = 18
    return {
        name,
        getAge: () => age
    }
})
// b.js
define(['a.js'], function(a){
    var name = 'lilei'
    var age = 15
    console.log(a.name) // 'morrain'
    console.log(a.getAge()) // 18
    return {
        name,
        getAge: () => age
    }
})
```


## CMD (Common Module Definition)  Sea.js
     
     Sea.js
     
```
<script src="sea.js"></script>
<script src="b.js"></script>
// a.js
define(function(require, exports, module){
    var name = 'morrain'
    var age = 18

    exports.name = name
    exports.getAge = () => age
})
// b.js
define(function(require, exports, module){
    var name = 'lilei'
    var age = 15
    var a = require('a.js')

    console.log(a.name) // 'morrain'
    console.log(a.getAge()) //18

    exports.name = name
    exports.getAge = () => age
})
```

## ES6 Moudule