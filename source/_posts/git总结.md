---
title: git总结
date: 2022-01-08 22:19:16
tags:
--- #标签
- git
# 一、基础知识

    工作区(workspace)      就是你平时存放项目代码的地方
    暂存区(stage)          git add
    仓库区/版本库(Repository)     git commit
    远程仓库

# 二、回退问题
## 1.git add 后回退

    git status 先看一下add 中的文件
    git reset HEAD 如果后面什么都不跟的话 就是上一次add 里面的全部撤销了
    git reset HEAD XXX/XXX/XXX.java 就是对某个文件进行撤销了


## 2.git commit后回退

    git log    命令显示从最近到最远的提交日志

        [root@localhost test-jenkins]# git log
        commit e2c7f2d567cbdc36e4503479e15ea51d19816d33
        Author: root <root@localhost.localdomain>
        Date:   Fri Jan 7 11:17:27 2022 +0800

        dev branch change

        commit 8943166480734ac5a760e482a887ee8da0140ec8
        Author: root <root@localhost.localdomain>
        Date:   Fri Jan 7 10:56:22 2022 +0800

        dev branch add index.php
        。。。。

    git reest --hard  commit_id
                      上一个版本就是HEAD^，
                      上上一个版本就是HEAD^^，
                      当然往上100个版本写100个^比较容易数不过来，
                      所以写成HEAD~100。
    例：
        git reset --hard 139dcfaa558e3276b30b6b2e5cbbb9c00bbdca96
        git reset --hard HEAD^

    将会删除此版本之前添加

    强制push到对应的远程分支（如提交到develop分支）
    git push -f -u origin develop


## 3.回退后悔药

    git reflog用来记录你的每一次命令
    [root@localhost test-jenkins]# git reflog
    8943166 HEAD@{0}: reset: moving to 8943166480734ac5a760e482a887ee8da0140ec8
    e2c7f2d HEAD@{1}: commit: dev branch change
    8943166 HEAD@{2}: commit: dev branch add index.php
    1ca6f06 HEAD@{3}: checkout: moving from release to dev
    1ca6f06 HEAD@{4}: pull origin release: Fast-forward
    14fbb47 HEAD@{5}: checkout: moving from master to release
    14fbb47 HEAD@{6}: clone: from ssh://git@117.89.131.220:44222/zhaoyoushui/test-jenkins.git

    git reset --hard e2c7f2d

## 4.已经提交远程分支

    git reset 方法回退
    拉过的错误版本的本地分支  多需要回退
    回退  强制推送  远程

    git revert 版本号
    git revert HEAD 上个版本
    git revert HEAD~n 上n个版本
    git push 远程
    其他客户端正常拉取就可以


# 三、Git Reset 三种模式
    --hard: 会清空工作目录和暂存区的改动
    --soft：保留工作目录，并把重置 HEAD 所带来的新的差异放进暂存区  **变得只是仓库区**
    --mixed reset 不加参数(mixed)：保留工作目录，并清空暂存区


# 四、修改远程url

    1.方法一
        git remote set-url origin [url]
        git remote -v 查看
    
    2.方法二
        git remote rm origin
        git remote add origin [url]
        git remote -v 查看
    
    3.方法三
        直接修改config 文件

