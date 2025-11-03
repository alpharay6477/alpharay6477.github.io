---
title: 计算方法记录
summary: 将学过的计算方法做简要记录。
tags:
  - Quantum Chemistry
date: 2024-12-05
categories:
  - Work
draft: false
weight: 1
showTableOfContents : true
isCJKLanguage: true
showAuthor: true
authors:
  - 葛世杰
---
理论计算是实验的延伸，而非简单的复现实验数据，计算的内容重点在于用理论解释现象，用计算验证理论。
    
## 一、量子化学

### 1. 基态

&emsp;&emsp;分子或原子在最低能量时的电子结构和几何构型。

&emsp;&emsp;可计算：限制性优化、构象分析、结合能计算、分子轨道模拟、静电势模拟 (ESP)、偶极矩计算、键级分析、振动分析、Mulliken 电荷分析、核磁 NMR 预测、分子体积（pdb 盒子）、热力学函数计算 (内能 U、焓 H、功函 A、吉布斯自由能 G)。

### 2. 垂直激发态

&emsp;&emsp;垂直激发态指分子在基态几何结构不变的情况下，通过吸收光子直接跃迁到激发态。该跃迁遵循 Franck–Condon 原理，因此激发过程发生得非常快，分子结构来不及改变。结果是在激发态下，分子通常处于一个非平衡的、高能量的几何构型。

&emsp;&emsp;可计算：激发态能级（LE 态可选择 Orca 使用双杂化计算）、电子-空穴分布、振子强度、电子-空穴距离/重叠程度、CT/LE 指数、偶极矩计算。空穴-电子重叠等值面（即 Sm 或 Sr 函数）、Cele 和 Chole 等值面、跃迁密度分布、跃迁偶极矩（VMD 箭头绘图）、跃迁磁偶极矩密度、电荷密度分布、空穴-电子库仑吸引能。

### 3. 绝热激发态

&emsp;&emsp;绝热激发态指分子在吸收光子后，沿着能量最低路径从基态几何结构缓慢调整到激发态的最低能量几何构型。这个过程保持电子状态不变，但允许分子结构在激发态中重新优化。绝热激发态通常用于描述分子在稳定的激发态几何构型下的能量和性质。

&emsp;&emsp;可计算：激发态能级、电子-空穴分布、振子强度、电子-空穴距离/重叠程度、CT/LE 指数、偶极矩计算、激发态重组能、分子构象差异分析 (RMSD)、振动分析。

### 4. 调控密度泛函方法计算分子

&emsp;&emsp;Tuned Density Functional Theory，TDFT。这种方法特别适用于那些标准泛函难以准确描述的系统，比如含有电荷转移激发或较大能带间隙的材料。通过调整密度泛函理论中的参数，如交换-相关泛函（Exchange-Correlation Functional）中的参数，来更精确地描述电子结构，尤其是激发态和光学性质。调控密度泛函的关键在于找到最佳的参数设置，使得计算结果与实验数据或其他高精度计算结果相吻合。这通常涉及到对泛函中的长程校正参数（如ω参数）进行优化，以使得如 HOMO 能量更接近电离能，从而提高激发能、极化率等性质的计算精度。

&emsp;&emsp;这种方法虽然可以提高计算的准确性，但也可能带来计算成本的增加，因为需要对每个体系单独优化参数。调控密度泛函的关键在于正确选择泛函（例如：lc-blyp、lc-whpbe、wb97xd）和调整参数 (ω)。

### 5. 自然跃迁轨道分析 

&emsp;&emsp;Natural Transition Orbital, NTO。虽然电子跃迁问题本质是体系电子态与电子态之间的跃迁，但是人们往往喜欢通过轨道模型来描述，以使跃迁模式更易于考察和理解。NTO 的思路是将多组态波函数密度矩阵对角化，得到的本征向量就是自然轨道，通常只需要本征值最大的 $n_{occ}$ 个轨道就能很充分地描述体系密度矩阵，冗余的信息就被去掉了，使一大堆轨道跃迁模式转化为一个“紧凑”的轨道跃迁模式。

