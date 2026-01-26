---
layout: post
title: "Failed to connect to raw.githubusercontent.com port 443: Connection refused"
date: 2023-03-10
tags: "Techniques"
mathjax: true
related_posts: false
---

用wget或者curl获取github文件的时候报出connection refused错误，可以通过以下方式解决：

在本机的host文件添加以下几行：

199.232.68.133 raw.githubusercontent.com

199.232.68.133 user-images.githubusercontent.com

199.232.68.133 avatars2.githubusercontent.com

199.232.68.133 avatars1.githubusercontent.com

[https://github.com/hawtim/hawtim.github.io/issues/10](https://github.com/hawtim/hawtim.github.io/issues/10)

[https://unix.stackexchange.com/questions/228412/how-to-wget-a-github-file](https://unix.stackexchange.com/questions/228412/how-to-wget-a-github-file)