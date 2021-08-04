## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/undistort_output.jpg "Undistorted"
[image2]: ./output_images/RoadTransformed.jpg "Road Transformed"
[image3]: ./output_images/binary_combo_example.jpg "Binary Example"
[image4]: ./output_images/warped_straight_lines.jpg "Warp Example"
[image41]: ./output_images/lane_detected_warped.jpg "searching in sub window"
[image42]: ./output_images/lane_detected_warped2.jpg "searching in sub window"
[image5]: ./output_images/color_fit_lines.jpg "Fit Visual"
[image51]: ./output_images/polynomial_fitting.jpg "Fit Visual"
[image6]: ./output_images/example_output.jpg "Output"
[image7]: ./output_images/curvature_rad1.jpg "Output"
[image8]: ./output_images/curvature_rad3.jpg "Output"
[image9]: ./output_images/mask_lane.png "Output"
[video1]: ./project_video_out2.mp4 "Video"
[video2]: ./challenge_video_out.mp4 "Video"


## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. the codes are in advanced_lane_detect.ipynb file, result images in ./out_put_images, result videos named with _out to original filename


### Camera Calibration

#### there are 4 steps for camera calibration

   1) initialize the corner position for chessboard, using the number in order, such as nx=(0,8), ny=(0,5)
   2) read the images from camera_cal and using findChessboardCorners to get the corner positions
   3) refine corners to subpixel for more precison
   4) save to dat file

read image and undistort as result :

![alt text][image1]

### Pipeline (single images)

#### 1. undistort image from calibration result

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. get edge result from combined way

combine the color detection result and gradient edge from sobel/magnide to generate the last binary image.  Here's an example of my output for this step. It works on project videos, but not well on challenge video

![alt text][image3]

#### 3. warp lane area in image into birdview

to find the right value for image projection, I test on image at different time along videos, not all the result iamge for lane area show vertical, but searching in sub window can find the right position.

![alt text][image4]
![alt text][image41]  ![alt text][image42]

#### 4. fit line position from discrete  to polynomial

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image51]

![alt text][image5]

#### 5. curvature radius

from left/right coefficents get left/right curvature radius, and return the average in find_curvature

![alt text][image7]
![alt text][image8]

#### 6. mark the lane area with green.

mark the area in array green_channel, then warp back by projection, and get the index for all those pixel, set the green channel from index in origianl image 

![alt text][image6]


#### 7. if fail to find lane.

1) in the first 5 frames, using find_lane_firsttime to find lane using the initalize parameter value, searching in edge without mask
2) during the failure, number of subwindows for successfule search lane is less than half(4), then using the previous value
3) success, generate a new mask area for next searching, and save polynomial result to memo
![alt text][image9]

---

### Pipeline (video)

#### 1. the pipeline using memo for failure search processing in last step7

Here's a [link to my video result](./project_video_out2.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

When I tried on challenge video, the edge detection and warp function can not work well, after change parameters, it works but not satisfied, then I using adaptive threshold in every small window before edge detection. The result is still not well enough. I think the new technology like deep learning could help to resolve the complicated real videos
Here's a [link to my video result](./challenge_video_out.mp4)
