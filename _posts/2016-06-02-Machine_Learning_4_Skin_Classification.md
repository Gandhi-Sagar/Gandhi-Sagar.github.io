---
title: 'Machine Learning 4 : Skin Color Classification'
author: Sagar Gandhi
layout: post
permalink: /Machine-Learning-4-Classification
categories:
  - Posts
---
<!--excerpt.start-->
Let us step ahead for some real world problem solving.
Some History:
Few days ago, while working on Gesture Recognition, one of the problems I faced was related to segmentation. I was trying to recognize hand gestures. Started off with classification of simple gestures - 1 finger, 2 finger .. 5 fingers, we created own dataset of images. This went off very smoothly, and accuracy of 85% plus was achieved. Then came a time to implement this in real time and I was stumped by a problem of segmentation. 
In real life scenario, with varying lighting conditions, segmenting human hand from background is a quite a challenging problem. 
There are various methods to do this. Our problem statement for this blog post is going to be one of the methods - Segmenting skin color from the given image.
<center><img src="/images/ml_4_1.jpg"></center>
**Problem Statement** <!--excerpt.end-->

What if we classify image pixel into "skin_color" vs "Non_Skin_color". This would make a life of an image processing programmer a little easier. Then in a constrained environment, we would be able to segment out the hand (well, rather a skin color, which includes forearms, face, etc. as well).

What is an image, well digitally? Image is made up if rectangles, each cell in a rectangle has a set of 3 values, one for blue, green and red and this rectangle is known as pixel. So in order to represent an image of say 32X32 pixels, there are 32X32X3 integer values used.

