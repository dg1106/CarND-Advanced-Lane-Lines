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

[chessboard_result]: ./chessboard_result.png
[distortion_corrected_result]: ./distortion_corrected_result.png
[threshold_combined_result]: ./threshold_combined_result.png
[warped_result]: ./warped_result.png
[lane_polynomial_result]: ./lane_polynomial_result.png
[curvature_calc_result]: ./curvature_calc_result.png
[testForImage]: ./testForImage.png
[myresult_backdown]: ./myresult_backdown.png
[my_video1]: ./project_video_processed.mp4 "Video"


---
### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.  
 
 I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result:

![alt text][chessboard_result]


---

### Pipeline (single images)

#### 1. Apply a distortion correction to raw images.

With the result matrix of camera calibration step, I applied calibration for image.
To demonstrate this step, I will show the distortion correction to one of the test images like this one:

![alt text][distortion_corrected_result]


#### 2. Use color transforms, gradients, etc., to create a thresholded binary image.

I used a combination of saturation of HLS color and magnitude gradient thresholds to generate a binary image.
Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![alt text][threshold_combined_result]


#### 3. Apply a perspective transform to rectify binary image ("birds-eye view").

The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 460      | 320, 0        | 
| 203, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |

I verified that my perspective transform 'warper()' was working as expected.

![alt text][warped_result]


#### 4. Detect lane pixels and fit to find the lane boundary.

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][lane_polynomial_result]

First frame of image of video, I found 2nd order polynomial from histogram peaks.
And then, I searched the polynomial fit values from previous frame.


#### 5. Determine the curvature of the lane and vehicle position with respect to center.

![alt test][curvature_calc_result]

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

![alt text][myresult_backdown]
![alt text][testForImage]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result][my_video1]


---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

In the common situation, my pipeline works well. 

But an error occured in a situation another car passed the next lane of ego vehicle's driving lane. I think that it's caused from the 2nd order polynomial line fitting because I applied some margin area for fitting that.
For making more robust, I need to apply the ROI(Region of Interest) more tightly and fitting margin also.