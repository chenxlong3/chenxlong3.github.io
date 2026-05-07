---
layout: post
title: 影响力最大社区搜索
date: 2026-05-07 09:50:54
tags: "Research"
mathjax: true
comments: true
related_posts: false
---

今天读两篇出自同一个组的关于influential community search的文章。随着社交网络影响力最大化领域的研究越来越深入，研究人员的目光也渐渐从“找到最有影响力的节点集合”转向了“找到最有影响力的社区”。这两者之间的区别主要在于最终选取的节点是否具有结构上的限制。

# Top-L Most Influential Community Detection Over Social Networks
Authors：Nan Zhang, Yutong Ye, Xiang Lian, Mingsong Chen

Venue: ICDE 2024

## 动机与背景
已有社区检测的工作往往忽略社区所产生的影响力，已有的影响力最大化工作主要专注在节点集合的选取而忽略了社区结构。在线上营销/广告等领域，找出多个有影响力的社区。该文章提出的问题意在找出影响力最大的前L个种子社区（seed community）。接下来我们会更正式地介绍这个问题以及一些必要的符号解释。


## 问题定义
### 社交网络
社交网络被建模为一个带属性的无向加权图$G = (V, E)$，其中节点代表用户，边代表用户之间的关系，每个节点附加一组关键词（如“电影”“运动”等兴趣标签），记作 $v.W$ = {Movie, Sport}。每条边 $e_{u,v}$ 带有一个概率 $p_{u,v}$，用于建模$u$影响$v$的概率。

### 信息传播
该文章采用的传播模型为maximum influence arborescence (MIA)模型。给定$u$到$v$的一条路径 $P_{u,v} = \langle u=u_1, u_2, \dots, u_m=v \rangle$，则这条路径的传播概率为：
$$
pp(P_{u,v}) = \prod_{i=1}^{m-1} p_{u_i, u_{i+1}}
$$
MIA传播模型利用最大影响路径（maximum influence path, MIP）来简化描述u到v的传播。MIP的定义为两点之间传播概率最大的路径，数学上：
$$
MIP_{u,v} = \argmax  pp(P_{u,v}).
$$
基于上述定义，用户个体之间的影响概率被简化为它们之间的MIP的概率，即：
$$
upp(u, v) = pp(MIP_{u,v})
$$
相应地，一个社区g对个体的community-to-user的影响概率计算如下
$$
cpp(g, v) = 
\begin{cases}
\max\limits_{u \in V(g)} upp(u, v), & \text{if } v \notin V(g) \\
1, & \text{if } v \in V(g)
\end{cases}
$$


### TopL-ICDE
种子社区（seed community）：给定一个网络 $G$，中心节点 $v_q$，数字 $k$，最大半径 $r$，一组关键词 $Q$，一个连通子图 $g \subseteq G$ 在满足以下条件时，可被称为种子社区：
1. $v_q \in V(g)$
2. 对 $V(g)$ 中任意一点 $v_i$，$\mathrm{dist}(v_q, v_i) \leq r$
3. $g$ 是 $k$-truss
4. 对 $V(g)$ 中任意一点 $v_l$，$v_l.W \cap Q \neq \emptyset$

被影响社区（Influenced Community）及其影响力分数 （Influence Score）：给定一个阈值\theta，seed community所连接的一个子图，其中种子社区对这个子图中的每个节点的cpp(g, v)大于 \theta，那么这个子图称为被影响社区，种子社区的影响力得分为被影响社区所有节点的cpp(g,v)之和。

Top-L most Influential Community DEtection (TopL-ICDE)问题旨在找L个影响力得分最高的的种子社区，注意这里是选前L个最大的，而不考虑组合效应。

### DTopL-ICDE
为解决选出的L个communities影响的用户重叠的问题，作者还提出了一个新问题Diverse TopL-ICDE。其实这个就是把组合优化那一套用上了而已。

## 传播模型

此前的问题定义也还是算比较抽象，因为传播模型还并未确定，针对不同的传播模型，该优化问题也许会有不同的性质。接下来我们介绍一种最广泛使用的传播模型之一——独立级联模型（Independent Cascade）。

