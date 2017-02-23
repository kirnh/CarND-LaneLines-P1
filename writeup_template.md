#**Finding Lane Lines on the Road** 

---

###The goals of the project were the following:

  * Make a pipeline that finds lane lines on the road
  * Reflect on your work in a written report


[//]: # (Image References)

[image0]: ./test_images/solidWhiteRight.jpg "Input image"
[image1]: ./test_images/gray.jpeg "Grayscale"
[image2]: ./test_images/gaussian_blur.jpeg "Gaussian blur"
[image3]: ./test_images/edges.jpeg "Canny edges" 
[image4]: ./test_images/lane_region_edges.jpeg "Region of interest"
[image5]: ./test_images/hough_lines.jpeg "Hough Lines"
[image6]: ./test_images/extrapolated_lines.jpeg "Extrapolated Lines"
[image7]: ./test_images/final.jpeg "Output image"

---

### Reflection

####1. Description of the lane detection pipeline.

The pipeline consists of 5 steps:

Input image -> Grayscale -> Gaussian blur -> Canny edges -> Region of interest -> Hough lines -> Output image 

  * The input image is first converted to grayscale and blurred using Gaussian blur to smoothen the edges before using Canny edge detection to detect the edges present in our image. 

---

![Input image][image0]

![Grayscale][image1]

![Gaussian blur][image2]

![Canny edges][image3]

---

  * Then to remove edges other than those part of the lanes, we define a region of interest and select edges present within this region before using Hough transform to identify lines from the binary image containing the canny edges.

---

![Region of interest][image4]

![Hough lines][image5]

---

  * To identify the full extent of the lanes on the road, we need to extrapolate the lines identified as Hough lines. To do this, we modify the draw_lines() function used within our Hough lines detection step in our pipeline to accomodate extrapolating. First, we classify the Hough lines as either belonging to the left lane or the right lane depending on their slope values, store these values along with the calculated y_intercepts of each of the lines before finding the average values (median is used as a measure of center because the distribution of the values are skewed). We are then left with an average value for the slope and y_intercept of left lane and right lane. These can be used to define our lane lines in the form [y = slope * x + y_intercept]. By intersecting these lines with two horizontal limit lines defined by y co-ordinate values (upper limit and lower limit depending on the region of interest), we get end points for our lane lines that extend to the full length of our region of interest. This is then used to draw the extrapolated lines and augmented on the original image as required.

---

![Extrapolated lines][image6]

![Output image][image7]

---

####2. Potential shortcomings with the current pipeline

One potential shortcoming would be what would happen when the contrast between lane lines and the road becomes very less or too varying across the road image due to various reasons. If this happens, the Canny edge detector would pick up edges apart from lanes in our region of interest. This might make the Hough tranform detect the noise as lines (which could have slopes very far from those of lane lines). Our pipeline that finds the lane lines by averaging the values of slopes of detected Hough lines would be drasticaly affected by this noise.

Another shortcoming could be identifying lane lines that form steep curves in the image. Because we classify lines as either belonging to left or right lane depending on whether the slope is positive or negative in our current pipeline, the edges detected from the curved lines could potentially be misclassified. Our pipeline that finds the lane lines by averaging the values of slopes of detected Hough lines would be drasticaly affected by this misclassification.

---

####3. Suggested possible improvements to the pipeline

A possible improvement would be to include a more robust method of classifying lines as either belonging to the left lane or right lane. One way to do this is to use two regions of interest to identify left and right lanes seperately and not use slopes to classify the lines. And we could use slopes further down the line to remove any noise (lines not belonging to the lanes detected as Hough lines) and make the pipeline more robust. 

Also if we can use any method to identify curves other than just straight lines instead of using Hough transform to detect straight lines and replace the draw_line() function with a function that could draw a curve, it would make the pipeline better at handling curved lane lines in the image.

---