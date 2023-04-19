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

> https://zhuanlan.zhihu.com/p/607711316


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
 中间行，反应方程式，然后为该方程的Arrhenius系数(依次为A，β ，E)； E 和A 的默认单位是：cal/mole和cm-mole-sec-K单位制。
- $k(T)=AT^bexp(-E_a/R_uT)$
 
 - AL + O2 = ALO + O &emsp; 9.72E13 &emsp; 0. &emsp; 159.95
 ## 2.6 OpenFOAM反应机理文件格式
- 使用chemkinToFoam命令可将CHEMKIN反应机理文件转换为OpenFOAM反应机理文件，使用方法查看chemkinToFoam -help。
- 使用chemkinToFoam的其他注意事项如下
> https://openfoam.top/chemkin/

- OpenFOAM中的默认单位是：m-mole-sec-K。。
- OpenFOAM中的系数与chemkin中的系数转换关系为。
- $\beta_{OpenFOAM}=\beta_{chemkin}$
- ${T_a}_{OpenFOAM}=（cal to J）*{E_a}{chemkin}/R_u=0.5032{E_a}{chemkin}$
- 然而值得注意的是，指前系数A的单位转换较为复杂。其中A的单位与反应系数k一致，在OpenFOAM中为
- $(kmol/m^3)^{order-1}/s$
- 而在CHEMKIN中则为
- $(mol/cm^3)^{order-1}/s$
- order为反应的级数，因此二则转换关系为：
- $A_{OpenFOAM}=0.001^{order-1}A_{chemkin}$
 ## 2.7 创建新液滴热物性参数时编译报错“ error: no matching function for call to ‘Foam::NSRDSfunc0::NSRDSfunc0(double, int, int, int, int)’”
- 值得注意的是，这里每次报错位置都是指向最后一个参数D_的格式，然而实际上格式错误可能发生在中间参数的任意位置，需要仔细核对NSRDSfunc格式（参考源码如下）与书写参数格式的差异。
> https://cpp.openfoam.org/v8/dir_a2bac1f1c72a552f3f257577a8b591e1.html
 ## 2.8 运行时中间报错“Maximum number of iterations exceeded”
 - 解决方法：选用更稳定的离散格式
> https://groups.google.com/g/openfoam/c/5DaXxteXPWQ?pli=1
 ## 2.9 sprayCloudProperties字典中部分参数解析
  - heatTransferModel   RanzMarshall;
  - 流体-颗粒之间的对流传热模型中Nu数通过RanzMarshall经验公式计算
  - $Nu=a+bRe_p^mPr^n$
  > https://www.openfoam.com/documentation/guides/latest/api/classFoam_1_1RanzMarshall.html
  
  > https://cpp.openfoam.org/v8/src_2lagrangian_2intermediate_2submodels_2Thermodynamic_2HeatTransferModel_2RanzMarshall_2RanzMarshall_8H_source.html
    
   - phaseChangeModel   liquidEvaporationBoil;
   - 液滴相变蒸发模型，来自文献     "Studies of Superheated Fuel Spray Structures and Vaporization in GDI Engines" Zuo, B., Gomes, A. M. and Rutland C. J. International Journal of Engine Research, 2000, Vol. 1(4), pp. 321-336 相关实现参见讨论
  > https://cpp.openfoam.org/v8/LiquidEvaporationBoil_8C_source.html
  
  > https://www.cfd-online.com/Forums/openfoam-programming-development/176771-liquidevaporationboil-liquidevaporation.html
  
  > https://cpp.openfoam.org/v8/classFoam_1_1LiquidEvaporationBoil.html
  
  ## 2.10 sprayFOAM的连续性方程中源项parcels.Srho(rho)输出为负
  - 使用parcels.Srho(rho).ref().source()可输出源项的值，然而输出的均为负值。考虑蒸发相变的连续性方程:
  - $\frac{\partial\rho}{\partial t}+\nabla\cdot\rho U=\dot{S}_{\rho}$
  - 这里的源项为颗粒由液相->气相时带来的源项，显然应该为正值，为何输出为负值呢？
  - 需要注意到parcels.Srho(rho).ref()这一项类别为fvMatrix，里面储存了离散后的矩阵方程系数，详细解析如下：
  > https://marinecfd.xyz/post/from-geometricfield-to-fvmatrix/

  - 需要注意对于fvMatrix的运算，在OpenFOAM中均进行了重载如下：
  > https://cpp.openfoam.org/v8/fvMatrix_8C_source.html
  
  - 例如对于运算符“==”，有
  >  template<class Type>  
 Foam::tmp<Foam::fvMatrix<Type>> Foam::operator==
 (
     const tmp<fvMatrix<Type>>& tA,
     const tmp<fvMatrix<Type>>& tB
 )
 {
     checkMethod(tA(), tB(), "==");
     return (tA - tB);
 }
