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

# The Task and the Algorithm Idea
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

**Watch Out! Later in the code I'll use ```imshow()```. [As can be seen here](https://answers.opencv.org/question/83562/why-pixels-can-have-float-values/), opencv can display only "float images" _that are bound between 0...255_.**

Ok, let's dive in the code!

# Code

{% raw %}
```cpp
#include <iostream>
#include <opencv2/opencv.hpp>


int main(int argc, char** argv){
  cv::Mat image;
  int width, height;
  float counting_constant= -1; // arbitrary initialization 
  float floating_counting = 0;   
  cv::Point p;  
```
{% endraw %}

No mistery in this introductory part: let's include the necessary libraries.

* iostream: input/output stream objects;
* opencv: the image processing library I'm using.

I define some objects also :
* ```Mat``` object that will contain n-dimensional dense arrays: ```image``` will contain the original image;
* ```int``` objects needed to display the ```width``` and the ```height``` of the ```image```
* ```float``` objects needed for the counting: ```counting_constant``` and the ```floating_counting```. Note these variables were arbitrary initialized;
* ```Point``` object needed as a parameter for the OpenCV ```floodfill``` implemented algorithm. 

{% raw %}
```cpp
  image = cv::imread(argv[1], cv::IMREAD_GRAYSCALE);
  
  if(!image.data){
    std::cout << "imagem nao carregou corretamente\n";
    return(-1);
  }

  width=image.cols;
  height=image.rows;
  std::cout << width << "x" << height << std::endl;
```
{% endraw %}

In the previous chunk of the code I read the image on the path defined on ```argv[1]``` parameter and check if it's fine (if something goes wrong, let's stop the code with a message). If everything is okay, the code read the image dimension and displays it on the terminal. 

{% raw %}
```cpp
  p.x=0;
  p.y=0;

  do{
    std::cout<<"please insert counting constant: \n";
    std::cin>>counting_constant;
  }while((counting_constant<0)||(counting_constant>1));
```
{% endraw %}

In the previous part, the code asks for the ```counting_contant```, and make sure it has valid value. Notice It will be used as a fraction's denominator, then it needs to be between 0 and 1.

I also initialize ```Pointer p``` coordinates arbitrary.

{% raw %}
```cpp
  for(int i=0; i<height; i++){
    for(int j=0; j<width; j++){
      if(image.at<uchar>(i,j) == 255){
        // achou um objeto
        floating_counting = floating_counting + counting_constant;
        p.x=j;
        p.y=i;
  		// preenche o objeto com o contador
		  cv::floodFill(image,p,floating_counting);
      }
    }
  }
  std::cout << "a figura tem " << floating_counting/counting_constant << " bolhas\n";
  
```
{% endraw %}

This portion of the code does the object counting itself. Notice it will iterate over the pixels of the image, going from the top-left corner until it reaches the bottom right corner, checking if any of them have value equal to 255. If so,  we increment the variable ```floating_counting```, set ```Point p``` to that pixel location and apply OpenCV ```floodfill``` algorithm implementation. Lastly it is displayed how many objects were found.

{% raw %}
```cpp
  cv::imshow("image", image);
  cv::waitKey();
  
  return 0;
}
```
{% endraw %}

The last thing to be done is to show the "floodfilled" image. It will keep such window opened until some key is pressed.

# Example

Let's run the code on the terminal passing as argument the file path (later it is necessary to define the constant):

<img src="{{ site.url }}{{ site.baseurl }}/images/posts_images/2020-10-15-labeling_various_objects/running_code.png" alt="">

And here we can the result:


<img src="{{ site.url }}{{ site.baseurl }}/images/posts_images/2020-10-15-labeling_various_objects/result.png" alt="">