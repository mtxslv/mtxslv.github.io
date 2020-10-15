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

I will explain the solution I figured out and coded. Is it the only one ever? I think not. Is it the better one? That is improbable. But it is a solution anyway, and it works as well.

I will not highlight comments, only the raw code. If you want to have access to the complete code, please check out [my code on Github](https://github.com/mtxslv/dca0445_dip/blob/master/exercises/labeling_for_more_objects.cpp).


As can be seen in [Agostinho's blog post](https://agostinhobritojr.github.io/tutorial/pdi/#_descri%C3%A7%C3%A3o_do_programa_labeling_cpp), his idea of labeling is based on incrementing an iterator as objects are found, and applying ```floodfill``` on such stuff. The trick I figured out myself is: counting is about incrementing, right? What if we increment a float counter by a known float constant? Notice that, if we increment following the rule: 

```
floating_counting = floating_constant + floating_constant + ...
```

where there are as much ```floating_contant``` as scene objects, then we actually have

```
floating_counting = nobjects*floating_constant
```

Then we just need to do:

```
nobjects = floating_counting/floating_constant
```


We must to pay attention on the value of the ```floating_constant``` also. Such parameter should be chosen by the user. For example, if ```floating_constant=0.5```, the code can counts up to ```2*254=508``` objects. Why to limit the counting up to "254 objects"?

**Watch Out! Later in the code I'll use ```imshow()```. [As can be seen here](https://answers.opencv.org/question/83562/why-pixels-can-have-float-values/), opencv can display "float images" _that are bound between 0...255_.**

It is important to notice that

Ok, here we go!

{% raw %}
```cpp
#include <iostream>
#include <opencv2/opencv.hpp>


int main(int argc, char** argv){
  cv::Mat image, realce;
  int width, height;
```
{% endraw %}


{% raw %}
```cpp

```
{% endraw %}


{% raw %}
```cpp

```
{% endraw %}



{% raw %}
```cpp

```
{% endraw %}