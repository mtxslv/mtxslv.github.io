---
title: "Motion Detector"
date: 2020-10-20
tags: [Digital Image Processing, OpenCV, C++]
excerpt: "Digital Image Processing, OpenCV, C++"
toc: true
author_profile: true
mathjax: true
---

Hey there! How are you all? Hope you're healthy. 
At the moment of this post I am studying Digital Image Processing, under the tutelage of [Agostinho Brito Jr](https://agostinhobritojr.github.io/). You can check out his online Digital Image Processing Classes by clicking [on this link](https://www.youtube.com/playlist?list=PLWWAoQUirHNruwCBxOgBhHKXdf7et_rAO).

# The Task 
I'll address the following task troughout this post:

> [The histogram of an image is a function that maps each gray level of an image to the number of times it occurs in the image](https://www.sciencedirect.com/topics/engineering/image-histogram). Notice the histogram is the image information condensed. For example, imagine a greyscale image with 256 intensity levels and shape equal to 640x480: the histogram is a vector with 256 positions, while the image is an array with 307200 values stored! Now suppose we need to know if an image changed. Instead of computing pixel-wise image differences, we just need to calculate differences between histograms! Pretty better, uh? 

# How to know Histogram Changes?

I remember when I was studying Experimental Physics and, when I was making experiments and collected data, if two samples differ less than 10%, they were considered equal. That is, suppose two numerical samples: _s1_ and _s2_. Do the calculation:

$$percentage = \frac{s1-s2}{s1}$$.

If $$percentage<0.1$$ we consider s1 approximately equal to s2.

I also remember when I was learning Iterative Methods for solving Matrix Equations in Numerical Methods. One of these methods is the [Gauss-Seidel Method](https://en.wikipedia.org/wiki/Gauss%E2%80%93Seidel_method). In such techinique we need to know when the solution was reached and it is measured using the [maximum norm](https://en.wikipedia.org/wiki/Norm_(mathematics)#Maximum_norm_.28special_case_of:_infinity_norm.2C_uniform_norm.2C_or_supremum_norm.29) of the difference between two vector samples ($$x_1$$ and $$x_2$$, for example) divided by the maximum norm of the first sample. It is defined as:

$$percentage :=\frac{ ||\overrightarrow{x_1}-\overrightarrow{x_2}||_\infty }{ || \overrightarrow{x_1} ||_\infty} $$

given that

$$ ||\overrightarrow{x}||_\infty := max(|x_1|,|x_2|, ..., |x_n| )$$.

I remember at the time such metric did not make any sense for me. But after studying a little more it seems pretty reasonable and a smart way of measuring if a change is significant. Notice the numerator of _percentage_ is telling us the largest difference between the samples and when we divide such value by the largest value of the previous sample, we get a form of relative measure. Why is it important? Because it somewhat guarantees us no other value will have greater deviation.

> Personally, IMHO I believe we should use in the denominator the shortest absolute value of the previous sample, so we guarantee we're looking at the largest deviation. I decided to keep things as I learned. _Or... maybe I simply don't remember the correct definition anyway_.

In the context of Numerical Analysis, if _percentage_ was smaller than 10% we considered the numerical solution was reached.

Ok, then why such story now? It is because I am going to use the same idea while assuming the histograms are vectors just like any other. 

You can [look at my code here](https://github.com/mtxslv/dca0445_dip/blob/master/exercises/motiondetector.cpp) and [here you'll find Agostinho's explanation about the original code](https://youtu.be/L-nZf4hwk_w) I based myself on.

The implementation of _percentage_ happens in a few lines I am going to explain below.

# Code

The first thing to do is to define ```cv::Mat``` objects to contain the following histograms: ```previous_histR``` and ```hist_difference```.

The algorithm logic is to always compare the current _Red plane_ histogram (the histogram related to the red component) with the last one (which will be saved in ```previous_histR```). Such difference will be stored in ```hist_difference```.

> Why the red component? It was arbitrary chosen.

{% raw %}
```cpp
    cv::absdiff(previous_histR,histR,hist_difference);
    cv::minMaxIdx(hist_difference,NULL,&max_hist_diff);
    cv::minMaxIdx(histR,NULL,&max_red_hist);
```
{% endraw %}

In the previous chunk, I calculate the [absolute difference](https://docs.opencv.org/3.4/d2/de8/group__core__array.html#ga6fef31bc8c4071cbc114a758a2b79c14) between the respective samples I defined alreay.

The following lines saves the maximum values in the ```double``` variables ```max_hist_diff``` and ```max_red_hist``` (this last one is the denominator of the)

{% raw %}
```cpp
    if(max_hist_diff/max_red_hist>=0.07)
        std::cout<<"histogram severely changed!"<<std::endl;
```
{% endraw %}

The previous block calculate the ratio that defines _percentage_ and, if such value is greater than 0.07 (7%), a message is displayed at the terminal.

> Watch Out! Actually, the ratio is wrong. I noticed it while I was writing this post. Notice I am not dividing by an absolute value, but the value itself.
> Why to use 7%? Such value was chosen with experiments. I noticed it represents a better threshold when I am dealing with histograms.

# Example

To test the code yourself, please navigate to the correct folder and run:

{% raw %}
```
./exercises/motiondetector
```
{% endraw %}

> Have a little fun and play around with the threshold! Remember that, to use the new one, you'll need to make the file again.