IC模型下，每个节点有激活和未激活两种状态，给定种子节点集合 $S$， 影响力的传播与计算方式如下：

1. 在第1步，所有种子节点被激活，其余节点处于不激活状态。
2. 在第 $t$ 步新激活的节点 $u$ ，对每个 $u$ 的未激活出邻居 $v$， 有且仅有一次机会以概率在 $t+1$步激活 $v$。

当没有新节点被激活时，传播过程停止。一次传播过程 $\phi$ 中，我们把 $S$激活的点的数量记为 $I_\phi(S)$。$S$ 的影响力被定义为传播过程中被激活的点的数量的期望，记作 $\sigma(S)$，即:

$$
\sigma(S) = \sum_{\phi \sim G} \Pr[\phi] I_{\phi}(S)
$$


# State-of-the-art: 反向影响力采样

## 基本思想

2014年，Christrian Borgs等人在理论领域顶会SODA上发表了**Maximizing social influence in nearly optimal time**这篇文章，提出了著名的反向影响力采样（Reverse Influence Sampling, RIS）框架（注：在原始文章里作者们把他们的方法称为Hypergraph，反向影响力采样是后来的文章对该方法的命名）。为了帮助读者更好地理解RIS，我们先介绍反向可达集（Reverse Reachable Sets， RR-sets）的概念。

给定一个节点$v$和一个确定的图$\phi$，一个以$v$为根节点的RR-set包含$\phi$中所有能到达$v$的点。基于此，一个**随机**反向可达集（random RR-set）按照如下方式构建：(i) 均匀随机采样一个点$v$，(ii) 在一个随机的传播实例$\phi$中，存储所有能到达$v$的点，它们构成的集合记作RR-set $R$。这个定义还是有点晦涩，事实上，在IC模型下，我们构建一个random RR-set就是先随机选一个点，然后从这个点开始反向传播，把能reach的点都存到$R$中。

对于一个种子节点集合$S$来说，如果$S$中有至少一个点在$R$中（即$S\cap R \neq \emptyset$），我们说$S$ cover了$R$。对于一组RR sets $\mathcal{R}$来说，$S$的coverage就被定义为被$S$ cover的RR-sets的数量，即$\Lambda_{\mathcal{R}}(S) = \sum_{R \in \mathcal{R}} \mathbb{I}(R\cap S\neq \emptyset)$。

直觉上，如果一个种子节点影响力很高，那么它应该会经常出现在别的节点的RR-sets里，也就是说，它的coverage会很大。Borgs等人证明了这个coverage可以用来无偏地估计$S$的影响力。数学上来说，就是$\sigma(S)=n\cdot \mathbb{E}[ \frac{\Lambda_{\mathcal{R}}(S)}{ \mid \mathcal{R} \mid} ]$，这里的期望是作用在random RR-sets上，即根节点的随机性和传播过程的随机性。High-level地来讲，这个式子的意思就是假设有一堆random RR-sets，他们被$S$ cover的比例就可以（期望上）视作被$S$影响节点占总节点数的比例。 一个简单的示意图如下：

<img src="/assets/img/posts/IM/IM_cover.png"  
     style="width: 80%; height: auto; margin:auto; display:block;">

## 算法框架

基于上述估计方法，RIS框架基本分两步走：(i) 采样大量RR-sets，随后 (ii) 找coverage最大的$S$。而第二步，即优化$\Lambda_{\mathcal{R}}(S)$， 实质上就是经典的最大覆盖问题（maximum coverage），贪心算法可以在线性时间复杂度下完成。剩下的就是第一步的事情：怎么决定采样多少RR-sets？此后的一系列工作都在围绕这个问题进行改进。Borgs等人通过数采样过程中遍历的边的数目来确定RR-sets是否足够。他们提出只要总共遍历的边数超过$cmk\log(n)/\varepsilon^2$ （$c$为某个常数），就能以较高概率获得具有理论保证的近似解。随后的TIM提出上述方式是有问题的，因为这种采样会使得采样的RR-sets之间并不完全独立，影响Chernoff不等式的使用。TIM从RR-sets的数量角度来思考，提出当RR-sets的数目大于$O(\frac{nk\log n }{\varepsilon^2 \text{OPT}})$时，我们能以较高概率获得具有理论保证的近似解。这种框架一直被沿用至今。此后的IMM、OPIM等，都是在想办法减少RR-sets的数量，去达到相同的$1-1/\mathrm{e}-\varepsilon$的理论保证。

