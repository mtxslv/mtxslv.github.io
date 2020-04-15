---
title: "Basic OpenCV operations: dealing with regions"
date: 2020-04-15
tags: [Computer Vision, OpenCV, C++]
excerpt: "Computer Vision, OpenCV, C++"
toc: true
author_profile: true
---

Hey there! In [another post](https://mtxslv.github.io/opencv_basics/) I already started talking about OpenCV usefulness and described an example of pixels manipulation. Let's move forward: this time we will manipulate entire areas of an image. This must answer [the second question](https://agostinhobritojr.github.io/tutorial/pdi/#_exerc%C3%ADcios).

It asks to swap the areas of the input picture. Consider an image cutted in four parts:

| A | B |
| C | D | 

what we are going to do is swap the positions of the areas A, B, C and D. This we will lead to:

| D | C |
| B | A |
 
