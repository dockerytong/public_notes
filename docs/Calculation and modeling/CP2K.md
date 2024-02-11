# 1 CP2K

- Kohn-Sham density functional theory (KS-DFT)
	- PlaneWaveCalculator 使用 DFT 结合范数守恒赝势或 PAW 势 [Blochl94] 来描述电子结构。该方法基于在平面波函数的基础上扩展单粒子波函数来求解Kohn-Sham方程，而不是LCAO基组。
	- 使用LCAO的软件：GPAW、SIESTA、ABACUS、DMol3
	- [[CP2K]]中的GPW方法
	- [[CP2K]]中的GAPW方法
# 2 技术细节

## 2.1 泛函

- 半局域泛函（(semi)local density functionals， LDA & GGA）的缺点
	- 热化学性质：up to 1 eV error 
	- 结构性质：23% error
	- 弹性常数：10% error
	- 强关联体系：过渡金属氧化物
	- Van der Waals bonding missing
		- 使用DFT-D3/DFT-D4校正，或rVV等泛函解决
	- 带隙问题
		- 来源
		- 解决方法
			- 量子蒙特卡洛
			- 后HatreeFock方法 （MP2, CI, CC）
			- 多体微扰方法
				- *GW*近似
				- BSE方程
			- TD-DFT
			- 杂化泛函
	- 电子激发态的描述
- 杂化泛函
	- 定义
		- Characterized by the admixture of a certain  amount of nonlocal Fock exchange energy to a  part of (semi)local density functional exchange  energy
	- 分类
		- PBE based
			- PBE0 无参数方法
			- HSE03 单参数方法
				- 与PBE0相比，Screened Coulomb kernel accelerates the convergence  of twoelectron integrals!
		- B3 based
			- B3LYP 三参数方法
			- B3PW91 三参数方法
		
## 2.2 基组和赝势
- 赝势
	- 为何使用赝势
		- 减小基组大小，有效加速计算
		- 减小电子数量，减少计算的自由度
		- 包括相对论效应（relativistic effects） 
- 基组
	- Molecular Optimized Basis Sets (MOLOPT)
		- currently available for H-Rn 
## 2.3 SCF
### 2.3.1 KS方程的求解方法
- 轨道变换方法（Orbital Transformation (OT) Method）
		- J. VandeVondele and J. Hutter, JCP 118 4365 (2003)
- 对角化和OT方法的比较
		- ![[Pasted image 20230329002921.png | 300]]
