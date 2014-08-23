Practical Machine Learning Course Project
========================================================

Data Cleaning
========================================================
Features that consisted primarily of N/A or blank fields wer manually removed from the csv.

Library Selection
========================================================
Numerous training methods from the caret package were attempted but would not run in a reasonable time on available hardware. The randomForest library was chosen based on run time and accuracy obtained in initial attempts.

randomForest
===========================================================
Data were first split into a training and test set.


```r
library("caret")
```

```
## Loading required package: lattice
## Loading required package: ggplot2
```

```r
library("randomForest")
```

```
## randomForest 4.6-10
## Type rfNews() to see new features/changes/bug fixes.
```

```r
pml.data<-read.csv("~/Downloads/pml-training.csv",sep="\t")
inTrain<-createDataPartition(pml.data$classe,p=0.75,list=FALSE)
train.data<-pml.data[inTrain,]
test.data<-pml.data[-inTrain,]
```

Cross Validation
===========================================================
The cross-validating function for random forests rfcv was used to determine the accuracy rate with different numbers of factors from the training set.


```r
model.cv<-rfcv(train.data[,1:52],train.data$classe)
model.cv$error.cv
```

```
##       52       26       13        6        3        1 
## 0.005707 0.008085 0.010803 0.045862 0.115029 0.593967
```

```r
with(model.cv, plot(n.var, error.cv, log="x", type="o", lwd=2))
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 

The plot if the errors versus number of features used indicates using all the variables produces the lowest estimated error.

Fitting the Model
===============================================================
The model was fit using the randomForest function.
 

```r
model.fit<-randomForest(classe~.,data=train.data)
```

The random forest method effectivley keeps track of its own out of sample error via the out of bag property of the model. This and the confusion matrix of the model indicate the model is a good fit.


```r
model.fit
```

```
## 
## Call:
##  randomForest(formula = classe ~ ., data = train.data) 
##                Type of random forest: classification
##                      Number of trees: 500
## No. of variables tried at each split: 7
## 
##         OOB estimate of  error rate: 0.47%
## Confusion matrix:
##      A    B    C    D    E class.error
## A 4181    2    0    1    1   0.0009558
## B   13 2831    4    0    0   0.0059691
## C    0   16 2548    3    0   0.0074016
## D    0    0   20 2391    1   0.0087065
## E    0    0    1    7 2698   0.0029564
```
Data from the test partition were used to predict against. 


```r
outcome<-predict(model.fit,test.data)
cor(as.numeric(test.data$classe),as.numeric(outcome))^2
```

```
## [1] 0.9978
```

Result Submission
====================================================================
The model was used on the test data provided for the pragramming assignment and resukted in all correct submissions for the 20 test cases.
