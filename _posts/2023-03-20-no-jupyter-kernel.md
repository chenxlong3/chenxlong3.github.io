---
layout: post
title:  VSCode+Jupyter在远程服务器上没有可选kernel
date: 2023-03-20
tags: "Techniques"
mathjax: true
comments: true
related_posts: false
---
首先安装jupyter和ipykernel

可参考https://ipython.readthedocs.io/en/stable/install/kernel_install.html

```bash
conda install jupyter
conda install ipykernel
python -m ipykernel install --user --name myenv --display-name "Python (myenv)"
```

然后在VSCode通过ssh连接的远程窗口中安装Jupyter拓展应用，如果一直卡在installing状态，在settings中搜索remote.downloadExtensionsLocally，把这个选项给勾选上，然后再安装就可以了。

参考https://github.com/microsoft/vscode-remote-release/issues/1106