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

\sigma(g) = \sum_{v \in V(g^{Inf})} cpp(g, v)

Top-L most Influential Community DEtection (TopL-ICDE)问题旨在找L个影响力得分最高的的种子社区，注意这里是选前L个最大的，而不考虑组合效应。

### DTopL-ICDE
为解决选出的L个communities影响的用户重叠的问题，作者还提出了一个新问题Diverse TopL-ICDE。其实这个就是把组合优化那一套用上了而已。给定一组社区S，diversity score计算如下

D(S) = \sum_{v \in V(G)} max_{g \in S} cpp(g,v)

## 方法
该文章提出的方法主要由两步组成：（1）离线的预计算，构建索引；（2）线上问询处理。

### 离线索引构建
对于索引的构建，就是以每个点为中心，逐渐扩大半径r，对每个r-hop的子图，计算子图的一些信息，包括关键词的位向量，支持度的上界，影响力分数的上界等。这两个上界值主要是用于剪枝的看，剪枝思想相对简单，关键词不符合要求剪掉，支持度上界小于k-2剪掉，影响力最大也小于维护的第L大分数的剪掉。

计算完每个节点不同半径的中心图后，把所有节点按两个上界值的平均值排序，以它们为叶子节点，自底向上构建一棵树，提高查询效率，方便剪枝。

### 线上查询处理

在online query processing的阶段，算法分下面四大步：
1. 初始化查询半径、关键词，topL社区为空集，第L大的影响力分数设为负无穷。
2. 从根节点开始深度搜索遍历，对每个访问的节点 N（或顶点 vi）进行剪枝检查。
3. 对没有被剪枝掉的叶子节点v，在以v为中心的r跳子图上面枚举候选子图验证 k-truss、关键词、半径约束，计算影响力分数。
4. 维护的前L大的种子社区被返回。

### DTopL-ICDE
1. 选出一定数量的topL社区作为候选。
2. 对候选集合运行贪心算法，每一轮选边际收益最大的社区。

## 实验结果
作者们分别在公开的真实数据集和合成数据集上做了实验，大部分是展示改变各种参数在efficiency上面的结果，揉了这么多剪枝的技术，当然是比naive方法快不少，整体的实验结果其实还算是符合直觉的。

## 讨论
从比较high-level的角度来说，我们可以从影响力最大化（influence maximization, IM）进行一下类比：TopL-ICDE其实就类似找Top-k个最有影响力的节点，Diversified TopL-ICDE就类似IM，找k个节点使得它们综合的影响力最大。可以看到，区别主要在于我们把目光从找点转移到了找社区这个问题上，这种转移带来的挑战在于搜索空间的扩大，也是这篇文章主要在处理的问题。为了让这个问题更加tractable，这篇文章还是做了很多的假设/限制的。比如，社区的radius是给定的，社区的structure也是给定的（k-truss），传播模型是一个确定性的模型。如果传播模型是独立级联（independent cascade, IC）的话，基于threshold去做剪枝就没那么简单了，因为我们没法准确计算influence。

总体看来，这篇文章的motivation其实还是比较容易想到的，但问题建模足够正式完整，也提出了有效的方法去做。和传统的IM那条线相比，这篇文章在方法论上其实更“工程”一点，整体理论含量比较少。在这之上还要拓展其实还是有点难度的，而且如果只是把这个问题放在IC上再研究一遍，虽然可能会有些non-trivial的结果，但整体motivation还是比较weak的。

# Reverse Influential Community Search Over Social Networks
有了第一篇文章的前置知识，第二篇文章其实相当好理解了。

## 背景
除了找到影响力最大的社区，另一个比较常见的需求是找到最能影响某个特定社群的种子社区。

## 问题定义
社区对社区的影响力定义为

inf(S, Q) = \sum u\in S \sum v \in Q upp(u,v)

给定一组query keywords L_q，整数k，最大community用户数量N，以及一个目标社区Q，Reverse Influential Community Search (RICS)旨在找到一个seed community S，使得inf(S,Q)最大。作者也提出了一个变种，relaxed RICS (R^2 ICS)，放松了k-truss和半径的限制。

## 方法
同样地，分两步走，离线构建索引和在线问询处理。

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