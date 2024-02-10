#DFT #QuantumEspresso

# 1 介绍

参考[链接](http://theossrv1.epfl.ch/Main/DFTHubbard)

DFT+Hubbard（即 DFT+_U_ 及其推广 DFT+_U_+_V_、DFT+_U_+_J_ 等）是一种流行且强大的[[工具]]，用于模拟具有局部 d 或 f 电子。从 DFT 到 DFT+_U_ 的功能扩展简单且计算成本低（与计算成本更高的方法相比，例如混合泛函）；此外，它通常是准确的（我们实际上希望它可以比混合动力车更准确，如果应用得当，因为当使用以下技术应用时，它会意识到周围的环境）。可以在这里找到一些重要的开创性参考资料  [V.I. Anisimov et al., Phys. Rev. B 44, 943 (1991)](https://journals.aps.org/prb/abstract/10.1103/PhysRevB.44.943), [A.I. Liechtenstein et al., Phys. Rev. B 52, R5467(R) (1995)](https://journals.aps.org/prb/abstract/10.1103/PhysRevB.52.R5467), [S. L. Dudarev et al., Phys. Rev. B 57, 1505 (1998)](https://journals.aps.org/prb/abstract/10.1103/PhysRevB.57.1505)。适当的 DFT+Hubbard 研究的关键要素是 Hubbard 参数，例如 onset _U_，或者在下面讨论的情况下，还有 intersite _V_。了解 _U_ 和 _V_ 的正确值是什么是正确使用这种计算技术的关键。

## 1.1 DFT+Hubbard: correlations or self-interactions?

DFT+U 历来旨在描述固体中的电子相关性；事实上，它的灵感来自晶格上电子的 Hubbard 模型。但是，当这种方法不是应用于晶格，而是应用于固体的非均匀相互作用电子气时，如密度泛函理论所描述的，这种方法实际上如何解决相关性问题？ 在我们看来，第一个关键步骤是表明 DFT+U 泛函旨在校正能量曲率相对于标准 DFT 泛函（[M. Cococcioni and S. de Gironcoli, Phys. Rev. B 71, 035105 (2005)](https://journals.aps.org/prb/abstract/10.1103/PhysRevB.71.035105)））。请注意，没有定理可以证明这是一个需要满足的确切属性，但有人可能会争辩说，在一个非常简单的系统（氢分子离子 H2+）的解离中，只有在以下情况下才能获得正确的左右简并性：能量存在于向左/右原子添加/去除任意部分的电子。这种考虑可以启发式地扩展到固体中的局部 Hubbard 流形（即 3d 或 4 f 电子的集合），DFT+U 旨在在流形中添加或移除一小部分电子时使能量线性化 - 证明上面的线性化参数。当然，H2+ 是系统遭受单电子自相互作用的一个定义非常明确的情况，所以哈伯德项实际上是在修复自相互作用，而不是相关性！这个新颖的观点是通过以分子为例（而不是固体 - 如果系统仅包含一个过渡金属原子，则不存在 Hubbard 模型预期的相关性）并表明 DFT+U 非常显着地改进了它们的描述（[H.J. Kulik et al., Phys. Rev. Lett. 97, 103001 (2006)](https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.97.103001)）。许多关于分子的论文紧随其后，强化了这样一种情况，即当嵌入到真正的 DFT 计算而不是格子模型中时，Hubbard 校正根本不处理相关性，而是处理自相互作用（这一点可以更清楚通过显示 U 控制从哈伯德流形到环境的电荷转移 - 在晶格模型中，除了另一个站点没有其他地方可以去，但在真实的 DFT 计算中，离开哈伯德流形的电子将继续并停留在配体，或四处游荡，但不会去另一个地点/哈伯德歧管（Hubbard manifold）。

## 1.2 使用 DFT+Hubbard 校正带隙是否恰当?

DFT 是一种总能理论。用于求解 Kohn-Sham 方程的非相互作用 Kohn-Sham 电子辅助系统能够相当好地捕获真正的量子动能，从而捕获相互作用系统的基态能量，但 Kohn-Sham 能级没有理论基础可以解释为能带结构（HOMO 确实如此，但仅仅因为它决定了电荷密度的渐近衰减，所以精确的 DFT 必须准确描述）。因此，要求 DFT+U 或其扩展来表示光谱特性（例如带隙或任何其他带电激发）是不正确的。

## 1.3 Hubbard _V_ 参数是什么? 

DFT+U is now widely used, but not everybody is aware of DFT+U+V, which turns out to be a valuable and important extension of DFT+U ([V.L. Campo Jr and M. Cococcioni, J. Phys.: Condens. Matter 22, 055602 (2010)](https://iopscience.iop.org/article/10.1088/0953-8984/22/5/055602/meta)). V, like U, is a Hubbard parameter, but contrary to U it describes inter-site electronic interactions and not on-site ones (e.g. how a 3d Hubbard manifold talks to the 2p orbitals of oxygen ligands). The idea of including V in the DFT+Hubbard formulation comes from the "extended Hubbard model", where both on-site U and inter-site V parameters are used. So, DFT+U+V is a natural extension of DFT+U. Is it important to include V in calculations? Yes, when covalent interactions between the Hubbard manifold and its surrounding are strong in the material of interest. For example, the inclusion of inter-site V turns out to be very important to compute accurately the structure of transition-metal di-oxides ([H. J. Kulik and N. Marzari, J. Chem. Phys. 134, 094103 (2011)](https://aip.scitation.org/doi/10.1063/1.3559452)), the voltages of Li-ion batteries ([M. Cococcioni and N. Marzari, Phys. Rev. Materials 3, 033801 (2019)](https://journals.aps.org/prmaterials/abstract/10.1103/PhysRevMaterials.3.033801)), or the formation energies of oxygen vacancies in transitional-metal oxides ([C. Ricca, I. Timrov, M. Cococcioni, N. Marzari, and U. Aschauer, Phys. Rev. Research 2, 023313 (2020)](https://journals.aps.org/prresearch/abstract/10.1103/PhysRevResearch.2.023313)).

## 1.4 如何选择 Hubbard 参数值？ 

经验 Hubbard 参数被广泛使用。例如，通常的做法是选择一个能够重现某些感兴趣特性的 U 值（有时甚至是实验带隙，从概念的角度来看这并不是真正正确的 - 特别是因为 DFT 不是带隙理论，但也因为边界态可能源自未涉及 U 项的原子轨道）。此外，如果没有可用于感兴趣材料的实验数据，则 U 通常取自对类似材料的研究，但这样做是不合理的：请参阅下面关于哈伯德参数可迁移性（[[Hubbard U#1 5 Hubbard参数的通用性 迁移性（universal transferable）如何？]]））。此外，使用经验 U 使 DFT+U 成为半经验方法，而不是第一性原理方法，因为 U 是手动调整/调整的。
我们认为正确的方法是根据第一原理计算 U（和 V）作为线性响应属性，这是([M. Cococcioni and S. de Gironcoli, Phys. Rev. B 71, 035105 (2005)](https://journals.aps.org/prb/abstract/10.1103/PhysRevB.71.035105)的关键建议。多年来，由于需要通过大型超晶胞中的有限差分来计算线性响应特性，其实际实施仍然有些麻烦。由于使用了密度泛函微扰理论 (DFPT)（[I. Timrov, N. Marzari, and M. Cococcioni, Phys. Rev. B 98, 085127 (2018)](https://journals.aps.org/prb/abstract/10.1103/PhysRevB.98.085127))。这种基于 DFPT 计算哈伯德 U 和 V 的方法在 HP 代码的 [[Quantum ESPRESSO]] 中实现，其中“HP”代表“哈伯德参数”（[I. Timrov, N. Marzari, and M. Cococcioni, Phys. Rev. B 98, 085127 (2018)](https://journals.aps.org/prb/abstract/10.1103/PhysRevB.98.085127))。 （注意：目前 HP 代码仅允许计算开壳系统的 Hubbard 参数；对于闭壳系统，Hubbard 参数在物理上非常大[K. Yu and E.A. Carter, J. Chem. Phys. 140, 121105 (2014)](https://aip.scitation.org/doi/full/10.1063/1.4869718)）。

## 1.5 Hubbard 参数的通用性/迁移性（universal/transferable）如何？

Mostly, not. When Hubbard parameters (U and V) are computed from first principles (e.g. using linear response theory - see above), their value depends on many physical or technical details: first and foremost, the actual size/shape of the Hubbard manifold (i.e. which localised functions are used for the Hubbard projections: atomic orbitals, orthogonalised atomic orbitals, Wannier functions, ...); these will change depending on which [pseudopotentials](https://www.materialscloud.org/discover/sssp/table/efficiency) are used, and which was the [oxidation state](http://theossrv1.epfl.ch/Main/OxidationStates) of the original all-electron atomic calculation (see a discussion of this in the Appendix of [H.J. Kulik and N. Marzari, J. Chem. Phys. 129, 134314 (2008)](https://aip.scitation.org/doi/10.1063/1.2987444#_i4)). Also, which exchange-correlation functional (LDA, PBE, PBEsol,...) has been used will matter, and whether U and V are computed as a one-shot response property, or iterated self-consistently (see e.g. [M. Cococcioni and N. Marzari, Phys. Rev. Materials 3, 033801 (2019)](https://journals.aps.org/prmaterials/abstract/10.1103/PhysRevMaterials.3.033801)). Therefore, Hubbard U and V computed under one set of technical assumptions (e.g. using PBEsol, ortho-atomic orbitals, ultrasoft pseudopotentials from the GBRV library) cannot be used for a different DFT+U+V (or DFT+U) calculation (e.g. using LDA, atomic orbitals, norm-conserving pseudopotentials from the Pseudo Dojo library). Consistency between how Hubbard parameters are determined, and how they are used, is crucial. Moreover, the Hubbard parameters for the same element/pseudopotential but in different environments can vary quite significantly (see the U for Co-3d when used in LiCoO2 and in CoO, whose values differ significantly, see e.g. [A. Floris, I. Timrov, B. Himmetoglu, N. Marzari, S. de Gironcoli, and M. Cococcioni, Phys. Rev. B 101, 064305 (2020)](https://journals.aps.org/prb/abstract/10.1103/PhysRevB.101.064305)). **Hopefully, the addition of the V term, or using orthogonalised atomic projectors, ameliorates transferability.**

## 1.6 使用原子轨道（Atomic orbitals）还是正交原子轨道（ortho-atomic orbitals）？ 

如上所述，U 和 V 的值在很大程度上取决于计算的技术细节。关于赝势，我们建议使用 [SSSP 库](https://www.materialscloud.org/discover/sssp/table/efficiency) 中建议的那些。关于交换相关泛函，对于固体 PBEsol 可能是最好的直接选择，因为 PBE 主要针对分子（如今 SCAN 变得非常流行 - 但仍然没有可用的 SCAN 赝势）。最后，对于 Hubbard 流形，应该使用原子轨道还是正交原子轨道（这里我们指的是 [Quantum ESPRESSO](https://www.quantum-espresso.org/) 中实现的两个选项）？正交原子轨道似乎提供了更准确的结果——可能是因为如果使用原子（即非正交）轨道，哈伯德校正可以在轨道重叠的区域添加两次，或者，当单独使用 U 时，正交化程序使系统更加“意识到”配体的存在（但是，最后，我们认为最通用的方法是在正交原子轨道中同时使用 U 和 V）。

# 2 DFT+_U_+*V*使用参考

**DFT+_U_+_V_ 用于过渡金属材料的精确电子特性研究** [10.1103/PRXEnergy.1.033003](https://doi.org/10.1103/PRXEnergy.1.033003)

在原子水平上了解过渡金属元素的特性如何导致高效的[[电化学]]过程对于锂离子电池新型阴极材料的开发至关重要。虽然使用局部和半局部交换相关泛函的密度泛函理论 (DFT) 计算可以在对这些材料进行第一性原理预测方面发挥重要作用，但由于自相互作用误差，它们也可能产生不令人满意的结果。

> [!writing]
> While density-functional theory (DFT) calculations with local and semilocal exchange-correlation functionals can play an important role in producing first-principles predictions for these materials, they can also produce unsatisfactory results because of self-interaction errors.

在 DFT+_U_+_V_ 中，_U_ 是一个数值现场 Hubbard 参数，用于固定同一原子上电子之间的相互作用，而 _V_ 表示相邻位点上电子之间的位点间相互作用的强度。包括位点间哈伯德相互作用对于准确预测热力学量至关重要，因为它平衡了由位点间 U 和位点间 V 轨道杂交引起的定位驱动。与其他方法相比，这允许 DFT+_U_+_V_ 准确地描述这种定位-杂交相互作用。

> [!writing]
> In DFT+_U_+_V_, _U_ is a numerical onsite Hubbard parameter meant to fix interactions between electrons on the same atom, while _V_ represents the strength of the intersite interactions between electrons on neighboring sites.
>
> including intersite Hubbard interactions is essential for accurately predicting thermodynamic quantities because it balances the drive for localization induced by the onsite U with intersite _V_ orbital hybridizations.

# 3 Hubbard *U*和*V*参数的计算

使用[[Quantum Espresso]]中的`hp.x`对 Hubbard *U*和*V*参数进行线性响应计算

为了计算磁绝缘体的*U*，需要遵循特别程序。 由于我们不知道先验的磁化强度
的系统，我们用有限的进行计算 starting_magnetization（在 -1 到 +1 的范围内）这需要将感兴趣的系统视为金属（即使用涂抹）。然而，使用拖尾会在线性响应中产生问题 U 的计算，因为在 DFPT 中，对于 q=0 处的金属，存在金属与费米 DOS 的倒数成正比的校正水平[^1]。 因此，如果我们将系统视为假金属，那么费米能级的 DOS 可能非常小，这将导致
极大的偏移费米能量以及作为数值发散的结果 DFPT 计算。计算的流程如下：

> 1. 通过将系统视为假磁性金属来执行 SCF 计算。
>    1. 即，指定 `occupations = 'smearing'` 并设置一些非常小的展宽参数，并设置`nspin = 2`和 `starting_magnetization`。而且，conv_thr 使用的收敛阈值不要太小，否则之后的 scf 计算可能出现收敛问题。
> 2. Perform a SCF calculation by treating the system as a magnetic insulator using the total magnetization obtained in the previous step. Extract from the previous calculation the total magnetization and the number of bands (Kohn-Sham states) and use them in this SCF calculation by using the keywords `tot_magnetization` and `nbnd`, respectively.

[^1]: See Eq.(79) in Rev. Mod. Phys. 73, 515 (2001)
