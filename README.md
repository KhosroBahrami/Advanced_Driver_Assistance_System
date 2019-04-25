# Advanced Driver Assistance System (ADAS) 

The goal of this project is deployment of sub-systems of Advanced Driver Assistance System (ADAS) including vehicle and pedestrian detection. 

The final product is an embedded system equipped with camera that detect vehicle and pedestrian in real-time. 
For object detection, I used SSD object detection method followed by optimization and fine tunning for detection of new objects.

This project has two main steps: 
- In the first step, I fine-tunned the SSD object detection (based on mobilenet backbone network) for new 
a dataset.
- In the second step, I deploy the inference part based on mobilenet backbone on embedded system (DragonBoard 410c) using C++ to have real-time performance. 


<p align="center">
  <img  src="images/adas_pedestrian.jpg" alt="alt text" width="50%" height="50%" title="Pedestrain detection">
</p>


# 1.Training 

## Dataset

In order to specialize the SSD network for vehicle and pedestrian detection, I fine-tuned the original network weights using the following dataset.

KITTI dataset: the Pascal VOC dataset that has been used to train the SSD detector already contains vehicles and pedestrians. However, I fine-tuned the SSD for vehicale and pedestrian detection. For KITTI, I divided the original training set of 7500 images into 6000 training and 1500 validation samples.

## Backbone Network
I used MobilenetV1 as the backbone network. The input to the network is an image with size of 300*300. The output of the network is 6 features map with 8732 bounding box calculated as follows:  

- Conv_5_pointwise: 38×38×4 = 5776 boxes (4 boxes for each location)
- Conv_11_pointwise: 19×19×6 = 2166 boxes (6 boxes for each location)
- AuxL_12_pointwise: 10×10×6 = 600 boxes (6 boxes for each location)
- AuxL_13_pointwise: 5×5×6 = 150 boxes (6 boxes for each location)
- AuxL_14_pointwise: 3×3×4 = 36 boxes (4 boxes for each location)
- AuxL_15_pointwise: 1×1×4 = 4 boxes (4 boxes for each location)

If we sum them up, we got 5776 + 2166 + 600 + 150 + 36 +4 = 8732 boxes in total for SSD with MobileNetv1.


