---
layout: post
title:  "记录Fluent EDEM耦合计算遇到的问题及解决方法"
categories: 教程
tags:  教程
author: JIANG Xiao
---

* content
{:toc}

# 1. Update_Time_Level: invalid data
参考CFD online的解决方法：https://www.cfd-online.com/Forums/fluent/109165-update_time_level-invalid-data.html
- 先输入命令"/solve/iterate 1" 之后再输入命令"/solve/dual-time-iterate 100 20"，注意不能点击“运行计算”

# 2. EDEM与fluent不能正确耦合，EDEM只计算了一个时间步长
可以大退EDEM与fluent，并重新打开计算。推出前记得保存case
