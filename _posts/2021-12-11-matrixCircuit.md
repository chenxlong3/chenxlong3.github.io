---
layout: post
title: 量子电路的矩阵“翻译”——以Grover Search Algorithm为例
date: 2021-12-11 23:33:57
tags: "Algorithm"
mathjax: true
comments: true
related_posts: false
---

急匆匆把电路搭完，却连哪条线对应的是哪个比特都不知道。

# 量子比特

经典计算机里的一个比特有两个状态——0和1。与之类似，一个量子比特 (qubit) 存在两个计算基本状态， $ |0 \rangle $ 和 $ |1\rangle $ ，它们可以写作如下的向量形式：

$$
\begin{aligned}
|0\rangle = 
\begin{bmatrix}
1 \\
0
\end{bmatrix}, 
|1\rangle = 
\begin{bmatrix}
0 \\
1
\end{bmatrix}
\end{aligned}
$$

一个qubit可以表示 $|0\rangle$ ， $|1\rangle$ ，也可以表示它们的任意线性组合，即：


$$
\begin{aligned}
|\psi\rangle = a|0\rangle + b|1\rangle
\end{aligned}
$$

其中 $a$ 和 $b$ 为满足 $a^2+b^2 = 1$ 的复数，笔者没学过复分析，对这方面知识并不了解。在这篇blog中先将 $a$ 与 $b$ 视为实数，以简化分析过程。 $a^2$ 表示这个qubit经过一次测量之后，得到的是 $|0\rangle$ 的概率，同理， $b^2$ 表示这个qubit经过一次测量之后，得到的是 $|1\rangle$ 的概率。

知道了上述的知识之后，我们就可以在IBM Quantum Experience上开始玩耍了。我们可以先放一个 $|0\rangle$ 到电路上。

<img src="/assets/img/posts/matrixCircuit/qubit0.png"
 style="width: 80%; height: auto; margin:auto; display:block;">

这个时候呢，我们已经初始化了一个qubit，它的状态为 $|0\rangle$ 。

那如果我们在下面加一条线，也加上一个 $|0\rangle$ 呢？

<img src="/assets/img/posts/matrixCircuit/qubit02.png"
 style="width: 80%; height: auto; margin:auto; display:block;">

这个时候，我们可以通过IBM Quantum Experience提供的可视化看到，这两个比特就构成了 $|00\rangle$ 。这个时候就有一个问题了，线路0上的qubit对应的到底是 $|00\rangle$ 左边这个0，还是右边这个0呢？按照从上到下，从左到右，或许很多人会很“自然”地认为线路0上的qubit对应的就是 $|00\rangle$ 左边的那一位。事实到底如何呢？实践出真知。我们可以在线路0那里加上一个非门，起到把 $|0\rangle$ 变成 $|1\rangle$ 的作用。

<img src="/assets/img/posts/matrixCircuit/qubit03.png"
 style="width: 80%; height: auto; margin:auto; display:block;">

这个时候的结果如何？

<img src="/assets/img/posts/matrixCircuit/qubit04.png"
 style="width: 80%; height: auto; margin:auto; display:block;">

事实证明，线路0代表的是右边的那一位。其实这并不难记住，因为这种记法我们在二进制的数中早已见过。最右边代表0位，往左一位代表1位，以此类推。

要注意到， $|0\rangle$ 和 $|1\rangle$ 都是向量，我们显然可以用矩阵对它们进行操作。在量子电路中，所有元件都可以表示成一个矩阵。错综复杂的电路，似乎可以变成简洁（但不简单）的代数表示。


# 电路元件的矩阵表示

## NOT Gate

从非门开始讲起，加上一个非门之后， $|0\rangle$ 可以变成 $|1\rangle$ ， $|1\rangle$ 可以变成 $|0\rangle$ 。用矩阵 $X$ 代表非门，那么我们就有：


