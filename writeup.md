


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

[image1]: ./output_images/chessboard_undistort.png "Undistorted"
[image2]: ./output_images/undistorted "Road Undistorted"
[image3]: ./output_images/thresholded.png "Binary Example"
[image4]: ./output_images/road_transformed.png "Warp Example"
[image5]: ./output_images/color_fit.png "Fit Visual"
[image6]: ./output_images/example_output.png "Output"
[video1]: ./project_video_output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  


### Camera Calibration

####  Comput the camera matrix and distortion coefficients

The code for this step is contained in the second code cell of the IPython notebook located in "./examples/example.ipynb"   

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Undistort images

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Filter the lane line points with HSV color thresholds

I used pure color filters to generate a binary image. After some trial and error, I found the HSV's V channel to be the best color filters for both yellow and white lane lines. Besides, it seems LAB B channel and HLS L channel are both helpful 

![alt text][image3]

#### 3. Transform perspective to get bird view of the lane line

The transform_perspective function takes source (`src`) and destination (`dst`) points, returning both matrix inverse matrix for perspective transform and transform back later. I chose the hardcode the source and destination points in the following manner:


This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 215, 705      | 350, 720    | 
| 576, 460      | 350, 0      |
| 705, 460      | 950, 0      |
| 1098, 705     | 950, 720    |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Identify lane-line points and fit their positions with 2nd order polynomial

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Calculate the radius of curvature of the lane and the position of the vehicle with respect to center.

Calculate the radius of curvature of the lane line as described in this [tutorial](http://www.intmath.com/applications-differentiation/8-radius-curvature.php). The position is calculated as the offset of the car center from the image center, suppose the camera is mounted at the center of the car.

#### 6. Plot back down onto the road such that the lane area is identified clearly.

Draw the lane line with the found lane line points and the polynomial coefficients. After drawing, transform the image back to original view and stack with the original image. Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

Of the whole pipeline, I found the lane line thresholding the most important. If the lane line points are not filtered very well in comparison with their surroundings, then lane line searching algorithm will have a hard time locating the real lane line. In this project, I used pure color threshold (V channel of HSV color space), because I found gradient based filters did not work well. But the HSV filter I used did not work well on the challenge video. The problems might include, not limited to, shadows from trees, lighting conditions, or even long white trucks. I expect other color space like LAB or HLS to be also incorporated, or even neural network (if massive data is available) to get a more robust pipeline. In addition, 
I found visualizing each step to be critically important for identifying the limiting factors. I am happy that Udacity asks for a writeup file (review time for me) and output images for each step in the submission. I learned to solve this specific problem, and also improved the metaskill of solving problems in general. This submission is not the end, but merely a start, I will further tweak the pipeline later to make it more robust.


