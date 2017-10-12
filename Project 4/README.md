# SelfdrivingCarND

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

[image1]: ./output_images/distort_image.jpg "distorted chessboard Image"
[image2]: ./output_images/undistort_image.jpg "Undistorted Chessboard Image"
[image3]: ./output_images/undistort_road_image.jpg "Undistorted Image of the road"
[image4]: ./output_images/H_image.jpg "H image"
[image5]: ./output_images/S_image.jpg "S image"
[image6]: ./output_images/L_image.jpg "L image"
[image7]: ./output_images/B_image.jpg "B image"
[image8]: ./output_images/Mag_image.jpg "gradient magnitude image"
[image9]: ./output_images/comb_image.jpg "combination image"
[image10]: ./output_images/Road_image_with_points.jpg "Road image with source points"
[image11]: ./output_images/pers_road_image.jpg "perspective transformed road image"
[image12]: ./output_images/poly_fit_image.jpg "Image with lane lines marked"
[image13]: ./output_images/warped_back_image.jpg "warped back image to the road"
[video1]: ./result.mp4 "Video"

###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of `P4.ipynb`.  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: ### Original Image

![alt text][image1]

### Undistorted Image

![alt text][Image2]

###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.

#### Undistorted Image of the road

I applied the same technique I used to undistort the chess board image to the road image like this:

![alt text][image3]

####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
I used a combination of color and gradient thresholds to generate a binary image. I tested with different combinations of color and gradient threshold and I finally came up with a combination of l channel in HLS color space and b channel in Lab color space.  Here's the procedure of how I came up with this combination.

I tried with H channel in HLS to help me find the lines. I used a threshold channel of [17, 30]. In this channel H is able to detect the yellow line perfectly in all conditions. But It is associated with a large noise. The H channel image is as shown below.