&emsp;&emsp;需要说明的是电子-空穴分析比 NTO 强大得多、普适性强得多，而且使用很方便，因此强烈建议用空穴-电子分析而非 NTO！NTO 分析唯一的好处仅在于可以给出相位信息，你不需要专门考察相位的话一律应当用空穴-电子分析代替 NTO。

### 6. 片段间电荷转移分析 

&emsp;&emsp;Interfragment Charge Transfer Analysis, IFCT。用于研究分子中不同部分之间电子密度变化的计算化学方法，帮助理解分子激发态内电荷的分布和转移情况。在 IFCT 分析输出中会直接给出电荷转移百分比 CT (%) 和局域激发百分比 LE (%)。

&emsp;&emsp;此外，还可以在片段间电荷转移分析的基础上研究分子中电子给体（D）与电子受体（A）之间通过空间相互作用实现电子转移的现象。
即空间电荷转移分析，Space Charge Transfer Analysis, TSCT介绍：[计算化学公社：TSCT 计算](http://bbs.keinsci.com/thread-18597-1-1.html)。
电荷转移类型：空间电荷转移 (Through-space charge transfer, TSCT) 与化学键电荷转移 (Through-bond charge transfer, TBCT)

### 7. 各种光谱：IR、Raman、UV-Vis...

&emsp;&emsp;1）红外光谱（IR, Infrared Spectroscopy）：分子对红外光的吸收，对应化学键的振动和转动能级跃迁。可以识别官能团（如-OH、C=O、N-H等）。横轴为波数（cm⁻¹），纵轴为透射率（%）或吸光度（A）。<br>
&emsp;&emsp;2）拉曼光谱（Raman Spectroscopy）：拉曼散射，反映分子振动/转动模式，与IR互补（如对称振动在拉曼中更强）。横轴为拉曼位移（cm⁻¹），纵轴为散射强度。<br>
&emsp;&emsp;3）紫外-可见光谱（UV-Vis, Ultraviolet-Visible Spectroscopy）：分子对紫外/可见光的吸收，对应电子能级跃迁。横轴为波长（nm），纵轴为吸收强度。<br>
&emsp;&emsp;4）振动分辨光谱（Vibrationally Resolved Spectroscopy）：包括振动分辨荧光光谱、振动分辨磷光光谱。
电子跃迁时，会伴随振动能级的改变（电子-振动耦合），振动分辨光谱反映了激发态与基态振动波函数的重叠积分，通常表现为多个振动峰的序列（Franck–Condon 原理）。<br>
&emsp;&emsp;5）电子圆二色谱（ECD, Electronic Circular Dichroism）：测量手性分子对左/右旋圆偏振光的吸收差异。横轴为波长（nm），纵轴为椭圆度（mdeg）或Δε。<br>
&emsp;&emsp;6）振动圆二色谱（VCD, Vibrational Circular Dichroism）：类似红外光谱，但检测手性分子对红外左/右旋光的吸收差异。横轴为波数（cm⁻¹），纵轴为ΔA（吸收差异）。<br>
&emsp;&emsp;7）拉曼光学活性光谱（ROA, Raman Optical Activity）：拉曼散射的偏振差异，反映手性分子的振动模式。横轴为拉曼位移（cm⁻¹），纵轴为左/右旋光强度差。<br>
&emsp;&emsp;8）构象权重平均的光谱（Conformationally Averaged Spectrum）：通过对分子所有可能构象的光谱（如红外、紫外等）按其构象出现的概率（能量权重或 Boltzmann 分布）进行加权平均后得到的总光谱，反映分子在动态平衡下的整体光谱特征。

### 8. 轨道离域指数分析 

&emsp;&emsp;Orbital Delocalization Index Analysis, ODI。轨道离域指数是一个衡量轨道离域性的指标，它通过计算分子中各个原子对特定轨道的贡献来定量描述轨道的离域程度。

### 9. 片段相互作用分析 

&emsp;&emsp;Independent Gradient Model Based on Hirshfeld Partition, IGMH。片段相互作用分析（IGMH）是一种基于 Hirshfeld 分子密度分区的独立梯度模型（Independent Gradient Model），用于可视化研究化学系统中的相互作用，如：氢键、空间位阻效应、笼效应、范德华相互作用等。

### 10. 势能面扫描 

&emsp;&emsp;Potential Energy Surface Scanning, PES。它用于研究分子或一组分子在反应过程中 (构象变化过程中) 的能量变化情况。

#### &emsp;10.1 刚性扫描

&emsp;&emsp;Rigid Scan 在这种扫描中，逐渐改变选定的内坐标，并对分子体系进行单点能计算，而分子内其他变量保持不变。

#### &emsp;10.2 柔性扫描

&emsp;&emsp;Relaxed Scan 在柔性扫描中，改变被扫描的变量，在每个结构处固定当前变量，并优化分子结构（实际是一个限制性优化）

### 11. 黄里因子计算

&emsp;&emsp;Huang-Rhys Factor。分子的电子跃迁过程中，分子的振动模式与电子态之间的相互作用。这种耦合效应对分子光谱以及能量转移等特性有重要的影响。使用 FCclasses 3 软件包计算。

### 12. 计算激发态动力学 

&emsp;&emsp;Excited State Dynamics, 根据 Jablonski Diagram，分子收到激发到达不同的激发态能级后，会经历一系列激发态过程，包括振动弛豫（Vibrational Relaxation, VR）、内转换（Internal Conversion, iC）、系间窜跃（Intersystem Crossing, ISC）、反系间窜跃（Reverse Intersystem Crossing, RISC）、荧光（Fluorescence, Fl）、磷光过程（Phosphorescence, Phos）。这一系列过程被统称为激发态动力学。

{{< katex >}}

&emsp;&emsp;可以计算：荧光辐射速率、磷光辐射速率、（反）系间穿越速率、内转换速率。链接：[计算激发态动力学](https://blog.alpharay.space/quantumchemistry/250402-exstdy/)。


### 13. 单分子动力学 

&emsp;&emsp;Monomolecule Dynamics Method 一种用于研究单个分子运动和动态行为的模拟方法。

### 14. 键解离能

&emsp;&emsp;Bond disscociation energy, BDE。计算分子中化学键断裂过程的反应焓变，它反映了键断裂过程所需要的能量。

### 15. 反应机理拟定

&emsp;&emsp;Reaction mechanism formulation。过渡态分析 (Transition State Analysis) 、反应路径分析（IRC）。渡态分析 和 IRC 分析 是理解化学反应机理，可以精确地定位过渡态、计算反应的能量障碍，并探讨反应的路径。例如：激发态分子内质子转移 (ESIPT) 、螺手性分子反转能 ...


### 16. 芳香性计算

&emsp;&emsp;Aromaticity Calculations 芳香性计算方法一览：[思想家公社：芳香性计算](http://sobereva.com/176)

#### &emsp;16.1 核独立化学位移

&emsp;&emsp;Nucleus-Independent Chemical Shifts, NICS。NICS 是一个在人为设定的不在原子核位置上的磁屏蔽值的负值，负值越大（即对磁场屏蔽越强）则芳香性越强
。最初NICS(0)是将这个位置取在共轭环的几何中心。

&emsp;&emsp;后来，为了更准确地反映 π 电子的贡献，提出了在环中心上方或下方 1 埃的位置计算 NICS，称为 NICS(1)。进一步地，有研究者提出只考察磁屏蔽张量的 zz 值（z 轴垂直于环平面），以更好地体现 π 芳香性，这被称为 NICS(1)_zz。


#### &emsp;16.2 磁感应电流模拟

&emsp;&emsp;Anisotropy of the Induced Current Density，AICD。软件部署：[Zhe Wang 的博客：芳香性可视化 AICD](https://wongzit.github.io/visualization-of-aromaticity-aicd/)

&emsp;&emsp;用于衡量芳香性的方法，它基于分子在外加磁场下的电子响应。当分子体系中的电子在整体或某个部分有很强的离域性时，例如苯环的π电子，在外加磁场作用下，会在相应区域产生一圈明显的感应环形电流。这种电流密度的分布可以用来考察体系的电子离域性，进而研究芳香性。

&emsp;&emsp;环电流的方向与芳香性有直接关系。如果环电流方向与左手规则相同时（称为 diamagnetic 或 diatropic 环电流），电流越大，则芳香性越强。相反，如果电流方向与左手规则相反（paratropic 环电流），电流越大则表现出越强的反芳香性。如果没有产生明显净电流，则为非芳香性。

&emsp;&emsp;Gauge including magnetically induced current method, GIMIC。[考察分子磁感生电流的程序GIMIC 2.0的使用](http://sobereva.com/491)

&emsp;&emsp; GIMIC方法基于GIAO（gauge including/invariant atomic orbitals，范规不变的原子轨道）方法，并依靠磁性质计算过程产生的密度矩阵和密度矩阵对磁场的导数矩阵来计算感应电流密度。相对于 AICD 程序来说，计算更复杂，绘制的模型图片更加高级和多样化。

#### &emsp;16.3 化学屏蔽表面 

&emsp; &emsp;Iso-Chemical Shielding Surfaces, ICSS。ICSS 是指在分子周围某个特定等值面上，该面上的磁屏蔽值相等。这个等值面能够直观地展示分子中电子密度的分布情况，尤其是 π 电子的分布，从而反映分子的芳香性。

&emsp;&emsp;在芳香性区域，由于π电子的离域，磁屏蔽通常较强，因此在 ICSS 图中，芳香性区域通常会显示为屏蔽较强的区域（即低化学位移值的区域）。相反，非芳香性区域或反芳香性区域则显示出较弱的磁屏蔽。

### 17. 分子中的原子极化率 

&emsp;&emsp;Atomic Polarizabilities in Molecules。原子在孤立状态下的极化率是可以实验测量的，也很容易理论计算，在 [ctcp.massey](http://ctcp.massey.ac.nz/index.php?menu=dipole&page=dipole) 有全周期表各种元素的实验和高精度理论计算数据。分子的极化率可以视为是其中各个原子的有效 (effective) 极化率的总和，但是分子环境中原子极化率通常不是实验可观测的（除非是所有原子等价，比如C60，就用分子极化率除以60就是各个碳的极化率），但可以通过计算说明。

&emsp;&emsp;可以输出的信息：原子有效体积、自由体积和极化率。

### 18. 福井函数 

&emsp;&emsp;Fukui Function, FF。反映分子中电子密度对电子数变化的响应，用于定位亲核（\\(f^+\\)）、亲电（\\(f^−\\)）和自由基（\\(f^0\\)）反应位点：

* \\(f^+\\)：亲核攻击敏感位点（HOMO电子密度主导）。
* \\(f^−\\)：亲电攻击敏感位点（LUMO电子密度主导）。

**双描述符（Dual Descriptor, DD）** ：
定义为二阶电子密度变化率 \\(Δf=f^+−f^−\\)，可同时区分亲核和亲电位点：

* \\(Δf>0\\)：亲核反应位点（HOMO贡献大）。
* \\(Δf<0\\)：亲电反应位点（LUMO贡献大）。

### 19. 完全活性空间自洽场方法

&emsp;&emsp;Complete Active Space Self-Consistent Field Method, CASSCF 

&emsp;&emsp;把所有自洽场分子轨道分为内层(永远双占据)， 外层(永远不占据)和活性轨道，只对选择的活性轨道进行 MCSCF 计算。CASSCF(N,M)：N：活性电子数（分布在活性轨道上的电子），M：活性轨道数。对N个电子和M个轨道，所有可能的电子组态（CSFs）都被包含在计算中，通过这种方式，CASSCF能严格处理活性空间内的静态相关（多参考态特性）。

在计算过程中，可以指定：<br>
&emsp;&emsp;1) 多根（Roots）计算：可同时求多个态（如基态、激发态）的波函数和能量。<br>
&emsp;&emsp;2) 多重度（Multiplicities）：支持不同自旋态（单重态、三重态等）。<br>
&emsp;&emsp;3) 态平均（State-Average）：用户可定义多个态的权重，优化其平均能量（避免偏向某个态）。<br>

适合使用 CASSCF 计算的体系：<br>
&emsp;&emsp;1) 多个电子组态能量接近（简并），无法用单参考态（如HF）描述。<br>
&emsp;&emsp;2) 需多组态处理的波函数。<br>
&emsp;&emsp;3) 需平衡多个电子态（如基态和激发态）的轨道需求。<br>
&emsp;&emsp;4) 需保证波函数是自旋算符的本征态，避免自旋污染（Spin Contamination）。<br>
&emsp;&emsp;5) 为高阶多参考方法（如MRCI、NEVPT2）提供合理的初始轨道。<br>

