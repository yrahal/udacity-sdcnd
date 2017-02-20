# Vehicle Detection Project

[//]: # (Image References)
[image1]: ./output_images/exploration.png
[image2]: ./output_images/hog1.png
[image3]: ./output_images/hog2.png
[image4]: ./output_images/hog3.png
[image5]: ./output_images/windows_bbox-example-image.jpg
[image6]: ./output_images/output1.png
[image7]: ./output_images/output2.png
[video1]: ./output_images/project_video_out.mp4

## [Rubric Points](https://review.udacity.com/#!/rubrics/513/view)

Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

## Writeup / README

**Please note that I re-used many code snippets from the lessons.**

I loaded the training images and saved them as a pickle file for easier retrieval. I also did some
data exploration and executed sanity checks to make sure that the training data that will be fed to
the classifier is correct. This is done in cell #3 to #6 in the IPython notebook.

![image1]

## Histogram of Oriented Gradients (HOG)

The code for this step is contained in the 6th and 7th code cell of the IPython notebook.

I extracted the HOG features for different combinations colors spaces and parameters displayed them
to get a sense of how the output changes with each value. Here's the output of each combination for the example
above.

The code for this section is in cells 7 to 9 of the notebook.

![alt text][image2]
![alt text][image3]
![alt text][image4]

## Choice of HOG parameters.

I settled on a choice of:
```
color_space='YUV'
orientations=9
pix_per_cell=8
cell_per_block=2
hog_channel="ALL"
```

This seems to give a good balance of having a visual identification of the vehicle and performance.

## Classifier

I trained a linear SVM in the 12th code cell of the notebook. I also used spatial and color features
to add extra accuracy to the model, where each feature vector contains 6108 elements.
I randomly split the data between training (85%) and test (15%).

The classfier trains in 31 seconds and has 98.8% accuracy.

## Sliding Window Search

The sliding window code is defined in cell #7 and used in cell #11. I used 2 sizes (64 and 96) for
searching for cars, where the smallest are closest to the horizon, and the biggest close to the camera.
I chose an overlap of 50% and 60% for the medium and big sizes to increase chances of detection. The total number
of windows for images of 1280x720 is 433 windows, which increases the chances of detections while maintaining
a good performance. I don't search for cars above the horizon or close to the hood of the car, which also
reduces false positives and improves performance.

![image5]

## Pipeline

The pipeline is implemented in cells #11 of the notebook. Cells 13, 14 et 15 show it running on
some example images and displays the output. I used a combination of heatmap thresholding and decision
function to reduce the number of false positives. The heatmap method makes more sense when used in about
video context, which I'll show in the next section.

![image6]

## Video Implementation

The video is generated in cells #16 and #17 of the notebook.
You can find the video at `output_images/project_video_out.mp4`

![Video in output_images/][video1]

## False Positives Filter

In the pipeline (cell #11 of the notebook), I used a heatmap to record detections in the current frame
and combine it with the heatmaps from the previous 10 frames to create a thresholded heatmap from which
I select only the cells that have more than 3 detections. This reduces the false positives and insures
a good detection for the car. I also use the `scipy.ndimage.measurements.label` to group bounding boxes
from the same car.

I also added a size filter to exclude tiny detection windows that have a size smaller than 32x32.

I explored hard negative mining. In the last cell of the notebook, there's a snippet that uses the 6 test images
and defines bounding boxes around cars, if there are any in the frame. It then creates bounding boxes over the whole
frame and sorts them into cars/not_cars if there's a significant overlap with the manually identified bounding box.
I fed this data back to the classifier but I had unexpected results. Although, intuitively it should have improved the
detection, it made it less accurate on the video frames. So I ended up not using this method for the final video, but
 it's worth exploring in the context of a neural network classifier.

Here are the heatmaps and labels for the example shown in the pipeline section above.

![image7]

## Discussion

The pipeline developed here has too many moving pieces and is likely to fail:
* At night, since the training images contains mostly daylight data.
* In Rain, snow or other conditions.
* If there are many cars on the road and closer to the camera, it might succeed, but that has not been
tested here.
* If cars are coming in the opposite direction.
* At intersections, when cars are seen from the side.
* When cars are close together, since it does not implement a robust way to separate multiple vehicles.

These failure cases can mostly be mitigated by collecting and using data in these adverse conditions, and
also adding extra preprocessing (for the night conditions for example) and vehicle identification steps.

It is also a good learning exercice to learn traditional computer vision algorithms and feature extraction,
which gives a hands-on experience and gives a better apperciation for newer deep learning techniques that
have most of the feature extraction implicit in the model.
