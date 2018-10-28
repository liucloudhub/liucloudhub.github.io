---
layout: post
title:  Docker 的安装与常用命令示例
image: /img/hello_world.jpeg
date:  2017-06-29 15:00:00 +0800  
tags: [docker]
labels: [docker, 安装]
---

#### Docker的安装

   其实很简单了,两行命令即可完成:   
   
~~~   
 yum install docker-ce-selinux-17.03.2.ce-1.el7.centos.noarch.rpm
    yum install docker-ce-17.03.2.ce-1.el7.centos.x86_64.rpm
~~~

#### Docker 命令自动补全

   这里教大家如何使用shell 命令补全工具

~~~   
    yum install -y bash-completion(如果未安装需要先安装bash-completion)
    source /usr/share/bash-completion/completions/docker
    source /usr/share/bash-completion/bash_completion
~~~
    
 **Note:** 如果你遇到任何问题，欢迎下面留言哈！ 整理不易，欢迎打赏！
