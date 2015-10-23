---
title: "Code book"
author: "Alberto Pellegata"
date: "23 ottobre 2015"
output: html_document
---

script for project work Getting and Cleaning Data - October 2015
NB: script successfully run on Window 10 machine

summary of TASKS and deliverables:
TASK 0: donwload file, create and set working directory
TASK 1: merge datasets
TASK 2: extracts only mean and std measures
TASK 3: use descriptive names for acitivties
TASK 4: use descriptive variable names
TASK 5: create a second tify dataset for subject- activity- measure- value

---START

TASK 0: create a working directory where to store project data

´´´if (!file.exists("data")) {
´´´  dir.create("data")
´´´}

´´´setwd("./data")

download file from the url provided. NB: you can skip this part and start from TASK 1 if you already have the datesets on your local machine

fileurl <- ("https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip")
download.file(fileurl, destfile = "./datadownload.zip")

dateDownloaded <- date() # store date of download execution

# NB: please make sure you MANUALLY extract the downloded zip folder to the "./data" directory.
# this will enable the following part of code to run properly

# TASK 1: Merges the training and the test sets to create one data set.
# To do this first read the X data (both train and test) in R and rbind the two data tables into a single table "X"

X_test <- read.table("./UCI HAR Dataset/test/X_test.txt", quote = "")
X_train <- read.table("./UCI HAR Dataset/train/X_train.txt", quote = "")
X <- rbind(X_test, X_train)
rm("X_test")# remove to clear up memory space
rm("X_train") # remove to clear up memory space

# repeat the above step applying it "Y" (this variable describes activiy performed)
y_test <- read.table("./UCI HAR Dataset/test/y_test.txt", quote = "")
y_train <- read.table("./UCI HAR Dataset/train/y_train.txt", quote = "")
Y <- rbind(y_test, y_train)
colnames(Y) <- "ActivityCode"
rm("y_test")
rm("y_train")

# repeat the above steps applying to "subject" (this varaible describes the subject onserved)
subject_test <- read.table("./UCI HAR Dataset/test/subject_test.txt", quote = "")
subject_train <- read.table("./UCI HAR Dataset/train/subject_train.txt", quote = "")
subject <- rbind(subject_test, subject_train)
colnames (subject) <- "Subject"
rm("subject_test")
rm("subject_train")

# read features file (which is the label for the metric of X)
features <- read.table("./UCI HAR Dataset/features.txt", quote = " ")
Vfeatures <- features$V2
Vfeatures <- as.character(features$V2)
#assign features as colnames of data table "X"
colnames(X) <- Vfeatures  
rm("Vfeatures") # to clear up memory space

# since I am going to use the dplyr, we nee to upload the package
library(dplyr)

# cbind now the three data tables "X" + "Y" + "Subject"

Table <- cbind(subject, Y, X)
Table_DF <- tbl_df(Table)
rm("features", "subject", "Table", "X", "Y") # clear up memory space deleting useless tables

# TASK 2: Extracts only the measurements on the mean and standard deviation for each measurement. 
# there are invalid column names in the table: before selecting subsest of columns, must apply make.names func()

valid_column_names <- make.names(names=names(Table_DF), unique=TRUE, allow_ = TRUE)
names(Table_DF) <- valid_column_names

Table_DF_select <- select (Table_DF, Subject, ActivityCode, contains("mean"), contains("std"))
rm("Table_DF") # clear up memory space

# TASK 3: Uses descriptive activity names to name the activities in the data set
Table_DF_select$ActivityCode <- factor(Table_DF_select$ActivityCode, 
                                      levels = c(1, 2, 3, 4, 5, 6), 
                                      labels = c("WALKING", "WALKING_UPSTAIRS", "WALKING_DOWNSTAIRS", "SITTING", "STANDING", "LAYING"))


# TASK 4: Appropriately labels the data set with descriptive variable names. 
# I removed dots from column names and then replaced abbreviations with full names 

names(Table_DF_select) <- gsub("\\.", "", names(Table_DF_select)) 
names(Table_DF_select) <- gsub("Acc", "Acceleration", names(Table_DF_select)) 
names(Table_DF_select) <- gsub("Gyro", "Gyroscope", names(Table_DF_select)) 
names(Table_DF_select) <- gsub("Mag", "Magnitude", names(Table_DF_select))
names(Table_DF_select) <- gsub("tBody", "TimeBody", names(Table_DF_select)) 
names(Table_DF_select) <- gsub("fBody", "FrequencyBody", names(Table_DF_select))
names(Table_DF_select) <- gsub("tGravity", "TimeGravity", names(Table_DF_select))
names(Table_DF_select) <- gsub("mean", "Mean", names(Table_DF_select))
names(Table_DF_select) <- gsub("std", "StandardDeviation", names(Table_DF_select))
names(Table_DF_select) <- gsub("gravity", "Gravity", names(Table_DF_select))
names(Table_DF_select) <- gsub("angle", "Angle", names(Table_DF_select))


# TASK 5: From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
# use reshape2 package for this task

library("reshape2")
output <- melt(Table_DF_select, id.vars = c("Subject", "ActivityCode"))
final <- output %>% group_by(Subject, ActivityCode, variable) %>% summarize(mean(value))
names(final) <- c("Subject", "Activity", "Measure", "Measure Mean")
write.table(final, file = "submission.txt", row.names = FALSE)


THE END
---