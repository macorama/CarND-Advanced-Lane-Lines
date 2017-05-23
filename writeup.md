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



## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in cell #4  in function `calibrate_chessboard()`. Methods `grayscale()` and `undistort` are used for color and removing distortion.

We prepare the "object points" (x, y, z) that map to chessboard corners in the world. Here the assumption is that chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.

Each calibration image is imported and converted to grayscale and if corners are detected wirh `cv2.findChessboardCorners()`, the points are added to the object points and imagepoints arrays.

The output of undistor `objectpoints` and `imagepoints` i used to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  


### Pipeline (single images)

The pipeline is executed as follows:
* Do the Calibration of the lens

Process each image and 
1. Correct for distortion
2.  Apply color and gradient thresholds
3.  Warp the perspective to a bird's-eye view of the lane
4.  Find the lane line boundaries with a histogram and sliding window search
5. Fit the lane lines with a second-order polynomial
6. Apply smoothing with a moving average of previous images
7. Eliminate outliers above a specified threshold
8. Compute the radius of curvature and sitance from the center



#### 1. Provide an example of a distortion-corrected image.

The `undistort()` function is shown in the notebook at cell#2


#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

A combination of color and gradient thresholds was used to generate a binary image. See notebook at cell #5


#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

Perspective transform is called within `project_pipeline()` and specifically processed in the function `birds_eye()`. We played with the src and dst points to get a desirable result.

Further the transformation matrix was calculated using `cv2.getPerspectiveTransform()`. This was then warped to get a top-down view with `cv2.warpPerspective()`.

In the warped image in notebook, both curved lines appear parallel.


#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

First, I created a histogram of the bottom half of the warped perspective image. The code is located in the `histogram()` function. This histogram highlights the pixels (from left to right) that are relatively light in color:

![alt text][histtest]

Then, I implemented a sliding window search to get a polynomial fit for each lane line in the image. The polynomial fit of each line is plotted on the warped perspective image for visualization.

![alt text][polyfittest]

In a video feed, subsequent images are computed with a simpler margin search.
