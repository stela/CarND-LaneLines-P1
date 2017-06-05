# **Finding Lane Lines on the Road** 

## Writeup


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)
[img_original]: ./test_images/solidWhiteRight.jpg "Original"
[img_grey]: ./writeup_images/grey.jpg "Grey"
[img_edges]: ./writeup_images/edges.jpg "Edges"
[img_hough]: ./writeup_images/hough.jpg "Hough lines only"
[img_final]: ./out/resimg.jpg "final"

---

### Reflection

### 1. Pipeline description and draw_lines() modification.

My pipeline consists of 6 steps after the original image was imported.
![Original, solid white right][img_original]

First, I converted the images to grayscale, this later allows using
 the Canny edge detection out-of-the-box.
![Grey scale][img_grey]

Second the image is blurred (removing high-frequency artifacts), doing this will avoid mislabeling "noise"
 as edges later on. Lane lines are fairly thick and should be rather unaffected by this.

Third, Canny edge detection was used to detect the edges, with a threshold between 50-200.
![Edges detected using Canny Edge Detection][img_edges]

Forth the area of interest was masked off, to avoid seeing cars in the distance and
 to ignore neighbouring lane lines. If I would have put more effort into ignoring horizontal
 lines and classifying the closest left and right lane lines, this would probably not have been required,
 but the result was good enough with simple static masking on the example inputs. 

Fifth, the Hough transform was used to extract lines out of the edges detected previously.
 Since the lane lines were fairly wide, this outputs double or even triple lines sometimes.
![Hough lines][img_hough]
  
Sixth, I modified draw_lines() to first, depending on slope, sort lines into left or right "bins" of lane lines.
 For each one, I used the numpy.polyfit with degree set to one to extract the left/right lane equations.
 With some basic algebra, I used the line equations to draw lanes from the bottom
 of the image to 60% from the top of the image.
![Final result][img_final]



### 2. Potential shortcomings with the current pipeline

While switching lanes, three lane lines are relevant (left, center, right),
the current pipeline is hardcoded to two.

With different lighting, contrast and reflections (shadows, sunny/cloudy, rain e.g.)
the now fixed thresholds would need adjustments.

A fixed horizon isn't realistic, it should detected dynamically
based on the "flow" of image parts in movie.
The "origin" is where the horizon is, at least when driving straight
on a flat road.

With turns of the road, lane lines are curved and both are going
either left or right if the curve is sharp. The 1-degree line equation cannot
express this.


### 3. Possible improvements to the pipeline

For the last step, I first considered using k-means clustering to classify each line's equation into a number of
 lane line "clusters". By checking for deviations with different target counts of clusters,
 I would be able to see how many lines were actually detected. The most vertical ones to the left and right of
 the center would be the ones sought. By averaging each cluster's line equations, I should get a really good
 estimation of each line's equation without noise from surrounding lane lines or other "garbage".
 This seemed too complex though (input would have to be "whitened" and then "de-whitened etc etc) and given
 the limited amount of time, I skipped implementing this.

I noticed the example movies' output lanes moved around very smoothly, so there were probably
 some moving averaging used, but I skipped that as well.
 Using the per-frame output better shows how the algorithm
 actually performs I believe, instead of covering up imperfections.
 
Lane lines could be identified by more than just edges forming lines,
 by matching the whole lane line shape and color (perhaps through convolution
 with a set of lane-line-like kernels),
 or even a neural network trained to detect lane lines.

Instead of using fixed threshold levels, they could probably be determined
 dynamically based on a histogram of road surface colors, I'd guess there are
 typically clusters of plain asphalt and lane lines in the region you'd
 expect/hope there's a road.

The software as written can be cleaned up quite a bit, e.g. there's some
 repetition instead of abstraction and there is image-writing and display
 code commented out which is usedful for debugging and further finetuning.
