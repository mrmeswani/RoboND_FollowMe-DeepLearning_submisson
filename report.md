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
Final parameters are as follows:   
learning_rate = 0.005  
batch_size = 100  
num_epochs = 20  
steps_per_epoch = 400  
validation_steps = 50  
workers = 4  

Above are the parameters I finally chose for my neural network. I tuned each parameter one at a time, initially I started with the following values: learning_rate=0.5, batch_size=2, num_epochs=2, steps_per_epoch=10, validation_steps=50, workers=1. I arrived at the final parameters empirically through experimentation and explained below:   

* For learning rate I initially chose 0.5 and quickly realized that its a bad value as I was get 0% prediction, then i chose a value of 0.005 and i get to an accuracy of 2%. The learning rate determines the rate at which make corrections to the weights during backpropogation. For stability its better to have a small learning rate to avoid large fluctuations in accuracy.     
* num_epochs: I did some quick CPU runs on my laptop with 2 to 4 epoch and I added more epochs I noticed that accuracy went up. The number of epochs determines the number of iterations that the netowork keeps learning. Setting this parameter too high will overfit the model. I found that 20 epochs was good enough and was giving diminishing returns on the loss value. 
* The next parameters to tune is the batch size and steps per epoch. The batch size  determines how many images are sent through at a time and how much memory is available. The steps per epoch determine number of batches of images that are sent in one epoch. I choose the value of these two parameters such that it would equal the size of training set which was 4000 images. To provide the best possible coverage, I wanted to ensure that all the training set is used.   
* Validation_steps is also used to determine how many images are used for validation. 
* workers - I set this to a value of 4 to allow for parallel processing and can help in speeding up training. 

I also played around with number of filters. For my first trials on AWS GPU instance I tried running with smaller number of filters (3,6,9 for the first, second, and third layer respectively)for encode and decode layer and above mentioned hyper parameters and got an accuracy of approx 34%. To further increase the accuracy and meet the required goal i just tweaked the filters in network architecture to 16-32-64 for the first, second, and third layer respectively. With this tweak I was able to get an accuracy 40.7%. 

##### 1x1 convolutions 
The 1x1 convolution uses a kernel of size 1 and stride of 1 and essentially works on a patch size of 1 pixel. We can add 1x1 convolutions as a cheap and easy way to reduce or increase depth and dimensionality and thus can be used to create deeper networks quickly. We use 1x1 convolution between encoder and decoder stage to increase depth and also increase the number of parameters.   

##### Future enhancements 
There is a lot of scope to improve the accuracy of this model. I would have liked to explore different filter depths and epochs and batch sizes to improve 1) the execution time of the training and 2) improved accuracy. As it stands right now even with just 7 layers and relatively small data set, my models take almost 2 hours to train on the GPU. For more real world scenarios where we would be working with very large data sets, the focus would be to both get good accuracy and tractable model training time. 

##### Following other objects:
This model has a lot of scope for improvement. If I had more time, I would have liked to provide a lot more training samples, for instance it does not do well when hero is not present. The input masks to the model suggests that the model will not work well for any other subject matter like a dog or cat since the features would be different and thus I expect nearly 0% accuracy. The model would have to rearchitected to follow multiple targets and would need a whole set of training data. 