### 20. 轨道定域化

&emsp;&emsp;Orbital localization。将轨道与化学键直接联系起来。正则分子轨道（如HF或DFT计算的轨道）通常离域于整个分子，而定域化轨道能反映化学键、孤对电子或原子核芯电子等局域特征，更贴近传统的路易斯结构。定域化轨道可加速后续计算（如多参考态方法或大体系计算），因为局域性可减少电子关联计算的复杂度。

### 21. 自旋密度分布

&emsp;&emsp;对于开壳层体系（自由基、激发态、过渡金属）中电子自旋的空间分布情况。它通过 α 电子密度和 β 电子密度的差来定义，公式为：ρ（自旋密度）= ρ （α 电子密度） – ρ（β 电子密度）。
对于闭壳层体系（即电子总数为偶数，且每个自旋方向的电子都完全配对的体系），由于每一个 α 自旋电子都有一个 β 自旋电子与之配对，因此在空间中的任何位置，α 和 β 电子的分布密度是完全相等的。这就导致在整个空间中，自旋密度处处为零，即不存在净自旋密度。

### 22. 电子密度插图

&emsp;&emsp;Difference map for electron density，体系中电子密度的变化分布，通过计算实际电子密度与参考状态（如孤立原子或理论模型）电子密度的差值得到，用于直观显示化学键形成、电荷转移或外场作用下的电子重新分布。


