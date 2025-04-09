---
title: Gaussian | 计算双自由基
summary: 课题组同门要做一个双自由基分子，虽然终产还没拿到，但是可以先算算性质。
tags:
  - Chemistry
  - Gaussian
date: 2025-02-17T09:14:16Z
categories:
  - Work
draft: false
isCJKLanguage: true
showAuthor: true
authors:
  - 葛世杰
---

课题组同门要做一个双自由基分子，虽然终产还没拿到，但是可以先算算性质，于是在网上搜了搜计算双自由基的教程。

参考：[http://sobereva.com/264](http://sobereva.com/264)、[http://sobereva.com/353](http://sobereva.com/353)

## 一、计算关键词

主要为：开壳层使用 ub3lyp 泛函（unrestricted）；自由基分子如果电子和质子数相等则为中性；基态的双自由基自旋多重度有两种情况 $s=1$ 和 $s=3$；双自由基的自旋对称破缺态需要加 `gues(mix,alter)`​ 。

单线态：

```TXT
%chk=2C-s.chk
%nproc=32
%mem=80GB
# opt ub3lyp/6-311g(d,p) emp(gd3bj) scf(maxcycle=64) freq scrf(solvent=toluene)

2C-s

0 1
 C                 -8.87930000    0.59590000   -0.35860000
```

三线态：

```TXT
%chk=2C-t.chk
%nproc=32
%mem=80GB
# opt ub3lyp/6-311g(d,p) emp(gd3bj) scf(maxcycle=64) freq scrf(solvent=toluene)

2C-t

0 3
 C                 -8.87930000    0.59590000   -0.35860000
```

自旋对称破缺态：

```TXT
%chk=2C-bs.chk
%nproc=32
%mem=80GB
# opt ub3lyp/6-311g(d,p) emp(gd3bj) scf(maxcycle=64) freq scrf(solvent=toluene) guess(mix,alter) nosym

2C-bs

0 1
 C                 -8.87930000    0.59590000   -0.35860000
```

‍

## 二、自旋密度分布图

将计算的 chk 转为 fchk 导入到 Multiwfn

```TXT
5    //计算格点数据
5    //自旋密度
2    //用中等质量格点，总共算约51200个点。对于较大体系，建议选3用高质量格点（对于很大体系则建议选4并输入较大格点数），否则等值面可能不平滑

2 Export data to a Gaussian-type cube file in current folder
//  3 Export data to output.txt in current folder

0
7   //布居分析
5   //Mulliken分析（如果用SCPA，选7；用Lowdin，选6）
1   //Output Mulliken population and atomic charges
y
```

## 三、衡量双自由基特征

基于双正交化轨道计算 alpha 和 beta 重叠积分来衡量 双自由基特征，相关引用文献：[Chem. Phys. Lett., 33, 330 (1975)](https://doi.org/10.1016/0009-2614(75)80169-2)

```TXT
将 fchk 文件载入 Multiwfn 后依次输入
100  //其它功能（Part 1）
12  //双正交化
1   //双正交化占据轨道
0   //不计算双正交化轨道能量
此时从屏幕上可见
Singular values of orbital overlap matrix:
  1.0000   1.0000   1.0000   1.0000   0.9999   0.9999   0.9999   0.9998
  0.9993   0.9991   0.9990   0.9990   0.9968   0.9965   0.9960   0.1231
```
alpha 和 beta 分子轨道的重叠积分的绝对值很小，说明这两条轨道分离很充分，为 SOMO 轨道，表现出自由基特征。
可以看出，最后一个数对应第16号双正交化 alpha 轨道和第16号双正交化 beta 轨道间的重叠积分，有且仅有这一对轨道数值远小于1，说明这适合作为计算双自由基特征的重叠积分T。将 0.1231 (T) 代入双自由基特征的表达式，得到结果 0.780

$y=1-(2*T)/(1+T^2)$

可见，双激发组态的权重c22越大，双自由基特征越强，和基态组态权重c12相同时就是完美双自由基。基于这个考虑，Yamaguchi在 Chem. Phys. Lett., 33, 330 (1975) 中将y=2\*c22定义为双自由基特征，完美双自由基时 c2=1/2，故 y 恰为1。

‍
