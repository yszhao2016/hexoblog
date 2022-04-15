---
title: 通过vsftpd搭建ftp服务器
date: 2022-04-15 22:12:12
tags:
---

1.yum install vsftpd


yum -y install epel-release && yum -y install pam pam-devel db4-utils
wget https://security.appspot.com/downloads/vsftpd-3.0.5.tar.gz


tar xf vsftpd-3.0.3.tar.gz
make clean && make -j 4 && make install


如果编译的时候报错
/usr/bin/ld: cannot find -lcap
查找该 .so 文件
find / -name "*libcap.so*"
/usr/lib64/libcap.so.2.22
/usr/lib64/libcap.so.2
ln -sv /usr/lib64/libcap.so.2 /usr/lib64/libcap.so

