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




## Training

In order to specialize the SSD network for vehicle and pedestrian detection, I fine-tuned the original network weights using the following dataset.

KITTI dataset: the Pascal VOC dataset that has been used to train the SSD detector already contains vehicles and pedestrians. However, I fine-tuned the SSD for vehicale and pedestrian detection. For KITTI, I divided the original training set of 7500 images into 6000 training and 1500 validation samples.

For fine tunning, I used the following parameters:
- batch size = 32 
- learning rate = 0.001









## Deployment on DragonBoard 410c
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

















