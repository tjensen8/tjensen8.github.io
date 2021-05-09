---
layout: post
title: Hard Hat Detection in Images
---


Safety & Visual Networks
> A summary of my research to develope a model to detect if hardhats are being worn. The goal of this is to improve safety and reduce injuries in industrial work areas using machine learning.

## Introduction

Research has shown that traumatic brain injuries (TBIs) in the construction industry are of significant concern (Tiesman, Konda, & Bell, 2011). In fact, nearly 7,300 TBIs were recorded between 2003 and 2008 in the US (Tiesman, Konda, & Bell, 2011). Finding ways to ensure everyone wears hardhats (where required) would be an effective method to combat TBIs. In addition to communicating safety measures, electronic methods can be utilized to help ensure that hardhats are used. Potential solutions may be able to utilize images, cameras, or videos to identify hardhat usage and correct where 
needed. Neural networks can be utilized to increase hardhat detection in areas through a variety of architectures based on Convolutional Neural Networks (CNN) and object detection architectures. In this experiment, we will be comparing CNNs, and the pre-built object detection model based on the FasterRCNN-Inception-V2 model (Google, Inc, 2017). The object detection model was implemented using the same strategy as found in EdjeElectronics tutorial on GitHub (EdjeElectronics, 2019). In addition to comparing the model structure itself, we will also review observations about training the model, lessons learned, and judge the generic of the models. 

## Brief Review of Literature

Safety is of chief concern in many industries, and there have been experiments done previously. The experiments were performed to evaluate the applicability of neural networks for hard hat detection. Some of the studies available thus far are “Automatic detection of hardhats worn by construction personnel: A deep learning approach and benchmark dataset” by Wu Et al. and “Detecting non-hardhat use by a deep learning method from far-field surveillance videos” by Qi Fang Et al. Both papers utilize existing methods with a reduced VGG16 model and the Faster R-CNN (for real time object detection) (Fang, et al., 2018) (Wu, Cai, Chen, Wang, & Wang, 2019). Both models were used as inspiration while developing a deep neural network for hardhat object detection.

## Design & Methods

### Data Used

A public domain dataset through Harvard’s Dataverse was utilized during testing. The dataset contains train/test split images of people with hardhats and without hard hats (Liangbin, 2019) The dataset contains approximately 7,000 images and will be helpful for testing and training the model. Some of the images vary in depth and number of hardhats in the image. The image annotations are provided in .xml format and contain bound boxes of labeled classes “head” and “hardhat”. A “head” is a person’s head without a hardhat. A “hardhat” is a person’s head with a hardhat on. Example images from the training corpus are in the appendix in Figure 1.

![Figure 1](/images/hardhat-figure1.PNG "Figure 1")

### Preprocessing
During the testing process, there were two distinct types of inputs and tests of the model. The first test is a binary test to identify if the image contains a hardhat or not. The second test is to use bound box identification to drive model learning. To run the tests, two different preprocessing methods were utilized. For the binary test, images were converted into tensors of shape (height, width, channels). The dataset was also balanced so there were equal amounts of images with and without a hardhat. For the bound box model, the .xml files that contain the bound boxes are converted into .csv that contains the image, class, and location of the bound box in each image. The .csv files are fed into the model in order to identify and train on each bound box. The images are fed into the model and preprocessed as a part of the Faster-RCNN-Inception-V2 model (Google, Inc, 2017).

### Network Design
There are several networks tested on this dataset in order to capture the effect of varying structure, accuracy, and computational time. It is desirable to capture a lack of a hardhat in an image, so we will be focusing on the models that are able to capture the most images without a hardhat. The CNN networks ran for 10 epochs with a batch size of 3. Models were compiled with gradient decent and sparse categorical cross entropy loss. Precision, recall, and confusion matrices were used for the basis of comparison. The activation functions of the hidden layers are Relu with a Sigmoid activation function to predict the probability of a hardhat. For the Faster-RCNN-Inception-V2 model, precision and recall for each class was utilized and the model was trained for approximately 22,000 steps. This amount was chosen as previous work with the model was shown to be successfully trained at this step quantity (EdjeElectronics, 2019). However, this may lead to overfitting in this use case. In addition, the tests were limited by the capability of provided hardware and time. The structures of what models are utilized are in Table 1, below. For full performance outputs, refer to the outputs included with this submission.



## Performance