## 好在哪里？

从时间复杂度上来说，RIS-based的方法当然是显著优于其他方法，但它到底好在哪里呢？首先，在RIS的框架下，我们的估算函数$\Lambda_{\mathcal{R}}(S)$也是monotone and submodular的，使得贪心算法能获得$1-1/\mathrm{e}$的近似解。这个特性让我们只需要bound住一个size-$k$种子节点集合的**绝对误差**在$O(\varepsilon)\text{OPT}$内就行。更细节来说，这种近似的模式可以大致表示成下面的形式：

$$
\begin{align*}
\sigma(S) &\geq \frac{n}{\theta}\Lambda_{\mathcal{R}}(S) - O(\varepsilon)\text{OPT} \\
& \geq (1-1/\mathrm{e})\frac{n}{\theta}\Lambda_{\mathcal{R}}(S^\circ) - O(\varepsilon)\text{OPT}\\
&\geq (1-1/\mathrm{e}-O(\varepsilon)) \sigma(S^\circ) -O(\varepsilon)\text{OPT}\\
&=(1-1/\mathrm{e}-O(\varepsilon))\text{OPT}
\end{align*}
$$

这种近似模式需要的采样数量更少，因为不再需要非常精确地估计每一个点的influence。此外，对coverage函数$\Lambda_{\mathcal{R}}(S)$的优化也可以高效完成。而且，一般来讲，RR-set的大小一般不会很大，而在此前介绍的蒙特卡洛估计方式中，随着选的点越来越多，一次模拟（采样）的时间会越来越长。

以上几点就是RIS这个框架比较高效的主要原因。

## 问题

事实上，虽然所有文章的实验效果都很好，但在各种setting上跑一下代码就会发现还是有一些问题的。主流的文章里，都把每条边$\langle u,v \rangle$的概率设置成$1/d_{in}(v)$，其中$d_{in}(v)$是$v$的入度。这种设置其实比较tricky，因为这样的话相当于做反向采样的时候，对每个点来说期望只有一个邻居被采到，使得RR-sets整体来说都不会太大，而且这种设定下，最优解的influence也不会很小，所以整体运行时间较快。

然而，当每条边的传播概率都很高时，RR-sets会很大，采样会很耗时间，不过这一点在Qintian Guo等人的文章里已经得到部分解决，他们提出先根据很少的采样选出一些哨兵节点，然后在后续的每个RR-set的采样中，如果遇到哨兵节点就立即停止，这样的方式可以减小RR-set的size。但这个只适用于节点选取，当涉及其他变种问题时（如对给定节点集合的影响力估计），这种方式就不能直接使用了。第二种情况就是当每条边的传播概率都很低时，最优解的influence变得很小，达到理论保证需要的RR-sets数量也会非常大，使得算法运行时间变长。

值得一提的是，RIS这种估计方式的方差实际上是比蒙特卡洛方法要大的，但采样的过程比蒙特卡洛快很多。有没有办法结合正向和反向的传播，使得估算更加高效，也是一个潜在的研究方向。


# 参考文献

