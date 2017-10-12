
**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given an arrangement of chessboard pictures. 
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to make a thresholded binary image.
* Apply a perspective transform to rectify binary image. Here it is Birds-eye view
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

Step 1.Camera Calibration


The code for this step is contained in the `adv_lane_line.ipynb`.  cells 2 and 4 in the code clearly explains the calibration code


I begin by getting ready "object points", which will be the (x, y, z) directions of the chessboard corners. Here I am accepting the chessboard is settled on the (x, y) plane at z=0, with the end goal that the object points are the same for every calibration picture. In this manner, `objp` is only a repeated array of coordinates, and `objpoints` will be appended with a duplicate of it each time I effectively distinguish all chessboard corners in a test picture. `imgpoints` will be added with the (x, y) pixel position of each of the corners in the picture plane with each effective chessboard discovery. I then used the output `objpoints` and `imgpoints` to calculate the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

### Original Image

![alt text][image1]

### Undistorted Image

![alt text][Image2]

Finally, the camera calibration matrix and distortion coefficients were used with the OpenCV function undistort to remove distortion from highway driving images.


Provide an example of a distortion-corrected image.

#### Undistorted Image of the road

I applied the same technique I used to undistort the chess board image to the road image like this:

![alt text][image3]

2.Color transforms, gradients or other methods to create a thresholded binary image.  
Provided an example of a binary image result.

I utilized a blend of color and gradient thresholds to produce a binary image. I tried with various blends of color and gradient threshold and I at long last thought of a mix of l channel in HLS color space and b channel in Lab color space. Here's the methodology of how I thought of this mix.

I tried with H channel in HLS to help me find the lines. I used a threshold channel of [17, 30]. In this channel H is able to detect the yellow line perfectly in all conditions. But It is associated with a large noise. The H channel image is as shown below.

![alt text][image4]

I used S channel in a threshold range [100, 255]. S is able to detect all color lines but I found it is not perfect in all conditions. Then I thought of using S channel with h channel which produced very good results. The S channel is a s shown below.

![alt text][image4]

I used L channel in a threshold range [200, 255]. L channel has a capability to detect white lines nearly perfect in all conditions. The L channel image is as shown below.

![alt text][image6]

I also tried with B channel in Lab color space in a threshold range [155,255]. B produced good results finding the yellow line very perfectly in all conditions. This made me use B channel in all my test combinations to find the lane lines. The image of B channel is as shown below.

![alt text][image7]

I tried using Magnitude gradient threshold with range [50,200]. But color spaces are  better in detecting the lane lines. I thought of just using the color space would prodice good results in my final result. The image produced by gradient thresholding is as follows.

![alt text][image8]

After trying different combinations like ((S&H) | L), (H | L), (S | L), (B | L), (S | B), ((S&H) | L | B), I got good results with (L | B ) and ((S&H) | L | B). I used (L | B) combination to finally produce my result. The combination image is shown below:

![alt text][image9]

####3. Performed a perspective transform 

The code for my perspective transform includes a function called `perspective_transform()`, which appears in cell 6 of code `adv_lane_line.ipynb`. The `perspective_transform()` function takes as inputs an image (`img`).  I chose the hardcode the source and destination points in the following manner:

```
src = np.float32([[545,470],[750, 470],
                      [60, 720],[1280, 720]])
dst = np.float32([[0, 0], [1280, 0], 
                     [0, 720],[1280, 720]])

```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

### Points selected for Warped Image

![alt text][image10]

### Perspective transformed image

![alt text][image11]

4.Identified lane-line pixels and fit their positions with a polynomial

I  have computed a histogram and found peaks in left and right parts of image and formed a rectangle window around those peaks and then stack those windows which follow the line by checking and if the number of points in each window is greater than 50 then change the x position of the window to the mean x position of the points. I maintain record of all the points in the window and then used polyfit() functions to fit a curve to those points.

Once I fit a curve in a frame of video, for the next frame I do not repeat the whole procedure but search within the region around the pervious fitted curve

![alt text][image12]

5.Calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I calculated the radius by the following code which takes care of the conversion from pixels to meters which is implemented in code 

```
ym_per_pix = 30/720 # meters per pixel in y dimension
xm_per_pix = 3.7/700 # meters per pixel in x dimension

left_fit_cr = np.polyfit(lefty*ym_per_pix, leftx*xm_per_pix, 2)
right_fit_cr = np.polyfit(righty*ym_per_pix, rightx*xm_per_pix, 2)
left_curverad = ((1 + (2*left_fit_cr[0]*y_eval*ym_per_pix + left_fit_cr[1])**2)**1.5) / np.absolute(2*left_fit_cr[0])
right_curverad = ((1 + (2*right_fit_cr[0]*y_eval*ym_per_pix + right_fit_cr[1])**2)**1.5) / np.absolute(2*right_fit_cr[0])

```


6.The lane area is identified clearly.

I implemented this step in my code in code cell 13 in `adv_lane_line.ipynb`.  Here is an example of my result on a test image:

![alt text][image13]

---


7.A link to  final video output. 

Here's a [link to my video result](./result.mp4)

---

Discussion

The video pipeline created in this project made a genuinely hearty showing with regards to of distinguishing the path lines in the test video accommodated the undertaking, which demonstrates a street in essentially perfect conditions, with genuinely particular path lines, and on a clear morning. But it has problems detecting the lines in shadows and road with bright color where it has problem differentiating the lines with the road.
What I have discovered from this project is that it is moderately simple to finetune a software pipeline to function admirably for steady street and climate conditions, yet what is testing is finding a solitary blend which creates a similar quality outcome in any condition.
I think evaluating the street as indicated by the full width of the street and past estimation of the path lines will prove to be useful a few times. There ought to be some exchange of with the estimation and the genuine estimation. Along these lines I figure, the framework will be more vigorous to various street conditions and light conditions.
I have yet to try the pipeline on extra video streams which could challenge the pipeline with changing lighting and climate conditions, street quality, blurred path lines, and leaving an expressway. 
