# SDC Project 4: Advanced Lane Finding

### Project Outline:
1. Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
2. Apply a distortion correction to raw images.
3. Use color transforms to create a thresholded binary image with lanes.
4. Apply a perspective transform to rectify binary image ("birds-eye view").
5. Detect lane pixels and fit to find the lane boundary.
6. Determine the curvature of the lane and vehicle position with respect to center.
7. Warp the detected lane boundaries back onto the original image.
8. Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/01_undistort.png "Undistorted"
[image2]: ./output_images/01_undistort2.png "Undistorted"
[image3]: ./output_images/02_binary.png "Binary"
[image4]: ./output_images/03_warped.png "Warped"
[image5]: ./output_images/04_window_search.png "Window Search"
[image6]: ./output_images/05_lanes.png "Detected Lanes"
[image7]: ./output_images/06_output.png "Unwarped Output"
[image8]: ./output_images/07_final_test.png "Final Pipeline Test"
[video1]: ./output_video.mp4 "Output Video"


### Below the [rubric points](https://review.udacity.com/#!/rubrics/571/view) are discussed individually, along with their implementation in the project

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for camera calibration can be found in p4_advanced_lane_lines_prep (section 1). Using images of distorted chessboards, image points of internal corners were determined using the OpenCV function findChessboardCorners(). These points were correlated with object points (in 3D space) with the same number of internal corners. The image points and object points were used to calibrate the camera using OpenCV's calibrateCamera() function. The resulting camera matrix and distortion coefficients were used to undistort camera images as shown below:

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

Similar to the undistorted chessboard image above, images from the car's camera were also corrected using the same matrix and coefficients:

![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I experimented with gradient (both x and y), magnitude, direction, HLS, HSV and CIELAB thresholds. They were used seperately and in combination to determine the best thresholds for lane detection. The code for this, along with various results can be found in p4_advanced_lane_lines_prep (section 3).

From my testing, I found CIELAB transformation and thresholding to produced the best results, which I couldn't improve upon by using other methods. The final thresholds used (based on 2 channels from CIELAB transform) can be found in p4_advanced_lane_lines_final (section 2). The binary image below shows the effects of this transform and threshold:

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper()`, which appears in lines 1 through 8 in the file `example.py` (output_images/examples/example.py) (or, for example, in the 3rd code cell of the IPython notebook).  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

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

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `my_other_file.py`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
