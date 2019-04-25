# Advanced Driver Assistance System (ADAS) 

The goal of this project is deployment of sub-systems of Advanced Driver Assistance System (ADAS) including vehicle and pedestrian detection. 

The final product is an embedded system equipped with camera that detect vehicle and pedestrian in real-time. 
For object detection, I used SSD object detection method followed by optimization and fine tunning for detection of new objects.

This project has two main steps: 
In the first step, I fine tunned the SSD object detection based on mobilenet backbone network for new 
dataset.
In the second step, I deploy the inference part based on mobilenet backbone on embedded system (Dragon board 410C) using C++ to have real-time performance. 


<p align="center">
  <img  src="images/adas_pedestrian.jpg" alt="alt text" width="50%" height="50%" title="Pedestrain detection">
</p>




## Training

In order to specialize the SSD network for vehicle and pedestrian detection, I fine-tuned the original network weights using the following dataset.

KITTI dataset: the Pascal VOC dataset that has been used to train the SSD detector already contains vehicles and pedestrians. However, I fine-tuned the SSD for vehicale and pedestrian detection. For KITTI, I divided the original training set of 7500 images into 6000 training and 1500 validation samples.




For fine tunning, I used the following parameters:
batch size = 32 
learning rate = 0.001


## Inference


## Pipeline of our system
The pipeline of our system includes the following steps:

- Extract the images/frames from the input video 
- Resize every image/frame into 300*300 pixels (the image size used for the SSD network)
- Run the SSD network on the resized image/frame
- Apply the Non-Maximum Suppression algorithm to get rid of multiple detections of a single object




## Deployment on DragonBoard 410C






