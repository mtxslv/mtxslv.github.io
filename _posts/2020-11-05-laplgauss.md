---
title: "Aplication of Multiple Filters over Images"
date: 2020-11-05
tags: [Digital Image Processing, OpenCV, C++]
excerpt: "Digital Image Processing, OpenCV, C++"
toc: true
author_profile: true
---

Hey there! How are you all? Hope you're healthy. 
At the moment of this post I am studying Digital Image Processing, under the tutelage of [Agostinho Brito Jr](https://agostinhobritojr.github.io/). You can check out his online Digital Image Processing Classes by clicking [on this link](https://www.youtube.com/playlist?list=PLWWAoQUirHNruwCBxOgBhHKXdf7et_rAO).

This post will account for a [minor upgrade](https://agostinhobritojr.github.io/tutorial/pdi/#_exerc%C3%ADcios_4) on the [code ```filtroespacial.cpp```](https://agostinhobritojr.github.io/tutorial/pdi/#_descri%C3%A7%C3%A3o_do_programa_filtroespacial_cpp). It is asked to apply two filters in sequence over a video stream: the _laplacian_ and the _gaussian_.

I will highlight details that relates directly to my modifications. If you want to know more about how the original code works, please watch [Agostinho's explanation here](https://youtu.be/ppztJOGBeu8).

> A brief comment on how the original code works: after defining several filters, a video stream is opened (from a video camera). A loop lets the video be continuously processed, and the user can choose the filters using some keys: a - "absolute" filter;g - gaussian filter; h - horizontal sobel filter ; v - vertical sobel filter; l - laplacian filter; b - high boost filter.  The processing loop works by making a filter available to be applied via ```switch-case``` and lately applying it over the frames.

# How to create Laplacian of a Gaussian Processed Image?

Notice in the comment above that processing happens by making a filter available for application. The trick is to apply the first filter inside a ```case``` and making the next one available to applied in the loop.

To be clear how it works, consider the regular filter choosing (laplacian filter):

{% raw %}
```cpp
      case 'l':
        mask = cv::Mat(3, 3, CV_32F, laplacian);
        std::cout<<"filtro laplaciano:"<<std::endl;
        printmask(mask);
        cv::imwrite("./exercises_images/result_laplacian.png",result);
        break;
```
{% endraw %}

Now you can see the method described at the beginning of this topic:

{% raw %}
```cpp
      case 'p':
        mask = cv::Mat(3,3,CV_32F,gauss);
        std::cout<<"\t primeiro se aplica gauss:"<<std::endl;
        printmask(mask);
        cv::filter2D(frame32f, frameFiltered, frame32f.depth(), mask,
                     cv::Point(1, 1), 0);
        mask = cv::Mat(3, 3, CV_32F, laplacian);
        std::cout<<"\t e agora se aplica laplace:"<<std::endl;
        printmask(mask);
        cv::imwrite("./exercises_images/result_laplgauss.png",result);
        break;  
```
{% endraw %}

> Another modification made was to save the image in the ```/exercises_images``` folder.

# A Brief Comment in Laplacian-Gauss Filtering

During the tests, I couldn't see any difference between applying the laplacian and the gaussian against applying just the laplacian. If you want to test itself, you can [download the executable file here](https://github.com/mtxslv/dca0445_dip/blob/master/exercises/laplgauss) and save it on the ```/exercises``` folder. To run it just type:

{% raw %}
```cpp
./exercises/laplgauss  
```
{% endraw %}

> Watch Out! I used python to convolute the gaussian and laplacian filters. I used the filter but the result was not good. If you want to play around with it, choose the convoluted filter using _k_.