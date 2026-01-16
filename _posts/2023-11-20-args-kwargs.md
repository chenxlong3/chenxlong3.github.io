---
layout: post
title: About *args and **kwargs
date: 2023-11-20
tags: "Techniques"
mathjax: true
comments: true
related_posts: false
---

我们在阅读python一些库的官方文档或源码时，经常会遇到`*args`和`**kwargs`这样的参数（见下图）。这篇blog的目的是解释一下这俩奇怪的参数是什么意思，以及我们为什么要使用它们。

<img src="/assets/img/posts/args_kwargs/args_kwargs.png"  
     style="width: 80%; height: auto; margin:auto; display:block;">

# *args

## 向函数传递多个参数

假设要你写一个函数，它的输入是两个数，要求返回两个数的和。我们可以很轻松地写出下面的代码。

```python
def my_sum(a, b):
    return a + b
```

另一种看起来更酷炫的写法是：

```python
my_sum = lambda a, b: a+b
```

显然这个函数是正确的，然而这并不算是一个好的函数定义。如果我需要这个函数能够计算三数之和、四数之和呢？难道我们又要重新写两个函数吗？

一个不错的方法是把`my_sum` 这个函数的输入换成一个list (tuple)，然后在内部遍历这个list求和。

```python
def my_sum(num_list):
    result = 0
    for num in num_list:
        result += num
    return result

print(my_sum([1, 2, 3, 4]))
# 10
```

其实这已经相当接近*args的作用了，我们暂且记住这个思路。

## *args的用法

将list传进我们写的函数里就要求我们每次调用都必须先创建好一个`list` (`tuple`)，而`*args`则不需要。

```python
def my_sum(*args):
    result = 0
    for num in args:
        result += num
    return result

print(my_sum(1, 2, 3, 4))
```

可见，如果我们定义一个函数时将它的形参写为`*args`，那这个函数就允许我们输入任意多的参数。而且在函数体内部，我们基本就将这个`args`看作是一个包含了这些参数的`tuple`。它是一个`tuple`而非`list`这一点可以通过下面的代码验证。我们知道，tuple里的元素是不允许进行修改的，这个性质很好地保护了我们传进来的参数，能够避免很多的误操作，如在函数内部更新了某变量的值。

```python
def my_sum(*args):
    args[0] = 1
    return

print(my_sum(1, 2, 3, 4))
# TypeError: 'tuple' object does not support item assignment
```

# **kwargs

直接从英文入手的话，kw可以看成是keyword的缩写。`**kwargs`支持我们用键值对的方式传入参数。

## Keyword Arguments

上一部分的例子`my_sum`函数的功能非常单一。现在考虑另外一个函数，用于打印个人信息。

```python
def print_info(name, university, school, student_id):
    print("Name:", name)
    print("University:", university)
    print("School:", school)
    print("Student ID:", student_id)
    return

print_info("John", "SCUT", "school of physics", "6666")
# Name: John
# University: SCUT
# School: school of physics
# Student ID: 6666
```

`print_info("John", "SCUT", "school of physics", "6666")`这种调用方法其实不是很好，因为我们需要记住每个位置对应的属性是什么，所以我们也经常会使用如下指定keyword的方法进行调用。

```python
print_info(name="John",
           university="SCUT",
           school="school of physics",
           student_id="6666")
```

然而，如果我想传入一个age的参数，这个程序就会报错，因为函数在定义的时候并没有包括这个参数。

```python
print_info(name="John",
           university="SCUT",
           school="school of physics",
           student_id="6666",
           age=18)

# TypeError: print_info() got an unexpected keyword argument 'age'
```

想要让这个函数对未知的属性也能正常运作，一个很简单的思路就是把输入改成一个字典。

```python
def print_info(info_dict:dict):
    for key, value in info_dict.items():
        print(key + ": " + str(value))
    return

info_dict = {
    "Name": "John",
    "University": "SCUT",
    "School": "School of Physics",
    "Student ID": "6666"
}
print_info(info_dict)
# Name: John
# University: SCUT
# School: School of Physics
# Student ID: 6666
```

## **kwargs的用法

`**kwargs`的用法其实和传入一个字典非常类似。如果我们定义函数时将参数写为`**kwargs` ，我们就可以通过keyword arguments来传入参数，并在函数内部访问`kwargs` 来获取具体关键词参数的键值对。

```python
def print_info(**kwargs):
    for key, value in kwargs.items():
        print(key + ": " + str(value))
    return

print_info(name="John",
           university="SCUT",
           school="school of physics",
           student_id="6666",
           age=18)
```

总结一下，`*args`和`**kwargs`允许我们向函数传入任意长度的位置参数（positional arguments）与关键词参数（keyword arguments）。下一部分，我们看看`*`和`**`是起到什么作用。

# Unpacking Operation (* and **)

在函数内部`args`是一个tuple，`kwargs`是一个dict。那么*和**到底是什么鬼东西？

`*`和`**`这两个操作符是对iteratable object（以下都以`list`为例）和`dict`的unpacking操作符。pack是打包，unpack就是从包里取出来。而我们的`list`和`dict`就可以看作是箱包。

考虑这样一个问题，假设我们有一个list `l=[1, 2, 3]`，现在我们想把里面的元素放到另外一个list的某个位置上，使得我们另外一个list是`L=[0, 1, 2, 3, 4]`，那应该怎么操作呢？我们当然可以这样写

```python
l = [1, 2, 3]
L = [0]
for i in l:
    L.append(i)
L.append(4)

print(L)
# [0, 1, 2, 3, 4]
```

然而这种写法非常啰嗦。如果用我们前面学到的`*`操作符，我们可以更优雅地完成这个任务。

```python
l = [1, 2, 3]
L = [0, *l, 4]

print(L)
# [0, 1, 2, 3, 4]
```

用一个例子来类比，就是我们出去旅游的时候打包行李箱，其他东西都装好了，然后留了个位置放电脑和充电器，我们就把电脑包打开，把里面的东西放进行李箱里，然后搞定了。

对dict也是同理。我们可以用unpacking operator `**`来简便地完成一些任务。比如合并两个dict：

```python
dict_1 = {"a":3, "b":1}
dict_2 = {"c":1}
merged_dict = {**dict_1, **dict_2}
print(merged_dict)
# {'a': 3, 'b': 1, 'c': 1}
```

# Conclusion

在这篇blog里，我们主要介绍了函数定义中`*args`和`**kwargs`的用法，以及`*`和`**`这两个unpacking操作符。