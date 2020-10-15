---
title: "Labelling Various Objects"
date: 2020-10-15
tags: [Digital Image Processing, OpenCV, C++]
excerpt: "Digital Image Processing, OpenCV, C++"
toc: true
author_profile: true
---

Hey there! How are you all? Hope you're healthy. 
At the moment of this post I am studying Digital Image Processing, under the tutelage of [Agostinho Brito Jr](https://agostinhobritojr.github.io/). You can check out his online Digital Image Processing Classes by clicking [on this link](https://www.youtube.com/playlist?list=PLWWAoQUirHNruwCBxOgBhHKXdf7et_rAO).

Following my activities on OpenCV, let's move on to another challenge. This post will try do answer the following question:

> It is known that _counting_ scene objects is a very common task on Image Processing/Artificial Vision. [This code](https://agostinhobritojr.github.io/tutorial/pdi/#exa_labeling) addresses this task by enabling the counting of scene objects on binary images. But there is a drawback: it can only count up to 255 different objects. That happens because the code uses unsigned char values to mark the found objects (it can assume 255 different values). What should we do to solve this problem?

