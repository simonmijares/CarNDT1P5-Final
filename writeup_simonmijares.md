# Writeup
## Simón Mijares

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./writeup/writeup1.png
[image2]: ./writeup/writeup2.png
[image3]: ./writeup/write03.png
[image4]: ./writeup/write04.png
[image5]: ./writeup/write05.png
[image8]: ./writeup/write06.png
[video1]: ./project_simonII.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the 4th and 5th code cells of the IPython notebook.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed some test images from the car class and display it to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `HSV` color space (in the end `YCrCb` was used) and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and based on the paper sugested by the course, 9 orientations should be enough to obtain a diferentiation values for the features of the image. As well as 8 by 8 block, as used by the jpeg standar.
A vector of a sample can be seen in the following image:
![alt text][image3]
In the image, in different colors we can apreciate every aspect of the vector.
The first three are the histogram of every channel and then the hog of every channel as well (YCrCb in every case).

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

In the code block 06is the first time in the notebook a classifier is trained using HOG features with the following results:
Loading Large dataset
Vehicles Samples:  4023
Non-vehicles Samples:  5068
Total Labels:  18182
Total Samples:  18182
Total features per sample:  5676
The train set have:
13636  Labels
13636  Samples
The test set have:
4546 Labels 
4546  Samples
train precision:  1.0
test_precision:  0.993180818302

Then in the code block 12, 13, 14 & 15th a classifier is trained and tested against hand made preclassified images to have an idea of how the classifier is performing.

The best result obtained was:
---
Loading Small dataset
---
Vehicles Samples:  1197
---
Non-vehicles Samples:  1125
---
Total Labels:  4644
---
Total Samples:  4644
---
Total features per sample:  5676
---
The train set have:
---
3483  Labels
---
3483  Samples
---
The test set have:
---
1161 Labels 
---
1161  Samples
---
train precision:  0.997703129486
---
test_precision:  0.993109388458
---
Avg Error rate: 0.910138345738
---
Avg Hit rate: 1.0
---
Avg Miss rate: 0.0
---


An example of the detection of the images is shown bellow (Green=Ok, Red=No Ok):
![alt text][image4]

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

To Begin I started to search at three resolutions starting at the training resolution (64x64), but in order to reach the car in the smalles size of the sequence, I later included a fourth resolurion. The final set was 48x48, 64x64, 96x96, 128x128. Afeter more intense tests, the most successfull windows sizes were the native 646x64 and the 128x128.
To improve the detection of small cars a second pass of 64x64 was implemented. The most succesfull overlaping for 64x64 was 0.9 and 0.8 for 128x128.

The detection obtained with these parameters can be showed bellow

![alt text][image5]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately after a lot of trial and error I use YCrCb 3-channel HOG features `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)` plus 3-histograms of YCrCb Channels in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image8]

To improve performance only the right side of the image was use for search, but in a real applications, the same should be applied to the left side and front of the car.

---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_simonIX.mp4)

And a link to the youtube version:
[![Final Video](https://img.youtube.com/vi/A92C7rUIfdw/0.jpg)](https://youtu.be/A92C7rUIfdw)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

More than one strategy was tried in order to prevent false positives.
In first place in the draw_labeled_bboxes function, if a box have a incorrect proportion is discarted to avoid small boxes produced by the posterior filtering.
Then a threshold for every instant heatmap frame (th2), after that several detection heatmaps frames are stored in a deque and added and divided by th as a second threshold (since later is multiplied by 10 and casted to int).
After this point a random walker segmentation aproach was tried, but the segmented regions were higly unstable (code block 24-26) so it was droped.
The final approach (code block 27-29) included a filtering in the heatmap for better detection.

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The aproach worked fairly good, detecting the cars on every frame, but the algorith requires a intensive search or it will depende greatly on the starting point of search to detect precisely enough features.
In the process appeared an annoying quantity of false positives, measures were taken and almost entirely reduced.
The selection of the features, the Metaparameters, data to be trained was a process tremendously time consuming. Particularly the feature selection, spatial characteristics were used in most tests and the results were not good for more than two months. Only after intense trial and error, the spatial features were not used and the luma Hog was replaced by the all channels Hog features. After this point better results were obtained.
The pattern detection capabilities of other tecniques like deep learning probably would make a much better suited option for this particular case.
