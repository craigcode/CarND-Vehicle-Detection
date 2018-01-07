## Udacity SDC - Vehicle Detection & Tracking - Project 5
## Writeup 


The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/hog_vis1.png
[image2]: ./output_images/hog_vis2.png
[image3]: ./output_images/hog_vis3.png
[image4]: ./output_images/hog_vis4.png
[image5]: ./output_images/hog_vis5.png
[image6]: ./output_images/heatmap_vis.png
[image7]: ./output_images/bbox_vis.png
[video1]: ./project_video_output.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

In cell 3, I define a function `get_hog_features()` to return HOG features.
This wraps the `hog()` function imported from SciKit `skimage.feature`

The visualizations below were from 5 random sets of car and not-car images.

For this initial example visualization, I used the following feature parameters:

    Orientation bins = 6
    Pixels per cell = 8
    Cells per Block = 2 


![Random car and notcar HOG visualizations][image1]
![Random car and notcar HOG visualizations][image2]
![Random car and notcar HOG visualizations][image3]
![Random car and notcar HOG visualizations][image4]
![Random car and notcar HOG visualizations][image5]

#### 2. Explain how you settled on your final choice of HOG parameters.

Once I later incorporated this in my pipeline `find_cars()` I experimented with 
a variety of values, however my best SVC accuracy resulted from:

    Orientation bins = 9
    Pixels per cell = 8
    Cells per block = 2


#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

In cell 14, I trained a linear SVM using feature parameters:

    Color space = "YCrCb" 
    Orientation bins = 9 
    Pixels per cell = 8
    Cells per block = 2
    Number HOG channels used = 3
    Spatial imagesize = (32, 32)
    Histogram bins = 32

I used SciKit's `StandardScaler` was to normalize input parameters.

The resulting feature vector length was 8460, which took 14.26 Seconds to train the SVC resulting in:

    Test Accuracy of SVC =  0.99    

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

In cell 7, `slide_windows()` defines the sliding window search.
Once the region of interest had been limited from 400 to 720, I found combining scales of 1.5 and 1.0 and an overlap of 0.5 gave the best results with fewest false positives and missed identifications.

![alt text][image6]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

As stated, using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector provided the highest accuracy in my tests.  I improved the classifier accuracy by modifying the C parameter to 0.01. Here are some example images:

![alt text][image7]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)

Here's a [link to my video result](./project_video_output.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

Cell 22, `process_image()` marshalls my pipeline which employs functions
`apply_threshold()`, `add_heat()` and `draw_labeled_bboxes()` previously
defined in cell 19 which apply thresholding to heat maps,
store and average these across multiple images in the Car() class
defined in cell 21, erasing most false positives, and draw bounding boxes around these using the `label` feature from `scipy.ndimage.measurements`. 



### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

With more time I could absolutely make this more robust by introducing a sliding window
search with different dimensions, perhaps mindful of the perspective dimensions in 
various parts of the region of interest. Before limiting the region of interest,
the pipeline gave many false positives, notably in the trees. Trees, lighting transitions etc. would still cause these to failures. 

