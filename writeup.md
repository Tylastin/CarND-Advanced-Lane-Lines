## Advanced Lane Finding Project


---

**Advanced Lane Finding Project**

The goal of this project is to create a pipeline that detects lane lines in road images.

The steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)


[image1]: ./pipeline/undistorted_calibration_image.jpg "Undistorted"
[image2]: ./pipeline/undistorted_lane_image.jpg "Road Transformed"
[image3]: ./pipeline/binary_threshold_image.jpg "Binary Example"
[image4]: ./pipeline/warped_straight_lines.jpg "Warp Example"
[image5]: ./pipeline/color_fit_lines.jpg "Fit Visual"
[image6]: ./pipeline/final_output.jpg "Output"
[video1]: ./transformed_project_video.mp4 "Video"


## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./pipeline/lane_detection_pipeline.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image. `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the calibration image using the `cv2.undistort()` function and obtained the undistorted image. 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

Using the camera matrix and distortion matrix obtained in the last step i applied cv2.undistort() function to correct the lane images.

Example of a distortion-corrected lane image:
![alt text][image2]

#### 2. Describe how you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.


I used a combination of color and gradient thresholds to generate a binary image. The thresholding steps are in the third code cell of the IPython notebook located in "./pipeline/lane_detection_pipeline.ipynb".  
Here's an example of my output for this step:

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform is located in the 3rd code cell of the IPython notebook.  The code takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
# # # For source points I'm selecting four corners of a trapezoid that lie on the lane lines

src = np.float32([(595,450),(690,450),(1100,720) ,(210,720)])

# # # Code used to select source points by eyeballing 

# # For destination points, I'm choosing some points to be
# # arranged in a rectangle and fit the offset value 

dst = np.float32([[offset, 0], [img_size[0] - offset, 0], 
                             [img_size[0]-offset, img_size[1]], 
                             [offset, img_size[1]]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 595, 450      | 255, 0        | 
| 690, 450      | 1055, 0       |
| 1100, 720     | 1055, 720     |
| 210, 720      | 255, 720      |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?
.
Then i used sliding windows and histograms to detect lane line pixels. Once the pixels for each lane line were detected i used the cv2.polyfit function to fit a 2nd order polynomial to each lane line. The code for this process can be found in the fifth code block of the Jupyter notebook. Example polynomial fit:
![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I calculated the radius of curvature and the vehicle position with respect to the center of the lane in code block 6 of the Jupyter notebook. The lane center position was calculated by taking the mean of the x values for each polynomial fit line at the bottom of the image. The vehicle position was taken as x value of the center of the image base. The radius of curvature was calculated by scaling the polynomial to meters and using the formula R = (1+(2*A*y+B)^(3/2))/|2*A|

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

Finally, I plotted the lane line polynomial fits back onto the original image. I also added curvature and vehicle text output on the image. The code for this is in code block 7,8, and 9 of the Jupyter notebook. The following image illustrates the result:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./pipeline/transformed_project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Although the current pipeline performs well on the smooth project video, it fails to perform well on the challenge video. To make the pipeline more robust, a sobel magnitude threshold could be added. Also the paramters are not currently optimized. If the parameters are tuned more finely then shadows and foreign lines would be less likely to trick the pipeline. If these improvements are made the pipeline should perform better on the challenge videos.