There is dataset on UCI repository - [Skin segmentation data set](https://archive.ics.uci.edu/ml/datasets/Skin+Segmentation).

*“Dataset is collected by randomly sampling B,G and R values from face images of various age groups (young, middle, and old), race groups (white, black, and Asian), and genders obtained from FERET database and PAL database. Total learning sample size is 245057; out of which 50859 are the skin samples and 194198 are non-skin samples.”*

So for our dataset, number of attributes, aka features, are going to be 3  
1. blue component value of a pixel
2. green component value of a pixel
3. red component value of a pixel

Outcome is going to be answer to a question "whether combination of r, g, b values represents a skin color?" Yes or No.

With respect to Machine Learning terminologies, this is Supervised Learning. Given the labeled examples, we design a rule. This rule can be simple or complex depending on the dataset and methodologies. This is similar to solving other classification problems - from "Spam or Not-Spam" email classification problem to Complex Object recognition problem in Image Processing.

**Visualization**

So, just like we last time, let us march ahead to do a Data Visualization.

Now, for this problem, even though number of samples is too high, number of features is pretty less. So we can visualize each feature individually.

      {%highlight python%}
## Script - 1
from matplotlib import pyplot as plt
from matplotlib import patches as mpatches
import numpy as np
import scipy as sp

data = sp.genfromtxt("skin_color_binary.dat", delimiter = "\t")
sp.random.shuffle(data)
data = np.array(data)
print("Last 10 rows of Data: \n" + str(data[-10:]))
print(data.shape)

Y = data[:, (len(data[0]) - 1)] #equivalent to data[:, 3], we just made it generic
print("Last 10 labels: \n" + str(Y[-10:]))

X = data[:, range(0, 3)]
print("last 10 attributes: \n" + str(X[-10:]))

def plotter(c, index, plt):
    label_colors = ["yellow" if value == 1 else "red" for value in Y[:500]]
    c.scatter(sp.arange(500), X[:500, index], c = label_colors )
    if index == 0:
    	c.set_xlabel("blue")
    elif index == 1:
    	c.set_xlabel("green")
    else:
    	c.set_xlabel("red")
    c.set_ylabel("pixel value")
    skin_patch = mpatches.Patch(color = "yellow", label = "skin")
    non_skin_patch = mpatches.Patch(color = "red", label = "non-skin")
    plt.legend(handles = [skin_patch, non_skin_patch], loc = "upper left")

fig = plt.figure()
r = fig.add_subplot(131)
plotter(r, 0, plt)

g = fig.add_subplot(132)
plotter(g, 1, plt)

b = fig.add_subplot(133)
plotter(b, 2, plt)
plt.show()
      {%endhighlight%}

<center><img src="/images/ml_4_2.png"></center>

Now, we have to come up with a model that separates skin colors from non-skin colors. From the graph, one can evidently say that if red component is less than 100, it has to be a non-skin color. Let us first prove a point.

      {%highlight python%}
## script - 2
skin_data = data[data[ : , 3] == 1]
print("min of skin colors: " + str(skin_data[ : , 2].min()))
print("max of skin colors: " + str(skin_data[ : , 2].max()))

Output:
min of skin colors: 106.0
max of skin colors: 255.0
      {%endhighlight%}

Though this can be termed as one of the possible models, we can agree - from our visualization of our features that coming up with a single threshold is not possible. This is because our threshold value actually forms a line which is parallel to either of the axes and no horizontal or vertical line can separate the two classes. 

In the last post we have witnessed the notion of training data and testing data, in that, we want to estimate ability of our model to perform a task T for the new set of instances. Last time, this task T was of a prediction, in this particular problem it is of a classification of skin color from non-skin color. 

**Training and Testing Split**

There are various methods for splitting the data into training and testing. You might have heard about the terminologies such as "training accuracy" and "testing accuracy". Training accuracy is simply the one measured on the training data and testing accuracy is an accuracy achieved on the testing data. It is not at all unusual to experience the fact that testing score is less than training score. This fact will become more and more important as we proceed to use more complex data and classifiers. In fact, the scenario that 100% accuracy on the training data and random guess kind of accuracy on the testing data is witnessed at least once by every machine learning programmer.  

*One important note is we should always report the testing accuracy; score on the collection of exemplars that were not used for training.*

It is usual practice to randomly shuffle the data and perform split as training and testing as 80%-20% or 2/3rd-1/3rd, etc. One natural glitch is, obviously, what is ideal split? If we use more equal split, we would be missing a lot of data in training phase and if we use more for training, we are simply testing and verifying our model on very less data. Ideally, we would want to use all the data for training as well as testing. Somewhat similar case can be achieved by a technique called Cross Validation.  

Let us start from the extreme. Let us imagine a hypothetical case that, we have 100 samples. For the sake of using all the data for training and testing, we can do the following. Train the model using first 99 samples and test it on the last one, again train the model using 1-98 and 100, and test is on 99th sample, and so on. This way, we would be using all the data for training and testing. Obviously enough, the cost is 100 times in this case, in that, I have to train my model n times, n being the size of the data. BTW, this is also known as leave-one-out splitting, and can be useful sometimes.

Most of the benefits of leave-one-out can be pertained at a fraction of a cost using x-fold cross validation; x being a small number, say five. We break the data into five groups, i.e. five folds. Then we learn five models, leaving one fold out each time. It is similar to leave-one-out, but now we will be leaving out 20% of the data. We test our model of the left out data and average out the results. 

Of course, there comes an in-built complexity of balance. Each fold has to have (almost) equal share from all the classes. For e.g. in one of the fold, if all the examples belong to the same class, the model will not be a true representative. 

**Classification**

Coming back to our problem of classifying skin and non-skin color, having agreed that we will not able to come up with some threshold to separate two available classes, we need something else. Let us get our hands dirty with a very simple classifier - Nearest Neighbor Classifier.  
The idea is very simple. We represent each sample as a point in N dimensional space, N being number of features, 3 in our case. We can then choose to compute the distance between the samples. 

There are various ways and means to compute the distances. Let us adopt a variant commonly used - Euclidean Distance, to be specific, Squared Euclidean Distance:

      {%highlight python%}
## Script - 3
def Sq_Eu_Dist(p1, p2):
    	return np.sum( (p1-p2)**2)
      {%endhighlight%}

Now, at the time of classification, given a new example, we find the closest sample (Nearest Neighbor) to it, and use it's label.

      {%highlight python%}
## Script - 4
def slow_Eu_Dist(p1, p2):
    	return np.sum( (p1-p2)**2)

def little_faster_Eu_Dist(x, y):
    	diff = np.array(x) - np.array(y)
    	return np.dot(diff, diff)

def faster_Eu_Dist(t, td):
	sd = np.zeros(shape = (1, len(td)));
	for i in range (0, len(t)):
		sd = sd + ((t[i] - td[:, i]) ** 2)
	return sd

from tqdm import tqdm
def NN_Classify(tr_X, tr_Y, te_X):
	prediction = []
	for te in tqdm(range(0, len(te_X))):
		#distances = np.array([slow_Eu_Dist(tr, te_X[te]) for tr in tr_X])
		#distances = np.array([np.linalg.norm(tr - te_X[te]) for tr in tr_X])
		#distances = np.array([little_faster_Eu_Dist(tr, te_X[te]) for tr in tr_X])
		#distances = faster_Eu_Dist(te_X[te], tr_X)#3":8'
		# removing a function call overhead makes it ever faster!!		
		distances = (te_X[te, 0] - tr_X[:, 0]) ** 2 + (te_X[te, 1] - tr_X[:, 1]) ** 2 + (te_X[te, 2] - tr_X[:, 2]) ** 2
		nearest = distances.argmin()
		prediction.append(tr_Y[nearest])
	return prediction

def NN_Score(actual, predicted):
	score = 0
	for i in range(0, len(actual)):
		if actual[i] == predicted[i]:
			score += 1
	return score / len(actual)

train_percent = 0.80
data_train = data[: int(data.shape[0]*train_percent)]
data_test = data[int(train_percent*data.shape[0]) : data.shape[0]]

tr_Y = data_train[:, (len(data[0]) - 1)]
tr_X = data_train[:, range(0, len(data[0]))]
te_Y = data_test[:, (len(data[0]) - 1)]
te_X = data_test[:, range(0, len(data[0]))]

predicted = NN_Classify(tr_X, tr_Y, te_X)
score = NN_Score(te_Y, predicted)

print("Achieved accuracy of " + str(score) + " precents!")

Output: 
No of skin data elements in testing: (10103,)
(49012, 3)
100%|███████████████████████████████████████████████████████████████████████████████| 49012/49012 [02:26<00:00, 333.54it/s]
Achieved accuracy of 0.9996939525014282 percents!
      {%endhighlight%}

Well, with this particular algorithm, we are not really doing different training and testing phase. For every testing pattern, we pass through all the training data and simply assign a label.

And hence, it is quite slow as well. The code is an easy flow and I am sure you glanced over the slow, little-faster and faster way of doing the same thing. This, particularly, is an important task, usually goes unstated. By any means, this is not at all the fastest implementation, it is just practical. We have around 0.2 million data points, computing a distance of a 50K points against it just gets unreasonable in terms of time it takes. Hence many versions, hope you get a chance to play with it.

The results are quite satisfactory, 99% plus do not usually come along as easily.

This classifier can be further improved by considering k nearest points, and making them elect the best class. This is known as k-Nearest-Neighbor classifier. 

**Let us Fold It!**

But wait! We discussed a lot about cross validation. Why not to apply it?

      {%highlight python%}
## Script - 5
s_data = data[data[ : , 3] == 1]
ns_data = data[data[ : , 3] == 2]

print("Total Skin data: " + str(s_data.shape))
print("Total Non-Skin data: " + str(ns_data.shape))

folds = 5
total_score = 0
for i in range(0, folds):
    tr_s_data = sp.delete(s_data, [range( int(i*(len(s_data)/folds)), int((i+1)*(len(s_data)/folds)) )], 0)
    te_s_data = s_data[int(i*(len(s_data)/folds)) : int((i+1)*(len(s_data)/folds)), :]
    #print("te_s_data shape: " + str(te_s_data.shape))
    
    tr_ns_data = sp.delete(ns_data, [range( int(i*(len(ns_data)/folds)), int((i+1)*(len(ns_data)/folds)) )], 0)
    te_ns_data = ns_data[int(i*(len(ns_data)/folds)):int((i+1)*(len(ns_data)/folds)), :]
    #print("te_ns_data shape: " + str(te_ns_data.shape))
    
    tr_data = np.vstack((tr_s_data, tr_ns_data))
    te_data = np.vstack((te_s_data, te_ns_data))

    np.random.shuffle(tr_data)
    np.random.shuffle(te_data)

    #print("tr_data shape: " + str(tr_data.shape))
    #print("te_data shape: " + str(te_data.shape))

    tr_Y = tr_data[:, (len(tr_data[0]) - 1)]
    tr_X = tr_data[:, range(0, len(data[0]))]
    te_Y = te_data[:, (len(tr_data[0]) - 1)]
    te_X = te_data[:, range(0, len(data[0]))]
    
    #predicted = NN_Classify(tr_X, tr_Y, te_X)
    #score = NN_Score(te_Y, predicted)
    score = 0
    print("score in fold " + str(i) + ": " + str(score))
    total_score += score
    
avg_score = total_score / folds
print("for " + str(folds) + " folds cross validation, accuracy is: " + str(avg_score))

Output:
Total Skin data: (50859, 4)
Total Non-Skin data: (194198, 4)
score in fold 0: 0.9996327280146908
score in fold 1: 0.9995307271688566
score in fold 2: 0.999591928342617
score in fold 3: 0.9995715335019996
score in fold 4: 0.9995919366685709
for 5 folds cross validation, accuracy is: 0.999583770739347
      {%endhighlight%}

**Testing on *Real* Data**

Well, we can definitely say now, that out Nearest Neighbor Classifier is quite successful; it gives 99% plus accuracy in all the cases. And hence, now for a new incoming point, we can use ALL of our data. 

Well, this is fine; testing something on dataset is a way of creating a model. If we do not test it for real data, it is as good as ineffectual. 

      {%highlight python%}
## script - 6
img = sp.misc.imread('test_1.jpg')
print(img.shape)
w, h = img.shape[0:2]
img = img.reshape((img.shape[0] * img.shape[1], img.shape[2]))
img[:, [0, 2]] = img[:, [2, 0]]
print(img.shape)
# now we do not mind using all the data
tr_Y = data[:, (len(data[0]) - 1)]
tr_X = data[:, range(0, len(data[0]))]
predicted = np.array(NN_Classify(tr_X, tr_Y, img))

# if skin -white, black otherwise!
new_img = np.zeros(shape = (w*h, 3), dtype=np.uint8)
print("new image is of : " + str(new_img.shape))
print("predicted is of : " + str(predicted.shape))
for i in range(0, len(img)):
    if predicted[i] != 2.0:
        new_img[i] = [255, 255, 255]

new_img = new_img.reshape((w, h, 3))
import matplotlib.pyplot as plt
plt.imshow(new_img)
plt.savefig('test_1_out.jpg')
plt.show()
plt.close()
      {%endhighlight%}

Following are the input and output images:

<center><img src="/images/ml_4_3.png"></center>

As you can see, as we move towards more complex images, the accuracy seems to drop. In the last image, the pixels of a table, print on the T-shirt, etc. gets misclassified. Hence, this method, though we carried out 99% plus accuracy, in real-life situations, it is as good as around 60% of the times.  

**Conclusion**

In full swing, we have started to dig into the classification problems. And be assured, this is just a start.

As a matter of fact, the problem that we solved was too simple. We had only three features, and in that, all of them had a same scale. Hence, normalization was not needed. With data, we had uneven split between our samples, but the classifier we chose was unaffected by this datum. Yes, not all classifiers are this much considerate.

In the upcoming posts, these concerns will be addressed, of course one at a time, and of course, with a real-life example.

