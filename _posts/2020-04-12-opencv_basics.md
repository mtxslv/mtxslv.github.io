---
title: "Basic OpenCV operations: dealing with pixels"
date: 2020-04-12
tags: [Computer Vision, OpenCV, C++]
excerpt: "Computer Vision, OpenCV, C++"
toc: true
author_profile: true
---

# Introduction

[OpenCV](https://opencv.org/) is a library with programming functions aimed for Computer Vision applications. It is a very important tool and one way to begin working with it is trying to develop simple examples.

In this example let's try to solve [this one](https://agostinhobritojr.github.io/tutorial/pdi/#_exerc%C3%ADcios). Agostinho Brito Jr is a professor of Digital Image Processing at Universidade Federal do Rio Grande do Norte. You can find more about his work in [his personal github page](https://agostinhobritojr.github.io).

Ok, no more talking. Hands on!

# Code

The first step is to include dependencies. For our applications we need three: ```iostream```, ```opencv``` and ```algorithm```. We'll set up namespaces also.

```cpp
#include <iostream>
#include <opencv2/opencv.hpp>
#include <algorithm>    // std::min and std::max

using namespace cv;
using namespace std;
```

Ok, everything is ok. Now we are going to dive in the function. Since we need to get an image and change its color (remember: we will change *greylevels*), let's instantiate a ```Mat``` and a ```Vec``` objects.

```cpp
int main(int argc, char** argv){
  Mat image;
  Vec3b val;
```

Note the user will input two points, in order to draw a rectangle. For this, we'll use an array of ints:

```cpp
  int pts[2][2] = {{-1,-1},{-1,-1}}; // vertices positions
```  


