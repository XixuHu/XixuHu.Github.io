---
title: 'Information Bottleneck'
date: 2023-01-01
permalink: /_posts/2023-01-01-infobottleneck
tags:
  - information bottlenech
  - functional analysis
  - reproduction of a classical theorem
---

<font color="red">写在前面：写作本文时我还没有学习泛函求导的法则，泛函的链式法则与下文用到的普通求导的链式法则有些许不同，以下内容可能有错误之处，请审慎看待。</font>

大家好呀，新年快乐！今天我们一起来阅读一下The information bottleneck method这篇文章~这篇文章最有趣的地方在于给出了如下信息论相关的优化问题的解析解的形式，接下来我们就一起来看看吧！

首先回顾一下信息瓶颈理论想要干的是个什么事情。我们有一个原始信号X，还有一个原始信号Y，X中含有关于Y的信息，我们想要从X中学习到一个压缩表示$\widetilde{X}$，使得其可以丢弃掉X中不必要的信息，只留下X中关于Y的全部信息。

上述目标可以通过最小化如下的目标泛函来实现：
\begin{equation*}
\mathcal{L}[p(\tilde{x} \mid x)]=I(\tilde{X} ; X)-\beta I(\tilde{X} ; Y)
\end{equation*}
其中第一项压缩了$\widetilde{X}$关于X的信息携带，第二项最大化了$\widetilde{X}$关于目标信号Y的信息留存。我们感兴趣的是如何优雅地求解这个优化问题。

我们先给出结论，上述优化问题的最优解满足如下方程：
\begin{equation*}
\begin{aligned}
p(\tilde{x} \mid x)=&\frac{p(\tilde{x})}{Z(x, \beta)} \exp \left[-\beta \sum_y p(y \mid x) \log \frac{p(y \mid x)}{p(y \mid \tilde{x})}\right]\\\\
=&\frac{p(\tilde{x})}{Z(x, \beta)} \exp \left[-\beta\cdot KL(p(y \mid x)\mid p(y \mid \tilde{x}))\right]
\end{aligned}
\end{equation*}

证明：首先注意到我们有如下三个关系式
\begin{eqnarray*}
p(y \mid \tilde{x})=\sum_{x \in X} p(y \mid x) p(x \mid \tilde{x}),\\\\
p(\tilde{x})=\sum_x p(\tilde{x} \mid x) p(x),\\\\
p(\tilde{x} \mid y)=\sum_x p(\tilde{x} \mid x) p(x \mid y)
\end{eqnarray*}

这三个关系式把他们对应的分布和需要优化的分布$p(\tilde{x} \mid x)$给联系起来了。

对后两个式子关于$p(\tilde{x} \mid x)$求导，我们可以得到：

\begin{eqnarray*}
\frac{\delta p(\tilde{x})}{\delta p(\tilde{x} \mid x)}=p(x)\\\\
\frac{\delta p(\tilde{x} \mid y)}{\delta p(\tilde{x} \mid x)}=p(x \mid y)
\end{eqnarray*}

我们用$\lambda(x)$ 来表示约束条件$\sum_{\tilde{x}}p(\tilde{x} \mid x)=1$ 的拉格朗日乘子，原本的优化目标现在可以写为
\begin{equation*}
\mathcal{L}=I(X, \tilde{X})-\beta I(\tilde{X}, Y)-\sum_{x, \tilde{x}} \lambda(x) p(\tilde{x} \mid x)
\end{equation*}
使用互信息的定义展开之后有
\begin{equation*}
\mathcal{L}=\sum_{x, \tilde{x}} p(\tilde{x} \mid x) p(x) \log \left[\frac{p(\tilde{x} \mid x)}{p(\tilde{x})}\right]-\beta \sum_{\tilde{x}, y} p(\tilde{x}, y) \log \left[\frac{p(\tilde{x} \mid y)}{p(\tilde{x})}\right]-\sum_{x, \tilde{x}} \lambda(x) p(\tilde{x} \mid x)
\end{equation*}
接下来我们就可以对目标泛函关于$p(\tilde{x} \mid x)$进行求导。

