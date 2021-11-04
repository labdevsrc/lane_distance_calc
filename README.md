# Mask R-CNN for White & Yellow Lane Detection and Coords computation

This is an implementation of [Mask R-CNN Tensorflow 2.x](https://github.com/ahmedfgad/Mask-RCNN-TF2) for Yellow, White Lane Distance Calculation. 

![Instance Segmentation Sample](assets/lanecalculation.gif)

The repository includes:
* [VGG VIA](https://www.robots.ox.ac.uk/~vgg/software/via/) Image Annotator
* [Mask RCNN Tensorflow 2.x](https://github.com/ahmedfgad/Mask-RCNN-TF2) 
* Pre-trained weights for MS COCO
* Mask-RCNN Lane Detection Trained weights for Inference Test
* Custom Dataset (Yellow White line) Training  

# Get Started

## Environment
Working environment : [CUDA 10.1](https://developer.nvidia.com/cuda-10.1-download-archive-base) , CuDNN 7.6.5, Tensorflow 2.1.0, Keras 2.2.5.
Below combination known to worked with Tensorflow 1.x : CUDA 10.0 , CuDNN 7.4, Tensorflow 1., Keras 2.2.5.

## Training Preparation
In case we want to add more training image to increase accuracy or change segmentation object (ex : storm drain, arrow, pole ), just follow below step.   
* /images/train : folder for storing training images and annotation files
* /images/val : folder for validation images

* Image Annotation with [VGG VIA](https://www.robots.ox.ac.uk/~vgg/software/via/) tools (local)
![Annotation sample](assets/annotation.png)

* [/images/train/via_region_data.json](/images/train/via_region_data.json) : Exported annotation from VIA VGG Image Annotator
![Get via region data](assets/export%20annotation.png)

* In case object class num changed, inside CustomConfig & CustomDataset class , modify object class :
```bash
class CustomConfig(Config):
    ....
    NUM_CLASSES = 1 + 6  # background +  items


class CustomDataset(utils.Dataset):
    def load_custom(self, dataset_dir, subset):
        self.add_class("name", 1, "grass")
        self.add_class("name", 2, "yellow_line")
        self.add_class("name", 3, "white_line")
        self.add_class("name", 4, "road")
        self.add_class("name", 5, "scrub")
        self.add_class("name", 6, "fence") # must be same with NUM_CLASSES
```

## Run Training 

* [runmodel.py, start_training.bat](samples/main.py),  start_training.bat is batch file that invoke runmodel.py and address train image older 
* on Windows command prompt type below: 
    ```bash
    start_training
    ``` 
    or
    ```bash
    python runmodel.py train --dataset=ROOT_MaskRCNN_folder\images --weights=coco 
    ``` 
*  [/logs](/logs) : folder that stored training weight file result. 

## Inference

* Copy result weight file `/logs/mask_rcnn_name_030.h5` to root directory  
* Configure [samples/main.py](samples/main.py) 
```
ROOT_DIR_STR = "F:/MaskRCNN_folder"  #define root folder here
...
WEIGHTS_PATH = os.path.join(ROOT_DIR, "mask_rcnn_name_030.h5")  # trained weight file from/logs/mask_rcnn_name_030.h5
```



# Step by Step Detection

## 1. Create ROI through MaskRCNN segmentation on White Line and Yellow Line 
Do image segmentation on White and Yellow line. Define the masked area as ROI. Green mask is white line ROI.
Yellow mask is yellow line ROI.
This masked area will be  input to next line detection. 
![](assets/layer1.png)

## 2. Horizontal Geometry and Hough Lines on ROI
Implement HoughLinesP on ROI

![](assets/layer2.png)

## 3.  Horizontal Filtering  , Compute  Euclidian Distance


![](assets/layer3.png)

