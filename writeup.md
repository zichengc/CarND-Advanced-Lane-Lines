

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a distortion correction and a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/distortion_correction.jpg "Undistorted chessboard"
[image2]: ./output_images/distortion_correction_road.jpg "undistorted road"
[image3]: ./output_images/binary_road.jpg "Binary Example"
[image4]: ./output_images/select_region.jpg "Select Region"
[image5]: ./output_images/pt_road.jpg "Perspective Transform"
[image6]: ./output_images/binary_road_pt.jpg "Perspective Transform on Binary Image"
[image7]: ./output_images/pipline_demo.jpg "Demonstration of Pipline"
[video1]: ./output_video/test_project_video.mp4 "Video"


### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.   
Answer:

This is the writeup to be attached with the project.

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.
Answer:

**individual helper functions are tested on "./Pipline_test.ipynb" and "./P2_pipline.ipynb" contains the pipline that works on videos. Please refer to "./Pipline_test.ipynb" for code desribed in steps below.**

The code for camera calibration can be seen under "Camera calibration with chessboard images".  
I started with assignning 3D real world coordinates to corners in each of the test images. I keep the z axis fixed and assign $z = 0$ for all points and then used a 2D grid to generate $x$ and $y$ coordinates. I used `cv2.findChessboardCorners` to locate corresponding corners on the 2D test images. The 3D coordinates are stored as `obj_points` and 2D coordinates are stored as `img_points`. Then I used `cv2.calibrateCamera()` to compute the camera matrix `mtx` and the distortion coefficients `dist`, which are used for distortion correction using `cv2.undistort`. 

Result for chessboard example:

![][image1]


### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.
Result for test road image:
![][image2]


#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
Answer:

Please see code under "Color Thresholding" for detailed implementation.

A combination of gradient and color saturation thresholding is applied to get binary image for lane point detection. For the gradient thresholding, a combination of absolution values along $x$, $y$ directions, magnitude and direction thresholdings are considered for each pixel.
Result for test road image

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.
Answer:

Please see code under "Perspective Transform".

I started with selecting the desired region on a straight line image for the bird-eye view and recorded the coordiates.

![alt text][image4]

In the function `get_pt` I compute the perspective transform matrix $M$ and the inverse $Minv$ which is used to unwarp images.

I used the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 200, 720      | 360, 720        | 
| 1100, 720      | 920, 720      |
| 570, 467     | 360, 0      |
| 714, 467      | 920, 0        |

Result of perspective transform on a test image:

![][image5]

Result of bineary image (undistorted and with perspective transform):

![][image6]
#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

Please see code under "Lane Finding"

First I use the sliding windows to locate lane pixles from the binary warped image and fit a second order polynomial for both left and right lane. Based on the coeefficients, the curvature at the current position can also be computed. The center position of the road is determined based on lane points at the bottom of the image and the center of the car is assumed to be the midpoint of the image width.

To make the lane searching more efficient, the following steps will search for lane pixels based on the second order polynomials. 


#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.
The piplne function is written in the $8^{th}$ cell of './P2_pipline.ipynb'.

![alt text][image7]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./output_video/test_project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?
Sometimes the algorithm failed to find the right lane properly. In my code, if the algorithm failed to find the lane points around the polynomials, I will restart the sliding window approach.

I think it also make sense to add a momentum term for the polynomial terms such that the lanes will not be shifting too fast.
