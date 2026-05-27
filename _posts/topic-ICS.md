---
layout: post
title: 【文献阅读】 Topic-aware Most Influential Community Search in Social Networks
date: 2026-05-20 09:50:54
tags: "Research"
mathjax: true
comments: true
related_posts: false
---

今天读一篇来自华东师范团队的关于Topic-aware Most Influential Community Search的文章。

Topic-aware Most Influential Community Search in Social Networks
Authors：Long Teng, Yanhao Wang, Zhe Lin, Fei Yu

Venue: Neurocomputing 2025

# 动机与背景
影响力社区搜索（Influential Community Search, ICS）旨在从社交网络中找到一组连接紧密且具有高影响力的节点。尽管已有大量工作研究 ICS 问题，但现有方法大多没有考虑所找到的影响力社区与特定话题的相关性。少数关注 topic-aware ICS 的尝试又无法刻画社交网络中社区形成与影响力传播的随机性（stochastic nature），比如它们使用MIA模型来描述影响力传播，假设节点的影响力传播仅按照概率乘积最大的路径传播。

为了同时兼顾“话题相关性”、“结构紧密性”与“影响力传播的随机性”，本文提出了一个新的问题：Topic-aware Most Influential Community Search（TAMICS）。给定一个话题向量 $q$，TAMICS 旨在在一个不确定有向交互图（uncertain directed interaction graph）上找到一组节点，使其满足 $(k, l, \eta)$-core 的结构约束，并在topic-aware的独立级联（Independent Cascade, IC）模型下具有最高的影响力分数。