$$
\begin{aligned}
X
\begin{bmatrix}
1 \\
0
\end{bmatrix}
=
\begin{bmatrix}
0 \\
1
\end{bmatrix}\\
X
\begin{bmatrix}
0 \\
1
\end{bmatrix}
=
\begin{bmatrix}
1 \\
0
\end{bmatrix}\\
\end{aligned}
$$


其实就是交换两行，对吧。所以非门的矩阵表示为：

$$
X = \begin{bmatrix}
0 & 1 \\
1 & 0
\end{bmatrix}
$$

## H Gate

到现在为止，我们还是在用一个qubit表示 $|0\rangle$ 和 $|1\rangle$ ，与传统计算机没有差别。而H门就可以让一个qubit变为叠加态。

$$
H = \frac{1}{\sqrt{2}}\begin{bmatrix}
1 & 1 \\
1 & -1
\end{bmatrix}
$$

那么，


$$
\begin{aligned}
H|0\rangle &= \frac{1}{\sqrt{2}}
\begin{bmatrix}
1\\
1
\end{bmatrix}\\
H|1\rangle &= \frac{1}{\sqrt{2}}
\begin{bmatrix}
1\\
-1
\end{bmatrix}
\end{aligned}
$$


通过H门，原本的 $|0\rangle$ 和 $|1\rangle$ 就变成了它们的线性组合，并且通过测量得到 $|0\rangle$ 和 $|1\rangle$ 的概率分别都是 $\frac{1}{2}$ 。

下面简短介绍一下这篇blog会用到的逻辑门的矩阵，物理意义暂不深究（~~我不会~~）。


## S Gate

$$
\begin{aligned}
S = \begin{bmatrix}
1 & 0 \\
0 & i
\end{bmatrix}
\end{aligned}
$$


## Z Gate

$$
\begin{aligned}
Z = \begin{bmatrix}
1 & 0 \\
0 & -1
\end{bmatrix}
\end{aligned}
$$


## CZ Gate

$$
\begin{aligned}
CZ = \begin{bmatrix}
    1 & 0 & 0 & 0\\
    0 & 1 & 0 & 0\\
    0 & 0 & 1 & 0\\
    0 & 0 & 0 & -1\\
    \end{bmatrix}.
\end{aligned}
$$


# 量子电路里的矩阵运算

## 从右到左

每个新出现的元件，都对前面所获得的向量进行左乘操作。

## 从下到上

在介绍这部分的运算规则之前，不妨首先想一想，我们应该怎么样表示 $|01\rangle$ 这两个qubits。在有两个qubits的情况下，计算的基态一共有四种，即 $|00\rangle$ ， $|01\rangle$ ， $|10\rangle$ 和 $|11\rangle$ 。根据线性代数的方式，我们将它们视为四个基向量，它们的所有线性组合，会span出来一个四维空间。所以，基于 $|0\rangle$ 和 $|1\rangle$ ，我们需要得到以下四个向量。

$$
\begin{aligned}
\begin{bmatrix}
1 \\
0 \\
0 \\
0
\end{bmatrix}, 
\begin{bmatrix}
0 \\
1 \\
0 \\
0
\end{bmatrix},
\begin{bmatrix}
0 \\
0 \\
1 \\
0
\end{bmatrix},
\begin{bmatrix}
0 \\
0 \\
0 \\
1
\end{bmatrix}
\end{aligned}
$$

But how？


### 克罗内克积 (Kronecker product)

克罗内克积的运算非常直观。假设 $A$ 是一个 $m\times n$ 的矩阵， $B$ 是一个 $p\times q$ 的矩阵。那么


$$
\begin{aligned}
A\otimes B=
\begin{bmatrix}
a_{11}B & a_{12}B & \cdots & a_{1n}B \\
a_{21}B & a_{22}B & \cdots & a_{2n}B \\
\vdots & \vdots & \ddots & \vdots \\
a_{m1}B & a_{m2}B & \cdots & a_{mn}B
\end{bmatrix}
\end{aligned}
$$

