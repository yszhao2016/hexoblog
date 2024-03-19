---
title: linux环境编译安装总结
abbrlink: 1b78fbfa
date: 2024-03-06 15:41:02
tags:
---


# make 很慢

make -j4   表示同时运行4个编译任务

编译任务数  一般情况是cpu 核心的一半



# make遇到语法错误

大概率是gcc 版本高了或者低了

安装示例

    wget https://mirrors.tuna.tsinghua.edu.cn/gnu/gcc/gcc-8.2.0/gcc-8.2.0.tar.gz
    tar -zxvf gcc-8.2.0.tar.gz
    ./contrib/download_prerequisites
    mkdir build
    cd bulid
    ../configure --prefix=/usr/local/gcc-8.2.0 --enable-bootstrap --enable-checking=release --enable-languages=c,c++ --disable-multilib
    make 
    make install
    echo -e '\nexport PATH=/usr/local/gcc-8.2.0/bin:$PATH\n' >> /etc/profile.d/gcc.sh && source /etc/profile.d/gcc.sh
    ln -sv /usr/local/gcc-8.2.0/include/ /usr/include/gcc
    ldconfig -v
    ldconfig -p |grep gcc
    gcc -v