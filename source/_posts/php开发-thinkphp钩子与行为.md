---
title: php开发-thinkphp钩子与行为
tags: PHP开发
categories:
  - PHP开发
abbrlink: 9d1b4110
date: 2024-03-19 11:11:00
---


# 系统预留钩子

    app_init        应用初始化标签位
    app_dispatch    应用调度标签位
    app_begin       应用开始标签位
    module_init     模块初始化标签位
    action_begin    控制器开始标签位
    view_filter     视图输出过滤标签位
    app_end         应用结束标签位
    log_write       日志write方法标签位
    log_wrire_done  日志写入完成标签位
    reponse_send    响应发送标签位
    reponse_end     输出结束标签位
# 绑定钩子
## 方法一：
        tags.php 定义数组键值对
    如下所示：
        ```php
        return [
            // 应用结束
            'app_end'      => [
                'app\\admin\\behavior\\AdminLog',
            ],
        ];
    ```
## 方法二

        Hook::add('abc'," app\\index\\behavior\\abc")
# 行为(具体钩子具体实现)定义

    namespace app\index\behavior
    class abc{
        public function run()
        {
            // 行为逻辑
        }
    }
    
##如果行为类需要绑定到多个标签，可以采用如下定义：

```php
class Test 
{
    public function app_init(&$params)
    {

    }
    
    public function app_end(&$params)
    {

    }    
} 
```
namespace app\index\behavior;
   
# 下钩子

    Hook:listen('钩子名称','参数','是否只有一次有效返回值')
    
    
# hook 基本实现
hook::listen 下钩子  具体实现  判断当前的钩子名称 是否在 tag 中定义 ，
有就执行，没有就返回空
其中hook::exec 方法是具体的执行钩子



