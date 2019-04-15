---
title: "machine learning"
author: "ng"
date: "April 15, 2019"
output:
  pdf_document: default
  html_document: default
  word_document: default
---

#Background
Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. In this project, my goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. 

The training data is found here:
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv
The test data is found here:
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv
The data is source here: http://groupware.les.inf.puc-rio.br/har.


The goal of my project is to predict the manner in which participants did the exercise. This is the "classe" variable in the training set. The report describes how I built your model, how I used cross validation, what the expected out of sample error is, and why I made the choices I did

#Executive Summary
Given the computing power of random forests - a model is developed through random forests from 60% of cases in the training dataset. The model is then applied on 40% of cases in the training dataset (Test / validation). The expected out of sample error is very small. As it turns out - the confusion matrix yields 99% accuracy. Subsequently, the model is applied on the testing dataset (20 cases) where I achieved 100 correct predictions 

#Loading packages 
Loading packages required for this machine learning assignment
```{r, echo = TRUE}
library(caret)
library(rpart)
library(RColorBrewer)
library(randomForest)
```


#Data Download 
Download files including a training dataset and a testing dataset which will be used to test the accuracy of predictions from the model
```{r, echo = TRUE}
TrainLINK <- "http://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
TestLINK  <- "http://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"
TRAINING <- read.csv(url(TrainLINK))
TESTING  <- read.csv(url(TestLINK))
```

#Data Transformation 
Filter in only useful elements. Remove all columns containing NA and remove independent variables that are not in the testing dataset. will also remove the first 7 independent variables

```{r, echo = TRUE}
NAMEStesting <- names(TESTING[,colSums(is.na(TESTING)) == 0])[8:59]
TRAINING <- TRAINING[,c(NAMEStesting,"classe")]
TESTING <- TESTING[,c(NAMEStesting,"problem_id")]
dim(TRAINING); 
dim(TESTING);
```

#Partition training dataset
we will split our data into a training data set (60% of cases from training dataset) and a testing data set (40% of cases from training dataset). The purpose of the testing dataset here is for validation to estimate the out of sample error of our predictor
```{r, echo = TRUE}
set.seed(12345)
inTRAIN <- createDataPartition(TRAINING$classe, p=0.6, list=FALSE)
INtraining <- TRAINING[inTRAIN,]
INtesting <- TRAINING[-inTRAIN,]
dim(INtraining)
dim(INtesting)
```

#Random forest
Create random forest model on training dataset. The out of sample error should be small. The error will be estimated using the 40% testing sample from the training dataset. 
```{r, echo = TRUE}
set.seed(12345)
RFmodel <- randomForest(classe ~ ., data = INtraining, ntree = 1000)
predictINtesting <- predict(RFmodel, INtesting, type = "class")
confusionMatrix(predictINtesting, INtesting$classe)
```

#Apply model on Test dataset
Run random forest model on test dataset (20 cases). We find the prediction to be very accurate. This is expected because the out of sample error is very small

```{r, echo = TRUE}
set.seed(12345)
predictTESTING <- predict(RFmodel, TESTING, type = "class")
predictTESTING
```