1. Kempe, D., Kleinberg, J., & Tardos, É. (2003, August). Maximizing the spread of influence through a social network. In *Proceedings of the ninth ACM SIGKDD international conference on Knowledge discovery and data mining* (pp. 137-146).
2. Borgs, C., Brautbar, M., Chayes, J., & Lucier, B. (2014, January). Maximizing social influence in nearly optimal time. In *Proceedings of the twenty-fifth annual ACM-SIAM symposium on Discrete algorithms* (pp. 946-957). Society for Industrial and Applied Mathematics.
3. Tang, Y., Xiao, X., & Shi, Y. (2014, June). Influence maximization: Near-optimal time complexity meets practical efficiency. In *Proceedings of the 2014 ACM SIGMOD international conference on Management of data* (pp. 75-86).
4. Tang, Y., Shi, Y., & Xiao, X. (2015, May). Influence maximization in near-linear time: A martingale approach. In *Proceedings of the 2015 ACM SIGMOD international conference on management of data* (pp. 1539-1554).
5. Tang, J., Tang, X., Xiao, X., & Yuan, J. (2018, May). Online processing algorithms for influence maximization. In *Proceedings of the 2018 international conference on management of data* (pp. 991-1005).
6. Ohsaka, N., Akiba, T., Yoshida, Y., & Kawarabayashi, K. I. (2016). Dynamic influence analysis in evolving networks. *Proceedings of the VLDB Endowment*, *9*(12), 1077-1088.
7. Peng, B. (2021). Dynamic influence maximization. *Advances in Neural Information Processing Systems*, *34*, 10718-10731.
8. Yang, Y., Wang, Z., Pei, J., & Chen, E. (2017). Tracking influential individuals in dynamic networks. *IEEE Transactions on Knowledge and Data Engineering*, *29*(11), 2615-2628.
9. Chen, X., Song, Y., & Tang, J. (2024, May). Link recommendation to augment influence diffusion with provable guarantees. In *Proceedings of the ACM Web Conference 2024* (pp. 2509-2518).
10. Guo, Q., Feng, C., Zhang, F., & Wang, S. (2023). Efficient algorithm for budgeted adaptive influence maximization: An incremental rr-set update approach. *Proceedings of the ACM on Management of Data*, *1*(3), 1-26.
11. Han, K., Huang, K., Xiao, X., Tang, J., Sun, A., & Tang, X. (2018). Efficient algorithms for adaptive influence maximization. *Proceedings of the VLDB Endowment*, *11*(9), 1029-1040.
12. Huang, K., Tang, J., Han, K., Xiao, X., Chen, W., Sun, A., ... & Lim, A. (2020). Efficient approximation algorithms for adaptive influence maximization. *The VLDB Journal*, *29*(6), 1385-1406.
13. Cautis, B., Maniu, S., & Tziortziotis, N. (2019, July). Adaptive influence maximization. In *Proceedings of the 25th ACM SIGKDD International Conference on Knowledge Discovery & Data Mining* (pp. 3185-3186).
14. Tang, J., Huang, K., Xiao, X., Lakshmanan, L. V., Tang, X., Sun, A., & Lim, A. (2019, June). Efficient approximation algorithms for adaptive seed minimization. In *Proceedings of the 2019 International Conference on Management of Data*(pp. 1096-1113).
15. Sun, L., Huang, W., Yu, P. S., & Chen, W. (2018, July). Multi-round influence maximization. In *Proceedings of the 24th ACM SIGKDD international conference on knowledge discovery & data mining* (pp. 2249-2258).
16. Zhang, S., Huang, Y., Sun, J., Lin, W., Xiao, X., & Tang, B. (2023, August). Capacity constrained influence maximization in social networks. In *Proceedings of the 29th ACM SIGKDD conference on knowledge discovery and data mining* (pp. 3376-3385).
17. Huang, Y., Zhang, S., Lakshmanan, L. V., Lin, W., Xiao, X., & Tang, B. (2024). Efficient and Effective Algorithms for A Family of Influence Maximization Problems with A Matroid Constraint. *Proceedings of the VLDB Endowment*, *18*(2), 117-129.
18. Chen, X., & Tang, J. (2025, July). Scalable Link Recommendation for Influence Maximization. In *Proceedings of the 31st ACM SIGKDD Conference on Knowledge Discovery and Data Mining V. 1* (pp. 130-141).
19. Rui, X., Wang, Z., Zhao, J., Sun, L., & Chen, W. (2023). Scalable fair influence maximization. *Advances in Neural Information Processing Systems*, *36*, 66675-66691.