#Traffic Sign Classifier
*Using ConvNets*

[//]: # (Image References)

[histogram]: ./images/hist.png "Histogram"
[classes]: ./images/classes.png "Classes"
[web-images]: ./images/web-images.png "web-images"
[image4]: ./images/placeholder.png "Traffic Sign 1"
[image5]: ./images/placeholder.png "Traffic Sign 2"
[image6]: ./images/placeholder.png "Traffic Sign 3"
[image7]: ./images/placeholder.png "Traffic Sign 4"
[image8]: ./images/placeholder.png "Traffic Sign 5"

### Data Set Summary & Exploration
Number of training examples = 34799
Number of testing examples = 12630
Image data shape = (32, 32, 3)
Number of classes = 43

![Histogram] [histogram]

![Classes Summary] [classes]

###The Approach & Findings
There are numerous architectures that are possible with the design of the ConvNet so I've used, as suggested in the instructions, the LeNet (0.887 validation accuracy with all relu activations) as the starting point.
Several changes were tested and the following is what I've learned:

- **Activation functions:** The activations used and the order they were used in had significant difference in the performance of the classifier.
The best combination for the LeNet architecture turned out to be alternating tanh (starting) and relu activations, granting a boost of ~3% with the default setup (to 0.943).

- **Number of layers:** Adding another Conv layer increased the validation accuracy again to about 0.953. That makes the 3 layers of Convolution followed by the classifier comprising of 3 fully connected layers and a softmax/cross entropy loss calculation.

- **Filter sizes:** Originally I used the default Lenet filter sizes however, after reading through papers by Yan LeCun and Ciresan, I changed them to 7x7 filter, as that is what proved more successful in their experiments.

- **Flow:** Based on the Yan LeCun paper I tried the feed forward approach where the output of the first & second Conv layer were flattened and fed into the classifier. This drastically increased the number of parametres and the run time of each epoch.

- **Preprocess:** I tried the two main flows explained above (feed forward and straight) with grayscaling, grayscaling-histogram equilization, YUV-histogram equilization and normalization (minus 128 and then divide by 128) however, none of these offered significantly better validation accuracy so I sought to use the images as provided.
My understanding of why this is the case is that, the raw pictures offer more variation leading to better feature extraction and reducing overfitting.

- **Distortion:** In order to expand the dataset to gain more training instances (as advised in the project instructions and also in Ciresan's paper), I used the distort function to rotate and translate the images randomly. This helped the accuracy.

- **Learning Rate Decay:** After noticing the validation accuracy diverge over epochs, I suspected that the learning rate was too high. However, since the starting accuracy was good and learning was clearly taking place initially with the 0.001 rate value, I decided to decay the learning rate.
The number of steps that tuned learning the best was 500.

- **Dropout:** I used dropout technique initially but found that the network didnt train at all. I suspect this was due to the presence of relu activations which also essentially suppress some outputs and hence there were too few non-zeros values for the classifier to effectively use.

- **Pooling:** While trying out different configurations I removed the pooling layers and noticed a significant improvement in the network output. So my final architecture had no intermediate pooling layers (also suggested in Andrej Karpathy's lectures), except the last Conv layer, used so that the processing became easier.
##The Final Model
Unfortunately, there isn't much time to test all combinations of the techniques, activations, layers numbers, filter sizes etc. to tune the best network.
Using the tuning, tests and reasons above, I decided that the best network configuration for the objective is as summarized below:


| Layer         		|     Description	        					| 
|:---------------------:|:---------------------------------------------:| 
| Input (w/ distortion)	| 32x32x3 RGB image   							| 
| Convolution 7x7x10    | 1x1 stride, valid padding, outputs 26x26x10 	|
| tanh					|												|
| Convolution 7x7x16   	| 1x1 stride,  outputs 20x20x16 				|
| tanh  			    | etc.      									|
| Convolution 5x5x32	| 1x1 stride,  outputs 16x16x32        			|
| tanh					|  	        									|
| Max pooling 2x2		| 2x2 stride, output 8x8x32						|
| flatten				| output 2048									|
| Fully connected		| output 400									|
| Fully connected		| output 100									|
| Fully connected 		| output 43 (logits)						 	|
| Softmax				| Classification probabilities					|

###Results
The final network achieved 0.973 validation accuracy after 75 epochs of training.
The accuracy on test set was 0.965.

###Testing on web images
I gathered some traffic sign images from Google StreetView over some roads in Germany. This test set had a few images that the network wasn't trained on however, it was interesting to see the softmax probabilities for them.
![web-images] [web-images]