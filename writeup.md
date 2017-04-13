**Advanced Lane Finding Project**

[image1]: ./for_writeup/calibration0.jpg "Calibration Before"
[image2]: ./for_writeup/calibration1.jpg "Calibration After"
[image3]: ./for_writeup/thresholded.jpg "Thresholded"
[image4]: ./for_writeup/transformed.jpg "Transformed"
[image5]: ./for_writeup/plotted_back.jpg "Plotted Back"

### How I computed the camera matrix and distortion coefficients.

I first use cv2.findChessboardCorners to find image points and generate object points. I then use cv2.calibrateCamera to compute the camera calibration matrix and distortion coefficients.

### Example of a distortion-corrected image.
![alt text][image1]
![alt text][image2]

### Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image. Provide an example of a binary image result.

The code is in image_utils.py LaneDetector.generate_thresholded_image(). I first transform the image into YUV color space and HLS color space. I used Y and U channels of YUV space and S channel of HLS to identify lane pixels because that lane pixels are most prominent in these channels. I then use Sobel filter to detect edges. I also used two custom methods to extract yellow pixels and highlight pixels from the image. I combine all these binary thresholds to create single thresholded binary image.
![alt text][image3]

### Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code is in image_utils.py LaneDetector. I assumed that the road is on a plane and the plane does not change throughout the video. I selected four points on the original image, two on the left line and two on the right line and I then map the four points to two straight lines. I use cv2.getPerspectiveTransform to calculate the matrix.
![alt text][image4]

### Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?
I use the following steps to identify lane-line pixels and fit their positions with a polynomial. I first divide the image into horizontal strips. I then search lane pixels from bottom up. For each step I count all the pixels for each strip using a histogram. If I find minimum number of lane pixels in the window I'm searching I consider it to be a valid window. I also set a maximum shift pixels between two adjacent windows. Once I have a series of windows that contain all the lane pixels, I do a second order polynomial fitting of all the lane pixels to determine the lane position.

### Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.
I first estimate how long a pixel represent in real world in both x axis and y axis. I transform the fitted polynomials from pixel space to meter space. I then use the curvature formula to calculate the curvature of the curve. I choose the bottom of the polynomial to calculate curvature because that is closest to the camera and should be more reliable than other position to calculate the curvature. I also calculate the bottom position of the two curves and then use the center point as the estimated position of the vehicle.

### Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.
![alt text][image5]

### Provide a link to your final video output.
[Link](https://youtu.be/YcR2yBNeWRg)


### Briefly discuss any problems / issues you faced in your implementation of this project. Where will your pipeline likely fail? What could you do to make it more robust?

I find that the step of creating a binary thresholded image is crucial in this project. If this step does not produce good quality binary image, the later steps are likely to fail. I tuned the hyper parameters manually but I think as an improvement of the pipeline the parameter tuning could be performed automatically. This will also make the pipeline more robust when using it in different situations. 

There are also other information that we could incorporate into the pipeline to potentially make it more robust. For example, we could utilize the fact that the two lane lines are almost always parallel to each other. Also, in a lot of cases, we have more confidence in one lane line. By incorporating these knowledge into our pipeline, we could potentially filter out some false positive lane pixels.
