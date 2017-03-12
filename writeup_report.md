**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/calibration2_undistorted.jpg "Undistorted"
[image2]: ./output_images/straight_lines_undist.jpg "Road Transformed"
[image3]: ./output_images/test6_channel_RS_binary.jpg "Binary Example"
[image4]: ./output_images/straight_lines1_warped.jpg "Warp Example"
[image5]: ./output_images/straight_lines1.jpg "Fit Visual"
[image6]: ./output_images/test6.jpg "Output"
[video1]: ./result_video.mp4 "Video"

---
###Writeup / README

###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the fourth code cell of the IPython notebook "CARND-Advanced-Lane-Lines.ipynb". I have also submitted IPython notebook's HTML "CARND-Advanced-Lane-Lines.html" file with sample execution of code.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.
To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

I used 'cv2.undistort()' using previously computed distortion correction and camera matrix to produce this image in cell 5.

####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
I used a combination of color and gradient thresholds to generate a binary image (thresholding steps from cells 6 - 9 IPython notebook "CARND-Advanced-Lane-Lines.ipynb").  Here's an example of my output for this step:

![alt text][image3]

####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `get_warped()`, which appears in the 11th cell of the IPython notebook  (output_images/straight_lines1_warped).  The `get_warper()` function takes as inputs an image (`img`) and apply `cv2.warpPrespective()` function to the input image with pre-computed prespective transform M.  I chose the hardcode the source and destination points in the following manner:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 460      | 320, 0        | 
| 697, 460      | 960, 0        |
| 1044, 690     | 960, 720      |
| 259, 690      | 320, 720      |

Slelection src and dst points correctly for the prespective transform proved to be a challenge for me and I searched the CarND forum for advice from fellow students regarding the selection of points. I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

In cells 13 and 14, I used the sliding search approach as described in lecture notes to find the `left_fit` and `right_fit` of a lane on the road. An example of resultant image is:

![alt text][image5]

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in 15th cell of the Ipython notebook. I have added the position of vehichle with respect to center in current revision. Following is a sample output:

> Estimated radius of curvature: 807.0
> Car is 0.33m right of center

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in 16th cell of the IPython botebook in the function `map_lanes_image()`.  Here is an example of my result on a test image:

![alt text][image6]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

For correctly identifying lane marking in project_video.mp4 I use `Line` class defined in  21st cell of the IPython notebook to keep track of the lane marking from previous frame. This also means that I don't need to apply the sliding frame search for lane on every frame of video.

Here's a [link to my video result](./result_video.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I tried different methods of getting correct warped image from prespective transform unsuccessfully. Then I read more material about prespective transform including discussion about computing vanishing points in an image in order to compute src and dst points. After several unsuccessful attempts, I hardcoded the src, dst by visually looking at image and finding them manually.

In my current city of living, Montreal Canada, there are several roads where the lane markings are so faded that it is hard for a human driver to correctly idenify the lanes. I think the current approach will fail on such roads where the lane markins are fairly sparse due to fading over time. I think in such cases we will need additional sensor information like from a radar so that the car is aware about its surroundings while it plots an imaginary lane for driving.
