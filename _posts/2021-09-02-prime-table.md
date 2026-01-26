---
layout: post
title: 质数表的建立
date: 2021-09-02 23:01:31
tags: "Algorithm"
mathjax: true
related_posts: false
---

# 质数

质数的定义：在大于1的自然数中，只能被1和它本身整除的数。

# 质数的判断

给定一个数$n$，我们要怎么判断这个数是不是一个质数呢？显然，根据定义，我们可以遍历2到$n$的每一个数（不包括$n$自身），如果$n$可以被某个数整除，那么显然$n$就不是质数。如果没有找到这样的数，那么$n$就是质数。可是，我们真的有必要从2遍历到$n$吗？

当然是没有必要的。当我们知道10能被2整除的时候，自然知道10能被5整除；当我们知道21能被3整除的时候，自然知道21能被7整除。其实可以隐约地感受到，假设$n=ab$，当$a$是一个较小的数的时候，$b$就是一个较大的数。问题就是，我们在感受到”较小“”较大“的时候，到底是以哪个数为界的呢？

当 $a = b$ 时，$n = a^2$，即得出$a=\sqrt{n}$。模糊地感觉到，这个界限应该是$\sqrt{n}$。那么我们接下来可以尝试证明一下这个命题：若能将$n$分解为两整数因数的乘积，其中一个因数大于$\sqrt{n}$，那么另外一个因数一定小于$\sqrt{n}$。假设$n = ab$，且$a>\sqrt{n}$，则：

$$
b = \frac{n}{a} < \frac{n}{\sqrt{n}} = \sqrt{n}
$$
$$
b < \sqrt{n}
$$



好了，那么我们事实上就可以只遍历2到$\sqrt{n}$的数（包括$\sqrt{n}$，如果它是整数的话），就能知道一个数是质数与否（因为如果两个整数因数都大于$\sqrt{n}$，那么乘积也必定会大于$n$，与我们的条件是相悖的）。 下面给出判断质数的C++和Python代码。

C++:

```cpp
#include<math.h>
#include<iostream>
using namespace std;
bool isPrime(int n)
{
    if(n <= 1) return false;
    int sq = (int)sqrt(n);
    for(int i=2; i<=sq; i++)
        if(n%i == 0) return false;
    return true;
}
int main()
{
    //判断1到20之间的数是否为质数
    for(int i=1; i<=20; i++)
    {
        cout << i << " is ";
        if(!isPrime(i)) cout << "not ";
        cout << "a prime number" << endl;
    }
    return 0;
}

```

Python:

```python
from math import sqrt

def isPrime(n):
    if n <= 1:
        return False
    sq = int(sqrt(n))
    for i in range(2, sq+1):
        if n%i == 0:
            return False
    return True

for i in range(1, 21):
    if isPrime(i):
        print(str(i) + " is a prime number")
    else:
        print(str(i) + " is not a prime number")
```



# 质数表（素数表）的建立

知道如何判断一个数是否为质数，那么也就很简单地可以构建1到$n$的质数表了。质数表也有两种，一种是bool数组，用于判断下标是否为质数，另外一种就是把这个范围内所有的质数放在一个数组中。质数表的建立其实是非常有用的，比如在我们需要多次判断一个数是否为素数的时候，如果一直调用isPrime()函数，其实在时间上有相当大的浪费，所以我们可以把这个范围中的每个数都判断一次，存放在一个哈希表中，这样以后如果还需要判断，那么所需要的时间复杂度就是$O(1)$了。下面介绍两种构建质数表的方法，朴素遍历和筛法。

## 朴素遍历法

非常朴素，遍历1到$n$的所有数，然后通过isPrime函数判断，建立质数表。C++和Python代码如下，得到的prime_table为哈希表，prime_list为该范围内的质数数组

C++：

```c++
#include<math.h>
#include<vector>
#include<iostream>
using namespace std;
bool isPrime(int n)
{
    if(n <= 1) return false;
    int sq = (int)sqrt(n);
    for(int i=2; i<=sq; i++)
    {
        if(n%i == 0) return false;
    }
    return true;
}
int main()
{
    vector<int> prime_list;
    vector<bool> prime_table(101);
    for(int i=0; i<101; i++)
    {
        if(isPrime(i))
        {
            prime_table[i] = true;
            prime_list.push_back(i);
        }
    }
    for(int i=0; i<prime_list.size(); i++)
    {
        cout << prime_list[i] << endl;
    }
    return 0;
}

```

Python：

```python
from math import sqrt

def isPrime(n):
    if n <= 1:
        return False
    sq = int(sqrt(n))
    for i in range(2, sq+1):
        if n%i == 0:
            return False
    return True
prime_table = [False for i in range(0, 101)]
prime_list = []
for i in range(0, 101):
    if isPrime(i):
        prime_table[i] = True
        prime_list.append(i)
print(prime_table)
print(prime_list)
```



##  筛法

可以看出，朴素遍历法构建素数表的时间复杂度是$O(n\sqrt{n})$。

> 接下来介绍Eratosthenes筛法，时间复杂度为$O(nloglogn)$。

这个笔者不会证明...想必证明的过程也会十分有趣。

筛法的思想非常简单，对于每一个质数，它的所有倍数必定不是质数，那么我们筛掉即可。首先我们知道2是素数，那么我们就可以筛去4、6、8、10等等。3没有被筛去，那么3是质数，然后3的倍数，即6、9、12等不是素数。然后到4，4已经被筛掉了，我们看5，5没有被筛掉，所以是质数...循环往复。下面给出C++和Python的代码。

（有一个小关键就是要首先把所有数都当作质数，再筛）

C++：

```c++
#include<vector>
#include<iostream>
int main()
{
    vector<int> prime_list;
    vector<bool> prime_table(101);
    fill(prime_table.begin()+2, prime_table.end(), true);   //要首先假设所有数都是质数
    for(int i=2; i<101; i++)    //我们需要事先确定0、1不是质数，2是质数
    {
        if(prime_table[i])
        {
            prime_list.push_back(i);
            for(int j=i+i; j<101; j += i)
            {
                prime_table[j] = false;
            }
        }

    }
    for(int i=0; i<prime_list.size(); i++)
    {
        cout << prime_list[i] << endl;
    }
    return 0;
}
```

Python：

```python
prime_table = [True for i in range(0, 101)]
prime_list = []
prime_table[0] = prime_table[1] = False
for i in range(0, 101):
    if prime_table[i]:
        for j in range(i+i, 101, i):
            prime_table[j] = False
        prime_list.append(i)
print(prime_table)
print(prime_list)
```



#  参考

[1]胡凡, 曾磊. 算法笔记[M]. 机械工业出版社, 2016.
