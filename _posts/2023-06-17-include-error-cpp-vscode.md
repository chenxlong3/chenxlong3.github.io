---
layout: post
title: Include errors detected in VSCode - C++
date: 2023-06-17
tags: "Techniques"
mathjax: true
related_posts: false
---

The error message is like

```xml
cannot open source file "iostream". Please run the 'Select IntelliSense Configuration...' command to locate your system headers.
```

<img src="./include-errors/Untitled.png"  
     style="width: 80%; height: auto; margin:auto; display:block;">

1. Click the bulb beside the error line
    
    <img src="./include-errors/Untitled2_01.png"  
     style="width: 80%; height: auto; margin:auto; display:block;">

    
2. Click `Edit “compilerPath” setting`
3. Select one from the list
    
    <img src="./include-errors/Untitled2_02.png"  
     style="width: 80%; height: auto; margin:auto; display:block;">
    

Now the problem should be fixed.