于是


$$
\begin{aligned}
|00\rangle =\begin{bmatrix}
1 \\
0
\end{bmatrix}
\otimes
\begin{bmatrix}
1 \\
0
\end{bmatrix} = 
\begin{bmatrix}
1 \\
0 \\
0 \\
0
\end{bmatrix}
\\|01\rangle =\begin{bmatrix}
1 \\
0
\end{bmatrix}
\otimes
\begin{bmatrix}
0 \\
1
\end{bmatrix} = 
\begin{bmatrix}
0 \\
1 \\
0 \\
0
\end{bmatrix}
\end{aligned}
$$


其余同理。那么对于纵向的电路元件的计算，也不难推理，就是从下到上一直计算克罗内克积。


# Grover Search Algorithm

Grover Search algorithm可以实现对无序列表中的某个item的搜索。这个算法呢，把所有的物体用不同的基态表示，再生成这些基态的叠加态（线性组合），用来表示整个列表。前面已经提到，线性组合中的系数其实与测量得到对应基态的概率有关。事实上，这个Grover Search algorithm就是通过提高对应基态的概率，达到搜索的效果。接着我们来看一下具体是怎么实现的。

用 $|\omega\rangle$ 表示要搜索的目标，用 $|s\rangle$ 表示叠加态。那么 $|s\rangle$ 就肯定可以分解为 $|\omega\rangle$ 方向上的分量和垂直于 $|\omega\rangle$ 的分量 $|s^\prime\rangle$ 。如下图，

<img src="/assets/img/posts/matrixCircuit/initial.jpeg"
 style="width: 80%; height: auto; margin:auto; display:block;">

随后对 $|s\rangle$ 进行关于 $|s^\prime\rangle$ 的翻转，如下图。这个操作我们称为Oracle，用 $U_\omega$ 表示。

<img src="/assets/img/posts/matrixCircuit/flip.jpeg"
 style="width: 80%; height: auto; margin:auto; display:block;">

随后对这个新的向量进行关于原本的 $|s\rangle$ 的翻转，得到下图。这个操作我们称作diffuser，用 $U_s$ 表示。

<img src="/assets/img/posts/matrixCircuit/reflection2.jpeg"
 style="width: 80%; height: auto; margin:auto; display:block;">

这样，我们就增大了 $|\omega\rangle$ 方向上的分量的大小，即 $|\omega\rangle$ 对应的系数。从而增大了一次测量中，得到 $|\omega\rangle$ 的概率。一般来说，我们会重复这两个“翻转”的过程，使得这个概率足够大，当然，这里面也存在一些问题，后面会提到。


## 两个算符的矩阵表示

以在两个比特的叠加态中搜索 $|01\rangle$ 为例。 $U_\omega$ 的目的是翻转 $|\omega\rangle$ 方向上的分量，其实就相当于把系数乘以-1。在这个例子中，这个操作算符可以写为，

$$
\begin{aligned}
U_\omega = 
    \begin{bmatrix}
    1 & 0 & 0 & 0\\
    0 & -1 & 0 & 0\\
    0 & 0 & 1 & 0\\
    0 & 0 & 0 & 1\\
    \end{bmatrix}.
\end{aligned}
$$


那么对于第二次的翻转，可以理解为向 $|s\rangle$ 方向上投影，然后延长两倍，再减去自身，可以写为，

$$
\begin{aligned}
    U_s &= 2|s\rangle\langle s| - I
    =\begin{bmatrix}
    -1 & 1 & 1 & 1\\
    1 & -1 & 1 & 1\\
    1 & 1 & -1 & 1\\
    1 & 1 & 1 & -1\\
    \end{bmatrix}.
\end{aligned}
$$


我们可以利用numpy来实际操作一下，看这样能不能得到我们想要的效果。

