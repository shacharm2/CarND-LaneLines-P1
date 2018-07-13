# **Finding Lane Lines on the Road** 

**Author: Shachar Mendelowitz**

### ToC

- [**Finding Lane Lines on the Road**](#finding-lane-lines-on-the-road)
        - [ToC](#toc)
        - [Test images](#test-images)
        - [Project strategy](#project-strategy)
        - [Reflection](#reflection)
        - [1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.](#1-describe-your-pipeline-as-part-of-the-description-explain-how-you-modified-the-drawlines-function)
        - [2. Identify potential shortcomings with your current pipeline](#2-identify-potential-shortcomings-with-your-current-pipeline)
        - [3. Suggest possible improvements to your pipeline](#3-suggest-possible-improvements-to-your-pipeline)
---

**Project required files**

* notebook with code
* markdown report
* output files 

        test_videos_output/solidWhiteRight.mp4
        test_videos_output/solidYellowLeft.mp4
        test_videos_output/challenge.mp4


**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

[//]: # (Image References)

[cielab]: images/CIELAB.png

---

### Test images

solid white curve | solid white right | 
 :---:  | :---: 
![](test_images/solidWhiteCurve.jpg) |  ![](test_images/solidWhiteRight.jpg) 

solid yellow curve | solid yellow curve | 
 :---:  | :---: 
![](test_images/solidYellowCurve.jpg) |  ![](test_images/solidYellowCurve2.jpg) 

solid yellow left | white car lane switch | 
 :---:  | :---: 
![](test_images/solidYellowLeft.jpg) |  ![](test_images/whiteCarLaneSwitch.jpg) 


### Project strategy

**1. Soft cost function** - cost function cosist of a combination of rough expected lanes location X & Y axes, angle and more. We want to "punish" lines that are flat 0 deg, or are too high in the picture

**2. domain knowledge** - for hard stop. Use ROI (region of interest) will make sure that (high confidence) irrelevant locations are masked out

**3. Road lanes** - lanes come in yellow/white - Use CIELab color space. Use native perceptive CIELab colorspace - L,b channels. 

        L - luminance - axis
        a - greed-red axis
        b - blue-yellow axis

L,b channels are used to mask white & yellow patches, while the a channel is irrelevant

<div style="text-align:center" markdown="1">

![alt text][cielab]

</div>


**4. Road lanes detection** - as anomaly. Look for the 95% percentile of Luminance and b-channel yellow markings in the picture. Thus, no need for absolute values, only the 5% as outliers assumption.


### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of several steps. First, I converted the images to grayscale, then I .... 

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by ...

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
