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
---

Hey there! Have you watched the video starting this post? Stunning, right? People looks like tiny ants in a toy world, uh? Such witchcraft has a name. It is called [tiltshiftiny](https://en.wikipedia.org/wiki/Tilt%E2%80%93shift_photography). According to Wikipedia, it is defined as

> Tilt–shift photography is the use of camera movements that change the orientation or position of the lens with respect to the film or image sensor on cameras. 

> Sometimes the term is used when the large depth of field is simulated with digital post-processing; the name may derive from a perspective control lens (or tilt–shift lens) normally required when the effect is produced optically.

> "Tilt–shift" encompasses two different types of movements: rotation of the lens plane relative to the image plane, called tilt, and movement of the lens parallel to the image plane, called shift. 

The trick used in video has a very explicit name: [miniature faking](https://en.wikipedia.org/wiki/Miniature_faking). I love to know how magic illusions are made, and I'd like to share how to turn real world images into these tiny scenes. _And... no. It does not involve the usage of pym particles_

<figure>
  <img src="{{ '/images/posts_images/2020-10-23-tiltshift/pim_building.gif' | relative_url }}" alt="No pym particles here...">
  <figcaption>Representational Space Evolution</figcaption>
</figure>

It is actually simpler than that:
> _Blurring parts of the photo simulates the shallow depth of field normally encountered in close-up photography, making the scene seem much smaller than it actually is; the blurring can be done either optically when the photograph is taken, or by digital postprocessing._ [Wikipedia Definition](https://en.wikipedia.org/wiki/Miniature_faking).


references for code:

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