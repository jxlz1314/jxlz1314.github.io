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
## 1.3 连接云服务器
- 下载远程连接软件“SSH Secure Shell Client”
> https://ccm.digidip.net/visit?url=https%3A%2F%2Fec.ccm2.net%2Fccm.net%2Fdownload%2Ffiles%2FSSHSecureShellClient-3.2.9.exe&ppref=https%3A%2F%2Fccm.net%2Fdownloads%2Fsecurity-and-maintenance%2F4997-ssh-secure-shell-windows%2F&currurl=https%3A%2F%2Fccm.net%2Fdownloads%2Fsecurity-and-maintenance%2F4997-ssh-secure-shell-windows%2F%3Fn%3D%26version%3Dwindows
- 打开软件，输入：Host Name 为服务器ip地址，User Name 为 root，进行远程连接
![HW5.jpg](https://s2.loli.net/2023/03/25/QZxPEr3qK1yL97i.jpg)

# 2. 服务器端安装配置Shadowsocks客户端

## 2.1 安装Shadowsocks客户端
- 安装epel扩展源
- 采用Python包管理工具pip安装。
> sudo yum -y install epel-release \
sudo yum -y install python-pip
- 安装Shadowsocks客户端
> sudo pip install shadowsocks 

## 2.2 配置Shadowsocks客户端
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

# 3. 客服端安装配置Shadowsocks客户端
- 下载Shadowsocks的windows版本，并解压安装
> https://github.com/shadowsocks/shadowsocks-windows/releases/download/2.3.1/Shadowsocks-win-2.3.1.zip

- 配置Shadowsocks客户端：点击服务器->编辑服务器，输入服务器ip，密码及端口即可保存，之后启用系统代理即可
![HW6.jpg](https://s2.loli.net/2023/03/25/AIgvPkXE6KTzryh.jpg)
- 开始你的愉快网上冲浪之旅吧！
![HW7.jpg](https://s2.loli.net/2023/03/25/mBlyniaWHbw15M6.jpg)