Utilizing the model structures in the previous section, I was able to recall about 90% of the heads in the test dataset. However, overfitting was a concern. Almost all models fit better on the training data than the test data. Dropout of 50% was utilized during training to remedy, with varying results. When dropout is applied, the models are typically not able to capture as many of the images containing heads. However, as more layers are added, the capture rate of heads in the test dataset increased. It is interesting to note that while most models were able to classify some of the images that contained a head, the models with more than 2 layers were able typically able to capture more of the head instances. However, when one looks at a sample of activation heatmaps from each of the models, the models are tending to capture the changes in the environment from images to images (like an industrial site vs an office), rather than the hardhats themselves. A sample of the outputs from the best convolutional network is in Figure 2, below. However, the model trained with Faster-RCNN-Inception-V2 model was trained using guidance from a bound box outline of the heads and hardhats, so it has the benefit of being told specifically what size, color, and position of hardhats are possible. A sample of outputs from the Faster-RCNN-Inception-V2 model is in Figure 3 below. The Convolutional network models were able to capture from 70% to 95% of the heads in the test set. While the average precision of the Faster-RCNN-Inception-V2 model was about to capture 84% of the heads and 57.5% of hardhats in the images, with an average mean precision of 71%. CNNs also took only minutes to train, while the Faster-RCNN model took hours. A summary of performance is in Table 1.

![Table 1](/images/hardhat-table1.PNG "Table 1")


![Figure 2](/images/hardhat-figure2.PNG "Figure 2")

**Figure 3.** Sample output from object detection model Faster-RCNN-Inception-V2

![Figure 3](/images/hardhat-figure3.PNG "Figure 3")

## Additional Experiences

During training, much time was spent working to get the models past predicting only the most popular class (hardhats) at an accuracy of 91.8%. Several different architectures were attempted, without success. It was realized that batch size was playing a significant effect on predictions and batch size was set as 123. When reduced to 3 and the training data balanced, training improved dramatically, and the models no longer predicted “hardhat” for all images.

## Conclusion

When trained, all models were able to achieve various degrees of head detection, with the best model able to capture 95% of all head images. While these models looked good with these metrics, I found that the filters and layers tended to capture the environment of the photos as indicative of people wearing hardhats, not the hardhats themselves. As an alternative, the object detection architecture was used and obtained decent results at 84% mean precision for head images. However, the experiment was limited by equipment and time. Further training could yield improved results.

## Implementation & Reccomendation

Although the object detection model took significantly longer to train and had increased hardware requirements, I theorize that it would perform better than a simple CNN in a real-world industrial environment. For example, if a camera were placed in an industrial environment with the best performing CNN on it, the model’s performance would decrease substantially. This is because the environment is indicative of the existence of hard hats, not that a hard hat object exists. However, the object detection model would be looking for the hardhat or head specifically and would not give as much consideration to the environment during prediction.

## References

EdjeElectronics. (2019, June 22). How To Train an Object Detection Classifier for Multiple Objects Using TensorFlow (GPU) on Windows 10. Retrieved from Github: https://github.com/EdjeElectronics/TensorFlow-Object-Detection-API-Tutorial-Train-Multiple Objects-Windows-10 

Fang, Q., Heng, L., Luo, X., Ding, L., Luo, H., Rose, T., & An, W. (2018). Detecting non-hardhat-use by a deep learning method from far-field surveillance videos. 

Automation in Construction, 1-9. doi:https://doi.org/10.1016/j.autcon.2017.09.018 Google, Inc. (2017). 

Tensorflow Object Detection API. Retrieved from Github: https://github.com/tensorflow/models/tree/master/research/object_detection 

Lecun, Y., Bottou, L., Bengio, Y., & Haffner, P. (1998). Gradient-based learning applied to document recognition. Proceedings of the IEEE, 86(11). doi:10.1109/5.726791 

Liang, T., Yang, G., Lv, F., Zhang, J., Cao, Z., & Li, Q. (2018). Convolutional Neural Networks for Text Classification with Multi-size Convolution and Multi-type Pooling. Database Systems for Advanced Applications, 3-12. Liangbin, X. (2019). 

Hardhat. doi:https://doi.org/10.7910/DVN/7CBGOS Tiesman, H. M., Konda, S., & Bell, J. (2011). 

The Epidemiology of Fatal Occupational Traumatic Brain Injury in the U.S. American Journal of Preventive Medicine, 61-67. doi: https://doi.org/10.1016/j.amepre.2011.03.007 

Wu, J., Cai, N., Chen, W., Wang, H., & Wang, G. (2019). Automatic detection of hardhats worn by construction personnel: A deep. Automation in Construction.
