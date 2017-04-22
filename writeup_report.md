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

---

**Camera Calibration**

**1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.**

By using the function `cv2.drawChessboardCorners` we can locate the coordinates of the corners inside a distorted chessboard image.  Having the object points and image points we can compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function. Once calibration is complete, OpenCV's `cv2.undistort` function is very effective in undistorting the image. The same steps can be applied to a road image as well.

<img src="./output_images/example_undist.png">

---

**Pipeline (single images)**

**1. Provide an example of a distortion-corrected image.**

<img src="./output_images/example_undist_test1.png">

**2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.**





**3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.**

In the 6th cell of my jupyter notebook file, I've pinpointed the source coordinates of a test image where the lane lines appeared to be parallel and the area where I wanted my perspective transform to take place.  Once this process was complete, I utilize a warp function I defined in the previous cell and map out the destination coordinates and apply a prespective transform using OpenCV's `cv2.warpPerspective`.

This resulted in the following source and destination points:

| Source      | Destination | Position    |
|-------------|-------------| ------------|
| 730, 460    | 1120, 0     | Top Right   |
| 1100, 690   | 1120, 680   | Bottom Right|
| 280, 695    | 200, 680    | Bottom Left |
| 600, 455    | 200, 0      | Top Left    |


<img src="./output_images/example_hls_warp.png">

<img src="./output_images/example_undist_warp.png">

**4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?**

**5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.**

In the process_image() function, to calculate the radius of curvature, we average out the left and right curvature radii and calculate the second order of polynomial and them to the x,y in world space.  By utilizing these two formulas we are able to calculate the radius of the curvature for each line.

Assuming that the camera is mounted in the middle of the car, we can compare the center points of the binary warped space of the image's center and measure the differences in position with respect to the center.  

**6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.**

<img src="./output_images/process_image.png">

---

**Pipeline (video)**

**1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).**

https://github.com/atlas604/advanced-lane-lines/blob/master/video_output.mp4

---

**Discussion**

**1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?**

Since my pipeline takes the input frame by frame, some lanes at certain frames may not be detected by the algorithm.  Having an empty array will result in errors.  To accommodate this issue, we can either lower the threshold to help detect less visible lanes or take in the data from the previous frame whenever an empty lane is detected.  I recognize the lane boundaries may slightly overflow to the other lane; however, as long as the car is maintaining its position relatively close to the center, this shouldn't impose a problem.  

The contrast of the image affects the pipeline a lot as well.  Having a lot of shadows or running the pipeline at night with bad lighting can ruin the visibility of the lanes and cause errors.  To fix these issues, we can implement an algorithm that adjusts the contrast when the detected image is too dark or normalizes the data in a certain way. Additionally, we can improve the model by storing data of the frames and average out the lane coordinates with a certain amount of frames then plots the curvature which should create smoother lines and fix distortion caused by shadows or changes in the landscape.  
