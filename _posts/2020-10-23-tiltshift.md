---
title: "Tiltshift"
header:
    video:
        id: IpCdMGfducg
        provider: youtube
date: 2020-10-18
tags: [Digital Image Processing, OpenCV, C++]
excerpt: "Digital Image Processing, OpenCV, C++"
toc: true
author_profile: true
mathjax: true
---

Hey there! Have you watched the video at the top of this post? Stunning, right? People looks like tiny ants in a toy world, uh? Such witchcraft has a name. It is called [tiltshift](https://en.wikipedia.org/wiki/Tilt%E2%80%93shift_photography). According to Wikipedia

> Tilt–shift photography is the use of camera movements that change the orientation or position of the lens with respect to the film or image sensor on cameras. 

> Sometimes the term is used when the large depth of field is simulated with digital post-processing; the name may derive from a perspective control lens (or tilt–shift lens) normally required when the effect is produced optically.

> "Tilt–shift" encompasses two different types of movements: rotation of the lens plane relative to the image plane, called tilt, and movement of the lens parallel to the image plane, called shift. 

The trick used in video has a very explicit name: [miniature faking](https://en.wikipedia.org/wiki/Miniature_faking). I love to know how magic illusions are made, and I'd like to share how to turn real world images into these tiny scenes. _And... no. It does not involve the usage of pym particles_

<figure>
  <img src="{{ '/images/posts_images/2020-10-23-tiltshift/pim_building.gif' | relative_url }}" alt="No pym particles here...">
  <figcaption>No pym particles here</figcaption>
</figure>

It is actually simpler than that:
> _Blurring parts of the photo simulates the shallow depth of field normally encountered in close-up photography, making the scene seem much smaller than it actually is; the blurring can be done either optically when the photograph is taken, or by digital postprocessing._ [Wikipedia Definition](https://en.wikipedia.org/wiki/Miniature_faking).

Hm... ok ok... Up to this point we _kind of_ know what's happening. To get a more precise idea, I would like to make a reference to my [Digital Image Processing professor's blog](https://agostinhobritojr.github.io/tutorial/pdi/#_filtragem_no_dom%C3%ADnio_espacial_ii) about the inner works of _tiltshift_. Please, go there and read the section. At the end of it you'll find the details of the code I am about to explain.

# Code

I would like to make a note: the code is somewhat long and non-linear, then I will explain the main ideas and the codes and variables that relate to them.

## The calculation of alpha

As stated in [Agostinho's post](https://agostinhobritojr.github.io/tutorial/pdi/#_filtragem_no_dom%C3%ADnio_espacial_ii), the formula for calculating the alpha (the weight matrix) is 

$$ \alpha(x) = \frac{1}{2}(tanh\frac{x-l1}{d} - tanh\frac{x-l2}{d}) $$.

Notice, in his example, the function is centralized around a value $$x_0$$ that can be found using

$$ x_0 = l1 + l2 $$.

Why is it important? Well, one of the requirements of the project is to control the center of focus using a slider. Such center is $$x_0$$.

Another requirement is to control the width of the focus band using another slider. I will approximate this band such that

$$ l1 = x_0 - band ,$$
$$ l2 = x_0 + band $$.

The $$d$$ parameter must be controled using slider also. But such value is directly read from the visual icon, so there is nothing else to discuss about it.

Ok, now let's move to the code

{% raw %}
```cpp
float alpha(float domain_point,float x0, float b,float d){
  float l1 = x0 - b;
  float l2 = x0 + b;
  float y = 0.5*(tanhf((domain_point-x0-l1)/d)-tanhf((domain_point-x0-l2)/d));
  return y;
}
```
{% endraw %}

The previous function is pretty straightforward: it receives 3 parameters:
* ```domain_point``` is the _x_ in the equation;
* ```x0``` is the point around which the curve centralizes; 
* ```b``` the _band_ parameter will be used to define l1 and l2;
* ```d``` is the parameter that controls the spread of the curve.

This function implements the calculation of alpha defined as before. It is stored in ```y``` and returned.

## Weight Matrices




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


# References

[trackbar opencv](https://docs.opencv.org/3.4/da/d6a/tutorial_trackbar.html)
[imshow opencv](https://docs.opencv.org/3.0-beta/modules/highgui/doc/user_interface.html#imshow)

http://ninghang.blogspot.com/2012/11/list-of-mat-type-in-opencv.html

https://stackoverflow.com/questions/14028193/size-of-matrix-opencv

https://cppsecrets.com/users/203110310511410511510410011599115495764103109971051084699111109/C00-OpenCV-cvsplit.php

images:

https://search.creativecommons.org/photos/379be537-6a19-4e58-8c1d-aa22460a1ff3

https://search.creativecommons.org/photos/e647aa66-c3f9-476c-a7c4-e5eb84fcc938

https://search.creativecommons.org/photos/fd46c192-5bc5-4569-868c-2f9589fbed54

https://search.creativecommons.org/photos/1672c8cf-4cc3-4016-9936-8a327322b0a0

https://search.creativecommons.org/photos/eba97784-fce0-43d4-8bd5-61b32780c90d

https://search.creativecommons.org/photos/9fa6fc7c-cc54-45db-902a-62dd4658e12e