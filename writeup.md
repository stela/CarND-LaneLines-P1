# **Finding Lane Lines on the Road** 

## Writeup


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Pipeline description and draw_lines() modification.

My pipeline consists of 5 steps.

First, I converted the images to grayscale, this later allows using
 the Canny edge detection out-of-the-box.

Second the image is blurred (removing high-frequency artifacts), doing this will avoid mislabeling "noise"
 as edges later on. Lane lines are fairly thick and should be rather unaffected by this.

Third, Canny edge detection was used to detect the edges, with a threshold between 50-200.

Forth the area of interest was masked off, to avoid seeing cars in the distance and
 to ignore neighbouring lane lines. If I would have put more effort into ignoring horizontal
 lines and classifying the closest left and right lane lines, this would probably not have been required,
 but the result was good enough with simple static masking on the example inputs. 

Fifth, the Hough transform was used to extract lines out of the edges detected previously.
 Since the lane lines were fairly wide, this outputs double or even triple lines sometimes.
 I modified draw_lines to first, depending on slope, sort lines into left or right "bins" of lane lines.
 For each one, I used the numpy.polyfit with degree set to one to extract the left/right lane equations.
 With some basic (linear) algebra, I used the lane equations to draw lanes from the bottom
 of the image to 60% from the top of the image.

For the last step, I first considered using k-means clustering to classify each line's equation into a number of
 lane line "clusters". By checking for deviations with different target counts of clusters,
 I would be able to see how many lines were actually detected. The most vertical ones to the left and right of
 the center would be the ones sought. By averaging each cluster's line equations, I should get a really good
 estimation of each line's equation without noise from surrounding lane lines or other "garbage".
 This seemed too complex though (input would have to be "whitened" and then "de-whitened etc etc) and given
 the limited amount of time, I skipped implementing this.

I also noticed the example movies' output lanes moved around very smoothly, so there were probably
 some moving averaging used, but skipped that as well. Using the per-frame output better shows how the algorithm
 actually performs I believe, instead of covering up imperfections.




here is how to include an image: 

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...

TODO:
While switching lanes, 3 lines relevant (left, center, right)
With different lighting, contrast and reflections (rain + sun e.g.) thresholds would need adjustments.
Fixed horizon isn't realistic, should detect it dynamically based on "flow" of image parts in movie.
With sharp turns of the road, curved lane lines and both going left/right.
 


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
