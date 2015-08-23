# Coursera-Getting-and-Cleaning-Data-Course-Project
## Downloading and unzipping the file:

fileURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileURL, destfile = "./Data.zip")
unzip(zipfile = "./Data.zip")

## Reading the activity, subject and features files for train and test:

dataActivityTest <- read.table("./UCI HAR Dataset/test/Y_test.txt",header = FALSE)
dataActivityTrain <- read.table("./UCI HAR Dataset/train/Y_train.txt", header = FALSE)
dataSubjectTest <- read.table("./UCI HAR Dataset/test/subject_test.txt", header = FALSE)
dataSubjectTrain <- read.table("./UCI HAR Dataset/train/subject_train.txt", header = FALSE)
dataFeaturesTest <- read.table("./UCI HAR Dataset/test/X_test.txt", header = FALSE)
dataFeaturesTrain <- read.table("./UCI HAR Dataset/train/X_train.txt", header = FALSE)

## Combining the train and test data for activity, subject and features, giving column names

dataActivity <- rbind(dataActivityTest,dataActivityTrain)
dataSubject <- rbind(dataSubjectTest,dataSubjectTrain)
dataFeatures <- rbind(dataFeaturesTest,dataFeaturesTrain)
names(dataActivity) <- c("activity")
names(dataSubject) <- c("subject")
dataFeaturesNames <- read.table("./UCI HAR Dataset/features.txt", header = FALSE)
names(dataFeatures) <- dataFeaturesNames$V2

## Combining subjet, activity and features data

dataCombine <- cbind(dataActivity, dataSubject)
data <- cbind(dataCombine, dataFeatures)

## Extracting only mean & std data; Using descriptive & appropriate activity names 

subdataFeaturesNames<-dataFeaturesNames$V2[grep("mean\\(\\)|std\\(\\)", dataFeaturesNames$V2)]
selectedNames <- c("activity","subject",as.character(subdataFeaturesNames))
datameanstd <- subset(data, select=selectedNames)
activityLabels <- read.table("./UCI HAR Dataset/activity_labels.txt", header = FALSE)
datameanstd[,1] <- activityLabels[datameanstd[,1], 2]
names(datameanstd) <- gsub('\\(|\\)',"",names(datameanstd), perl = TRUE)
names(datameanstd) <- make.names(names(datameanstd))
names(datameanstd) <- gsub('Acc',"Acceleration",names(datameanstd))
names(datameanstd) <- gsub('GyroJerk',"AngularAcceleration",names(datameanstd))
names(datameanstd) <- gsub('Gyro',"AngularSpeed",names(datameanstd))
names(datameanstd) <- gsub('Mag',"Magnitude",names(datameanstd))
names(datameanstd) <- gsub('^t',"TimeDomain.",names(datameanstd))
names(datameanstd) <- gsub('^f',"FrequencyDomain.",names(datameanstd))
names(datameanstd) <- gsub('\\.mean',".Mean",names(datameanstd))
names(datameanstd) <- gsub('\\.std',".StandardDeviation",names(datameanstd))
names(datameanstd) <- gsub('Freq\\.',"Frequency.",names(datameanstd))
names(datameanstd) <- gsub('Freq$',"Frequency",names(datameanstd))

## Calculating Mean

library(plyr)
dataavg <- aggregate(. ~subject + activity, datameanstd, mean)
dataavg <- dataavg[order(dataavg$subject,dataavg$activity),]
write.table(dataavg, file = "tidydata.txt",row.name=FALSE)






