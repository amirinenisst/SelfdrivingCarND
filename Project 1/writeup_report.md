# **Finding Lane Lines on the Road** 

## Writeup report

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Installing python, jupyter notebook and required libraries 
* Write the code in jupyter notebook 

### Reflection

### 1. My pipeline consisted of 5 steps. First, I converted the images to grayscale as a part of color selection, then I applied 
gaussian smoothing to the selected image. To detect the edges of an object I've used canny edge detection where The thresholds 
are chosen wisely. Hough transform is then applied to the detected image. Now its time to test on images whether we are able to detect
the lines in that image. Finally I've tested on a video.

### 2.potential shortcomings with in current pipeline

It's nice to see the cool output. But when I observed deeply, my piece of code will not be robust for some camera angles and this
algorithm can be further modified for robustness.
one more thing that I figured is, this developed algorithm may not work for steep roads(up/down). 

### 3. Suggest possible improvements to current pipeline

A possible improvement would be to figure out the horizontal road where the sky and the road meets and then implement the algorithm
in case of steep roads

