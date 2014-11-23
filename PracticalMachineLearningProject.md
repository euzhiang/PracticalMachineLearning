# Practical Machine Learning Project

## Executive Summary
The goal of this project is to predict the manner in which the participants did the exercise. I have used random forest method to build the model and used cross validation to confirm the effectiveness of the random forest model. The sample error is very small and based on this model, I have managed to predict all the 20 different test cases correctly.

## Loading libraries

```r
library(knitr)
library(caret)
```

```
## Loading required package: lattice
## Loading required package: ggplot2
```

```r
library(randomForest)
```

```
## randomForest 4.6-10
## Type rfNews() to see new features/changes/bug fixes.
```

```r
set.seed(999)
```

## Reading and Cleaning Data
I first read the training data from the files and split the data into training and testing sets. The data is cleaned by removing the incomplete columns as well as the first seven columns that are not used as predictors.

```r
trainingData<-read.csv("pml-training.csv", na.strings=c("#DIV/0!"," ", "NA"))
trainingData<-trainingData[,colSums(is.na(trainingData))==0]
trainingData<-trainingData[,-(1:7)]
inTrain<-createDataPartition(y=trainingData$classe, p=0.6, list=FALSE)
training<-trainingData[inTrain,]
testing<-trainingData[-inTrain,]
```

## Model Building 
I build the model using the random forest method.The out of sample error rate is estimated to be 0.63%.

```r
modelFit<-randomForest(classe ~ ., data=training)
modelFit
```

```
## 
## Call:
##  randomForest(formula = classe ~ ., data = training) 
##                Type of random forest: classification
##                      Number of trees: 500
## No. of variables tried at each split: 7
## 
##         OOB estimate of  error rate: 0.63%
## Confusion matrix:
##      A    B    C    D    E  class.error
## A 3346    2    0    0    0 0.0005973716
## B   13 2264    2    0    0 0.0065818341
## C    1   13 2036    4    0 0.0087633885
## D    0    0   27 1900    3 0.0155440415
## E    0    0    2    7 2156 0.0041570439
```

## Cross validation
I validate the random forest model on the testing data set. The accuracy is 99.32% with the sample error very small at 0.68%. This sample error is very close to the estimated sample error rate of 0.63%.

```r
predictions<-predict(modelFit, newdata=testing)
confusionMatrix(predictions, testing$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 2232   11    0    0    0
##          B    0 1505    8    0    0
##          C    0    1 1359   24    2
##          D    0    1    1 1262    5
##          E    0    0    0    0 1435
## 
## Overall Statistics
##                                           
##                Accuracy : 0.9932          
##                  95% CI : (0.9912, 0.9949)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9915          
##  Mcnemar's Test P-Value : NA              
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            1.0000   0.9914   0.9934   0.9813   0.9951
## Specificity            0.9980   0.9987   0.9958   0.9989   1.0000
## Pos Pred Value         0.9951   0.9947   0.9805   0.9945   1.0000
## Neg Pred Value         1.0000   0.9979   0.9986   0.9964   0.9989
## Prevalence             0.2845   0.1935   0.1744   0.1639   0.1838
## Detection Rate         0.2845   0.1918   0.1732   0.1608   0.1829
## Detection Prevalence   0.2859   0.1928   0.1767   0.1617   0.1829
## Balanced Accuracy      0.9990   0.9951   0.9946   0.9901   0.9976
```

The model is then used to predict 20 different test cases.

```r
testingData<-read.csv("pml-testing.csv", na.strings=c("#DIV/0!"," ", "NA"))
testingData<-testingData[,colSums(is.na(testingData))==0]
testingData<-testingData[,-(1:7)]
predictions<-predict(modelFit, newdata=testingData)
predictions
```

```
##  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 
##  B  A  B  A  A  E  D  B  A  A  B  C  B  A  E  E  A  B  B  B 
## Levels: A B C D E
```

Using the code given in the course, the prediction outcomes are written to individual files.

```r
pml_write_files = function(x){
  n = length(x)
  for(i in 1:n){
    filename = paste0("problem_id_",i,".txt")
    write.table(x[i],file=filename,quote=FALSE,row.names=FALSE,col.names=FALSE)
    }
  }
pml_write_files(predictions)
```

## Conclusion
The random forest machine learning method is very good for the problem in this project. It gives very low error.
