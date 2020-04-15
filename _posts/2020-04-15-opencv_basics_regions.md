---
title: "Basic OpenCV operations: dealing with regions"
date: 2020-04-15
tags: [Computer Vision, OpenCV, C++]
excerpt: "Computer Vision, OpenCV, C++"
toc: true
author_profile: true
---

Hey there! In [another post](https://mtxslv.github.io/opencv_basics/) I already started talking about [OpenCV](https://opencv.org/) usefulness and described an example of pixels manipulation. Let's move forward: this time we will manipulate entire areas of an image. This must answer [the second question](https://agostinhobritojr.github.io/tutorial/pdi/#_exerc%C3%ADcios).

It asks to swap the areas of the input picture. Consider an image cutted in four parts:

| A | B |
| C | D | 

what we are going to do is swap the positions of the areas A, B, C and D diagonally. This we will lead to:

| D | C |
| B | A |

Let's do the magic!

# Code

We start including dependencies. Let's use [`iostream`](http://www.cplusplus.com/reference/iostream/) and [`opencv`](https://docs.opencv.org/2.4/index.html) to, respectively, inform the user about any issue that may appear (the image was not opened correctly) and manipulate the picture. 

Let's set up [namespaces](https://www.geeksforgeeks.org/namespace-in-c/) also. They [prevent name conflicts](https://en.cppreference.com/w/cpp/language/namespace) and save lazy coders from typing too much. 

{% raw %}
```cpp
#include <iostream>
#include <opencv2/opencv.hpp>


using namespace cv;
using namespace std;
```
{% endraw %}

Now that everything is fine we need to allocate memory to the original image. Images are matrices, so  we are going to use [OpenCV `Mat` class](https://docs.opencv.org/trunk/d3/d63/classcv_1_1Mat.html) to do that. 

{% raw %}
```cpp
int main(int argc, char** argv){
  Mat image;
```
{% endraw %}

Let's get the image. It will be passed to our code during its call, and to know how to do that please [read here](https://github.com/mtxslv/dca0445_dip/wiki/How-to-run-the-algorithms-(an-example)). It's important to break the process if image opening goes wrong, in order to avoid greater troubles, so `return 0`.

{% raw %}
```cpp
  // checking image reading
  image= imread(argv[1],CV_LOAD_IMAGE_GRAYSCALE);
  if(!image.data){
    cout << "image could not be opened" << endl;
    return 0;
  }
```
{% endraw %}

We love to see pictures. Let's visualize what we're working on! Notice [`namedWindow` and `imshow` does basically the same](https://docs.opencv.org/2.4/modules/highgui/doc/user_interface.html?highlight=namedwindow), but I believe there must be a difference between them. The `waitKey` is pretty straight forward, and its inner working is [well-documented](https://docs.opencv.org/2.4/modules/highgui/doc/user_interface.html?highlight=waitkey#waitkey).

{% raw %}
```cpp
  namedWindow("Original Image",WINDOW_AUTOSIZE);
  imshow("Original Image", image);
  waitKey();
```
{% endraw %}

We will create our _collage_  using Regions of Interest (ROI). I chose this way of doing this due to [some](https://answers.opencv.org/question/37568/how-to-insert-a-small-size-image-on-to-a-big-image/) [references](https://stackoverflow.com/questions/46617801/how-to-copy-and-paste-of-image-as-picture-in-picture-in-opencv), but there may be other ways of doing so. Anyway... this way requires we define rectangles (our collage is basically swapt rectangles, see?), and these rectangles need _height_ and _width_ information to be created. So... to get this data, we just need to divide the image rows and cols by two. 

{% raw %}
```cpp
  int dim_x = image.rows/2;
  int dim_y = image.cols/2;

  cout<<"dim_x = "<<dim_x<<"; dim_y = "<< dim_y<<endl; 
```
{% endraw %}



{% raw %}
```cpp
  //creating clone image which will become the processed image
  Mat processed_image = image.clone();
```
{% endraw %}

{% raw %}
```cpp
  Rect A_area(0, 0, dim_x, dim_y);  
  Rect B_area(dim_x,0 ,dim_x, dim_y);
  Rect C_area(0 , dim_y, dim_x, dim_y);
  Rect D_area(dim_x , dim_y, dim_x, dim_y);
```
{% endraw %}

{% raw %}
```cpp
  Mat A = image(A_area);
  Mat B = image(B_area);
  Mat C = image(C_area);
  Mat D = image(D_area);
```
{% endraw %}

{% raw %}
```cpp
  A.copyTo(processed_image(D_area));
  B.copyTo(processed_image(C_area));
  C.copyTo(processed_image(B_area));
  D.copyTo(processed_image(A_area));
```
{% endraw %}

{% raw %}
```cpp
  namedWindow("Processed Image",WINDOW_AUTOSIZE);
  imshow("Processed Image", processed_image);

  waitKey();


  return 0;
}
```
{% endraw %}


# Example

