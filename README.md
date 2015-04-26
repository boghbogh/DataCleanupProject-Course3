# Data Cleanup Project-Week 3 Assignment 


## In line comment  has been provided for each line of code. Please study in line comments for better understanding of the code. 

### Setting Working Directory
### This Setting should be commented before final commit to repo. 

setwd("C:/R/Projects/UCI HAR Dataset")

### Loading Libraries
library(plyr)
library(data.table)

### --------------------- User Defined Functions -------------------------

cleanupDataSets<-function(features,ActivityMaster,subjects,activitylables,activitydata)
{
  ### Setting Feature names in main data first
  names(activitydata)<-features[,2]
  names(subjects)<-c("Subject")
  names(ActivityMaster)<- c("activity_label_id","activity_name")
  names(activitylables)<-c("activity_label_id")

  ### Selecting features based on the Name
  selectedFeatures<-features[grep("mean|std", features[,2]),]
  mergedWideDatasetWithMeanandSTD<-activitydata[,selectedFeatures[,2]]
  
  ### Merging with Activity ID and Subject ID  
  mergedFinal<- cbind(activitylables,subjects,mergedWideDatasetWithMeanandSTD)
  
  ### Merging with Activity label ID
  mergedFinal<-merge(mergedFinal,ActivityMaster)

  mergedFinal
}


### --------------- End of User Defined Functions -------------------------

### Reading master data for this program

features<-read.table("./features.txt")
activityLabels<-read.table("./activity_labels.txt")


### Reading Test Data
testSubjects<-read.table("./test/subject_test.txt")
testActivityLabels<-read.table("./test/y_test.txt")
testActivityData<-read.table("./test/X_test.txt")

### Reading Train Data
trainSubjects<-read.table("./train/subject_train.txt")
trainActivityLabels<-read.table("./train/y_train.txt")
trainActivityData<-read.table("./train/X_train.txt")


### Written this logic as a function since it is same for both test and train data.

### Calling clean_up_datasets for test data
cleandUpTest<- cleanupDataSets(features, activityLabels,testSubjects,testActivityLabels,testActivityData)

### Calling clean_up_datasets for train data
cleandUpTrain<- cleanupDataSets(features, activityLabels,trainSubjects,trainActivityLabels,trainActivityData)

### Now adding Rows of Train and Test Data to one data set
tidyDataset<- rbind(cleandUpTest,cleandUpTrain)
tidyDataset <- tidyDataset[,2:82]

### Averaging on column 2:80 as first and last column are subject_id and activity_name
averages_data <- ddply(tidyDataset, .(Subject, activity_name), function(x) colMeans(x[, 2:80]))

###  Writing to file as instructed. 
write.table(averages_data, "averages_data.txt", row.name=FALSE)


