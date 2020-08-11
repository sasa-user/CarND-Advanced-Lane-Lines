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

[image0]: ./examples/undistort_output.png "UndistortedExample"
[image1]: ./output_images/undistorted.jpg "Undistorted"
[image2]: ./output_images/binaries.png "Binary Example"
[image3]: ./output_images/top_down.png "Warp Example"
[image4]: ./output_images/lane_pixels.png "Fit Visual"
[image5]: ./output_images/make_lane.jpg "Output"
[video1]: ./result.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Writeup / README

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./examples/example.ipynb" (or in lines # through # of the file called `some_file.py`).

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function.

![alt text][image0]

### Pipeline (single images)

##### Common notes: 
* For each step (methods) I tried to showcase results of the methods.
You can use IPython notebook for example images.
* Lessons were very useful and on point so I tried to go along the lines of code from lessons for the sake of readability and traceability.

#### 1. Provide an example of a distortion-corrected image.

In this step I simply used cal_undistort with object and image points from privious step.

![alt text][image1]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

Method in the code is called binary_img. I used S-channel of HLS colorspace (good to find the yellow line) in combination with gradients. s_thresh=(170, 255), sx_thresh=(20, 100).

![alt text][image2]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.


Method that covers this is called top_down. I used hardcoded coordinates for src and dst.

Example:
src = np.float32([[w, h-10], [0, h-10], [546, 460], [732, 460]])
dst = np.float32([[w, h], [0, h], [0, 0], [w, 0]])

As stated: I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image3]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

In this step I combined few methods for doing several things.
In the code those are:
top_down, find_lane_pixels, fit_polynomial, search_around_poly

I these methods as during the lessons I used histogram for bottom part of the image, slinding window method to detect points further and there is method for search in the margin of 100 pixels.
Then I fit my lane lines with a 2nd order polynomial.

![alt text][image4]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

Method for cuvature part is called measure_curvature_real. I used f(y)=Ay2+By+C to fit second order polynomial curve.
Also, I needed to define conversions in x and y from pixels space to meters, and used:
    ym_per_pix = 30./720 # meters per pixel in y dimension
    xm_per_pix = 3.7/700 # meteres per pixel in x dimension

For the center, its calculated in main method make_lane, basically calculated diff from the center of the image to the center of each line, and depending on the position of the center printing result.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

In the make_lane method....

![alt text][image5]

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Video is saved as result.mp4 in main folder of the project.

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Testing I noticed that bumps in the road or big unevenness in the road can cause whole process to be unstable.
Also, weather conditions like snow could impact the process badly.

This version is much better with turns but didn't tested with some serious ones, I can imagine that can have impact as well.

Main issue here that interest me is solving bumps in the road and big shaking of the camera (along with the car).
First thing that comes to my mind is some kind of pixel grid that is monitored from frame to frame and slowing down the car, but that could be really impractical. Not sure yet...