```python
# Generating superposition
q0 = np.array([1, 0])
q1 = np.array([1, 0])
s = np.kron(H, H)@np.kron(q1, q0)
# Oracle
Uf = np.identity(4)
Uf[1, 1] = -1
# Diffuser
tmp = s[np.newaxis]
Us = 2*tmp.T@tmp - np.identity(4)
core = Us@Uf
iter_1 = core@s
iter_2 = core@core@s
iter_3 = core@core@core@s
print("Iterate once:", iter_1)
print("Iterate twice:", iter_2)
print("Iterate three times", iter_3)
```

可以得到如下结果：

```tex
Iterate once: [-2.22044605e-16  1.00000000e+00 -2.22044605e-16 -2.22044605e-16]
Iterate twice: [-0.5  0.5 -0.5 -0.5]
Iterate three times [-0.5 -0.5 -0.5 -0.5]
```

可以看到，在重复一次的时候，已经获得了非常理想的效果。然而重复两次和三次都并不能有效实现搜索。


## 两个算符的电路矩阵表示

```python
import numpy as np
# H Gate
H = 1/np.sqrt(2) * np.array([
    [1, 1],
    [1, -1]
])
# Z Gate
Z = np.array([
    [1, 0],
    [0, -1]
])
# cz gate
cz = np.identity(4)
cz[3, 3] = -1
# S gate
S = np.array([
    [1, 0],
    [0, 1j]
])
# superposition
q0 = np.array([1, 0])
q1 = np.array([1, 0])
s = np.kron(H, H)@np.kron(q1, q0)
# two core components
U_w = np.kron(np.identity(2), S)@cz@np.kron(np.identity(2), S)
diffuser = np.kron(H, H)@cz@np.kron(Z, Z)@np.kron(H, H)
core = diffuser@U_w
iter_1 = core@s
iter_2 = core@core@s
iter_3 = core@core@core@s
print("Iterate once:", iter_1)
print("Iterate twice:", iter_2)
print("Iterate three times", iter_3)
```

得到结果：

```tex
Iterate once: [0.+0.j 1.+0.j 0.+0.j 0.+0.j]
Iterate twice: [-0.5+0.j  0.5+0.j -0.5+0.j -0.5+0.j]
Iterate three times [-0.5+0.j -0.5+0.j -0.5+0.j -0.5+0.j]
```

可以看到，与理论上的结果基本一致。电路的搭建如下：

<img src="/assets/img/posts/matrixCircuit/circuit.png"
 style="width: 80%; height: auto; margin:auto; display:block;">

实验结果在：[我的report里](https://chenxlong.top/file/MSDM5001_hw5.pdf)


# 重复后无法有效搜索的原因

经过oracle之后，原本的叠加态在 $|\omega\rangle$ 方向上的分量得到了翻转，随后经过diffuser，向量就正好变为了 $|\omega\rangle$ 本身。经过两次上述操作的向量，即 $(U_sU_\omega)^2|s\rangle$ ，就偏离了 $|01\rangle$ 的方向，于是经过测量得到 $|01\rangle$ 的概率又下降了。然而，因为角度的周期性，继续重复这两个操作，经过特定次数后，又会回到一开始的结果。这个算法重复特定次数得不到正确答案是正常的，不过IBM官网给的电路似乎是有点问题的，读者可以尝试一下，得不到理论上的结果。

<img src="/assets/img/posts/matrixCircuit/iter_1.jpg"
 style="width: 80%; height: auto; margin:auto; display:block;">

<img src="/assets/img/posts/matrixCircuit/iter_2.jpg"
 style="width: 80%; height: auto; margin:auto; display:block;">


# 碎碎念

虽然只是一个小小的report，但也是整个学期为数不多的让自己满意的作业了。

如果能把全部的作业都做到让自己满意，就能拥有一个让自己满意的学期吗？事情好像也不是这样的。


「泣いていい、逃げでもいい、ただ諦めるんな。」