| Layer             | Type/Stride  | Filter Size        | Feature map    |# of Parameters | # of BBoxes | 
| :---:             | :---:        |  :---:             |  :---:          | :---:          | :---:            | 
| Conv_1            | Conv / s2    |  3 * 3 * 3 * 32    | 150 * 150 * 32  |  -             |  -               |
| Conv_2_depthwise  | Conv dw / s1 |  3 * 3 * 32 dw     | 150 * 150 * 64  |  -             |  -               |
| Conv_2_pointwise  | Conv / s1    |  1 * 1 * 32 * 64   | 150 * 150 * 64  |  -             |  -               |
| Conv_3_depthwise  | Conv dw / s2 |  3 * 3 * 64 dw     | 75 * 75 * 128   |  -             |  -               |
| Conv_3_pointwise  | Conv / s1    |  1 * 1 * 64 * 128  | 75 * 75 * 128   |  -             |  -               |
| Conv_4_depthwise  | Conv dw / s1 |  3 * 3 * 128 dw    | 75 * 75 * 128   |  -             |  -               |
| Conv_4_pointwise  | Conv / s1    |  1 * 1 * 128 * 128 | 75 * 75 * 128   |  -             |  -               |
| Conv_5_depthwise  | Conv dw / s2 |  3 * 3 * 128 dw    | 38 * 38 * 128   |  -             |  -               |
| Conv_5_pointwise  | Conv / s1    |  1 * 1 * 128 * 256 | 38 * 38 * 256   |  -             |  38 * 38 * 4     |
| Conv_6_depthwise  | Conv dw / s1 |  3 * 3 * 256 dw    | 38 * 38 * 256   |  -             |  -               |
| Conv_6_pointwise  | Conv / s1    |  1 * 1 * 256 * 256 | 38 * 38 * 256   |  -             |  -               |
| Conv_7_depthwise  | Conv dw / s2 |  3 * 3 * 256 dw    | 19 * 19 * 256   |  -             |  -               |
| Conv_7_pointwise  | Conv / s1    |  1 * 1 * 256 * 512 | 19 * 19 * 512   |  -             |  -               |
| Conv_8_depthwise  | Conv dw / s1 |  3 * 3 * 512 dw    | 19 * 19 * 512   |  -             |  -               |
| Conv_8_pointwise  | Conv / s1    |  1 * 1 * 512 * 512 | 19 * 19 * 512   |  -             |  -               |
| Conv_9_depthwise  | Conv dw / s1 |  3 * 3 * 512 dw    | 19 * 19 * 512   |  -             |  -               |
| Conv_9_pointwise  | Conv / s1    |  1 * 1 * 512 * 512 | 19 * 19 * 512   |  -             |  -               |
| Conv_10_depthwise | Conv dw / s1 |  3 * 3 * 512 dw    | 19 * 19 * 512   |  -             |  -               |
| Conv_10_pointwise | Conv / s1    |  1 * 1 * 512 * 512 | 19 * 19 * 512   |  -             |  -               |
| Conv_11_depthwise | Conv dw / s1 |  3 * 3 * 512 dw    | 19 * 19 * 512   |  -             |  -               |
| Conv_11_pointwise | Conv / s1    |  1 * 1 * 512 * 512 | 19 * 19 * 512   |  -             |  19 * 19 *6      |
| Conv_12_depthwise | Conv dw / s1 |  3 * 3 * 512 dw    | 19 * 19 * 512   |  -             |  -               |
| Conv_12_pointwise | Conv / s1    |  1 * 1 * 512 * 512 | 19 * 19 * 512   |  -             |  -               |
| AuxL_12_depthwise  | Conv dw / s2 |  3 * 3 * 512 dw    | 10 * 10 * 512   |  -             |  -              |
| AuxL_12_pointwise  | Conv / s1    |  1 * 1 * 512 * 512 | 10 * 10 * 512   |  -             |  10 * 10 * 6    |
| AuxL_13_depthwise  | Conv dw / s2 |  3 * 3 * 512 dw    | 5 * 5 * 512     |  -             |  -              |
| AuxL_13_pointwise  | Conv / s1    |  1 * 1 * 512 * 512 | 5 * 5 * 512     |  -             |  5 * 5 * 6      |
| AuxL_14_depthwise  | Conv dw / s2 |  3 * 3 * 512 dw    | 3 * 3 * 512     |  -             |  -              |
| AuxL_14_pointwise  | Conv / s1    |  1 * 1 * 512 * 1024 | 3 * 3 * 1024   |  -             |  3 * 3 * 4      |
| AuxL_15_depthwise  | Conv dw / s2 |  3 * 3 * 1024 dw    | 1 * 1 * 1024   |  -             |  -              |
| AuxL_15_pointwise  | Conv / s1    |  1 * 1 * 1024 * 1024 | 1 * 1 * 1024  |  -             |  1 * 1 * 4      |










## Number of Prior Boxes


## MultiBox Detection


## Hard Negative Mining (HNM)



## Image Augmentation


## Loss function


## Non Maxmimum Supression (NMS)



## Fine-tuning

For fine tunning, I used the following parameters:
- batch size = 32 
- learning rate = 0.001




# 2.Deployment on DragonBoard 410c
To deploy SSD object detection on the DragonBoard 410c, I did/prepared the following steps:


### Hardware requirements:
- Using a DragonBoard 410c, USB camera, keyboard, mouse, HDMI monitor.
- Using a 16GB MicroSD Card
- Partition the 16GB MicroSD card into 4GB and 12GB.
- The 4GB of SD card was used to increase the memory (DragonBoard 410c has 1GB memory).
- The 12GB of SD card was used to increase the storage (DragonBoard 410c has 8GB storage).


### Software requirements:
- Installation of Debian linux
- Installation of Bazel (to compile tensorflow)
- Installation of Tensorflow 
- Installation of OpenCV
- The object detection code


### Pipeline of our system for vehicle and pedestrain detection
The pipeline of our system includes the following steps:

- Capture the live video from camera 
- Extract the images/frames from the input video 
- Resize every image/frame into 300*300 pixels (the image size used for the SSD network)
- Run the SSD network on the resized image/frame
- Apply the Non-Maximum Suppression algorithm to get rid of multiple detections of a single object
- Display the frames with the bounding box of detected objects.

















