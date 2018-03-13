---
title: 'Machine Learning 3 : Prediction and its Traits'
author: Sagar Gandhi
layout: post
permalink: /Machine-Learning-3-Prediction
categories:
  - Posts
---
<!--excerpt.start-->
Let us begin. I am pretty excited about this post, here onward we start to learn how to do Machine Learning.  
From whatever posts I have followed, somehow, people take an approach of first explaining the types of things one can do using Machine Learning, namely classification, clustering, etc.  
  
Let us take a different path. Let us head straight to solve a problem and get our hands dirty. We will also, of course, come to a point where we will talk about types of problems that Machine Learning solves, but let us first get introduced to the problem solving.  
  
**Environment**  
We will be using Python, examples are tested with Python 3.5.  
We need numpy, scipy and matplotlib packages. How to install these is not covered here, as that will drift us. You can install [Anaconda](https://www.continuum.io/downloads), or follow along OS specific post for installation.  
  
**Problem Statement**  <!--excerpt.end-->
Let us look at a simple example. This will help us in getting started. Let us say we wish to predict the usage of a particular word in next 50 to 100 years. What are our options then? We need data of usage of that word till now.  
  
[Google's Ngram viewer](https://en.wikipedia.org/wiki/Google_Ngram_Viewer) comes to our rescue. As we are working on a small branch of Artificial Intelligence tree, let us pay a small tribute to the community, by predicting future of word "artificial". Below is the snap of the usage of the word, and you can play with it [here](https://books.google.com/ngrams/graph?content=artificial&year_start=1500&year_end=2008&corpus=15&smoothing=0&share=&direct_url=t1%3B%2Cartificial%3B%2Cc0)  
  
<center><img src="/images/ml_3_1.png"></center>
  
With the help of a friend, data has been downloaded, from years 1500 to 1999. For further simplifications, as this is our first dive-in, I have termed years from 0 to 499. You can have a look at data in the file: ML_curve_fitting_example.dat. Left column indicates years starting from 1500, right column indicates score of that word being used.  
  
Let us further pin-point our task. Let us say we have to predict when this word will become obsolete, i.e. when the usage will reach the zero, based on the provided data.  
  
**Visualizing and cleansing the data**  
Let us have a look at our data and its characteristics:  
  
      {%highlight python%}
# Script - 1
import scipy as sp
import numpy as np

data = sp.genfromtxt("ML_curve_fitting_example.dat", delimiter = "\t")
print(data[-10:])		## print last 10 items in our dataset
print(data.shape)

## separate data in two different lists
x = data[:, 0]
y = data[:, 1]

## a quick statistic: min and max of word's score
print("y min = " + repr(min(y)) + "\t y max = " + repr(max(y)))

## as the range of score is too low, let is scale 
y = sp.multiply(y, 100000)

## if there are missing values, those have to be taken care of
#print(sp.sum(sp.isnan(y))) 
x = x[~sp.isnan(y)] # Be careful. This is not a typo. NaN will be present for score, not for years!
y = y[~sp.isnan(y)]

## lets plot something
import matplotlib.pyplot as plt
plt.scatter(x, y)
plt.title("Use of word \"artificial\" over 1500 to 1999")
plt.xlabel("Years")
plt.ylabel("Score of use")
axes = plt.gca()
axes.set_ylim([min(y), max(y)])
plt.autoscale(tight = True)
plt.grid()
plt.show()
      {%endhighlight%}
  
<center><img src="/images/ml_3_2.png"></center>
  
We begin by reading the file. We display last ten values and min and max of Y, i.e. usage of the word. From these, we are sure that values are too small, hence we multiply each value by a 100000. In this particular case, we have a single dimensional data, i.e. only score, hence scaling a cake-walk. Be warned that this will not be this easy always.  
We also process our data so that it should not contain any missing value. In our particular case, we did not have any missing value. But it is always better to check the data beforehand to give ourselves an opportunity to handle any intricacies in data gathering. Then we simply draw a scatter plot.  
  
**Modelling**  
Now, as we have taken a look at our data, question is what will be usage score for upcoming years?  
To answer this question, we need to fit a model to our data and then extrapolate that model to perform prediction of the future.  
  
Let us assume that a straight line fits our model. There is Scipy's function - Polyfit(), which will help in finding equation of a line.  
  
      {%highlight python%}
# script - 2
# it is assumed that previously demonstrated code is present

## function: returns squared difference between function value and predicted value
def error(f, x, y):
	return sp.sum((f(x) - y) **2)

## generate 'x' values for our function
fx = sp.linspace(0, x[-1], len(y))

fp1, residuals, rank, sv, rcond = sp.polyfit(x, y, 1, full = True)
print("Res: " + str(residuals))
print("Model parameters: %s" % fp1)
f1 = sp.poly1d(fp1)
print("Error O_1: " + str(error(f1, x, y)) )

## let us plot our line 
plt.plot(fx, f1(fx), linewidth = 3, label = "d = %i"% f1.order)
plt.show()

      {%endhighlight%}
  
Output:  
Res: [ 208.94057049]  
Model parameters: [ 0.00623047 -0.11288484]  
Error O_1: 208.940570486  
  
<center><img src="/images/ml_3_3.png"></center>
  
Straight line is not all that fancy. If *"Curve_Fitting"* is so much easier to code, why not try higher order polynomials?  
  
	  {%highlight python%}
# script - 3
# it is assumed that previous code is present

## trying to fit second order polynomial
f2p = sp.polyfit(x, y, 2)
#print("Model parameters: %s" % f2p)
f2 = sp.poly1d(f2p)
print("Error O_2: " + str(error(f2, x, y) ))
plt.plot(fx, f2(fx), linewidth = 3, label = "d = %i"% f2.order)

## trying to fit third order polynomial
f3p = sp.polyfit(x, y, 3)
#print(" Model parameters: %s" % f3p)
f3 = sp.poly1d(f3p)
print("Error O_3: " + str(error(f3, x, y) ))
plt.plot(fx, f3(fx), linewidth = 3, label = "d = %i"% f3.order)

## trying to fit tenth order polynomial
f10p = sp.polyfit(x, y, 10)
#print("Model parameters: %s" % f10p)
f10 = sp.poly1d(f10p)
print("Error O_10: "+ str(error(f10, x, y) ))
plt.plot(fx, f10(fx), linewidth = 3, label = "d = %i"% f10.order)

## trying to fit hundredth order polynomial
f100p = sp.polyfit(x, y, 100)
f100 = sp.poly1d(f100p)
print("Error O_100: " + str(error(f100, x, y)))
plt.plot(fx, f100(fx), linewidth = 3, label = "d = %i"% f100.order)
plt.legend(loc = "upper left")
## plot all the polynomial's fittings
plt.show()
	  {%endhighlight%}
  
Output:  
Error O_1: 208.940570486  
Error O_2: 196.453545329  
Error O_3: 163.573700658  
Error O_10: 158.810240344  
Error O_100: 149.630548353  
  
<center><img src="/images/ml_3_4.png"></center>
  
So, which of the models to be used? It might seem that higher the degree of polynomial - better it is. Right?  
Absolutely Wrong! The higher degree polynomials not only model data, but they also model noise. Ideally, we would want to eliminate all the noise from our model, but it is not possible 100%. So, how can we confirm this? Well, visually ...  
  
If we have another look at the data, it can be seen that last 100 years, usage is going down. So let us split data into two sets, last 100 years separate from rest of the data.  

	  {%highlight python%}
# script - 4
# it is in continuity with previous code 
divider = 400
xa = x[: divider]
ya = y[: divider]
xb = x[divider :]
yb = y[divider :]

## fit two lines to two datasets
fa = sp.poly1d(sp.polyfit(xa, ya, 1))
fb = sp.poly1d(sp.polyfit(xb, yb, 1))
fa_error = error(fa, xa, ya)
fb_error = error(fb, xb, yb)
print("Error using partition: %f"% (fa_error + fb_error))
plt.plot(fx[: divider], fa(fx[: divider]), linewidth = 4, label = ("d = 1+"), color = "orange")
plt.plot(fx[divider: ], fb(fx[divider :]), linewidth = 4, label = ("d = 1+"), color = "orange")

plt.legend(loc = "upper left")
plt.show()
	  {%endhighlight%}
  
Output:  
Error O_1: 208.940570486  
Error O_2: 196.453545329  
Error O_3: 163.573700658  
Error O_10: 158.810240344  
Error O_100: 149.630548353  
Error using partition: 167.911429  
  
<center><img src="/images/ml_3_5.png"></center>
  
As it can be concluded from the figure that fitting two low dimensional models will reduce the error than any other higher dimensional model that we have tried. Hope this makes it clear that higher degree polynomials does NOT mean better model.  
  
So which model should be used in practice? Well, using multiple models is not a very bright idea. If we do that we would have to use some kind of conditioning that if year is greater than so and so, use this model otherwise the other model. And in totality, we would use the second model(closest to future), which effectively ignores all the data from the past.  
  
**Training, Testing and Conclusion**  
Till now, we have used all the data for our experiments. It would be really great if we could look into future and somehow grasp the actual values. But that is not possible. So what we do is - take a chunk of our data, and test the system on this data. Need no mention that this data should NOT be there while training the system.  
  
In our case, if we take the data from last 100 years, then it is going to be difficult to fit the model. Data clearly has a "shift" in its behavior. So let us consider only last 50 years for the testing. And let us try to fit all the models to this "Test Dataset". Note that all models need to be retrained. This would give a more realistic picture of how our models perform.  
  
	  {%highlight python%}
# script - 5
# entirely independent code
import scipy as sp
import numpy as np

data = sp.genfromtxt("ML_curve_fitting_example.dat", delimiter = "\t")
print("Last 10 rows: " + str(data[-10:]))#print last 10 items in our dataset
print(data.shape)

## separate data in two different lists
x = data[:, 0]
y = data[:, 1]
print("y min = " + repr(min(y)) + "\t y max = " + repr(max(y)))

y = sp.multiply(y, 100000)

#print(sp.sum(sp.isnan(y)))
x = x[~sp.isnan(y)]
y = y[~sp.isnan(y)]

train_data_len = 50
x_train = x[: (len(x) - train_data_len)]
y_train = y[: (len(y) - train_data_len)]
x_test = x[(len(x) - train_data_len) :]
y_test = y[(len(x) - train_data_len) :]

print("Training Set: %i"% len(x_train))
print("Test Set: %i"% len(x_test))

## lets plot something
importmatplotlib.pyplot as plt
plt.scatter(x_train, y_train)
plt.scatter(x_test, y_test)

## function: returns squared difference between function value and predicted value 
def error(f, x_train, y_train):
	returnsp.sum((f(x_train) - y_train) **2)

## function: plotting help, rather than repeatedly typing the same things, let us use a short-cut
defYearsVSUse():
	plt.title("Training and testing of usage of word \"artificial\" over 1500 to 1999")
	plt.xlabel("Years")
	plt.axis([0, 500, 0, 10])
	plt.ylabel("Score of use")
	plt.grid()

fx = sp.linspace(0, x[-1], len(x))
fx_tr = fx[: (len(x) - train_data_len)]
fx_tst = fx[(len(x) - train_data_len) :]

## First order polynomial fitted to data
fp1, residuals, rank, sv, rcond = sp.polyfit(x_train, y_train, 1, full = True)
#print("Res: " + str(residuals))
#print("Model parameters: %s" % fp1)
f1 = sp.poly1d(fp1)
print("Error O_1: " + str(error(f1, x_train, y_train) ))
plt.plot(fx_tr, f1(fx_tr), linewidth = 3, label = "d = %i"% f1.order, color = "blue")

## Second order polynomial fitted to data
f2p = sp.polyfit(x_train, y_train, 2)
#print("Model parameters: %s" % f2p)
f2 = sp.poly1d(f2p)
print("Error O_2: " + str(error(f2, x_train, y_train) ))
plt.plot(fx_tr, f2(fx_tr), linewidth = 3, label = "d = %i"% f2.order, color = "red")

## Third order polynomial fitted to data
f3p = sp.polyfit(x_train, y_train, 3)
#print(" Model parameters: %s" % f3p)
f3 = sp.poly1d(f3p)
print("Error O_3: " + str(error(f3, x_train, y_train) ))
plt.plot(fx_tr, f3(fx_tr), linewidth = 3, label = "d = %i"% f3.order, color = "green")

## Tenth order polynomial fitted to data
f10p = sp.polyfit(x_train, y_train, 10)
#print("Model parameters: %s" % f10p)
f10 = sp.poly1d(f10p)
print("Error O_10: " + str(error(f10, x_train, y_train) ))
plt.plot(fx_tr, f10(fx_tr), linewidth = 3, label = "d = %i"% f10.order, color = "yellow")

## Fiftieth order polynomial fitted to data
f50p = sp.polyfit(x_train, y_train, 50)
f50 = sp.poly1d(f50p)
print("Error O_50: " + str(error(f50, x_train, y_train)))
plt.plot(fx_tr, f50(fx_tr), linewidth = 3, label = "d = %i"% f50.order, color = "orange")

YearsVSUse()

print("test: order_1 = %f"% error(f1, x_test, y_test))
print("test: order_2 = %f"% error(f2, x_test, y_test))
print("test: order_3 = %f"% error(f3, x_test, y_test))
print("test: order_10 = %f"% error(f10, x_test, y_test))
print("test: order_50 = %f"% error(f50, x_test, y_test))

## plot the fitted values against test data
plt.plot(fx_tst, f1(fx_tst), linewidth = 3, label = "d = %i"% f1.order, color = "blue")
plt.plot(fx_tst, f2(fx_tst), linewidth = 3, label = "d = %i"% f2.order, color = "red")
plt.plot(fx_tst, f3(fx_tst), linewidth = 3, label = "d = %i"% f3.order, color = "green")
plt.plot(fx_tst, f10(fx_tst), linewidth = 3, label = "d = %i"% f10.order, color = "yellow")
plt.plot(fx_tst, f50(fx_tst), linewidth = 3, label = "d = %i"% f50.order, color = "orange")

YearsVSUse()
plt.legend(loc = "upper left")
plt.show()

fromscipy.optimize import fsolve
becomes_ancient_at = fsolve(f3, 500)
print("Word \"artificial\" will become ancient in: " + str(becomes_ancient_at[0] + 1500.0))
	  {%endhighlight%}
  
Output:  
Training Set: 449  
Test Set: 50  
Error O_1: 168.926371288  
Error O_2: 168.565578906  
Error O_3: 160.721239448  
Error O_10: 157.935350721  
Error O_50: 146.882568126  
test: order_1 = 59.359704  
test: order_2 = 69.137475  
test: order_3 = 13.847517  
test: order_10 = 346.005838  
test: order_50 = 868412738064986.500000  
**Word "artificial" will become ancient in: 2146.28104703**  
  
<center><img src="/images/ml_3_6.png"></center>
  
So, from Test Dataset, it is clear that polynomial of degree three is best out of what we have tried and as per the data and our prediction model, word artificial would see darkest year since in 2146.  
  
Now, there are other parameters as well. Given the fact that we both are studying Artificial Intelligence, well, a part of it, it seem to have a bright future.  
  
Also, intentionally data from 2000 to 2008 was left out. This is to put some light on the fact that even though Machine Learning is a great tool, it works really well when data is available, trusting it too much is not a good idea after all. This simply happens because we did not have any *"features"* as such. We simply gathered the past usage, and tried to fit a model to it. External parameters are so very important that they can actually help us *"Modelling a Problem"* rather than modelling a data.  
  
In the upcoming posts, we dive into other details, we will learn more about features, classification and data quality, etc.  
  
Hope you enjoyed a long post. All of the code is available [here](https://github.com/Gandhi-Sagar/Machine_Learning_Blog_Series/blob/master/3).  
  
Note: I am thankful to [Vivek](https://www.linkedin.com/in/vivekkulkarni) for providing valuable feedback and suggestions on this post.


