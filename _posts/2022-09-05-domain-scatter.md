---
title: '基于Domain Scatter的领域自适应/领域泛化 理论上界'
date: 2022-09-05
permalink: /_posts/2022-09-05-domain-scatter
tags:
  - theoretical blogs
  - domain adaptation
---

大家好，今天我们再来看一个bound的证明。（没错我就是bound收集狂人，笑）今天这个bound来自于一篇15年的TPAMI文章，名字叫做《Scatter Component Analysis: A Unifified Framework for Domain Adaptation and Domain Generalization》。

---

这篇文章做了一个什么样的工作呢？基于再生核希尔伯特空间做了一系列的所谓Scatter的工作，就是把领域和领域之间拉近，同一个类之间拉近，不同的类之间分开的一个工作。我们先来介绍一下背景知识再生核希尔伯特空间（Reproducing Kernel Hilbert Space，简称RKHS）。

首先记花体的$\mathcal{X}$为任意一个集合，$\mathcal{H} \subset\{f: \mathcal{X} \rightarrow \mathbb{R}\}$ 是一个希尔伯特函数空间，里面每个元素都是从$\mathcal{X}$映到实数域$\mathbb{R}$的一个函数，希尔伯特空间即具有內积，并且完备。定义一个泛函（函数的函数）$L_{x}: \mathcal{H} \rightarrow \mathbb{R}$为$L_{x}[h]:=h(x), \forall h \in \mathcal{H}$，如果泛函$L_x$总是有界，即对任意的$x\in\mathcal{X}$，都存在一个$\lambda>0$，使得$\left|L_{x}[h]\right|=|h(x)| \leq \lambda\|h\|_{\mathcal{H}}$，则称这个空间为再生核希尔伯特空间（RKHS）。

RKHS中总是存在一个函数$\phi: \mathcal{X} \rightarrow \mathcal{H}$，使得

$$
L_{x}[h]=\langle h, \phi(x)\rangle=h(x) \quad \text { for all } h \in \mathcal{H} \text { and } x \in \mathcal{X} \text {. }

$$

（记住这个，后面会用）

我们称这个函数$\phi$为特征映射，$\langle\phi(t), \phi(x)\rangle=: \kappa(t, x)$，二元函数$\kappa$称为再生核。我们再来定义均值，记$\mu_{\mathbb{P}}=\underset{x \sim \mathbb{P}}{\mathbb{E}}[\phi(x)]$，就是服从分布$\mathbb{P}$的数据经特征映射$\phi$作用后的均值。RKHS上的范数$\|\cdot\|_{\mathcal{H}}$，就是由內积诱导定义出的范数$\|f\|_{\mathcal{H}}=\sqrt{<f,f>}$。

接下来，文章定义了一个乘积算子$\mathbf{M}_{g}$，$\mathbf{M}_{g}(h)(x)=g(x) h(x)$。紧接着，文章给出了引理4，对于采用了universal kernel的RKHS，给定$g, h \in \mathcal{H}$，总有

$$
\left\|\mathbf{M}_{g}(h)\right\|_{\mathcal{H}}=\|g \cdot h\|_{\mathcal{H}} \leq\|g\|_{\infty} \cdot\|h\|_{\mathcal{H}}

$$

universal kernel的定义：对于输入空间$\mathcal{X}$的任意紧子集$\mathcal{Z}$，集合$K(Z):=\overline{\operatorname{span}}\left\{K_{y}: y \in Z\right\}$，总是在无穷模意义下在$C(\mathcal{Z})$中稠密。此处要求universal kernel是为了保证RKHS $\mathcal{H}$在乘积算子下封闭，i.e $g\cdot h \in \mathcal{H}$.

接下来我们再来介绍domain adaptation里面的一个经典项，discrepancy divergence

$$
\operatorname{disc}(\mathbb{P}, \mathbb{Q})=\sup _{h, h^{\prime} \in \operatorname{Hyp}}\left|\mathcal{L}_{\mathbb{P}}\left(h, h^{\prime}\right)-\mathcal{L}_{\mathbb{Q}}\left(h, h^{\prime}\right)\right|

$$

其中$\mathcal{L}_{\mathbb{P}}\left(h, h^{\prime}\right)=\mathbb{E}_{x \sim \mathbb{P}}\left[\ell\left(h(x), h^{\prime}(x)\right)\right]$，$\ell$是一个损失函数。根据Mansour 2009年的工作，只需要该损失函数满足对称性和三角不等式，即可将之融进一系列的泛化误差上界，为domain adaptation算法的设计提供理论保证。这里也正是利用了这个关系。

引理8：

