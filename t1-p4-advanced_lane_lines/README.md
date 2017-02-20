## Advanced Lane Finding

This project solves the issue of finding lane lines in pictures or videos and plotting them interactively.

# Note
* The accompanying iPython notebook has more detailed contextual information and comments, as well as figures to support the information summary presented here.
* This project uses the PeakUtils package. Please install via `pip install peakutils`.

# Steps

* It computes the camera calibration matrix and distortion coefficients given a set of chessboard images.
  It uses a grid of 9x6 for this purpose.
* Applies the distortion correction to the input image.
* Calculates and applies a perspective transform to the undistorted image and creates a "birds-eye view".
* Uses a Sobel filter in the X direction, as well as a calculated angle from X and Y Sobel filters, and L and
 S channels from the HLS color space, to create a thresholded combined binary image.
* Detects the start of the lines positions using the bottom third of the image by detecting peaks in the corresponding histogram.
* Uses sliding windows to detect right and left line pixels by detecting peaks in the corresponding histogram of each window.
* In the case of video, it will use the information of the previous 3 frames to improve the accuracy of the detection.
* It performs a 2nd degree polynomial fit on the data to determine each line.
* Determine curvature of the lane and vehicle position with respect to center.
* Warps the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position and other debug information.

# What could be improved

The pipeline could be improved by reducting the assumptions to a minimum. Some of the current assumptions are:

* A fixed perspective matrix. We could improve this by automatically determining the perspective transform.
* A flat surface. This is somewhat related to the previous point. We could improve the algorithm by making it work with any angle.
* The lines are well defined at the bottom of the image, which could be incorrect in some cases.
* And that the lines are both present in the image. Which could be false in bad lighting or weather conditions or when swtching lanes for instance.

# What could make this pipeline fail

* It does not behave well when the lines do not span a signification portion of the image or if one line is missing (like in the harder challenge video).
* Varying lighting or weather conditions (rain, snow, night) or different lane colors (in construction zones, lane colours could be orange for example).
* Lanes that disappear for a long period of time or do not start at the bottom of the image.
* The pipeline could fail in the presence of traffic or pedestrians ahead.
* It could fail in ascents or descents.
* It can fail during lane switching.
