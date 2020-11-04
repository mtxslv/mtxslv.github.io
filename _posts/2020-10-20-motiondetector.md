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

$$percentage :=\frac{ ||\overrightarrow{x_1-x_2}||_\infty }{ || \overrightarrow{x_1} ||_\infty} $$

given that

$$ ||\overightarrow{x}||_\infty := max(|x_1|,|x_2|, ..., |x_n| )$$

and 

$$ $$

During the code explanation I will not highlight code comments. If you want to have access to the complete code, please check out [my code on Github]().

# Code

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


# Example

Let's run the code on the terminal:
{% raw %}
```

```
{% endraw %}