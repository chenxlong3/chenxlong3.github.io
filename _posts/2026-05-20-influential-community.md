---
layout: post
title: 【文献阅读】影响力最大社区搜索 - Top-L Most Influential Community Detection Over Social Networks
date: 2026-05-20 09:50:54
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
该文章采用的传播模型为maximum influence arborescence (MIA)模型。给定 $u$ 到 $v$ 的一条路径 $P_{u,v} = \langle u=u_1, u_2, \dots, u_m=v \rangle$，则这条路径的传播概率为：
$$
pp(P_{u,v}) = \prod_{i=1}^{m-1} p_{u_i, u_{i+1}}
$$
MIA传播模型利用最大影响路径（maximum influence path, MIP）来简化描述 $u$ 到 $v$ 的传播。MIP的定义为两点之间传播概率最大的路径，数学上：
$$
MIP_{u,v} = \mathop{\arg\max}\limits_{P_{u,v}}\, pp(P_{u,v}).
$$
基于上述定义，用户个体之间的影响概率被简化为它们之间的MIP的概率，即：
$$
upp(u, v) = pp(MIP_{u,v})
$$
相应地，一个社区 $g$ 对个体的community-to-user的影响概率计算如下
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

被影响社区（Influenced Community）及其影响力分数 （Influence Score）：给定一个阈值 $\theta$，seed community 所连接的一个子图，其中种子社区对这个子图中的每个节点的 $cpp(g, v)$ 大于 $\theta$，那么这个子图称为被影响社区，种子社区的影响力得分为被影响社区所有节点的 $cpp(g,v)$ 之和。

$$
\sigma(g) = \sum_{v \in V(g^{Inf})} cpp(g, v)
$$

Top-L most Influential Community DEtection (TopL-ICDE)问题旨在找 $L$ 个影响力得分最高的的种子社区，注意这里是选前 $L$ 个最大的，而不考虑组合效应。

### DTopL-ICDE
为解决选出的 $L$ 个 communities 影响的用户重叠的问题，作者还提出了一个新问题 Diverse TopL-ICDE。其实这个就是把组合优化那一套用上了而已。给定一组社区 $S$，diversity score 计算如下

$$
D(S) = \sum_{v \in V(G)} \max_{g \in S} cpp(g,v)
$$

## 方法
该文章提出的方法主要由两步组成：（1）离线的预计算，构建索引；（2）线上问询处理。

### 离线索引构建
对于索引的构建，就是以每个点为中心，逐渐扩大半径 $r$，对每个 $r$-hop 的子图，计算子图的一些信息，包括关键词的位向量，支持度的上界，影响力分数的上界等。这两个上界值主要是用于剪枝的看，剪枝思想相对简单，关键词不符合要求剪掉，支持度上界小于 $k-2$ 剪掉，影响力最大也小于维护的第 $L$ 大分数的剪掉。

计算完每个节点不同半径的中心图后，把所有节点按两个上界值的平均值排序，以它们为叶子节点，自底向上构建一棵树，提高查询效率，方便剪枝。

### 线上查询处理

在online query processing的阶段，算法分下面四大步：
1. 初始化查询半径、关键词，topL社区为空集，第 $L$ 大的影响力分数设为负无穷。
2. 从根节点开始深度搜索遍历，对每个访问的节点 $N$（或顶点 $v_i$）进行剪枝检查。
3. 对没有被剪枝掉的叶子节点 $v$，在以 $v$ 为中心的 $r$ 跳子图上面枚举候选子图验证 $k$-truss、关键词、半径约束，计算影响力分数。
4. 维护的前 $L$ 大的种子社区被返回。

### DTopL-ICDE
1. 选出一定数量的topL社区作为候选。
2. 对候选集合运行贪心算法，每一轮选边际收益最大的社区。

## 实验结果
作者们分别在公开的真实数据集和合成数据集上做了实验，大部分是展示改变各种参数在efficiency上面的结果，揉了这么多剪枝的技术，当然是比naive方法快不少，整体的实验结果其实还算是符合直觉的。

## 讨论
从比较high-level的角度来说，我们可以从影响力最大化（influence maximization, IM）进行一下类比：TopL-ICDE其实就类似找Top-k个最有影响力的节点，Diversified TopL-ICDE就类似IM，找k个节点使得它们综合的影响力最大。可以看到，区别主要在于我们把目光从找点转移到了找社区这个问题上，这种转移带来的挑战在于搜索空间的扩大，也是这篇文章主要在处理的问题。为了让这个问题更加tractable，这篇文章还是做了很多的假设/限制的。比如，社区的radius是给定的，社区的structure也是给定的（k-truss），传播模型是一个确定性的模型。如果传播模型是独立级联（independent cascade, IC）的话，基于threshold去做剪枝就没那么简单了，因为我们没法准确计算influence。

总体看来，这篇文章的motivation其实还是比较容易想到的，但问题建模足够正式完整，也提出了有效的方法去做。和传统的IM那条线相比，这篇文章在方法论上其实更“工程”一点，整体理论含量比较少。在这之上还要拓展其实还是有点难度的，而且如果只是把这个问题放在IC上再研究一遍，虽然可能会有些non-trivial的结果，但整体motivation还是比较weak的。

# Reverse Influential Community Search Over Social Networks
Authors：Nan Zhang, Yutong Ye, Xiang Lian, Mingsong Chen
有了第一篇文章的前置知识，第二篇文章其实相当好理解了。

## 背景
除了找到影响力最大的社区，另一个比较常见的需求是找到最能影响某个特定社群的种子社区。

## 问题定义
社区对社区的影响力定义为

$$
\inf(S, Q) = \sum_{u\in S} \sum_{v \in Q} upp(u,v)
$$

给定一组query keywords $L_q$，整数 $k$，最大community用户数量 $N$，以及一个目标社区 $Q$，Reverse Influential Community Search (RICS)旨在找到一个seed community $S$，使得 $\inf(S,Q)$ 最大。作者也提出了一个变种，relaxed RICS (R^2 ICS)，放松了 $k$-truss 和半径的限制。

## 方法
同样地，分两步走，离线构建索引和在线问询处理。在离线构建索引的过程中，记录support和influence score的upper bound。这篇文章的influence upper bound是通过Q的边界顶点对Q的influence score计算得到的，以及构建的索引树是先进行图分区，然后把递归地将相邻分区分组，自下而上构建树索引，主要是为了降低跨区搜索成本。


# 总结
总体看来，这两篇文章总体还是比较相似的，设计的方法比较复杂，但用的是差不多的技术，当然也有一些根据问题特点进行了一些调整，推测作者应该是在做第一篇的时候就有第二篇的思路了。本人读起来相当吃力，不禁思考，database这个community现在都是要做这么复杂的事情才能发表文章吗？

两篇文章都是基于MIA这个确定性模型的，如果换到IC这个随机模型上，influence的剪枝就没那么容易了，index是不是直接采样RR sets就可以，也需要思考一下。此外，k-truss这种结构主要基于无向图，而更general的setting应该是有向图。不过再基于这两篇文章去做一些拓展也很难了，因为问题本身就比较复杂，往上堆砌各种设定反而会让审稿人更难理解并且质疑问题重要性。还是需要从现实入手，找到真的有价值的问题去做。
