# SDC Project 4: Advanced Lane Finding

Detecting lane lines from a mounted self-driving car camera

### Included files:
  1. **p4_advanced_lane_lines_prep.ipynb:** used to develop and fine-tune functions/parameters used in the final code.
  2. **p4_advanced_lane_lines_final.ipynb:** contains the full pipeline for creating the output video.
  3. **[output_video.mp4](./output_video.mp4):** output with lane lines, curvature and center offset.
  4. **project_report.md:** project writeup with development details and results.
  
### Project Outline:
1. Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
2. Apply a distortion correction to raw images.
3. Use color transforms to create a thresholded binary image with lanes.
4. Apply a perspective transform to rectify binary image ("birds-eye view").
5. Detect lane pixels and fit to find the lane boundary.
6. Determine the curvature of the lane and vehicle position with respect to center.
7. Warp the detected lane boundaries back onto the original image.
8. Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.
  
**Note:** the code used in p4_advanced_lane_lines_prep and p4_advanced_lane_lines_final is adopted from the project lessons, or developed by myself.

[//]: # (Image References)

[image1]: ./output_images/01_undistort.png "Undistorted"
[image2]: ./output_images/01_undistort2.png "Undistorted"
[image3]: ./output_images/02_binary.png "Binary"
[image4]: ./output_images/03_warped.png "Warped"
[image5]: ./output_images/04_window_search.png "Window Search"
[image6]: ./output_images/05_lanes.png "Detected Lanes"
[image7]: ./output_images/06_output.png "Unwarped Output"
[image8]: ./output_images/07_final_test.png "Final Pipeline Test"

---

### Camera Calibration

The code for camera calibration can be found in p4_advanced_lane_lines_prep (section 1). Using images of distorted chessboards, image points of internal corners were determined using the OpenCV function findChessboardCorners(). These points were correlated with object points (in 3D space) with the same number of internal corners. The image points and object points were used to calibrate the camera using OpenCV's calibrateCamera() function. The resulting camera matrix and distortion coefficients were used to undistort camera images as shown below:

![alt text][image1]

### Implementation

Similar to the undistorted chessboard image above, images from the car's camera were also corrected using the same matrix and coefficients:

![alt text][image2]

I experimented with gradient (both x and y), magnitude, direction, HLS, HSV and CIELAB thresholds. They were used seperately and in combination to determine the best thresholds for lane detection. The code for this, along with various results can be found in p4_advanced_lane_lines_prep (section 3).

From my testing, I found CIELAB transformation and thresholding to produced the best results, which I couldn't improve upon by using other methods. The final thresholds used (based on 2 channels from CIELAB transform) can be found in p4_advanced_lane_lines_final (section 2 final_binary()). The binary images below show the effects of this transform and threshold using various input images:

![alt text][image3]

The code for perspective transform can be found in p4_advanced_lane_lines_final (section 2 warp_image()). I started by warping an image of straight lanes and corrected the source and destination points such that the warped image contained almost parallel lane lines. The resulting image looked like this:
  
![alt text][image4]
  
Based on the following source and destination points:
  
| Source        | Destination   | 
|:-------------:|:-------------:| 
| 602, 445      | 320, 0        | 
| 200, 720      | 320, 720      |
| 1130, 720     | 960, 720      |
| 682, 460      | 960, 0        |
  
To identify lane pixels I used a sliding window search, the code for which can be found in p4_advanced_lane_lines_final (section 2 window_search()). Pixels for the left and right lanes were determined seperately (based on midpoint), and were used to fit a 2nd order polynomial. The detected pixels, along with their search window, and fit lines can be seen in the image below:

![alt text][image5]

The code to calculate lane curvature can be found in p4_advanced_lane_lines_final (section 2 real_lane_curvature()). This code is identical to the code provided in the lessons. It worked well for my purpose though I suspect my meter/pixel ratios might be off. 
  
The code to calculate the car's offset from the center of the lane can be found in p4_advanced_lane_lines_final (section 2 car_offset()). To calculate the offset, I subtract the lane's calculated center (based on fit lines) from the image center. Based on the output video, the offset calculation appears to be accurate.

The image below demonstrates the full pipeline for lane detection: original image > undistortion > binary transform and threshold > bird's eye view transform > lane detection > unwarp back on to original image. The code for this can be found in p4_advanced_lane_lines_final (section 3).

![alt text][image8]

---

### Discussion

The most difficult part of this project was determining the gradient/color transform used to produce the binary image with lane lines. No one technique was sufficient to detect lane lines under varying conditions: lane color (yellow or white), road color, shadows, etc. I expected the gradient, magnitude and direction transforms to produce the best results; however, they were not as good as simply using CIELAB color transform. By combining two channels of the CIELAB transform, I got the best binary results containing the lanes, and little else. Still I expect this is an area where the project could be improved, by using other forms of thresholding and transformation.

I suspect my calculations for left and right lane curvature is off, due to incorrect meters/pixels ratios. However they are within the expected order of magnitude.

Based on the output video, I noticed that movements in the car's camera (ie car goes over a bump), creates undesired fluctuations in lane detection. Though not catastrophic, this is something that would need to be addressed for more accurate results. Furthermore I found shadows to still be an issue when looking for lane pixels. I suspect better color transformtion and thresholding could improve results in this regard.
