## Project: Perception Pick & Place

---




[//]: # (Image References)

[p0]: ./Images/sim_screenshot.png
[nw]: ./Images/nwarch.png

## Deep Learning Project ##

The goal of this project, is to train a deep neural network to identify and track a target in simulation. In real world, “follow me” applications like this are key to many fields of robotics and the very same techniques applied here could be extended to scenarios like advanced cruise control in autonomous vehicles or human-robot collaboration in industry.  
![alt][p0]

## Requirements
### Write up requirements
* Include N/W architecture diagram for reference of the reviewer.
* The write-up conveys an understanding of the network architecture. Explain the role of each layer. Justify current n/w architecture and why its better than other choices using some factual data.  
* The student explains their neural network parameters including the values selected and how these values were obtained (i.e. how was hyper tuning performed? Brute force, etc.) Hyper parameters include, but are not limited to: Epoch, Learning Rate, Batch Size. All configurable parameters should be explicitly stated and justified.
* The student demonstrates a clear understanding of 1 by 1 convolutions and where/when/how it should be used.
* The student demonstrates a clear understanding of a fully connected layer and where/when/how it should be used.
* The student is able to identify the use of various reasons for encoding / decoding images, when it should be used, why it is useful, and any problems that may arise.
* The student is able to clearly articulate whether this model and data would work well for following another object (dog, cat, car, etc.) instead of a human and if not, what changes would be required.

### Model Requirements
* The file is in the correct format (.h5) and runs without errors.
* The neural network should obtain an accuracy greater than or equal to 40% (0.40) using the Intersection over Union (IoU) metric.

### Submission Materials
* Model_training.ipynb notebook.
* A HTML version of your model_training.ipynb notebook.
* Writeup report (This file)
* Model and weights file in the .h5 file format

 

## Report 
![alt][nw]  

##### Network Architecture:
The network I desgined was a 3 layer encoder followed by a 1x1 convolution followed by a 3 layer decoder and is shown in figure above. Each encoder layer picks up more and more complex patterns. By experimentation 2-3 encoder layers were sufficient for this project. The encoder layers compress the height and width of the image and increase the depth (number of outputs). To do so I chose a stride of 2 to trade off between accuracy and speed (more stride would train faster but lose more fidelity). I chose 3 encode layer with increasind depths of 16, 32, and 64 for the first, second, and third layer respectively. Then next I attach a 1x1 convolution layer usign a kernel of size 1 and and stride of 1. Once the encoder layers have extracted the feature information from the image, we must upsample the results to get the original image size and produce an output image that has semantically segmented each pixel. To do, the output of the 1x1 convolution is passed to the first decode layer. The first layer decodes and upsamples the 1x1 convolution and I also pass inputs from encode layer 2 as skip connections. In the second decoder I upsample the result of the first decode layer and pass in the input of the first encode layer as skip connections. In the final decode layer I upsample the output of second decode layer and pass the original inputs as skip connections. 

##### Hyper Parameter and network tuning: 
I first tried a quick run with some default settings of hyper parameters on my laptop using a couple of epochs to gauge the impact and senstivity fo prediction accuracy to each of them. I noticed that batch size and steps_per_epoch seemed to play an important role and I ensured that the product of the two was equal to the number of training samples. For my first trials on AWS GPU instance I tried running with smaller number of filters (3,6,9 for the first, second, and third layer respectively)for encode and decode layer and above mentioned hyper parameters and got an accuracy of approx 34%. To further increase the accuracy and meet the required goal i just tweaked the filters in network architecture to 16-32-64 for the first, second, and third layer respectively. With this tweak I was able to get an accuracy 40.7%. 

##### Following other objects:
This model has a lot of scope for improvement. If I had more time, I would have liked to provide a lot more training samples, for instance it does not do well when hero is not present. The input masks to the model suggests that the model will not work well for any other subject matter like a dog or cat since the features would be different and thus I expect nearly 0% accuracy. The model would have to rearchitected to follow multiple targets and would need a whole set of training data. 