首先我们对第一项
\begin{equation*}
\begin{aligned}
&\sum_{x, \tilde{x}} p(\tilde{x} \mid x) p(x) \log \left[\frac{p(\tilde{x} \mid x)}{p(\tilde{x})}\right]\\\\=&\sum_{x, \tilde{x}} p(\tilde{x} \mid x) p(x) \log p(\tilde{x} \mid x)-\sum_{x, \tilde{x}} p(\tilde{x} \mid x) p(x) \log p(\tilde{x})
\end{aligned}
\end{equation*}

对$p(\tilde{x} \mid x)$求导之后有
\begin{equation*}
\begin{aligned}
&p(x)(\log p(\tilde{x} \mid x)+1)-p(x)(\log p(\tilde{x})+p(\tilde{x} \mid x)\cdot \frac{p(x)}{p(\tilde{x})})\\\\=&{\color{blue}p(x)\log\left[\frac{p(\tilde{x} \mid x)}{p(\tilde{x})}\right]}+p(x)\left[1-p(x\mid \tilde{x})\right]
\end{aligned}
\end{equation*}

(似乎这里有什么隐藏条件使得$p(x\mid \tilde{x})=1$,但是我没有看懂，所以就先这样了)

接下来看第二项
\begin{equation*}
\begin{aligned}
&-\beta \sum_{\tilde{x}, y} p(\tilde{x}, y) \log \left[\frac{p(\tilde{x} \mid y)}{p(\tilde{x})}\right]\\\\=&-\beta\sum_{\tilde{x}, y} p(\tilde{x}, y) \log p(\tilde{x} \mid y)+\beta\sum_{\tilde{x}, y} p(\tilde{x}, y) \log p(\tilde{x})\\\\=&-\beta\sum_{\tilde{x}, y} p(\tilde{x}\mid y)p(y) \log p(\tilde{x} \mid y)+\beta\sum_{\tilde{x}, y} p(\tilde{x}\mid y)p(y) \log p(\tilde{x})
\end{aligned}
\end{equation*}
对$p(\tilde{x} \mid x)$求导之后有
\begin{equation*}
\begin{aligned}
&-\beta \sum_{y}p(y)\left[p(x\mid y)\log p(\tilde{x} \mid y)+p(\tilde{x} \mid y)\frac{1}{p(\tilde{x} \mid y)}p(x\mid y)\right] +\beta \sum_{y}p(y)\left[p(x\mid y)\log p(\tilde{x} )+p(\tilde{x} \mid y)\frac{p(x)}{p(\tilde{x} )}\right]\\\\=&{\color{blue}-\beta \sum_{y}p(y)p(x\mid y)\log\frac{p(\tilde{x} \mid y)}{p(\tilde{x} )}} -\beta \sum_{y} p(y)p(x\mid y)+\beta \sum_{y}p(y)p(\tilde{x} \mid y)\frac{p(x)}{p(\tilde{x} )}
\end{aligned}
\end{equation*}
同样地，我们来证明第二个加号后面的项可以互相抵消：
\begin{equation*}
\begin{aligned}
&-\beta \sum_{y} p(y)p(x\mid y)+\beta \sum_{y}p(y)p(\tilde{x} \mid y)\frac{p(x)}{p(\tilde{x} )}\\\\&=-\beta\cdot p(x)+\beta \sum_y\frac{p(\tilde{x},y)}{p(\tilde{x})}p(x)\\\\&=-\beta\cdot p(x)+\beta \frac{p(\tilde{x})}{p(\tilde{x})}p(x)\\\\&=-\beta\cdot p(x)+\beta\cdot p(x)\\\\&=0
\end{aligned}
\end{equation*}


最后一项
\begin{equation*}
-\sum_{x, \tilde{x}} \lambda(x) p(\tilde{x} \mid x)
\end{equation*}
求导之后剩下
\begin{equation*}
\color{blue}-\lambda(x)
\end{equation*}
整合上述三个结果，我们有
\begin{equation*}
\begin{aligned}
\frac{\delta \mathcal{L}}{\delta p(\tilde{x} \mid x)}=&p(x)\log\left[\frac{p(\tilde{x} \mid x)}{p(\tilde{x})}\right]-\beta \sum_{y}p(y)p(x\mid y)\log\frac{p(\tilde{x} \mid y)}{p(\tilde{x} )}-\lambda(x)\\\\=&p(x)\left[\log\left[\frac{p(\tilde{x} \mid x)}{p(\tilde{x})}\right]-\beta \sum_{y}p(y\mid x)\log\frac{p(y\mid \tilde{x})}{p(y)}-\frac{\lambda(x)}{p(x)}\right]
\end{aligned}
\end{equation*}

