# Getting-And-Cleaning-Data

This repo contains the following files:

- run_analysis.R - R scrip to perform analysis
- CodeBook.md - document that describes the variables, data, and transformations

Instructions

The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set.

Review criteria

The submitted data set is tidy.
The Github repo contains the required scripts.
GitHub contains a code book that modifies and updates the available codebooks with the data to indicate all the variables and summaries calculated, along with units, and any other relevant information.
The README that explains the analysis files is clear and understandable.
The work submitted for this project is the work of the student who submitted it.
Getting and Cleaning Data Course Projectless
The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project.
One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained:

[http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones]

Here are the data for the project:

[https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip]

You should create one R script called run_analysis.R that does the following:

-Merges the training and the test sets to create one data set. -Extracts only the measurements on the mean and standard deviation for each measurement. -Uses descriptive activity names to name the activities in the data set -Appropriately labels the data set with descriptive variable names. -From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

Download and unzip file, then set as working directory.

setwd("C:/Users/Erika/Desktop/getdata-projectfiles-UCI HAR Dataset")

Read Data

subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt")

subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt")

X_test <- read.table("UCI HAR Dataset/test/X_test.txt")

X_train <- read.table("UCI HAR Dataset/train/X_train.txt")

y_test <- read.table("UCI HAR Dataset/test/y_test.txt")

y_train <- read.table("UCI HAR Dataset/train/y_train.txt")

activity_labels <- read.table("UCI HAR Dataset/activity_labels.txt")

features <- read.table("UCI HAR Dataset/features.txt")

Analysis Merges the training and the test sets to create one data set.

dataSet <- rbind(X_train,X_test)

Extracts only the measurements on the mean and standard deviation for each measurement. Create a vector of only mean and std, use the vector to subset.

MeanStdOnly <- grep("mean()|std()", features[, 2])

dataSet <- dataSet[,MeanStdOnly]

Appropriately labels the data set with descriptive activity names. Create vector of "Clean" feature names by getting rid of "()" apply to the dataSet to rename labels.

CleanFeatureNames <- sapply(features[, 2], function(x) {gsub("[()]", "",x)})

names(dataSet) <- CleanFeatureNames[MeanStdOnly]

combine test and train of subject data and activity data, give descriptive lables

subject <- rbind(subject_train, subject_test)

names(subject) <- 'subject'

activity <- rbind(y_train, y_test)

names(activity) <- 'activity'

combine subject, activity, and mean and std only data set to create final data set.

dataSet <- cbind(subject,activity, dataSet)

Uses descriptive activity names to name the activities in the data set group the activity column of dataSet, re-name lable of levels with activity_levels, and apply it to dataSet.

act_group <- factor(dataSet$activity) levels(act_group) <- activity_labels[,2] dataSet$activity <- act_group

Creates a second, independent tidy data set with the average of each variable for each activity and each subject.

check if reshape2 package is installed

if (!"reshape2" %in% installed.packages()) { install.packages("reshape2") }

library("reshape2")

"Melt" data to tall skinny data and cast means. Finally write the tidy data to the working directory as "tidy_data.txt"

baseData <- melt(dataSet,(id.vars=c("subject","activity")))

secondDataSet <- dcast(baseData, subject + activity ~ variable, mean)

names(secondDataSet)[-c(1:2)] <- paste("[mean of]" , names(secondDataSet)[-c(1:2)] )

write.table(secondDataSet, "tidy_data.txt", sep = ",")
