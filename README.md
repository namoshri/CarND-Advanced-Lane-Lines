
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

[image1]: ./output_images/p4_1.png "Undistorted"
[image2]: ./output_images/distort.jpg "Road Transformed"
[image3]: ./output_images/threshold.jpg "Color and gradient Example"
[image4]: ./output_images/perspective.jpg "Perspective Example"
[image5]: ./output_images/poly.jpg "Sliding window Visual"
[image6]: ./output_images/skip_poly.jpg "Fit poly Visual"
[image7]: ./output_images/sliding.jpg "Radius and car position"
[video1]: ./output_images/project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

You're reading it!

### Camera Calibration

#### 1. Example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./p4.ipynb".

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  
I applied this distortion correction to the test image using the `cv2.undistort()` function. All output images saved in "./output_images".
Example result:

![alt text][image1]

### Pipeline (single images)

#### 1. Example of a distortion-corrected road image

To demonstrate this step, distortion correction applied on road image with help already calibrated data points :
![alt text][image2]

#### 2. Color transforms, gradients to create a thresholded binary image.

I used a combination of color and gradient thresholds to generate a binary image  Here's an example of my output for this step:
I have applied color, gradient, magitude, direction to generate different output. Output saved in "./p4.ipynb"
Example binary image:
 
![alt text][image3]

#### 3. Perspective transform

The code for my perspective transform includes a function called `perspective_transform()`, 
The `perspective_transform()` function takes as inputs an image.  I chose the hardcode the source and destination points in the following manner:

```python
xff1 = 60
xff2 = 60
yff = 95
src = np.float32(
         [[(img_size[0] / 2) - xff1, img_size[1] / 2 + yff],
         [((img_size[0] / 6) - xff2), img_size[1]],
         [(img_size[0] * 5 / 6) + xff2, img_size[1]],
         [(img_size[0] / 2 + xff1), img_size[1] / 2 + yff]])
dst = np.float32(
         [[(img_size[0] / 4), 0],
         [(img_size[0] / 4), img_size[1]],
         [(img_size[0] * 3 / 4), img_size[1]],
         [(img_size[0] * 3 / 4), 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 580, 455      | 320, 0        | 
| 153, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 600, 455      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Lane-line pixels and fit their positions with a polynomial:

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this using sliding window.

![alt text][image5]

..also did skip sliding window for next frame, displayed visible:

![alt text][image6]

#### 5. Calculate the radius of curvature of the lane and the position of the vehicle with respect to center.

Through `common_draw()` updated calcualted curvature of line and position of vehicle. This function
can take input from both slinding window and skip sliding window.

#### 6. Plot back down onto the road such that the lane area is identified clearly:

I implemented this in cell of "./p4.ipynb" through function `pipeline6()`.  Here is an example of my result on a test image:

![alt text][image7]

---

### Pipeline (video)

#### 1. Perform pipeling on video.

Here's a [link to my video result](./output_images/project_out.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?


1. There is one fram glitch observed in shadow and where there is no lane marking near car position. However, that get immediately
adjusted next frame.  

2. I could have added lines in perspective transorm and curve poly draw for better visualation, avoided to make code and wrapper simplified.

3. Source and Destination points in perspective transform are hardcoded for project video, it needs to make runtime based on image/curve.
   that should help for lane finding of advanced videos.

