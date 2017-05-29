# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.
I have also create a set of images that display step by step how pipeline works.
My pipeline consisted out of 7 steps:
#### 1. convert image to grey from BGR
![White Car Lane Switch in Gray](/test_images_final/whiteCarLaneSwitch_gray.jpg)
by using provided `def grayscale` function, inside I switched to use `cv2.COLOR_BGR2GRAY` because I am using `cv2.imread()`
#### 2. remove noise by applying Gaussian Blur with kernel size `5`
![White Car Lane Switch Blur Gray](/test_images_final/whiteCarLaneSwitch_blur_gray.jpg)
    I used kernel size `5` from the experiance in lecture where we learned how to use Gaussian Blur, it worked fine on this project
#### 3. apply Canny Edges aplgorythm
![Canny](/test_images_final/whiteCarLaneSwitch_canny.jpg)
    I defined parameters for Canny edges and applied it to the blured grey picture of the image, I picked `low_threshold = 50` and `high_threshold = 150` because we used it during lecture and quiz and it worked on the current project
#### 4. define a four sides of polygon to mask noise
![Region of interest](/test_images_final/whiteCarLaneSwitch_region_of_interest.jpg)
    I needed to create a polygon that would cover the area of lane lines and remove the noise of other objects of similar color for video 1 and 2 I went with `[(20,imshape[0]),(450,325), (510, 325), (imshape[1]-20,imshape[0])]` where apex defined by two coordinates of `(450,325), (510, 325)`
    it does not work for the challenge video, I had to change it to `[(20,imshape[0]),(610,430), (700, 430), (imshape[1],imshape[0])]`
    where the apex moved to `(610,430), (700, 430)`, this helped a little to maintain lines mapping lanes, because this video has shadows and road curves under different angel than in other two videos, my algorythm performed not as well as on other two vides, I have to continue working on it.
#### 5. use Hough algorythm to show lines
![Hough Lines](/test_images_final/whiteCarLaneSwitch_hough_lines.jpg)
    On this spet I had to provide input params to Hough algorythm. I descided to use 
    ```
    rho = 2                        # distance resolution in pixels of the Hough grid
    theta = np.pi/180              # angular resolution in radians of the Hough grid
    threshold = 15                 # minimum number of votes (intersections in Hough grid cell)
    min_line_len = 40              # minimum number of pixels making up a line
    max_line_gap = 20              # maximum gap in pixels between connectable line segments
    ```
    I went with `rho = 2` because it worked and we picked it during lectures and quiz. Same with `theta`. As for `threshold = 15`, `min_line_len = 40` and `max_line_gap = 20` I had to experiment and ended up testing best combination of values above.
#### 6. apply logystic regression to draw lines 
    This part was quite chellenging, I had to come up with a solution to loop through lines calculated by Hough algorythm, identify empty lines. 
    In order to draw a single line on the left and right lanes, I modified the draw_lines() function.
    I used slope equation (y2-y1)/(x2-x1). 
    If line had a positive slope I added it to right set of `left_slopes` and `left_line_pts`, if it was negative I appended it to right selt of `right_slopes` and `right_line_pts`. Then I used `max` function to look for most frequent slope. 
    When I have identified left and right most frequent slope I used it to create clean version of left and right lines and points. 
    At the end I used `cv2.fitLine` openCV function to draw lines.
#### 7. use weighted image provided function to overlay hough lanes and original image. I didn't change input params, after some experimenting, I descided to leave them as is.
![Weighted](/test_images_final/whiteCarLaneSwitch_weighted.jpg)

It helped a lot to have a set of test images to test my code before running it on the video, it was faster and more efficient.
Here is a list of processed images and it's originals:
![solid white curve](/test_images/solidWhiteCurve.jpg) 
![solid white curve](/test_images_output/solidWhiteCurve.jpg)
![solid white right](/test_images/solidWhiteRight.jpg) 
![solid white right](/test_images_output/solidWhiteRight.jpg)
![solid yellow curve 1](/test_images/solidYellowCurve.jpg)
![solid yellow curve 1](/test_images_output/solidYellowCurve.jpg)
![solid yellow curve 2](/test_images/solidYellowCurve2.jpg)
![solid yellow curve 2](/test_images_output/solidYellowCurve2.jpg)
![solid yellow left](/test_images/solidYellowLeft.jpg)
![solid yellow left](/test_images_output/solidYellowLeft.jpg)
![white car lane switch](/test_images/whiteCarLaneSwitch.jpg)
![white car lane switch](/test_images_output/whiteCarLaneSwitch.jpg)


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