- 其他的求解方法
	- 局部最优块预处理共轭梯度法 (Locally Optimal Block Preconditioned Conjugate Gradient Method，LOBPCG)。LOBPCG 是用于大型对称正定 (SPD) 广义特征值问题的预处理特征值求解器。 [Scipy中的LOBPCG算法](https://docs.scipy.org/doc/scipy/reference/generated/scipy.sparse.linalg.lobpcg.html)
### 2.3.2 mixing method
1. BROYDEN_MIXING
2. DIRECT_P_MIXING
	1. Direct mixing of new and old density matrices
	2. MAX_DIIS
	3. EPS_DIIS 参数：控制开始进行 DIIS 计算的时间，默认的0.1通常太小，可以设置为0.3到0.5，提前开始 DIIS 计算
	4. DIIS（迭代子空间中的直接反演或迭代子空间的直接反演），也称为 Pulay 混合，是一种外推技术。DIIS由Peter Pulay在计算量子化学领域开发，旨在加速和稳定Hartree-Fock自洽场方法的收敛。
3. KERKER_MIXING
	1. Mixing of the potential in reciprocal space using the Kerker damping
4. MULTISECANT_MIXING
	1. 在Multisecant scheme for mixing中，每一次自洽场迭代都会保存两个电荷密度和波函数的信息，然后根据这些信息计算一个新的混合比例，用于混合当前电荷密度和波函数的信息。这个混合比例是通过求解一个多项式方程得到的，该多项式方程的系数是根据保存的电荷密度和波函数信息计算得到的。通过使用多项式方程，Multisecant scheme for mixing可以避免使用简单的线性混合方案带来的慢收敛问题，并且可以更快地收敛到正确的自洽场解。
	2. Multisecant scheme mixing 是一种利用多项式非线性插值历史电子密度来推导下一个迭代步骤的方法。它的基本思路是:
		1.  假设在前n个迭代步骤得到的电子密度可以拟合成一个n次多项式。   
		2.  根据n+1个历史步骤的电子密度,求出通过这n+1个点的n次多项式。
		3.  利用这个n次多项式,在下一个点求解,得到新的电子密度。
	3. 这个方法的好处是：与简单线性混合相比,多项式非线性插值可以更有效地推导出一个更为自洽的电子密度。只需要存储n个历史步骤的信息,实现简单高效。在理论上等价于高级DIIS方法。可以提供比简单混合更快的收敛速度。
5. PULAY_MIXING
## 2.4 外加电荷
- `NET_CHARGE` 关键字
## 2.5 安装

### 2.5.1 使用 toolchain 编译
- [openmpi编译与slurm冲突问题的解决方法](http://bbs.keinsci.com/thread-36200-1-1.html)
	- 最终还是绕过了 slurm，编译成功。 具体而言是注释掉了 tools/toolchain/scripts/stage1/install_openmpi.sh 里第 58 -- 63 行，以及第 68 -- 69 行。
- 2023.2 2024.1 toolchain 编译没问题
### 2.5.2 使用官方的 docker

## 2.6 单点计算相关

- https://www.cp2k.org/exercises:2018_ethz_mmm:index
- SMEAR
	- 为了使特殊点法用于金属体系，人们提出了一些近似处理方法。展宽方法（Smearing method）。这种方法用较为平滑的解析函数（下图）替换 θ 函数，把费米面进行展宽。随着追求精度的提高，费米面宽度$\Delta \epsilon$越小，平滑函数就越接近$\theta$函数。但另一方面，越接近$\theta$函数，被特殊点法所忽略的误差项（第二项）就越大，这必须通过增加特殊 k 点数目来解决。因此在做金属体系的计算时，必须选用较多的 k 点。但是对于磁矩的计算，一般认为展宽方法不可靠。**SMEAR的目的是将homo-lumo间的阶跃函数变为平滑函数，即允许电子的分数占据，从而增强收敛性**
	- SMEAR的设置相当于设定电子的温度，但是只有当选择Fermi-Dirac smear时，设定值才准确的和温度有关系；其余展宽方式，如[[VASP]]中smear = 0 设置的Gaussian展宽，和温度无直接关系
	- 在[[CP2K]]中，加入SMEAR section，但是不选择method时，默认的method为`energy——window`，所以一般需要额外设置`METHOD  FERMI_DIRAC`参数
	- $$\lim_{\Delta \epsilon\to 0} \theta'(\Delta \epsilon, \epsilon) = \theta (\epsilon)$$
- DFT + U
	- [[Hubbard U]]
	- 纯泛函对强相关的描述不好，常用处理方法是进行 DFT+U 计算，即对交换相关项进行校正
	  - 将 s、p 电子与 d、f 电子分开考虑
	  - 常将校正添加到 d 电子中，如过渡金属氧化物
	  - 效果依赖于 U 参数的选取，化学环境对 U 有严重影响

- 平面波截断能能
	- CUTOFF 默认 280 Ry
	- REL_CUTOFF 默认 40 Ry
	- NGRIDS 默认 4
	- PROGRESSION_FACTOR 默认 3
	- MULTIGRID_SET 默认 False

- 使用平面波计算非周期性边界条件
  - Poisson 方程的求解方法
    - _Analytic_ for spherical cutoff or cylindrical or 1-d cutoff （Marx and Hutter, Ab initio molecular dynamics, NIC Series）
    - _Wavelet_ solvers （Genovese et al, JCP 2006, 125 074105）
    - Solvers by Martyna and Tuckerman，_MT_ solver （Martyna & Tuckerman, JCP 1999, 110 2810-2821）
### 2.6.1 磁性相关
- 磁性的种类
    - 顺磁性 抗磁性 铁磁性 反铁磁性（整体自旋多重度为 1） 亚铁磁性
- 铁磁和反铁磁都是由电子自旋贡献：
	1. 铁磁，铁磁体系中相邻的两个原子磁矩趋向于平行排列，磁有序，磁矩不为0。反铁磁，反铁磁体系中相邻的两个原子磁矩趋向于反平行，但是它和铁磁体系一样是磁有序的。虽然反铁磁材料里的原子都有强磁矩，并且排列得整整齐齐，只是一正一反所以总和为零。
	2. 虽然二者在温度足够高的情况下都会发生顺磁相变而失去磁序。相变温度对于铁磁叫做居里温度(Tc)，对于反铁磁叫做奈尔温度 (TN)，温度低于居里温度表现铁磁性，高于居里温度表现为顺磁性，磁化率温度关系服从Curie-Weiss定律
	3. 宏观表现为，铁磁体系对外展现磁性而反铁磁体系不展现磁性。但二者都能被磁化，并且磁化后有很强的磁性。但顺磁磁性响应很弱
	4. 反铁磁材料和无磁性材料又有区别，因为反铁磁体系仍然是磁有序的，而且被磁化后仍然有很强的磁性。无磁性材料的原子磁矩主要由原子核磁矩和电子轨道角动量贡献，微观上没有磁序，被磁化后只有微弱的磁矩。
  - 反铁磁性和亚铁磁性计算
    - 需要设置原子自旋状态，越接近真实越容易收敛
    - kind 中有两种定义方式（只影响初猜，需要同时设置 UKS，相当于 LSD）
      - 定义原子磁矩，即指定此类原子 α 与 β 电子数的差值，相当于原子的电子自旋磁矩，无法精确设置原子轨道占据状态，小数也可以设置
      - 设置 bs 字段，直接设置各个原子轨道上，α 和 β 电子的数量
    - 需要同时合理设置自选多重度
      - 反铁磁性自旋多重度为 1 ，互相抵消
  - 例子
    - 赤铁矿 （反铁磁性计算）
      - [赤铁矿](https://en.wikipedia.org/wiki/Hematite)  通常没有磁性，因为它在 255 K 以下是反铁磁性的； 即磁矩排列方向相反，但在 948 K 时为弱铁磁体，高于此温度为顺磁体。 它在室温下对磁场没有任何吸引力。
      - 将不同层数 Fe 原子的磁矩设置为相反，并将自旋多重度设置为 1
      - Fe 的 3d 轨道有 5 个 α 电子和 1 个 β 电子，因此将磁矩设置为 4
    - 为了检查计算的磁矩正确与否，可以计算时输出 cub 文件
      - （输出自旋密度信息，即 α 电子的密度减去 β 电子的密度），从自旋密度图中可以考察单电子的分布情况
      - 产生的 cub 文件可直接使用 VESTA 或 VMD 程序观看
    - 使用 bs 精确设置电子组态
      - _教程见 day2 下午录音 215 min 处_
    - 铁磁性 自旋多重度必大于 1
      - 铁单个原子有 5 个单电子，每个原子的自旋多重度为 5

## 2.7 振动分析、振动光谱与声子

- 振动分析 day 3 下午 269 min
  - 拉曼计算 
	  - 满足平移不变性的最小cutoff
	  - ![[Pasted image 20230405222141.png | 400]]
	  - ![[Pasted image 20230405211817.png | 400]]
	  - ![[Pasted image 20230405211831.png | 400]]
  - 声子计算  day 4 上午 71.31
  - 
# 3 特殊的计算功能
- RT-TDDFT
	- https://www.cp2k.org/exercises:2016_summer_school:excited
- 激发态 
	- 结构优化  http://bbs.keinsci.com/thread-32126-1-1.html
	- 激发态和非绝热分子动力学培训[链接](https://compchem-cybertraining.github.io/Cyber_Training_Workshop_2021/)
		- Newton-x程序
# 4 如何增强计算的收敛？
- If you are looking for an ultimate guide to convergence of pathological SCF cases, please consider the manual of ORCA software. Though they use a bit other SCF solvers (DIAGONALIZATION-based in the CP2K glossary), the general principles are thoroughly described and explained and especially for transition metal complexes.   如果您正在寻找病理性SCF病例收敛的终极指南，请考虑ORCA软件手册。尽管他们使用了其他一些 SCF 求解器（在 CP2K 术语表中基于对角化），但对一般原理进行了彻底的描述和解释，尤其是对于过渡金属配合物。  
- Briefly, if you prefer OT solver, you should benefit from precondition selection (btw, FULL_SINGLE_INVERSE was the most reasonable in my hands) and from improved LINESEARCH variants.   简而言之，如果您更喜欢 OT 求解器，您应该受益于前提条件选择（顺便说一句，FULL_SINGLE_INVERSE在我手中是最合理的）和改进的 LINESEARCH 变体。  
- If your use diagonalization routines (you can try both OT and DIAGONALIZATION and choose the best), 如果您使用对角化例程（您可以尝试 OT 和对角化并选择最佳），
	- you should use MIXING and decrease the ALPHA value (0.2--0.05 would do). As a second step, try LEVEL_SHIFT (0.1--2 a.u.). 您应该使用 MIX 并降低 ALPHA 值（0.2--0.05 就可以了）。
	- After that, I would try MIXING%METHOD KERKER_MIXING with BETA ca. 1.5 and probably smearing. 第二步，尝试LEVEL_SHIFT （0.1--2 a.u.）。在那之后，我会尝试 MIXING%METHOD KERKER_MIXING 与 BETA 大约 1.5 并可能进行涂抹。
- If that doesn't help, check the sanity of the initial geometry of your system (and please show the example of the system).  如果这没有帮助，请检查系统的初始几何形状的健全性（并请显示系统的示例）。
# 5 报错问题
- 有时体系过大，cp2k运行时显示Index to radix array not found错误：
	- **问题来源** by default CP2K uses the FFT roots available in the internal  FFT library. This is currently restricted to a maximum  of 1024. Your input requires a length of about 1200.  If you compiled cp2k with an external FFT library (probably FFTW3)  you can switch on extended lengths with the keyword  EXTENDED_FFT_LENGTHS in the GLOBAL section.
	- **解决方法** 此时在脚本的&GLOBAL中加上：`EXTENDED_FFT_LENGTHS T`
	