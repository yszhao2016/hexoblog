---
title: php开发-ftp下载上传
abbrlink: eae5a656
date: 2024-02-06 16:43:04
tags: PHP开发
categories:
  - PHP开发
---


# 检查ftp

1.使用ftp 客户端 连接ftp

windows系统      winscp
redhat发行版本   yum install ftp
debian发行版本   apt install ftp


    示例（一定要ls get 下，才能证明能传输数据）
        ftp 192.14.23.23 22221
        ls                     
        
# 基础方法
        ftp_connect(ip,port)                            #连接服务器  默认21  返回值 连接资源
        ftp_login($conn,$username,$passwd)              #登录  返回值是 bool
        ftp_pasv($conn,true)                            #设置被动模式  默认ftp 主动模式   
        ftp_nlist($conn,$path)                          #查看当前目录 文件列表 类ls功能
        ftp_pwd($conn)                                  #查看当前目录
        ftp_get($conn,$localpath,$filename,FTP_ASCII)   #下载远程 文件到 本地
        ftp_put($conn,$filename,$localpath,FTP_ASCII)   #上传本地文件 到服务器

# 注意事项
    
<font color="red">ftp 默认是主动模式 传输数据端口是随机的</font>

<font color="red">ftp 被动模式  是端口需要配置定义 </font>

<font color="red">防火墙需要放开端口</font>

排查方案 用步骤一

# 代码摘录示例
```php
        $conn = ftp_connect($ip, $port);
        if ($conn) {
            // 登录FTP服务器
            $login = ftp_login($conn, $username, $passwd);
            if ($login&&$pasv) {
//                ftp_set_option($conn, FTP_USEPASVADDRESS, false);
                  // 切换到主动模式
                  ftp_pasv($conn, true);
            }
        } else {
            throw new Exception("登录失败");
        }
        
        //查看目录下 列表 类似ls功能
        $dirList = ftp_nlist($conn, $dir);
        
        #下载文件
        $ret = ftp_get($conn, $local_filepath.$filename, $filename, FTP_ASCII);
        
        #上传文件       
        ftp_put($conn, $filename, $local_filepath . $filename, FTP_ASCII);

```  