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
- 错误图片如下
![error1.jpg](https://s2.loli.net/2023/04/06/TIwNYfdCPy93kFx.jpg)
- 通过注释大法定位到出错位置位于sprayFoam.C第59行的“#include "compressibleCourantNo.H"”
- 查看compressibleCourantNo.H如下
> https://cpp.openfoam.org/v8/src_2finiteVolume_2cfdTools_2compressible_2compressibleCourantNo_8H_source.html
- 发现其中“fvc::surfaceSum(mag(phi))().primitiveField()/rho.primitiveField()”存在除法，考虑除数为0时可能导致溢出错误
- 其中的密度由状态方程计算，注意看看初始/边界中的温度/压力等状态，发现初始压力全域为0，需要修改，问题解决。