## 二、分子动力学

### 1. 结构弛豫与能量最小化 (Structural relaxation and energy minimization)
 
&emsp;&emsp;找到分子体系的局部或全局能量最低的稳定结构，使用的是经典的力场进行几何优化。

### 2. 平衡态动力学模拟 (Equilibrium state dynamics simulation)

&emsp;&emsp;在指定的系综（NVT、NPT等）下，模拟体系在平衡状态下的运动轨迹，是所有后续分析的基础。

### 3. 构象变化与柔性分析 (Conformational changes and flexibility analysis)

&emsp;&emsp;研究蛋白质折叠/去折叠、配体与受体的诱导契合、分子内旋转等大尺度构象变化。均方根偏差：衡量模拟过程中结构相对于参考结构（如晶体结构）的整体偏差。均方根涨落：分析每个原子位置的波动性，常用于识别蛋白质中柔性（如环区）和刚性（如α螺旋核心）的区域。回转半径：表征分子的紧凑程度，常用于研究蛋白质的折叠状态。

### 4. 相互作用与能量分析 (Interaction and energy analysis)

&emsp;&emsp;1）相互作用能计算：精确计算蛋白质与配体、酶与底物、不同分子片段之间的范德华力和静电相互作用能。这与您的“片段相互作用分析”在概念上类似，但方法不同。
&emsp;&emsp;2）氢键分析：统计和分析模拟过程中氢键的形成、寿命和数量。
&emsp;&emsp;3）溶剂可及性表面积：计算分子表面被溶剂接触的面积，与疏水作用密切相关。
&emsp;&emsp;4）结合自由能计算：通过MM/PBSA或MM/GBSA方法，估算配体与受体的结合强度。这是药物设计中的关键技术。
&emsp;&emsp;5）伞形采样：一种增强采样技术，专门用于计算两个状态之间的自由能差，例如离子跨膜运输的自由能面（势能面）、配体从结合位点解离的路径等。这与您的“势能面扫描”和“反应机理拟定”在研究“过程”上类似。




