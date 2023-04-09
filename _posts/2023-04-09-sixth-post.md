---
layout: post
title:  "EPYC双路服务器搭建与系统安装"
categories: 技术
tags:  服务器
author: JIANG
---

* content
{:toc}

# 1. 关于服务器配置
- 考虑到服务器搭建主要目的为流体仿真与分子动力学模拟，考虑性价比最终CPU型号敲定为EPYC 7302。具体配置如下：

| 品类 | 型号/参数 | 数量 | 价格（元） |
| :----:| :----: | :----: | :----: |
| CPU | epyc 7302 （二手拆机，无暗病） | 2 | 2000 |
| 主板 | 超微H11dsi（全新） | 1 | 1600 |
| 散热 | 4U 散热（全新） | 2 | 400 |
| 内存 | 8GB RECC 2666MHz（二手拆机，无暗病） | 16 | 1600 |
| 电源 | 750w以上（全新） | 1 | 500 |
| 固态硬盘 | 三星 500G M. 2（全新） | 1 | 400 |
| 机械硬盘 | 机械硬盘 4T（全新） | 2 | 1000 |
| 机箱 | 中塔式（全新） | 1 | 300 |
| 显示器 | 24寸（全新） | 1 | 300 |
| 合计 | - | - | 8400 |

- 然而最终配置中，考虑到服务器主板与普通PC主板不同，机箱需要改装螺丝孔，否则会造成主板短路点不亮，因此最终放弃了自己动手DIY的想法。从淘宝上就近随便选择一家配置主机服务器的店家进行配置，最终花费略高于初始预算（8400）。
# 2. 关于服务器Ubuntu系统安装
- 最初打算在服务器上安装Ubuntu 16.04系统，然而在制作完成系统启动盘，安装时报错
> core perfctr but no constraints; unknown hardware!
- 经过查询得知这个是 AMD Family-17h 与较老版本内核（Linux 4.11及以下）之间的一个兼容性问题，如下：
> https://www.ogura.io/posts/2020/11/amd-family-17h-compatibility-issue-with-older-kernel-version/
- 因此最终改为安装更新Linux内核的Ubuntu 18.04，选用的安装教程如下：
> https://blog.csdn.net/weixin_45915259/article/details/123928722

> https://ac.nowcoder.com/discuss/1013182?type=5&order=3&page=1&channel=-1

# 2. 关于OpenFOAM v1902及paraview 5.6.0安装
## 2.1 安装教程
- 参考如下链接进行安装
> https://openfoamwiki.net/index.php/Installation/Linux/OpenFOAM-v1806/Ubuntu

> https://blog.csdn.net/dongyaoyao555/article/details/122923091

> https://zhuanlan.zhihu.com/p/541266216

## 2.2 安装问题
- 按照以上教程安装，可以成功的运行OpenFOAM v1902及paraview 5.6.0，然而不幸的是paraview的界面无法正确显示，出现黑屏，但是运行正常，该有的按钮仍能够点击。目前讨论的原因集中于paraview使用高版本的OpenGL，以及系统显卡驱动没有正确安装导致的。相关讨论如下：
> https://www.cfd-online.com/Forums/openfoam-installation/247326-paraview-black-screen.html  

> https://www.cfd-online.com/Forums/paraview/213554-black-screen-paraview-5-6-0-ubuntu-18-04-after-openfoam-update.html

> https://www.cfd-china.com/topic/3769/paraview%E6%97%A0%E6%95%85%E9%BB%91%E5%B1%8F

> https://www.cfd-china.com/topic/6282/%E5%85%B3%E4%BA%8Eubuntu18-04%E4%B8%8Bparafoam%E9%BB%91%E5%B1%8F%E7%9A%84%E9%97%AE%E9%A2%98

> https://www.cnblogs.com/erichf/p/12929481.html

安装/更新显卡驱动，使用--mesa版本的paraview可能是解决方案，其次，可将系统升级至Ubuntu20.04
