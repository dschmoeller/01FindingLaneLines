# **Project 1: Finding Lane Lines on the Road** 



The goals / steps of this project are the following:
* Implementation and parameter tuning of a pipeline that finds lane lines on the road
* Performance verification on test images
* Both lanes should be visualized as solid lines
* Testing the pipeline utilizing two video streams
* Reflection on own work in a written report

---

## Reflection

### 1. Description of the pipeline

The pipeline is implemented as two streams. The reason for this design choice was the fact that the Canny algorithm should be applied on the entire image information (expect color), rather than getting some preprocessed images as input. 

Therefore, the first stream consists of a sequence of both **color** **selection** and region of interest (**ROI**) masking. The color selection component takes the original image as input and applies a certain color threshold in order to mask/filter out all colors except white and yellow. Afterwards ROI cuts out the region where the lanes are expected to occur on the image. 

The second stream transforms the original input image to the **grayspace**, reduces noise utilizing a **Gaussian filter** and applies the **Canny algorithm** in order to detect edges (based on calculating the gradients utilizing the sobel operator). The output of the Canny component is fed in to the **Hough lane** component. This algorithm internally utilizes the Hough space in order to find points/pixels which build ("straight") lane segments. 

Constructing the solid line covers for left and right lane comprises the following steps: 

- Identifying which lane pixels/points corresponds to the left and the right lane. This is done applying another ROI assumption, taking advantage of the fact that right lane points are supposed to occur on the right part of the image. The same argumentation is true for left lane points. (Assuming that the curvature of the lane isn´t too big, which was reasonable for the existing training images) 
- Fit a polynomial model which describes the respective " lane point clouds" best.  
- Use the polynomial model to sample points 
- Extend the existing points with points located at the ROI boundaries (top left , top right, bottom left and bottom right)
- Connecting all respective sample points (including the ROI extended ones) to a solid lane which now covers the entire ROI
- Merging/Adding both solid lines for right and left lane to the initial input image  

The pipeline and its corresponding outputs are visualized in the Pipeline.pdf, which is attached in the repository. (Note that the numbers (1) - (7) are consistent with the comments in the jupyter notebook)
![](C:\Users\Schmoeller\Desktop\Bodi\Job\01 ARGO AI\Udacity Self Driving Car Engineer\01FindingLaneLines\CarND-LaneLines-P1-master\CarND-LaneLines-P1-master\Pipeline.PNG)




### 2. Identify potential shortcomings with your current pipeline

One shortcoming is the fact that the entire approach highly depends on ROI assumptions. If there would be a (white/yellow) car close to the AV (autonomous vehicle) or to any of the lanes, the pipeline would have a way harder lifer properly detecting the lanes. Also, the color thresholds are not perfectly tuned for sure. If there are some deviations of the lighting conditions, some lane pixels or even segments might be missed. In this setting, classifying a pixel as left vs right lane was rather easy to achieve (by applying ROI), leveraging the fact that the lanes actually occur straight on the image. However, if there are some curvatures involved, this pipeline might fail with the current ROI approach. Another huge drawback is the process time. If the pipeline should be used for data labeling reasons, that´s fine. However, in the case of  being applied as an online algorithm, the computation performance needs to be increased i.e. the computation time needs to be decreased. 

   


### 3. Suggest possible improvements to your pipeline

One improvement would be to utilize other color spaces where one can apply more sophisticated and stable thresholds. In general, more challenging driving scenarios would incorporate more traffic, i.e. potentially cars which are close to the AV or close to lane points within the ROI. That´s why another improvement would be to add additional features to the pipeline, which enables the distinction from lane points to certain objects. I can think of gradient directions or object shape matching techniques in order to filter out cars for instance. Dealing with more curved lanes could be mitigated by transforming the camera space into birds eye view and acting there. In general, the code of this entire project is not very clean, yet. One would have to refactor it quite a bit. (I figured that´s not necessarily a major part of this course though. Having that said, for collaboration or production code that´s vital of course) Refactoring the code might also help making the pipeline faster. Potential actions could be reducing the number of image copy commands. Also reducing the number of points which are used to calculate the polynomial and to create the solid line could potentially mitigate the rather long computation time. I can also think of compressing the images in that regard.      


