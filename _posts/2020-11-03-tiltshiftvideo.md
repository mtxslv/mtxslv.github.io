---
title: "Tiltshift"
date: 2020-11-03
tags: [Digital Image Processing, OpenCV, C++]
excerpt: "Digital Image Processing, OpenCV, C++"
toc: true
author_profile: true
mathjax: true
---

In a [previous post](https://mtxslv.github.io/tiltshift/) I introduced the concept of _tiltshift_ using an example video at the top of the post. It was not only a collection of tiltshift images. It was something even more interesting: [stopmotion](https://en.wikipedia.org/wiki/Stop_motion) like video!

Now we know how to make _tiltshift_ images. What about to make entire stopmotion like videos? Such idea is the other task assigned by my professor Agostinho [on his blog](https://agostinhobritojr.github.io/tutorial/pdi/#_exerc%C3%ADcios_5) on the topic "Spacial Filtering".

# The idea

We know how to make a tiltshift image already, right? To make a stopmotion video, we will need just 
to get rid of some frames in the video to create the effect of the scene not being really continuous.

I designed the program to work following the steps:
* When the program starts running, it needs to display a screen so the user can choose the parameters of tiltshifting (area of focus, basically);
* After the user chooses the parameters, the video is processed, displayed and saved. 

> I actually wanted the system to work using [Modular Programming](https://en.wikipedia.org/wiki/Modular_programming). I even watched [Agostinho's class about the topic](https://youtu.be/bLHJwcljTFE?t=2111) to understand more about it, but at the end I couldn't arrange the compiler instructions to make it work, so let's go with only one file.

# Code

Through the explanation I will detail only the aspects that do not relate to tiltshift itself, given that I have explained tiltshift in [another post](https://mtxslv.github.io/tiltshift/).

The first ninety lines are roughly tiltshift stuff: slider functions and image processing. Again, if you want to now more about how it works, please read [my post about it](https://mtxslv.github.io/tiltshift/).

{% raw %}
```cpp
int main(int argvc, char** argv){
            
    cv::Mat frame;
    int frame_counter = 0;
```
{% endraw %}

The ```main``` function begins with some definitions.I will use ```frame``` (a ```cv::Mat``` object) to contain the video frames to be processed and ```frame_counter``` as an iterator to make it clear for me what frame I am dealing with so I could debug it properly.

{% raw %}
```cpp
    std::cout<<argv[1];
    cv::VideoCapture cap(argv[1]);
    std::cout<<"\n existem "<<cap.get(cv::CAP_PROP_FRAME_COUNT)<<" frames no video \n";


```
{% endraw %}

Since the program will be executed via terminal, the image path must be provided via that means. A ```cv::VideoCapture``` object reads such string.

>  During debugging I was doubtful if the code was reading the right argument, then I displayed the path using ```std::cout```.
> The code also shows on terminal the amount of frames because at the point I wrote such line I was not sure about how to discard the frames to make the stopmotion effect, and I wanted to see [how many frames there exist](https://docs.opencv.org/3.4/d4/d15/group__videoio__flags__base.html#ggaeb8dd9c89c10a5c63c139bf7c4f5704dadadc646b31cfd2194794a3a80b8fa6c2) (confused, I know).

From this point [to line 144](https://github.com/mtxslv/dca0445_dip/blob/master/exercises/tiltshiftvideo.cpp#L144) we see the same tiltshift processing we have in the [```tiltshift``` file](https://github.com/mtxslv/dca0445_dip/blob/master/exercises/tiltshift.cpp). The idea of this chunk of code is to get the first video frame and display it on the user interface so the user can choose the processing parameters that I defined above. When the user is done, he/she must press any button, causing  the UI to disapear and the video processing to take place.  

{% raw %}
```cpp
    // Define the codec and create VideoWriter object.The output is stored in 'outcpp.avi' file. 
	  cv::VideoWriter video("./exercises_images/stopmotion.avi",CV_FOURCC('M','J','P','G'),10, cv::Size(cap.get(CV_CAP_PROP_FRAME_WIDTH),cap.get(CV_CAP_PROP_FRAME_HEIGHT))); 
```
{% endraw %}

At this point I create a ```cv::VideoWriter``` object which will handle the creation of the stopmotion video. It needs some parameters like the path (and the name of the file) as a string, [the codec to be used](https://en.wikipedia.org/wiki/FourCC) and the screen size (I will let it equal to the original one).

> I needed to learn how to read and write video using OpenCV. If you want to see the complete tutorial, please go to [this link](https://www.learnopencv.com/read-write-and-display-a-video-using-opencv-cpp-python/).


{% raw %}
```cpp
    while(1){
        frame_counter = frame_counter + 1;

        std::cout<<"frame_counter = "<<frame_counter<<std::endl;

        cap >> frame;
        cap >> image1;

        if(frame.empty()){
            std::cout<<"frame empty"<<std::endl;
            break;
        }    
        
        cv::GaussianBlur(image1,image1_borrada,cv::Size(5,5),10.0,10.0);
        gerar_imagem_ponderacao(img_ponder);


        if (frame_counter%4==0){
            cv::imshow("Frame",imagem_renderizada);

            // Write the frame into the file 'outcpp.avi'
            video.write(imagem_renderizada);
        }    
        char c = (char)cv::waitKey(25);
        if(c==27)
            break;    
    }
```
{% endraw %}

The previous chunk of code is the video processing itself. It is based on iterating over the video while there are frames available.

The first line of the loop is the ```frame_counter``` incrementing. Since it starts with 0, the counting will be fine.

The current frame of the video (variable ```cap```) is saved in two different variables: ```frame``` and ```image1```.

The variable ```frame``` is tested: if _empty_ (there is no available frame anymore) the loop breaks.

> Watch Out! When I was arranging the code this condition was at the end of the loop. It caused the program to try to blur (the next instruction) an empty image, what was bugging the entire process. **This brings an important lesson: always check out if a frame is not empty before trying to do anything with it**.

```image1``` is blured and the ```gerar_imagem_ponderacao()``` function is called. Such function calling will start all the needed processing. Notice since the parameters were all set already, all the frames will be equally processed.

> Watch Out! In the [```tiltshift``` file](https://github.com/mtxslv/dca0445_dip/blob/master/exercises/tiltshift.cpp) I used a very large Gaussian filter with low standard deviation. Actually, a small filter with [high deviation](https://en.wikipedia.org/wiki/Gaussian_blur) works better. Here I am using a 5x5 filter with a standard deviation equal to 10.0. It was corrected in the tiltshift code.

The following condition is created to create the stopmotion effect. It will discard 3 frames from 4 (such value - 3 from 4 - was ~manually~ empirically set). The remaining frame will be show (the user watch the stopmotion video while it is processed) and saved.

A break condition is created at the end of the loop: if the user press the Esc key.

{% raw %}
```cpp
    std::cout<<"Video Processing Finished!"<<std::endl;

    cap.release();
    video.release();

    cv::destroyAllWindows();
  
  return 0;
}
```
{% endraw %}

When the process ends, the software/hardware resources are released and the windows are closed. The user receives a message about it via terminal. 

# Example

I decided to use the free video "Oceano Brasil Praia", downloaded at pixabay. If you want to get the original video, [please go here](https://pixabay.com/pt/videos/oceano-brasil-praia-litoral-7518/). I chose this one because of the camera angle, looking somewhat "from above" to the beach. You can watch such material below:

<video width="480" height="320" controls="controls">
  <source src="/images/posts_images/2020-11-03-tiltshiftvideo/Ocean-7518.mp4" type="video/mp4">
</video>

> Watch Out! I needed to change the name of the video file, since it has spaces " " and the terminal cannot handle that.

After saving the video in the correct folder, I run the following commands:

```
$ make ./exercises/tiltshiftvideo

g++ -Wall -Wunused -std=c++11 -O2 exercises/tiltshiftvideo.cpp -o exercises/tiltshiftvideo `pkg-config --libs opencv`
exercises/tiltshiftvideo.cpp: In function ‘int main(int, char**)’:
exercises/tiltshiftvideo.cpp:143:10: warning: variable ‘key’ set but not used [-Wunused-but-set-variable]
     char key;
          ^~~

$ ./exercises/tiltshiftvideo exercises_images/Ocean-7518.mp4
```

The warning is only telling us a variable is not being used. Do not worry about that.

After playing around with the sliders, we have the following result:

<video width="480" height="320" controls="controls">
  <source src="/images/posts_images/2020-11-03-tiltshiftvideo/stopmotion.avi" type="video/avi">
</video>

# More References To Make this Post Possible

* [Learn more about makefiles](https://opensource.com/article/18/8/what-how-makefile)

* [Minimal Mistakes: how to display video from folder](https://github.com/mmistakes/minimal-mistakes/issues/101)