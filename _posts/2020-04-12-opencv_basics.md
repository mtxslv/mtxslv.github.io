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

{% raw %}
```cpp
  int pts[2][2] = {{-1,-1},{-1,-1}}; // vertices positions
```
{% endraw %}

The next step is to input the image.

{% raw %}
```cpp
  // checking image reading
  image= imread(argv[1],CV_LOAD_IMAGE_GRAYSCALE);
  if(!image.data)
    cout << "image could not be opened" << endl;
```
{% endraw %}

Now the image is saved in ```image```. Let's get the vertices.

{% raw %}
```cpp
 //inputing  vertices and checking if they don't match image size
  do{
    cout<< "Please input the upper vertex: \n";
    cin>> pts[0][0] >> pts[0][1];  
  }while(pts[0][0] < 0 || pts[0][0] > image.cols || pts[0][1] < 0 || pts[0][1] > image.rows );

  do{
    cout<< "Please input the lower vertex: \n";
    cin>> pts[1][0] >> pts[1][1]; 
  }while(pts[1][0] < 0 || pts[1][0] > image.cols || pts[1][1] < 0 || pts[1][1] > image.rows );
```
{% endraw %}

Let's se the original image...

{% raw %}
```cpp
namedWindow("Original Image",WINDOW_AUTOSIZE);
  imshow("Original Image", image);
  waitKey();
```
{% endraw %}

Processing the image...

{% raw %}
```cpp
  for(int i = std::min(pts[0][0],pts[1][0]); i < std::max(pts[0][0],pts[1][0]); i++){
    for(int j = std::min(pts[0][1],pts[1][1]) ; j < std::max(pts[0][1],pts[1][1]); j++){
      image.at<uchar>(i,j)= 255 - image.at<uchar>(i,j);
    }
  }
```
{% endraw %}

and showing this processed pic!

{% raw %}
```cpp
  imshow("Processed Image", image); 

  waitKey();

  return 0;
}
```
{% endraw %}