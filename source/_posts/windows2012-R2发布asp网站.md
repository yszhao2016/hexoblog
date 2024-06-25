---
title: windows2012-R2发布asp网站
date: 2024-06-25 20:22:20
tags:
---

#一 、安装asp.net 3.5 总是失败


卸载相关补订

#二、 应用程序池

    设置为 集成  2.0 的版本【安装.net 3.5 后 这边就是只有2.0的选项哦】

# 三、总是显示500，开启报错

双击ASP 设置

    将错误发送到浏览器  true
    
# 四、设置文件夹权限

    everyone  读写就可以了
    
# 五、Win2012  Microsoft JET Database Engine 错误 '80004005' 

    access 数据库没有权限访问
    
    设置c:/windows/temp或者%windir%/temp/
    
    erveryone  读写权限就可以了
