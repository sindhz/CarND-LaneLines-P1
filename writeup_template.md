**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection


My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I applied a
gaussian filter with a kernel size of 7 to blur the image. Noise in the image is reduced by blurring and 
then the output of gaussian filter which is smoothened image is passed to a canny edge detector to 
extract the edges with a lower threshold of 25 and higher threshold of 125. 
    The output of the canny edge detector is an image with all the edges in the image, 
however for lane line detection we only need to focus on a portion of image, 
so the region of interest helper function is used to mask out the image except a traingle portion
of the image which contains lane lines. This step also greatly reduces the required computation time of next step
where we search for edges that fit a line model. So after this step, the masked output from previous step is given
as an input to the Probablistic Hough Transform Lines algorithm which takes the following arguments:
-> edges: input which is the output of the canny edge detection.
-> lines: A vector to store the coordinates of the start and end of the line.
-> rho: The resolution parameter rho in pixels.
-> theta: The resolution of the parameter theta in radians.
-> threshold: The minimum number of intersecting points to detect a line.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by adding functions
separate_lines which separates left and right lines based on the slope value, if the slope value is positive it is 
treated as right line and negative slope value is left line.
After separating lines, each line segment of both right and left lines are joined by taking average of the position of each of 
the lines and extrapolate to the top and bottom of the lane. Additionally for both right and left lane lines outliers are rejected by setting a cutoff and threshold.

After drawing the lines before the original image is combined with the drawn lines, once again region_of_interest is called
to adjust the size of the mask. To overlay the drawn lane lines on to the original image openCV function 'addWeighted' is called 
original image and image mask with lane lines drawn, which gives us our expected output image with lane lines overlayed.

Please note that the same pipeline is good for both solid white line and solid yellow line because
it is not color dependent instead it is based on Edge detection of lane lines.

### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when there is a snow 
on the road, as our pipeline is entirely relying on camera only sensor for lane lines
detection it is not robust enough to different enviorment conditions and different forms
occlusions. To mitigate this shortcoming a robust pipeline based on multiple sensors like Radar,
Lidar, HDMap and Camera should be developed.

Other shortcoming I think of is during lane change this pipeline may not work correctly
during the transition because of the scope of the pipeline.

Another shortcoming could be that this pipeline only works with straight lane lines because
the model used in hough transform detects only simple lines with a slope but not the curvature.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to taper the lane lines width linearly so that 
the drawn lane lines wouldn't merge at the end. To do that draw_lines should be
modified such that the thickness parameter is inversely proportional to the length 
of the lane line.

Another potential improvement could be to improve robustness of pipeline to detect lane line
with curvature.
