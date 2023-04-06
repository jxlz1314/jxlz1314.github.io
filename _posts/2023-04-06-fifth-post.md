---
layout: post
title:  "sprayFoam模拟铝粉燃烧注意事项"
categories: OpenFOAM
tags:  OpenFOAM
author: OpenFOAM
---

* content
{:toc}

# 1. 关于sprayFoam
- 代码可在下面查看
> https://cpp.openfoam.org/v8/sprayFoam_8C_source.html
- 以下是关于sprayFoam的一些解析及算例中的参数解释
> https://www.cfd-china.com/topic/5956/euler-lagrange%E7%9A%84%E4%B8%80%E4%BA%9B%E8%A7%A3%E6%9E%90-sprayfoam

# 2. 模拟中常见错误
## 2.1 Unknown liquidProperties type xxxx
- 解决方案如下
> https://blog.csdn.net/acaist201/article/details/122150850
## 2.2 初始化时Floating point exception 溢出错误
