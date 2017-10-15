

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Histogram of Oriented Gradients (HOG) feature extraction should be performed on a labeled training set of images and train a classifier (Linear SVM classifier)
* Application of color transform and additional features like binned color features, histograms of color can be appended to HOG feature vector
* Implement a sliding-window technique and trained classifier is used to search for vehicles in images.
* Run your pipeline on a video stream (begin with the  test_video.mp4 and later implement on full project_video.mp4) and create a heat map of repeating recognitions frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.

[//]: # (Image References)
[image1]: ./output_images/car.png
[image2]: ./output_images/car_hog.jpg
[image3]: ./output_images/notcar.png
[image4]: ./output_images/notcar_hog.jpg
[image5]: ./output_images/cary.jpg
[image6]: ./output_images/hogy.jpg
[image7]: ./output_images/caru.jpg
[image8]: ./output_images/hogu.jpg
[image9]: ./output_images/carv.jpg
[image10]: ./output_images/hogv.jpg
[image11]: ./output_images/bbox.jpg
[image12]: ./output_images/all_boxes_car.jpg
[image13]: ./output_images/heat_map.jpg
[image14]: ./output_images/all_boxes_car.jpg
[image15]: ./output_images/final_car_boxed.jpg

[video1]: ./project5_result.mp4


Here I will consider the rubric focuses exclusively and portray how I tended to each point in my execution. 



1.Histogram of Oriented Gradients (HOG)


The code for this step is contained in the code cell 3 of `veh_det.ipynb`.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes and their HOG visualizations:

### Car

![alt text][image1]

### HOG visualization of car

![alt text][image2]

### Not a Car

![alt text][image3]

### HOG visualization of a non car

![alt text][image4]

To extract Hog features, spatial and color histogram features. The `extract_features` function is called for the list of images,  in the code cell 5 in `veh_det.ipynb`. 
The aftereffects of these extracated features for each arrangement of pictures are then stacked and  normalized (utilizing sklearn's `StandardScaler` strategy) and afterward split into training and testing datasets (utilizing sklearn's `train_test_split` technique).

I at that point investigated distinctive color spaces like HLS, HSV, YUV, RGB. I used YUV for my feature extraction because of its highest accuracy. 

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I have built a classifier and tried to detect cars using these paramters in an image.


#### Y image of car in YUV space:

![alt text][image5]

#### HOG visualization of Y image of car in YUV space:

![alt text][image6]

#### U image of car in YUV space:

![alt text][image7]

#### HOG visualization of U image of car in YUV space:

![alt text][image8]

#### V image of car in YUV space:

![alt text][image9]

#### HOG visualization of V image of car in YUV space:

![alt text][image10]

2.Final choice of HOG parameters.
Trial and error method worked for me when I need to choose and finalize. 
The following parameters of HOG in YUV color space are the best set of paramters that worked well on the test images in detecting cars.
```
color space = 'YUV'
orientations=8
pixels_per_cell=(8, 8)
cells_per_block=(2, 2)
```
I attempted different mixes of parameters and I at long last picked the parameters appeared previously. I changed the number of orientations from 5 to 10 and 8 introductions worked best for me.
I chose my last parameters in light of length of the vector it created as it specifically mirrors the time utilization of the code and accuracy/precision I am getting with my classifier. 



3.Classifier
Features are needed to train a classifier and make predictions on the test or real-world images.

The project required to construct a classifier that can answer if there is a car in a given picture (subset of the entire picture). To address this assignment three sorts of features can be utilized: HOG (Histogram of Oriented Gradients)- color features, binned -  color and shape features and color histogram features-color only. This blend of highlights can give enough data to picture order. 
I have only used HOG features and not included color or spatial features to avoid very huge feature vectors. Extracting features and training the classifier is implemented in code cell 8 in `veh_det.ipynb`. 
I have divided the data(car and non car images) into training and testing datasets and then extracted features (HOG) using `extract features` function. I have then scaled and normalized the features to zero mean and unit variance and fed to Support vector machine classifier with appropriate labels(car or not car). The model learnt by the classifier was then tested to predict labels on the testing dataset. 

Accuracy = 98.65% on the testing data set.

4.Sliding Window Search

This is implemented in code cell 14 in `veh_det.ipynb` where I have a function for sliding the window and searching for vehicles in this window.
Since the cars are located on the lower half of the road I've only searched in the bottom part.
I've again used trial and error method 
After several combinations and detecting which are fast and good, I've  have commited to the point sliding window overlaps for 0.75 (both x and y).

The classifier checks if there isa car in the frame and all windows where a vehicle is anticipated is returned. The  cars which are closer seem greater and which are further seem littler. So I have limited the bigger windows to bring down piece of the pursuit region and I have additionally looked on three sizes of window sizes little, medium and substantial with a specific end goal to have the capacity to distinguish cars in all positions in a picture which are observed to be best on the test pictures. 
The following are the window sizes in x and y directions I have used which also in code cell 14 in `veh_det.ipynb`

```
75,75 
100,100
125,125
```
Here is the image containing the list of windows that are usually searched for:

![alt text][image11]

At last I sought on three scales utilizing YUV 3-channel HOG highlights. The classifier anticipated whether a given window has an auto with some certainty. The windows that returned high certainty are chosen to be powerful to false positives and afterward I made a heatmap which is like likelihood of distinguishing a car in a window. 

The following image shows all the windows returned by the `search_windows` where the car is detected.


![alt text][image12]

On the off chance that there are numerous windows in a locale then the heatmap has high value and demonstrates higher likelihood of distinguishing a car which should be possible by thresholding heatmap in the wake of attempting distinctive values for an edge and the best limit for which I could sensibly identify cars and take out false positives is 2.

---
Video 

Here's a [link to my video result](./project5_result.mp4)

Filter for false positives and method for combining overlapping bounding boxes.

False positive may happen when a window has no car and was distinguished to be a car. One of the least complex approach to wipe out the False positives is utilizing multi-outline amassed heatmap where I put away the last 15 heatmaps and ascertained the normal heatmap and after that thresholded the heatmap. When you consider numerous edges the odds of False positive getting identified various circumstances is difficult and picking a decent edge on the normal heatmap can take out the False positives to a decent degree. This is actualized in code cell 18 in `Vehicle_detect function`

To combine the result of overlapping windows I have used connectedness property of pixels in a heat map to combine all the combine the overlapping windows using `scipy.ndimage.measurements.label()`. The following are the results obtained:

#### Heatmap

![alt text][image13]

#### All the windows corresponding to heat map

![alt text][image14]

#### Combined window by using 8-connected

![alt text][image15]

---

Discussion


-Obviously, the calculation may flop if there should be an occurrence of troublesome light conditions, which could be incompletely settled by the classifier change. 

-It is conceivable to enhance the classifier by extra information growth, hard negative mining, classifier parameters tuning and so on. 

-I feel there ought to be a superior heuristic for choosing parameters like the colorspace and hyper parameters of HOG which were tested by checking their execution on the test pictures. The pipeline is probably going to flop in the event of impediments and furthermore if there are False positives between genuine positives.

-Likewise I found that it is requiring a great deal of investment to figure for a 50 second video. I think if this calculation need to work continuously, speedier GPUs may be vital. To make it more strong Bag of words kind of methodologies could be utilized to deal with impediments.

