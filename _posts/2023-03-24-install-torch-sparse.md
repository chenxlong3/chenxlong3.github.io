---
layout: post
title: Building wheels for torch_sparse takes forever
date: 2023-03-24
tags: "Techniques"
mathjax: true
related_posts: false
---

安装torch_sparse包的时候，一直卡在building wheels这一步，可以通过以下方式解决：

```bash
pip install torch-sparse -f https://pytorch-geometric.com/whl/torch-1.8.0+cu101.html
```

其中最后的”torch-1.8.0+cu101”按照自己的torch和cuda版本进行调整。

参考：

https://stackoverflow.com/questions/67285115/building-wheels-for-torch-sparse-in-colab-takes-forever

https://github.com/pyg-team/pytorch_geometric/issues/5015