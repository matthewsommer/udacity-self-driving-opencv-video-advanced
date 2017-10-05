# Advanced Lane Finding Project

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

[image1]: ./output/perspective_transform_example.jpg "Perspective transform example"
[image2]: ./output/perspective_transform_mask.jpg "Perspective transform mask example"
[image3]:  ./output/sobel_x_example.jpg "Sobel X Example"
[image4]:  ./output/sobel_y_example.jpg "Soble Y Example"
[image5]:  ./output/grad_mag_example.jpg "Gradient Magnitude Example"
[image6]:  ./output/grad_dir_example.jpg "Gradient Direction Example"
[image7]:  ./output/color_binary_example.jpg "Color Binary Example"
[image8]:  ./output/binary_example.jpg "Binary Example"
[image9]:  ./output/example.jpg "Output Overlay Example"
[image10]:   ./output/camera_calibration_example.jpg "Camera Calibration Undistorted Example"
[image11]: ./input/camera_cal/calibration1.jpg "Camera Calibration Input example"
[image12]: ./input/test_images/straight_lines1.jpg "Camera Calibration Example"
[image13]: ./output/camera_calibration_output_example.jpg "Camera Calibration Output Example"
[image14]: ./output/lane_lines_histogram.jpg "Lane lines histogram"
[image15]: ./output/filepath_warped_binary.jpg "Warped Binary"


# Camera Calibration

To calibrate my pipeline to adjust for the input sensor (a camera) I used three helpful functions in the OpenCV library:

    1. cv2.findChessboardCorners
    2. cv2.calibrateCamera(objpoints, imgpoints, img_size, None, None)
    3. cv2.undistort(img, mtx, dist, None, mtx)

First each calibration image is loaded into an array and these are used with the cv2.findChessboardCorners method to generate output points identifying square corners on the chessboard. These image points are then used in cv2.calibrateCamera to generate a transformation matrix that can be used in cv2.undistort.

Here is an example of using undistort on one of the input chessboard images and an example image from the vehicle.

| Source        | Undistorted   | 
|:-------------:|:-------------:| 
| ![alt text][image11]       |  ![alt text][image10]        | 
| ![alt text][image12]       |  ![alt text][image13]        | 

# Pipeline (single images)

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at lines # through # in `another_file.py`).  Here's an example of my output for this step.  (note: this is not actually from one of the test images)

|         Source        | Sobel X and Y          | 
|:-------------:|:-------------:| 
| ![alt text][image13]     | ![alt text][image3]        | 
| ![alt text][image13]      | ![alt text][image4]      |

|         Source        | Gradient Magnitude          | 
|:-------------:|:-------------:| 
| ![alt text][image13]     | ![alt text][image5]        | 

|         Source        | Gradient Direction          | 
|:-------------:|:-------------:| 
| ![alt text][image13]     | ![alt text][image6]        | 

|         Source        | Color Binary          | 
|:-------------:|:-------------:| 
| ![alt text][image13]     | ![alt text][image7]        | 

|         Source        | Final Binary Image          | 
|:-------------:|:-------------:| 
| ![alt text][image13]     | ![alt text][image8]        | 

# Perspective Transform

To transform the input image perspective I used the OpenCV methods 'cv2.getPerspectiveTransform' and 'cv2.warpPerspective'. I hard coded the input points to generate the transformation. This assumes that the road is flat and the camera location does not change. Here is the hard coded code.

```python
y_mid_adjustment = np.int(img.shape[0]/2) + 100
x_mid_adjustment = np.int(img.shape[1]/2)

point1 = [190, 650]
point2 = [1100, 650]
point3 = [720, y_mid_adjustment]
point4 = [561, y_mid_adjustment]
points = np.array([point1, point2, point3, point4])
```

|         Source        | Transformed image          | 
|:-------------:|:-------------:| 
| ![alt text][image2]     | ![alt text][image1]        | 


# Identify Lane Line Pixels

The code used to find the lane lines and overlay a polyfill polygon onto an input image is in the method 'overlay_lane'. As recommended in the course material and using the example code from the class, sliding window search is used to look for the peaks in the histogram of the lane lines.

Here is an example image of a warped binary representation of the lane lines and it's correspoding histogram.

|         Source        | Transformed image          | 
|:-------------:|:-------------:| 
| ![alt text][image15]     | ![alt text][image14]        | 

Here is a single image showing the lane overlay drawn onto an input image: ![alt text][image9]

---

# Pipeline (video)

Here's a [link to my video result](./output/video.mp4)

---

# Discussion

To improve this project I think I could make much better image manipulation functions. My function that combines the different image extraction could be better such that there is less noise.

My polynomial fitting function could be better. I mostly used what was provided in the lecture but I think I could improve it by limiting the polynomial to have zero curvature by the car (but this assumes the car is driving down the center for the lane so it might not work).