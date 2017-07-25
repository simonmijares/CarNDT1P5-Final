## Writeup Template
### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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
[image3]: ./writeup/writeup3.png
[image4]: ./writeup/writeup4.png
[image5]: ./writeup/writeup5.png
[image6]: ./writeup/writeup6.png
[image7]: ./writeup/writeup7.png
[image8]: ./writeup/writeup8.png
[image9]: ./writeup/writeup9.png
[video1]: ./project_simonII.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the 7th code cell of the IPython notebook.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed some test images from the car class and display it to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `HSV` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and based on the paper sugested by the course, 9 orientations should be enough to obtain a diferentiation values for the features of the image. As well as 8 by 8 block, as used by the jpeg standar.
A vector of two features vectorized of a sample of each class can be seen in the following image:
![alt text][image3]

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

In the code block 27 can be showed that I trained a linear SVM using the hog features of the RGB channels and the histogram of the HSV channels of the image.
The trainning itself is showed on Code Block 29 with the following results:

- Vehicles Samples:  1197
- Non-vehicles Samples:  1125
- Total Labels:  2322
- Total Samples:  2322
- Total features per sample:  5388
- The train set have:
    - 1741  Labels
    - 1741  Samples
- The test set have:
    - 581 Labels 
    - 581  Samples
- train precision:  1.0
- test_precision:  0.975903614458

An example of the detection of the images is shown bellow (Green=Ok, Red=No Ok):
![alt text][image4]

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

To Begin I started to search at three resolutions starting at the training resolution (64x64), but in order to reach the car in the smalles size of the sequence, I later included a fourth resolurion. The final set was 48x48, 64x64, 96x96, 128x128. The overlaping in the following sample was of 0.9 but for matters of time, the final vide was trained at 0.75

![alt text][image5]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on the trainning scale using YCrCb 3-channel HOG features, plus 3-Laplacian edge detection, plus 3-histograms of YCrCb Channels in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image8]

---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_simonII.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded (Code block 31) the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here a frame heatmap:

![alt text][image6]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap:

![alt text][image7]

### Here the resulting bounding boxes are drawn onto the last frame in the series:
![alt text][image9]

Ths is in reference to a single frame, but the heatmap as a global variable was averaged with the following frame and then thresholded in the cumulative variable. This in order to take in consideration more than one frame at a time.

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The aproach worked fairly good, detecting the cars on most of the frames, but the algorith requires a intensive search or it will depende greatly on the starting point of search to detect precisely enough the features.
In the process appeared an annoying quantity of false positives, besides the precuations takken that reduced then in a good quantity this still occurs frequently.
The pattern detection of other tecniques like deep learning probably would make a better suited option for this particular case.