第二个等号中的第二项是因为$p(y)p(x\mid y)=p(x,y)=p(x)p(y\mid x)$,并且中间那项等于$I(\tilde{X},Y)=H(\tilde{X})-H(Y\mid\tilde{X})=H(Y)-H(Y\mid \tilde{X})$

接下来我们再做一个小小的变形，变换出原始结论中关于$p(y\mid x)$和$p(y\mid \tilde{x})$的KL距离。

我们把第二项拆开，并且加一项减一项$\log p(y\mid x)$，然后把第一三项合并，第二四项合并：
\begin{equation*}
\begin{aligned}
& -\beta \sum_y p(y \mid x)[\log p(y \mid \tilde{x})+{\color{blue}\log p(y \mid x)-\log p(y \mid x)}-\log p(y)] \\\\
=& -\beta \sum_y p(y \mid x)\left[\log \frac{p(y \mid \tilde{x})}{p(y \mid x)}+\log \frac{p(y \mid x)}{p(y)}\right]\\\\=& \beta \sum_y p(y \mid x)\log \frac{p(y \mid x)}{p(y \mid \tilde{x})}-\beta \sum_y p(y \mid x)\log \frac{p(y \mid x)}{p(y)}
\end{aligned}
\end{equation*}
这是我们就可以留下合并后的第一项，第二项
$$-\beta \sum_y p(y \mid x) \log \frac{p(y \mid x)}{p(y)}=I(x, Y)$$

由于是$x$的函数，所以可以合并到后面的$\lambda(x)/p(x)$中:
\begin{equation*}
\tilde{\lambda}(x)=\frac{\lambda(x)}{p(x)}-\beta \sum_y p(y \mid x) \log \left[\frac{p(y \mid x)}{p(y)}\right]
\end{equation*}

由此我们可以写出新的整理好的形式并使之为0：
\begin{equation*}
\frac{\delta \mathcal{L}}{\delta p(\tilde{x} \mid x)}=p(x)\left[\log \frac{p(\tilde{x} \mid x)}{p(\tilde{x})}+\beta \sum_y p(y \mid x) \log \frac{p(y \mid x)}{p(y \mid \tilde{x})}-\tilde{\lambda}(x)\right]=0
\end{equation*}

从此式中将$p(\tilde{x} \mid x)$解出
\begin{equation*}
\begin{aligned}
\log \frac{p(\tilde{x} \mid x)}{p(\tilde{x})}&+\beta\cdot KL(p(y \mid x)\mid p(y \mid \tilde{x})) -\tilde{\lambda}(x)=0\\\\\Rightarrow p(\tilde{x} \mid x)&=p(\tilde{x})\exp\tilde{\lambda}(x)\cdot \exp(-\beta\cdot KL(p(y \mid x)\mid p(y \mid \tilde{x})))
\end{aligned}
\end{equation*}
记概率的正则化常数为$Z(x,\beta)$,
\begin{equation*}
\frac{1}{Z(x, \beta)}=\exp  \tilde{\lambda}(x)=\frac{1}{\sum_{\tilde{x}} p(\tilde{x}) \exp \left(-\beta D_{K L}[p(y \mid x) \mid p(y \mid \tilde{x})]\right)}
\end{equation*}
此时则满足归一化条件
\begin{equation*}
\sum_{\tilde{x}}p(\tilde{x}\mid x)=\sum_{\tilde{x}}\frac{p(\tilde{x}) \exp(-\beta\cdot KL(p(y \mid x)\mid p(y \mid \tilde{x})))}{\sum_{\tilde{x}} p(\tilde{x}) \exp \left(-\beta D_{K L}[p(y \mid x) \mid p(y \mid \tilde{x})]\right)}=1\ \forall x
\end{equation*}

故最终的解为
\begin{equation*}
p(\tilde{x} \mid x)=\frac{p(\tilde{x})}{Z(x, \beta)} \exp \left(-\beta D_{K L}[p(y \mid x) \mid p(y \mid \tilde{x})]\right)
\end{equation*}




