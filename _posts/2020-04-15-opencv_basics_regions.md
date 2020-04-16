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

Ok, now we must allocate memory to the processed image. The object `processed_image` receives a copy of `image` as its initialization.

{% raw %}
```cpp
  //creating clone image which will become the processed image
  Mat processed_image = image.clone();
```
{% endraw %}

Below I define the rectangular areas that captures the Regions of Interest. The [Rect Class](https://docs.opencv.org/3.4/d2/d44/classcv_1_1Rect__.html) has four constructor parameters: the top left corner coordinates, the width and the height. If we consider the origin of the system at top left corner, the _X axis_ points towards right and the _Y axis_ points downwards. 

{% raw %}
```cpp
  Rect A_area(0, 0, dim_x, dim_y);  
  Rect B_area(dim_x,0 ,dim_x, dim_y);
  Rect C_area(0 , dim_y, dim_x, dim_y);
  Rect D_area(dim_x , dim_y, dim_x, dim_y);
```
{% endraw %}

Now we define our four parts _A, B, C and D_ using the rectangular areas. It means A, B, C and D are images, as well!

{% raw %}
```cpp
  Mat A = image(A_area);
  Mat B = image(B_area);
  Mat C = image(C_area);
  Mat D = image(D_area);
```
{% endraw %}

The final product is the original rectangle with the four little images copied.

{% raw %}
```cpp
  A.copyTo(processed_image(D_area));
  B.copyTo(processed_image(C_area));
  C.copyTo(processed_image(B_area));
  D.copyTo(processed_image(A_area));
```
{% endraw %}

The process is done! We just need to show the processed picture.

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

One example of code run can be seen below.

<img src="{{ site.url }}{{ site.baseurl }}/images/posts_images/2020-04-15-opencv_basics_regions/running.png" alt="">

And here we can see, at the left, the original image, and at the right, the processed one.

<figure class="half">
    <a href="/assets/images/image-filename-1-large.jpg"><img src="/images/posts_images/2020-04-12-opencv_basics/biel.png"></a>
    <a href="/assets/images/image-filename-2-large.jpg"><img src="/images/posts_images/2020-04-15-opencv_basics_regions/processed_image.jpg"></a>
    <figcaption>Original and Processed images.</figcaption>
</figure>

# Conclusion

The code explained in this post can be found [here](https://github.com/mtxslv/dca0445_dip/blob/master/exercises/trocaregioes.cpp). There may be some trash I didn't removed. Anyway... thanks for reading! 