记$\mathcal{H}$为具有universal kernel的RKHS，损失函数为平方损失函数$\ell\left(y, y^{\prime}\right)=\left(y-y^{\prime}\right)^{2}$（有对称性和三角不等式），假设集$\operatorname{Hyp}=\left\{f \in \mathcal{H}:\|f\|_{\mathcal{H}} \leq 1 \text { and }\|f\|_{\infty} \leq r\right\} $，则有下列不等式成立

$$
\operatorname{disc}_{\ell}(\mathbb{P}, \mathbb{Q}) \leq 8 r \sqrt{\Psi_{}\left(\left\{\mu_{\mathbb{P}}, \mu_{\mathbb{Q}}\right\}\right)}

$$

其中左边的项为前面定义的discrepancy divergence，右边根号下的项为文章提出的domain scatter，具体定义为

$$
\Psi\left(\left\{\mu_{\mathbb{P}}, \mu_{\mathbb{\mathbb{Q}}}\right\}\right)=\frac{1}{2} \left(\left\|\bar{\mu}-\mu_{\mathbb{P}}\right\|^{2}+\left\|\bar{\mu}-\mu_{\mathbb{Q}}\right\|^{2}\right)

$$

其中$\bar{\mu}=\frac{1}{2} \left( \mu_{\mathbb{P}}+\mu_{\mathbb{Q}}\right)$，就是两个域在特征空间上各自均值的平均。最终这个domain scatter项是要被最小化，含义也很清晰，就是要在特征空间上拉近源域与目标域的距离，从而使得在源域上训练好的分类器能在目标域上正常使用。

在证明之前，我们先来稍微做一点计算，将$\bar{\mu}$的定义代入，我们可以得出$\Psi\left(\left\{\mu_{\mathbb{P}}, \mu_{\mathbb{\mathbb{Q}}}\right\}\right)$更精简的一个表达

$$
\Psi\left(\left\{\mu_{\mathbb{P}}, \mu_{\mathbb{\mathbb{Q}}}\right\}\right)=\frac{1}{2} \left(\left\|\frac{1}{2} \left( \mu_{\mathbb{P}}+\mu_{\mathbb{Q}}\right)-\mu_{\mathbb{P}}\right\|^{2}+\left\|\frac{1}{2} \left( \mu_{\mathbb{P}}+\mu_{\mathbb{Q}}\right)-\mu_{\mathbb{Q}}\right\|^{2}\right)\\
=\frac{1}{2}\left(\left\|\frac{1}{2} \left( \mu_{\mathbb{Q}}-\mu_{\mathbb{P}}\right)\right\|^{2}+\left\|\frac{1}{2} \left( \mu_{\mathbb{P}}-\mu_{\mathbb{Q}}\right)\right\|^{2}\right)\\
=\frac{1}{4} \left\|  \mu_{\mathbb{Q}}-\mu_{\mathbb{P}}\right\|^{2}

$$

记maximum mean discrepancy (MMD)距离为

$$
M M D_{\mathcal{F}}[\mathbb{P}, \mathbb{Q}]:=\sup _{f \in \mathcal{F}}(\underset{\mathbb{P}}{\mathbb{E}}[f(x)]-\underset{\mathbb{Q}}{\mathbb{E}}[f(x)])

$$

通过选取$\mathcal{F}=\left\{f: \mathcal{X} \rightarrow \mathbb{R} \mid f \text { is linear and }\|f\|_{\mathcal{F}} \leq 1\right\} $，以及借助另一篇文章的一个定理，可以证明

$$
\Psi\left(\left\{\mu_{\mathbb{P}}, \mu_{\mathbb{Q}}\right\}\right)=\frac{1}{4} M M D_{\mathcal{F}}^{2}[\mathbb{P}, \mathbb{Q}]

$$

这也会是我们接下来的证明里需要用到的一个引理。

有了这些准备步骤，我们就可以开始证明了。

首先我们依据discrepancy divergence的定义，将平方损失函数代入

$$
\operatorname{disc}_{\ell}(\mathbb{P}, \mathbb{Q})=\sup _{h, h^{\prime} \in \mathrm{Hyp}}\left|\underset{x \sim \mathbb{P}}{\mathbb{E}}\left[\left(h(x)-h^{\prime}(x)\right)^{2}\right]-\underset{x \sim \mathbb{Q}}{\mathbb{E}}\left[\left(h(x)-h^{\prime}(x)\right)^{2}\right]\right|

$$

我们将平方项展开来，就有

