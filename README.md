# **Finding Lane Lines on the Road**
##### _Lorenzo's version_

[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

<img src="examples/laneLines_thirdPass.jpg" width="480" alt="Combined Image" />

Overview
---
When we drive, we use our eyes to decide where to go.  The lines on the road that show us where the lanes are act as our constant reference for where to steer the vehicle.  Naturally, one of the first things we would like to do in developing a self-driving car is to automatically detect lane lines using an algorithm.


The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road




[pipeline]: ./test_images_output/output.png "Pipeline"

---

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 6 steps:
1. convert image to grayscale
2. apply Gaussian Blur (I used a static kernel size of 5)
3. apply Canny transform in order to detect edges on the image
4. narrow down the image to a particular area of interest that consists of a triangle at the bottom of the image
5. pass this through a Hough Transform in order to detect the lines in the narrowed image and use these lines to draw the 2 lanes the car is supposed to follow (_see draw lines for details_)
6. finally, we overlay the 2 lanes with the original image

Below is an example of a pipeline, step by step:

![alt text][pipeline]

In order to draw a single line on the left and right lanes, I modified the draw_lines() the following way:
- _Filtering Step_: I select only the lines having a slope absolute value between 0.4 and 1.2, as in `y = x*slope + intercept`, positive slopes are then allocated to the left lane and negative ones to right lanes, I then only take the 10 (or less) longer lines in each bucket
- _Regression Step_: I finally fit a **weighted linear regression** on all the points (2 per line), weights are function of proximity (the closer from the car, the more important the data point) and line length (the longer the line, the bigger the weight)


### 2. Identify potential shortcomings with your current pipeline

One potential shortcoming would be what would happen when a lot of lines are detected on the road itself, such as when the road is not clean or when trees create some shadows. (as in the challenge video)

Another shortcoming could be when the front of the car starts appearing at the bottom of the screen, lines detected there would then have a big weight in the regression. (also seen in the challenge video)


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to use temporal information, since two consecutive images in a video are highly close one from another, we should incorporate the initial belief that the lanes in the current image should not be far from the previous ones.

Another potential improvement could be to use a better regression! Robust regression would help with outliers when some Hough lines are far from the main lanes.
