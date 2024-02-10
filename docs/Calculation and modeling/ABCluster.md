# ABCluster
# 1 Introduction
ABCluster is one of the most **successful, powerful, and popular** program for the global optimization of chemical cluster structures. The essential **artificial bee colony** algorithm of ABCluster enables the following tasks to be routine for every chemist:  
ABCluster 是用于全局优化化学团簇结构的最成功、最强大和最受欢迎的程序之一。ABCluster 的基本人工蜂群算法使以下任务成为每个化学家的常规任务：

- **Conformation search for molecules:** find the conformation of the lowest energy for a flexible molecule (both bond rotations and ring deformations can be considered);  
    分子构象搜索：求柔性分子最低能的构象（键旋转和环形变均可考虑）;
- **Global optimization of clusters:** find the structure of the lowest energy for the aggregates of any rigid and flexible structural units;  
    团簇全局优化：求任意刚性和柔性结构单元的集合体能量最低的结构;
- **Structure sampling:** sample on the potential energy surface to generate structures of high diversities;  
    结构采样：在势能面上取样，生成高多样性的结构;
- **Structure generator:** rapidly generate clusters of assigned flexibilities (some bond rotations can be frozen) and complicated topology (like bi-phase, micelle, ligated) for subsequent studies.  
    结构发生器：快速生成指定柔性（某些键旋转可以冻结）和复杂拓扑（如双相、胶束、连接）的簇，用于后续研究。
    
最新版本的 ABCluster 可以从以下位置获得： [http://www.zhjun-sci.com/software-abcluster-download.php](http://www.zhjun-sci.com/software-abcluster-download.php)

# 2 ABCluster Citation
The best way to support the development of ABCluster is that in any published works using ABCluster, please include the following references:  
- Zhang, J.; Dolg, M. [ABCluster: The Artificial Bee Colony Algorithm for Cluster Global Optimization.](https://pubs.rsc.org/en/Content/ArticleLanding/2015/CP/C5CP04060D) _Phys. Chem. Chem. Phys._ **2015**, _17_, 24173-24181.  
- Zhang, J.; Dolg, M. [Global Optimization of Clusters of Rigid Molecules Using the Artificial Bee Colony Algorithm.](https://pubs.rsc.org/en/Content/ArticleLanding/2016/CP/C5CP06313B) _Phys. Chem. Chem. Phys._ **2016**, _18_, 3003-3010. 
# 3 [[ABCluster]] 结合 [[CP2K]] 进行表面结构优化
**参考**[文档链接](https://zhjun-sci.com/abcluster/doc/eg-geom-cp2k.html)
To use CP2K with `geom`, you need two files: `misc/runCP2K.sh` and `CP2K.tmp`.
`misc/runCP2K.sh` is a script to call CP2K. There is **ONLY 1 line** that you need to change:
runCP2K.sh[](https://zhjun-sci.com/abcluster/doc/eg-geom-cp2k.html#id1 "Permalink to this code")
```bash
cp2kcmd="mpirun cp2k.popt"
```

You need to change it so you can correctly call CP2K on your hardware, like `srun -n 192 cp2k.popt` or `/opt/cp2k-8.0/cp2k.popt`.

The below file `CP2K.tmp` is a CP2K input template. You’d better **ONLY change the following parts:**

```
 1CHARGE       0
 2MULTIPLICITY 1
 3!     LSD
 4!     SURFACE_DIPOLE_CORRECTION T
 5!     SURF_DIP_DIR              Z
 6BASIS_SET_FILE_NAME    BASIS_MOLOPT
 7POTENTIAL_FILE_NAME    GTH_POTENTIALS
 8... omitted ...
 9PARAMETER_FILE_NAME  dftd3.dat
10... omitted ...
11&CELL
12   A                  30.00000000    0.00000000    0.00000000
13   B                   0.00000000   30.00000000    0.00000000
14   C                   0.00000000    0.00000000   30.00000000
15   PERIODIC            XYZ
16   MULTIPLE_UNIT_CELL  1 1 1
17&END CELL
18... omitted ...
19&KIND H
20   BASIS_SET DZVP-MOLOPT-SR-GTH
21   POTENTIAL GTH-PBE-q1
22&END KIND
```

To sum up, you need to change:
- The `CHARGE` and `MULTIPLICITY` of the system
- If `MULTIPLICITY` is not `1`, then `LSD` or `UKS` should be un-commented.
- For **surface-supported systems**, un-comment `SURFACE_DIPOLE_CORRECTION T` and `SURF_DIP_DIR Z`. Please ensure that the vacuum direction should be Z.
- The paths and filenames for `BASIS_SET_FILE_NAME`, `POTENTIAL_FILE_NAME`, and `PARAMETER_FILE_NAME`.
- The cell constants `A`, `B`, and `C`.
- The basis sets for each element, `KIND`.
- If you understand exactly what you are doing, you can change anywhere.

# 4 ABCluster 海洋相关用途