$$
=\sup _{h, h^{\prime} \in \operatorname{Hyp}}\left|\underset{x \sim \mathbb{P}}{\mathbb{E}}\left[h(x) h(x)-2 h(x) h^{\prime}(x)+h^{\prime}(x) h^{\prime}(x)\right]-\underset{x \sim \mathbb{Q}}{\mathbb{E}}\left[h(x) h(x)-2 h(x) h^{\prime}(x)+h^{\prime}(x) h^{\prime}(x)\right]\right|

$$

接着，我们借助乘积算子，可以把它写成算子的形式

$$
=\sup _{h, h^{\prime} \in \mathrm{Hyp}}\left|\underset{x \sim \mathbb{P}}{\mathbb{E}}\left[\left\langle\mathbf{M}_{h} h-2 \mathbf{M}_{h^{\prime}} h+\mathbf{M}_{h^{\prime}} h^{\prime}, \phi(x)\right\rangle_{\mathcal{H}}\right]-\underset{x \sim \mathbb{Q}}{\mathbb{E}}\left[\left\langle\mathbf{M}_{h} h-2 \mathbf{M}_{h^{\prime}} h+\mathbf{M}_{h^{\prime}} h^{\prime}, \phi(x)\right\rangle_{\mathcal{H}}\right]\right|

$$

此处可以回顾一下$\phi(x)$的定义：$h(x)=\langle h, \phi(x)\rangle \quad \text { for all } h \in \mathcal{H} \text { and } x \in \mathcal{X}$，

接下来可以把期望取进去，再利用內积的线性性，整理为一个內积，获得两个均值相减的形式

$$
=\sup _{h, h^{\prime} \in \mathrm{Hyp}}\left|\left\langle\mathbf{M}_{h} h-2 \mathbf{M}_{h^{\prime}} h+\mathbf{M}_{h^{\prime}} h^{\prime}, \mu_{\mathbb{P}}-\mu_{\mathbb{Q}}\right\rangle_{\mathcal{H}}\right|

$$

接下来利用柯西-施瓦兹不等式

$$
\leq\left\|\mathbf{M}_{h} h-2 \mathbf{M}_{h^{\prime}} h+\mathbf{M}_{h^{\prime}} h^{\prime}\right\|_{\mathcal{H}} \cdot\left\|\mu_{\mathbb{P}}-\mu_{\mathbb{Q}}\right\|_{\mathcal{H}}

$$

因为MMD是在空间上取supremum，所以后一项可以放大成为MMD，同时前一项用一下范数的三角不等式，拆成独立各项的范数和

$$
\leq\left(\left\|\mathbf{M}_{h} h\right\|_{\mathcal{H}}+2\left\|\mathbf{M}_{h^{\prime}} h\right\|_{\mathcal{H}}+\left\|\mathbf{M}_{h^{\prime}} h^{\prime}\right\|_{\mathcal{H}}\right) \mathrm{MMD}_{\mathrm{Hyp}}[\mathbb{P}, \mathbb{Q}]

$$

再对各项使用一次之前证明好的关于乘积算子的不等式$\left\|\mathbf{M}_{g}(h)\right\|_{\mathcal{H}} \leq\|g\|_{\infty} \cdot\|h\|_{\mathcal{H}}$

$$
\leq\left(\|h\|_{\infty}\|h\|_{\mathcal{H}}+2\left\|h^{\prime}\right\|_{\infty}\|h\|_{\mathcal{H}}+\left\|h^{\prime}\right\|_{\infty}\left\|h^{\prime}\right\|_{\mathcal{H}}\right) \mathrm{MMD}_{\text {Hyp }}[\mathbb{P}, \mathbb{Q}]

$$

同时回顾一下假设集的条件$\operatorname{Hyp}=\left\{f \in \mathcal{H}:\|f\|_{\mathcal{H}} \leq 1 \text { and }\|f\|_{\infty} \leq r\right\}$，把各个范数同一放大到常数上界，整理一下即有

$$
\leq 4 r \cdot \mathrm{MMD}_{\mathrm{Hyp}}[\mathbb{P}, \mathbb{Q}]

$$

再结合之前证明的最后一个引理，有$\Psi\left(\left\{\mu_{\mathbb{P}}, \mu_{\mathbb{Q}}\right\}\right)=\frac{1}{4} M M D_{\mathcal{F}}^{2}[\mathbb{P}, \mathbb{Q}]$，变形一下为$M M D_{\mathcal{F}}[\mathbb{P}, \mathbb{Q}]=2\sqrt{ \Psi\left(\left\{\mu_{\mathbb{P}}, \mu_{\mathbb{Q}}\right\}\right)}$，再次带入到最后一个不等式，即有

$$
\leq 8 r \sqrt{\Psi_{}\left(\left\{\mu_{\mathbb{P}}, \mu_{\mathbb{Q}}\right\}\right)}

$$

得证。