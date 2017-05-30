# **Finding Lane Lines on the Road**

## Writeup Template

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps:
* Convert the images to grayscale.
* Apply a Gaussian blur to the grayscale image.
* Use canny helper function for Canny edge detection.
* Clip the edge-containing image to a region of interest (quadrilateral)
* Perform Hough transform to detect lines in the region of interest
* Split the Hough lines into a set of left and right lines and draw an "average" line through them

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by filtering each line on slope. Lines with a slope between 0.5 to 0.9 were grouped into right side lines, while lines with a -0.9 to -0.5 slope were grouped into left side lines. For each side, I computed a least squares slope and intercept and drew the line from the bottom of the screen (maximum height) to the top (height = 0). Then I re-ran the region_of_interest helper function to clip the lines to the region of interest.

### 2. Identify potential shortcomings with your current pipeline

Perhaps the biggest shortcoming is the reliance on the region of interest. In particular, as the car drifts left and right, it can be very easy for the lane lines to move outside of the region of interest, in which case my pipeline would detect no lane line at all.

Another shortcoming is the reliance on the slope ranges. Filtering on slope helps eliminate spurious lines and the roughly horizontal lines that occur at the top and bottom of striped or segmented lane lines. However, this filtering might fail to find a line at all in a tight curve because the lane line slope may change significantly. I would expect in a tight curve that a given lane line might end up separated into two segments of differing slope.

Finally, this pipeline makes no consideration for line tint. It picks up shadows, changing pavement color, and tire marks when run on the challenge video. It would surely pick up lines in cars if they were within the region of interest.

### 3. Suggest possible improvements to your pipeline

One potential way to reduce the likelihood of detecting spurious lines is to do some sort of filtering on color in the original image, perhaps looking for white or yellow with consideration for different lighting conditions. This kind of filtering on color may also enable expanding the region of interest. A drawback might be losing detection of lane lines in the case where they are some other color than was anticipated. Of course, the more colors that we consider, the less effective this change would be.

For handling the reliance on slope, perhaps we could propose an alternate shape, like a parabolic curve instead of a line. Or, we could do a piece-wise linear approximation and join the pieces at the intersection of the two lines.
