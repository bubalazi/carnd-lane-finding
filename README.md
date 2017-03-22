# Finding Lane Lines on the Road

Author: **Lyuboslav Petrov**

[//]: # (Image References)

[grayscale]: ./test_images_output/grayscale_example.jpg "Grayscale"
[blur_thresh]: ./test_images_output/step_2.jpg "Blur and Threshold"
[roi]: ./test_images_output/step_3.jpg "Region of Interest Mask applied"
[edges_lines]: ./test_images_output/step_4.jpg "Edges and Hough Lines"
[result]: ./test_images_output/step_5.jpg "Final"
[RGB]: ./test_images_output/RGB.jpg "RGB Colorspace"
[YCBCR]: ./test_images_output/YCBCR.jpg "YCbCr"
[Angle]: ./test_images_output/AngleA.jpg "Angle"

---

### Introduction

One of the most distinctive features of any road is the markings used for guidance of drivers. Naturally, one of the first features to be discerned in a computer vision based self-driving car are **lane-markings**.

---

### Method

The markings in this project where infered using the following pipeline:

#### Step 1: Aquire image, convert it to grayscale and equalize-out the image using the opencv built-in histogram equalization routine

![][grayscale]

#### Step 2: Apply a Gaussian blur for noise removal and perform a binary threshold

![][blur_thresh]

#### Step 3: Extract the Region of interest using a trapezoid polygon
* Top threshold is set to 52% of image height
* Bottom threshold is the bottom border
* Left-Top edge is set to 48% of image width
* Rigth-Top edge is set to 52% of image width
* Left Bottom is set to 5% image width
* Right-Bottom is set to 95% image width

![][roi]

#### Step4: Convert ROI to edge image and apply a hough transform to identify lines

![][edges_lines]

#### Step5:
The lines detected through the Hough transform are now iterated through to:
* Filter line candidates by a slope threshold (abs(dy/dx) >= 0.44)
* Segment left from right lines by positive and negative slopes respectively
* Filter out noise by extracting all slopes that fall within the median +- one standard deviation
* Using the gathered information get good line candidates and make a first degree polynomial fit
* Finally, find the crossection of the lines found and draw them to the image

![][result]

---

### Shortcommings

The algorithm detailed is very unstable and assumes:

1. A given image color distribution - all images used for tests have a very similar spectrum
2. The camera is mounted at the centre of the car. Otherwise the chosen hard-coded ROI would be useless
3. The road is of a certain color, as well as the lane markings - dark and bright, respectively
    It can be seen that the algorithm brakes at the '*challange*' when a brighter section of the tarmac is entered.
4. The road is straight - The results from the '*challange*' task prove convinsingly that the straight-road assumption is flawed.

---

### Potential Improvements

Every of the mentioned shortcommings is a good candidate for improvements.

---

It has been attempted in this excercise to enable parametric polynomial inference of line-markings, however tests showed that the problem is easily overfitted and results are worst than a 1D polynomial fit.

---

Multiple colorspaces where tested. One promising candidate for detection of obstacles (i.e. pedestrians) on the road is the Cr-AngleA colorspace, that is derived from a YCbCr colorspace, which attempts a quasi 3D representation of the 2D image.

The RGB colorspace does not convey information on the orientation of surfaces in the image, whereas the Cb and Cr channels of the YCbCr colorspace carry this information, which can further be reduced to an Angle image in Cr-AngleA colorspace using:

        Cr = 0.5R - 0.419G - 0.08B + 128
        L = sqrt(R^2+G^2+B^2)
        AngleA = acos(B/L)

As can be seen from the results below, the Cr-AngleA colorspace does make perpendicular, with respect to the camera's frame of reference, objects more 'visible' than the plain r-g-b channels.

#### R-G-B colorspace



![][RGB]

#### Y-Cb-Cr colorspace


![][YCBCR]

#### Cr-AngleA colorspace

![][Angle]
---

---

### Conclusions

The attempted lane segmentation excercise showed the difficulties and peculiarities in the topic, while scratching the surface of Computer Vision in the Self Driving Car's domain.
