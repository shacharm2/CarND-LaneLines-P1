# **Finding Lane Lines on the Road** 

**Author: Shachar Mendelowitz**

- [**Finding Lane Lines on the Road**](#finding-lane-lines-on-the-road)
                - [Test images](#test-images)
- [**Project strategy**](#project-strategy)
                - [Reflection](#reflection)
                - [1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.](#1-describe-your-pipeline-as-part-of-the-description-explain-how-you-modified-the-drawlines-function)
                - [2. Identify potential shortcomings with your current pipeline](#2-identify-potential-shortcomings-with-your-current-pipeline)
                - [3. Suggest possible improvements to your pipeline](#3-suggest-possible-improvements-to-your-pipeline)

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

| solid white curve                    | solid white right                    |
| :----------------------------------: | :----------------------------------: |
| ![](test_images/solidWhiteCurve.jpg) | ![](test_images/solidWhiteRight.jpg) |

| solid yellow curve                    | solid yellow curve                     |
| :-----------------------------------: | :------------------------------------: |
| ![](test_images/solidYellowCurve.jpg) | ![](test_images/solidYellowCurve2.jpg) |

| solid yellow left                    | white car lane switch                   |
| :----------------------------------: | :-------------------------------------: |
| ![](test_images/solidYellowLeft.jpg) | ![](test_images/whiteCarLaneSwitch.jpg) |


# **Project strategy**

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

My pipeline consisted of several steps. 
There are several extra steps in order to increase robustness, assuming there will be outliers to filter out and other steps for image-adaptive calculations. 

1. White balance - initial illuminiation is unknown and white balance tries limiting the dynamic range of input illuminations. Gray world assumption assumes a balanced light source. The basic assumption in current project is sun light, a balanced source light. The gray-world white balance algorithm has been rewritten according to [GIMP Automatic white balance](https://pippin.gimp.org/image-processing/chapter-automaticadjustments.html)

2. CIELab color space

    working in the native colorspace of our target detection target allows simpler means of detection. 

    It can be seen the L channel is similar to a grayscale image,without color knowledge coded into it. the b channel highlights the yellow parts. Finally, as expected, the a channel contains no relevant information for neigther yellow or white lanes.


<div style="text-align:center" markdown="1">


| Original                             | L channel                  |
| :----------------------------------: | :------------------------: |
| ![](test_images/solidYellowLeft.jpg) | ![](images/L_channel3.png) |
| a channel                            | b channel                  |
| :---:                                | :---:                      |
| ![](images/a_channel3.png)           | ![](images/b_channel3.png) |

</div>


3. CIELab 95% thresholding 
    
    Threshold the L + b and add them to create a signel relevant mask image. 

| L mask                  | b mask                  | Joint                    |
| :---------------------: | :---------------------: | :----------------------: |
| ![](images/L_mask3.png) | ![](images/b_mask3.png) | ![](images/Lb_mask3.png) |

4. Gaussian blur - prior to Canny 
6. Adaptive Canny - low/high thresholds as 25%/75% image percentile
7. Apply ROI - the region of interest is a trapezoid between lower most part of the image (max Y)
8. Get Hough lines

    Here, allowing max_line_gap is large, since it is  require to join the defragment lane section as well.

9. Cost function 

    Calculate for left & right lanes, a score function for every line, based on angle (closest to 45), line around 40/60% of the width, proximity to lower part of the image

10. Outlier filtering

    calculate for left & right lanes the angle and intersection and filter out outliers (outside the standard deviation)

11. Estimate lanes

    From the inliers, evaluate the line - from the bottom of the image up to the highest detected point. 
    Evaluate and estimate Left & Right lanes.
    

    
In order to draw a single line on the left and right lanes, I modified the draw_lines() function by spliting to a positive & negative lines (all inliers, post outlier filtering) and extrapolating from the bottom of the image up to the highest point detected. The width is constant, after evaluating the width as the std of the (rotated) points has yielded "jumpy" results.

Final results:

![alt text](images/annotated3.png)


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when the vehicle might change lanes, as it gives preference to lanes in the 40%/60% width of the image.

Another shortcoming could be that straight lines work well for long straight drives, but not as well for a lot of types of roads which are curved.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
