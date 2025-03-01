---
title: TADF 材料计算部分常用的 Citation
summary: 在写文章时，对前人工作的引用是非常重要的。
tags:
  - Web
  - Html
date: 2025-02-10T10:17:21Z
categories:
  - Work
draft: false
isCJKLanguage: true
showAuthor: true
authors:
  - 葛世杰
---

在写文章时，对前人工作的引用是非常重要的，具体内容请转到 Sob 老师的文章 ：**[写计算化学文章时引用理论方法、基组、程序时应注意的问题](http://sobereva.com/370)**。这里将一些常用的参考文献列出。

## 一、计算方法

### 1. 泛函

PBE0 (DFT) : [10.1063/1.478522](http://doi.org/10.1063/1.478522)

PBE0 (TD-DFT) : [10.1063/1.479571](https://doi.org/10.1063/1.479571)

B3LYP : [10.1063/1.464913](https://doi.org/10.1063/1.464913)

ωB97XD : [10.1039/B810189B](https://doi.org/10.1039/B810189B "Link to landing page via DOI")

### 2. 基组

def2 series : [10.1039/B515623H](https://doi.org/10.1039/B515623H)、[10.1039/B508541A](https://doi.org/10.1039/B508541A)

def series : [10.1063/1.463096](https://doi.org/10.1063/1.463096)、[10.1063/1.467146](https://doi.org/10.1063/1.467146)

6-31g(d,p)/6-31g** : [10.1080/00268977400100171](https://doi.org/10.1080/00268977400100171)

6-311g(d,p)/6-311g** : [10.1063/1.455064](https://doi.org/10.1063/1.455064)

### 3. 色散矫正

Grimme’s D3 : [10.1063/1.3382344](https://doi.org/10.1063/1.3382344)

Grimme’s D3 (BJ) : [10.1002/jcc.21759](https://doi.org/10.1002/jcc.21759)

### 4. 其他分析方法

Electron - hole Anlysis : [10.1021/ct200308m](https://doi.org/10.1021/ct200308m)

IGMH : [10.1002/jcc.26812](https://doi.org/10.1002/jcc.26812)

RDG : [10.1021/ja100936w](https://doi.org/10.1021/ja100936w)

HuangRhys Index : [10.1098/rspa.1950.0184](https://doi.org/10.1098/rspa.1950.0184)

Natural transition orbital （NTO）[10.1063/1.1558471](https://doi.org/10.1063/1.1558471)

## 二、计算软件

Gaussian 16 : 需要替换小版本号 比如 Revision C.02（本课题组），Bibtex 内容为：

```BibTex
@misc{g16,
author={M. J. Frisch and G. W. Trucks and H. B. Schlegel and G. E. Scuseria and M. A. Robb and J. R. Cheeseman and G. Scalmani and V. Barone and G. A. Petersson and H. Nakatsuji and X. Li and M. Caricato and A. V. Marenich and J. Bloino and B. G. Janesko and R. Gomperts and B. Mennucci and H. P. Hratchian and J. V. Ortiz and A. F. Izmaylov and J. L. Sonnenberg and D. Williams-Young and F. Ding and F. Lipparini and F. Egidi and J. Goings and B. Peng and A. Petrone and T. Henderson and D. Ranasinghe and V. G. Zakrzewski and J. Gao and N. Rega and G. Zheng and W. Liang and M. Hada and M. Ehara and K. Toyota and R. Fukuda and J. Hasegawa and M. Ishida and T. Nakajima and Y. Honda and O. Kitao and H. Nakai and T. Vreven and K. Throssell and Montgomery, {Jr.}, J. A. and J. E. Peralta and F. Ogliaro and M. J. Bearpark and J. J. Heyd and E. N. Brothers and K. N. Kudin and V. N. Staroverov and T. A. Keith and R. Kobayashi and J. Normand and K. Raghavachari and A. P. Rendell and J. C. Burant and S. S. Iyengar and J. Tomasi and M. Cossi and J. M. Millam and M. Klene and C. Adamo and R. Cammi and J. W. Ochterski and R. L. Martin and K. Morokuma and O. Farkas and J. B. Foresman and D. J. Fox},
title={Gaussian 16 {R}evision {C}.02},
year={2016},
note={Gaussian Inc. Wallingford CT}
}
```

Orca : 至少要引用 [10.1002/wcms.1606](https://doi.org/10.1002/wcms.1606)，此外，在 .out 输出文件中会注明可选的其他 Citation 文献。

Multiwfn : [10.1002/jcc.22885](https://doi.org/10.1002/jcc.22885)、[10.1063/5.0216272](https://doi.org/10.1063/5.0216272)

VMD : [10.1016/0263-7855(96)00018-5](https://doi.org/10.1016/0263-7855(96)00018-5)

PySOC : [10.1021/acs.jctc.6b00915](https://doi.org/10.1021/acs.jctc.6b00915)

FCclasses3 :[10.1002/jcc.27027](https://doi.org/10.1002/jcc.27027)

CP2K : [10.1063/5.0007045](https://doi.org/10.1063/5.0007045)

GROMACS : [10.1016/j.softx.2015.06.001](https://doi.org/10.1016/j.softx.2015.06.001)

MOMAP

```BibTex
使用 MOMAP 光物理模块发表文章的用户，需要引用以下文献：

1、Peng, Q.; Yi, Y. P.; Shuai, Z. G.; Shao, J. S., J. Chem. Phys., 2007, 126, 114302.
2、Peng, Q.; Yi, Y. P.; Shuai, Z. G.; Shao, J. S., J. Am. Chem. Soc., 2007, 129, 9333-9339.
3、Niu, Y. L.; Peng, Q.; Shuai, Z. G., Sci. China, Ser. B, 2008, 51, 1153-1158.
4、Niu, Y. L.; Peng, Q.; Deng, C. M.; Gao, X.; Shuai, Z. G., J. Phys. Chem. A, 2010, 114, 7817-7831.
5、Peng, Q.; Niu, Y. L.; Shi, Q.; Gao, X.; Shuai, Z. G., J. Chem. Theory Comput., 2013, 9, 1132-1143.
6、Shuai, Z. G.; Peng, Q., Phys. Rep., 2014, 537, 123.

使用 MOMAP 电子输运模块发表文章的用户，需要引用以下文献：

1、Geng, H.; Yin, S. W.; Chen, K. Q., Shuai, Z. G., J. Phys. Chem. B, 2005, 109, 12304.
2、Yin, S. W.; Yi, Y. P.; Li, Q. X.; Yu, G., Liu, Y. Q., Shuai, Z. G., J. Phys. Chem. A, 2006, 110, 7138.
3、Yang, X. D.; Li, Q. K.; Shuai, Z. G., Nanotechnology, 2007, 18, 424029.
4、Wang, C. L.; Wang, F. H.; Yang, X. D.; Li, Q. K., Shuai, Z. G., Org. Electron., 2008, 9, 635.
5、Yang, X. D.; Wang, L. J.; Wang, C. L.; Long, W.; Shuai, Z. G., Chem. Mat., 2008, 20, 3205.
6、Nan, G. J.; Yang, X.; Wang, L. J.; Shuai, Z. G.; Zhao, Y., Phys. Rev. B, 2009, 79, 115203.
7、Nan, G. J.; Wang, L. J.; Yang, X. D.; Shuai, Z. G.; Zhao, Y., J. Chem. Phys., 2009, 130, 024704.
8、Wang, L. J.; Nan, G. J.; Yang, X. D.; Peng, Q.; Li, Q. K.; Shuai, Z. G., Chem. Soc. Rev., 2010, 39, 423.
9、Wang, L. J., Li, Q. K., Shuai, Z. G., Chen, L. P., Shi, Q., Phys. Chem. Chem. Phys., 2010, 12, 3309.
10、Shuai, Z. G., Wang, L. J., Li, Q. K., Adv. Mater., 2011, 23, 1145.
```

该引用的时候一定要引原文。绝对不能说比如张三写了篇应用性文章用到了 BLYP 泛函，或者李四写了本量化书或者写了篇DFT综述介绍了BLYP，于是自己用 BLYP 的时候引张三或李四的文章就完事了。

参考：

>  https://bane-dysta.github.io/posts/citation/
> 
> http://sobereva.com/370
