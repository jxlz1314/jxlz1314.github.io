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
- 即将所需的液态物性添加至库中，其格式及形式详见
> https://cpp.openfoam.org/v8/liquidProperties_8H_source.html


## 2.2 初始化时Floating point exception 溢出错误
- 错误图片如下


![error1.jpg](https://s2.loli.net/2023/04/06/TIwNYfdCPy93kFx.jpg)
- 通过注释大法定位到出错位置位于sprayFoam.C第59行的“#include "compressibleCourantNo.H"”
- 查看compressibleCourantNo.H如下
> https://cpp.openfoam.org/v8/src_2finiteVolume_2cfdTools_2compressible_2compressibleCourantNo_8H_source.html
- 发现其中“fvc::surfaceSum(mag(phi))().primitiveField()/rho.primitiveField()”存在除法，考虑除数为0时可能导致溢出错误
- 其中的密度由状态方程计算，注意看看初始/边界中的温度/压力等状态，发现初始压力全域为0，需要修改，问题解决。
## 2.3 量纲不符incompatible dimensions for operation
- 错误图片如下

![error2.jpg](https://s2.loli.net/2023/04/06/Hn1yOopUXCwN34z.jpg)
- 错误原因：压力量纲不同导致计算的密度量纲不一致。OpenFOAM中不可压缩流体中压力为p/rho（因为密度可由时间导数项提出），量纲为[0 2 -2 0 0 0 0]，可压缩流体中压力为p，量纲为[1 -1 -2 0 0 0 0]
## 2.5 CHEMKIN反应机理文件格式
- 起始行，关键字REACTIONS （或REAC），其后为Arrhenius系数的单位（可选）。
 中间行，反应方程式，然后为该方程的Arrhenius系数(依次为A，β ，E)； E 和A 的默认单位是：cal/mole和cm•mole•sec•K。
- $k(T)=AT^bexp(-E_a/R_uT)$
 
 - AL + O2 = ALO + O &emsp; 9.72E13 &emsp; 0. &emsp; 159.95
 ## 2.6 OpenFOAM反应机理文件格式
- 使用chemkinToFoam命令可将CHEMKIN反应机理文件转换为OpenFOAM反应机理文件，使用方法查看chemkinToFoam -help。
- 使用chemkinToFoam的其他注意事项如下
> https://openfoam.top/chemkin/

- OpenFOAM中的系数与chemkin中的系数转换关系为。
- $A_OpenFOAM=A_chemkin$
- $\beta_OpenFOAM=\beta_chemkin$
- $E_OpenFOAM=E_chemkin$
 
