# 高斯散度定理及其在 QuESo 中的应用

> 本文面向工程硕士/博士研究生，以简体中文写成，目的是帮助读者直观理解**高斯散度定理**（Gauss' Divergence Theorem，又称高斯定理），并了解 QuESo 如何利用该定理高效计算嵌入式固体的数值积分。

---

## 目录

1. [定理的数学表述](#1-定理的数学表述)
2. [几何与物理直觉——以流体通量为例](#2-几何与物理直觉以流体通量为例)
3. [二维解析算例：两侧均等于同一数值](#3-二维解析算例两侧均等于同一数值)
4. [三维解析算例：验证两侧相等](#4-三维解析算例验证两侧相等)
5. [QuESo 中的应用：将体积分转化为面积分](#5-queso-中的应用将体积分转化为面积分)
6. [小结](#6-小结)

---

## 1. 定理的数学表述

### 二维形式

设 $\Omega \subset \mathbb{R}^2$ 为有界区域，$\partial\Omega$ 为其分段光滑边界，$\mathbf{g}(x,y)$ 为定义在 $\Omega$ 上的光滑向量场，则

$$
\int_{\Omega} \nabla \cdot \mathbf{g}(\mathbf{x})\, \mathrm{d}\Omega
=
\oint_{\partial\Omega} \mathbf{g}(\mathbf{x}) \cdot \mathbf{n}\, \mathrm{d}\Gamma
\tag{1}
$$

其中

- $\nabla \cdot \mathbf{g} = \dfrac{\partial g_x}{\partial x} + \dfrac{\partial g_y}{\partial y}$ 是向量场的**散度**（标量）；
- $\mathbf{n}$ 是 $\partial\Omega$ 上的**单位外法向量**；
- $\mathrm{d}\Gamma$ 是边界弧长微元。

### 三维形式

设 $\Omega \subset \mathbb{R}^3$ 为有界体，$\partial\Omega$ 为其分段光滑封闭曲面，则

$$
\int_{\Omega} \nabla \cdot \mathbf{g}(\mathbf{x})\, \mathrm{d}\Omega
=
\iint_{\partial\Omega} \mathbf{g}(\mathbf{x}) \cdot \mathbf{n}\, \mathrm{d}\Gamma
\tag{2}
$$

其中

- $\nabla \cdot \mathbf{g} = \dfrac{\partial g_x}{\partial x} + \dfrac{\partial g_y}{\partial y} + \dfrac{\partial g_z}{\partial z}$；
- $\mathbf{n}$ 是封闭曲面 $\partial\Omega$ 上指向**区域外部**的单位法向量；
- $\mathrm{d}\Gamma$ 是面积微元。

> **核心含义**：体内所有"源/汇"的总强度 $=$ 通过外表面净流出的总通量。

---

## 2. 几何与物理直觉——以流体通量为例

想象 $\Omega$ 是充满流体的一个三维容器，$\mathbf{g}(\mathbf{x})$ 是流速场。

- **散度 $\nabla \cdot \mathbf{g}$ 的含义**：在某点处，流体是向外"喷出"（正值）还是向内"汇聚"（负值）。
  - 若 $\nabla \cdot \mathbf{g} > 0$：该点是"源"，流体在此产生；
  - 若 $\nabla \cdot \mathbf{g} < 0$：该点是"汇"，流体在此消失；
  - 若 $\nabla \cdot \mathbf{g} = 0$：该点出入平衡（无压缩流体的典型情形）。

- **左侧积分**：把容器内所有点的"产生/消耗量"全部加起来，得到**总净产生量**。

- **右侧积分**：只看容器壁，统计从壁面向外流出的**总通量**。

**散度定理说的就是：这两个量必然相等。**

直觉上很自然：容器内多产生了多少流体，最终一定从边界流出去多少——守恒！

---

## 3. 二维解析算例——两侧均等于同一数值

**设置**

- 区域：单位圆盘 $\Omega = \{(x,y) \mid x^2 + y^2 \leq 1\}$；
- 向量场：$\mathbf{g}(x,y) = (x,\; y)$（即径向流场）。

### 计算左侧（体积分）

$$
\nabla \cdot \mathbf{g}
= \frac{\partial x}{\partial x} + \frac{\partial y}{\partial y}
= 1 + 1 = 2
$$

因此

$$
\int_{\Omega} \nabla \cdot \mathbf{g}\, \mathrm{d}\Omega
= \int_{\Omega} 2\, \mathrm{d}\Omega
= 2 \cdot \text{Area}(\Omega)
= 2\pi
$$

### 计算右侧（边界积分）

单位圆的边界：$\partial\Omega = \{(\cos\theta,\sin\theta)\mid \theta\in[0,2\pi)\}$，外法向量 $\mathbf{n} = (\cos\theta,\sin\theta)$，弧长微元 $\mathrm{d}\Gamma = \mathrm{d}\theta$。

$$
\oint_{\partial\Omega} \mathbf{g}\cdot\mathbf{n}\, \mathrm{d}\Gamma
= \int_0^{2\pi} (\cos\theta,\sin\theta)\cdot(\cos\theta,\sin\theta)\, \mathrm{d}\theta
= \int_0^{2\pi} 1\, \mathrm{d}\theta
= 2\pi
$$

### 结果

$$
\underbrace{2\pi}_{\text{左侧（体积分）}}
=
\underbrace{2\pi}_{\text{右侧（边界积分）}}
\quad \checkmark
$$

两侧完全相等，定理得到验证。

---

## 4. 三维解析算例——验证两侧相等

**设置**

- 区域：单位球 $\Omega = \{(x,y,z)\mid x^2+y^2+z^2 \leq 1\}$；
- 向量场：$\mathbf{g}(x,y,z) = (x,\; y,\; z)$。

### 计算左侧

$$
\nabla \cdot \mathbf{g} = 1+1+1 = 3
$$

$$
\int_{\Omega} 3\, \mathrm{d}\Omega = 3\cdot\frac{4\pi}{3} = 4\pi
$$

### 计算右侧

单位球面 $\partial\Omega$，外法向量 $\mathbf{n} = (x,y,z)$（因为球面上 $|\mathbf{x}|=1$）。

$$
\iint_{\partial\Omega} \mathbf{g}\cdot\mathbf{n}\, \mathrm{d}\Gamma
= \iint_{\partial\Omega} (x^2+y^2+z^2)\, \mathrm{d}\Gamma
= \iint_{\partial\Omega} 1\, \mathrm{d}\Gamma
= 4\pi
$$

（单位球面的面积为 $4\pi$。）

### 结果

$$
\underbrace{4\pi}_{\text{左侧（体积分）}}
=
\underbrace{4\pi}_{\text{右侧（边界积分）}}
\quad \checkmark
$$

---

## 5. QuESo 中的应用：将体积分转化为面积分

### 背景问题

在嵌入式有限元方法（Embedded FEM / Immersed FEM）中，复杂几何体被"嵌入"到规则的背景六面体网格中。几何边界（由 STL 面片描述）任意切割背景单元，产生**被切割单元**（cut element）。

对每个被切割单元，需要计算"矩（moments）"：

$$
b_j = \int_{\Omega_c} f_j(\mathbf{x})\, \mathrm{d}\Omega, \quad j = 1,\dots,m
\tag{3}
$$

其中 $\Omega_c$ 是该单元内属于物体内部的不规则体积域，$f_j$ 是一组基函数（如 Legendre 多项式张量积）。

直接对 $\Omega_c$ 做三维体积分代价高昂且不稳健，QuESo 采用散度定理将其转化为边界积分。

### 利用散度定理降维

对每个基函数 $f_j$，构造一个向量场 $\mathbf{g}_j(\mathbf{x})$，满足

$$
\nabla \cdot \mathbf{g}_j(\mathbf{x}) = f_j(\mathbf{x})
\tag{4}
$$

（这相当于求 $f_j$ 的一个"反散度"，对于多项式基函数，可以显式写出解析表达式。）

由散度定理（公式 2）：

$$
b_j
= \int_{\Omega_c} f_j(\mathbf{x})\, \mathrm{d}\Omega
= \int_{\Omega_c} \nabla \cdot \mathbf{g}_j(\mathbf{x})\, \mathrm{d}\Omega
= \iint_{\partial\Omega_c} \mathbf{g}_j(\mathbf{x}) \cdot \mathbf{n}\, \mathrm{d}\Gamma
\tag{5}
$$

**体积积分被完全转化为面积积分！**

### 封闭曲面的构造

为了使散度定理成立，$\partial\Omega_c$ 必须是**封闭曲面**。QuESo 对每个被切割单元自动完成以下步骤：

1. **求交**：计算 STL 几何与该背景单元的交线，得到内部的三角形曲面片（来自 STL 切割部分）；
2. **补面**：用单元的部分外表面（位于物体内侧的那些面）自动补齐开口，形成封闭的表面网格；
3. **积分**：在封闭表面的每个三角形面片上，使用高阶高斯积分计算 $\iint \mathbf{g}_j \cdot \mathbf{n}\, \mathrm{d}\Gamma$，获得精确的 $b_j$。

### STL 面片（STL mesh）简介

**STL**（STereoLithography）文件是一种常见的三维几何格式，用大量**三角形面片**拼成物体表面：

- 每个三角形记录三个顶点坐标和一个面法向量；
- 所有三角形拼在一起近似描述物体的外表面（B-Rep）；
- STL 文件可能存在缝隙、翻面、重复点等"缺陷"——QuESo 的算法对这类缺陷有较强的鲁棒性，因为这些面片**只用来做积分**，而非用于几何建模或可视化。

### Moment Fitting（矩拟合）完整流程

利用散度定理求出 $\mathbf{b} = (b_1,\dots,b_m)^\top$ 之后，QuESo 通过求解线性方程组确定积分权重：

$$
A\,\boldsymbol{\omega} = \mathbf{b}
\tag{6}
$$

其中

- $A_{ji} = f_j(\mathbf{x}_i)$（基函数在候选积分点上的取值矩阵，$m \times n_q$）；
- $\boldsymbol{\omega} = (\omega_1,\dots,\omega_{n_q})^\top$（待求权重）；
- 通过**非负最小二乘（NNLS）**求解，保证所有权重 $\omega_i \geq 0$，且积分点数满足 $n_q \leq (p+1)^3$（$p$ 为多项式阶数）。

### 方法优势总结

| 传统方法（细分/剖分） | QuESo（散度定理 + Moment Fitting） |
|---|---|
| 对复杂体域直接做三维积分，产生大量积分点 | 只需对封闭表面做二维积分，积分点少 |
| 对 STL 几何质量要求高 | 对"脏几何"（裂缝、翻面等）鲁棒 |
| 积分点数随剖分深度指数增长 | 积分点数 $\leq (p+1)^3$，固定上界 |
| 每个 cut 单元需要复杂几何布尔运算 | 只需求交 + 补面，无需布尔运算 |

---

## 6. 小结

- **散度定理**将区域内的"发散量"（体积分）与通过外边界的"净通量"（面积分）联系起来，是向量分析的基石定理之一。
- 直观上等价于守恒：体内产生多少，就从边界流出多少。
- 在 QuESo 中，散度定理将难以直接计算的**不规则体积分**转化为**封闭曲面上的面积分**，从而实现对 STL 嵌入式固体的高效、鲁棒数值积分，为 moment fitting 提供精确的积分矩 $b_j$。

---

### 参考文献

1. Manuel Meßmer *et al.*, *Robust numerical integration of embedded solids described in boundary representation*, Comput. Methods Appl. Mech. Engrg. 419 (2024) 116670. <https://doi.org/10.1016/j.cma.2023.116670>
2. Manuel Meßmer *et al.*, *Efficient CAD-integrated isogeometric analysis of trimmed solids*, Comput. Methods Appl. Mech. Engrg. 400 (2022) 115584. <https://doi.org/10.1016/j.cma.2022.115584>
