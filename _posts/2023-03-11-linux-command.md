---
layout: post
title: Linux 相关命令
date: 2023-03-11
tags: "Techniques"
mathjax: true
related_posts: false
---


vim:

[https://www.runoob.com/linux/linux-vim.html](https://www.runoob.com/linux/linux-vim.html)

# SCP

## Download files

```bash
scp -P <Port> <User>@<IP/Host>:<File> <DestinationDirectory>
```

## Download Directories

```bash
scp -rP <Port> <User>@<IP/Host>:<Dir> <DestinationDirectory>
```

## **Uploading Files**

```bash
scp -P <Port> <File> <User>@<IP/Host>:<DestinationDir>
```

## Uploading Directories

```bash
scp -rP <Port> <Dir> <User>@<IP/Host>:<DestinationDir>
```

Git配置ssh key

[https://docs.github.com/en/authentication/connecting-to-github-with-ssh](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)

# Unzip .gz file

```bash
gzip -d file.gz
```

[https://linuxize.com/post/how-to-unzip-gz-file/](https://linuxize.com/post/how-to-unzip-gz-file/)

# Run .sh file shell script

```xml
chmod +x script.sh
```

# Output the message in both console and a file

```bash
make | tee output.txt
```

[https://unix.stackexchange.com/questions/274168/display-output-from-command-and-save-to-file](https://unix.stackexchange.com/questions/274168/display-output-from-command-and-save-to-file)

 

# List Directories and size

```jsx
du -h --max_depth=1 | sort -h
```

[https://linuxconfig.org/list-all-directories-and-sort-by-size](https://linuxconfig.org/list-all-directories-and-sort-by-size)

# Git

Multiple github accounts

[https://stackoverflow.com/questions/3860112/multiple-github-accounts-on-the-same-computer](https://stackoverflow.com/questions/3860112/multiple-github-accounts-on-the-same-computer)

# 服务器使用

怎么熟练使用服务器并避免给实验室添麻烦呢？ - Seasoning的回答 - 知乎
[https://www.zhihu.com/question/506241986/answer/3457669268](https://www.zhihu.com/question/506241986/answer/3457669268)

## GPU运行脚本

[占用GPU显卡资源的脚本 - lixinliang - 博客园](https://www.cnblogs.com/lixinliang/p/18043057)

# Conda

Install:

[https://repo.anaconda.com/archive/index.html](https://repo.anaconda.com/archive/index.html)

```jsx
wget -c [url]
```

Run the script in terminal

[Conda 的 yml 文件 Conda/PIP 国内镜像源的添加_conda 安装yaml文件怎么设置镜像-CSDN博客](https://blog.csdn.net/zyctimes/article/details/124598774)