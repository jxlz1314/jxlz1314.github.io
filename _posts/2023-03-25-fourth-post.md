---
layout: post
title:  "华为云服务器上配置Shadowsocks"
categories: 技术
tags:  Shadowsocks
author: JIANG Xiao
---

* content
{:toc}

# 1. 服务器购买配置
## 1.1 购买华为云服务器
- 进入官方网站
> https://activity.huaweicloud.com
- 或咨询万能的淘宝卖家 

选择购买境外的“弹性云服务器ECS”，价格¥99/年左右合适

## 1.2 配置云服务器
### 1.2.1 配置操作系统
- 登录进入个人服务器管理页面“控制台”
- 操作系统选择“CentOS 7.2 64bit”
### 1.2.2 配置防火墙
- 登录进入个人服务器管理页面“控制台”
- 选择“安全组”，点击“更改安全组”进行配置
![HW1.jpg](https://s2.loli.net/2023/03/25/Ay5NcCHzDqtk6IO.jpg)
- 点击“新建安全组”及“创建安全组”进行配置，将SS端口加入
![HW2.jpg](https://s2.loli.net/2023/03/25/uYwzCDpZXko7vSP.jpg)
![HW3.jpg](https://s2.loli.net/2023/03/25/wjzhK8UgsRyqQX5.jpg)
- 入方向规则添加“1035”端口（即后续设置的SS端口）
![HW4.jpg](https://s2.loli.net/2023/03/25/8L5pEdHkPYoO7nS.jpg)

# 1. 安装配置Shadowsocks客户端

## 1.1 安装Shadowsocks客户端
- 安装epel扩展源
- 采用Python包管理工具pip安装。
> sudo yum -y install epel-release \
sudo yum -y install python-pip
- 安装Shadowsocks客户端
> sudo pip install shadowsocks 

## 1.2 配置Shadowsocks客户端
- 新建配置文件
> sudo mkdir /etc/shadowsocks \
sudo vi /etc/shadowsocks/shadowsocks.json
- 添加配置信息
> { \
"server":"0.0.0.0", \
"server_port":1035, \
"local_address": "127.0.0.1", \
"local_port":1080, \
"password":"password", \
"timeout":300, \
"method":"aes-256-cfb", \
"fast_open": false, \
"workers": 1 \
}

- 参数说明：
server：Shadowsocks服务器地址 \
server_port：Shadowsocks服务器端口 \
local_address：本地IP \
local_port：本地端口 \
password：Shadowsocks连接密码 \
timeout：等待超时时间 \
method：加密方式 \
workers:工作线程数 \
fast_open：true或false。开启fast_open以降低延迟，但要求Linux内核在3.7+。开启方法 \
> echo 3 > /proc/sys/net/ipv4/tcp_fastopen
- 配置自启动 \
① 新建启动脚本文件 \
> vi /etc/systemd/system/shadowsocks.service \

  内容如下：
> [Unit] \
Description=Shadowsocks \
[Service] \
TimeoutStartSec=0 \
ExecStart=/usr/bin/sslocal -c /etc/shadowsocks/shadowsocks.json \
[Install] \
WantedBy=multi-user.target \

  ② 启动Shadowsocks客户端
> systemctl enable shadowsocks.service \
systemctl start shadowsocks.service \
systemctl status shadowsocks.service \
