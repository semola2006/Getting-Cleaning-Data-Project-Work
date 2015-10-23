---
title: "Code book"
author: "Alberto Pellegata"
date: "23 ottobre 2015"
output: html_document
---

# Code book for project work Getting and Cleaning Data - October 2015
*Blocks of code with explanations and list of variables*

The code is split into a sequence of TASKS each with specific deliverable, here the list:
 - TASK 0: donwload file, create and set working directory
 - TASK 1: merge datasets
 - TASK 2: extracts only mean and std measures
 - TASK 3: use descriptive names for acitivties
 - TASK 4: use descriptive variable names
 - TASK 5: create a second tify dataset for subject- activity- measure- value


## TASK 0: create a working directory where to store project data
```
if (!file.exists("data")) {
 dir.create("data")
}
setwd("./data")
```

Then download file from the url provided. NB: you can skip this part and start from TASK 1 if you already have the datesets on your local machine and store data of download.
```
fileurl <- ("https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip")
download.file(fileurl, destfile = "./datadownload.zip")
dateDownloaded <- date()
```

NB: Downloaded files are zipped, hence please make sure you MANUALLY extract the downloded zip folder to the "./data" directory, this will enable the following part of code to run properly.

## TASK 1: Merges the training and the test sets to create one data set.
To do this first read the X data (both train and test) in R and rbind the two data tables into a single table "X" (this table expresses the measure derived from smartphones, there are 561 measures). Then remove the original split data tables to clear up memory space.
```
X_test <- read.table("./UCI HAR Dataset/test/X_test.txt", quote = "")
X_train <- read.table("./UCI HAR Dataset/train/X_train.txt", quote = "")
X <- rbind(X_test, X_train)
rm("X_test")
rm("X_train")
```
repeat the above step applying it "Y" (this variable describes activiy performed by the subject, there are 6 activities). Then remove the original split data tables to clear up memory space.
```
y_test <- read.table("./UCI HAR Dataset/test/y_test.txt", quote = "")
y_train <- read.table("./UCI HAR Dataset/train/y_train.txt", quote = "")
Y <- rbind(y_test, y_train)
colnames(Y) <- "ActivityCode"
rm("y_test")
rm("y_train")
```
repeat the above steps applying to "subject" (this varaible describes the subject observed, there are 30 subjects). Then remove the original split data tables to clear up memory space.
```
subject_test <- read.table("./UCI HAR Dataset/test/subject_test.txt", quote = "")
subject_train <- read.table("./UCI HAR Dataset/train/subject_train.txt", quote = "")
subject <- rbind(subject_test, subject_train)
colnames (subject) <- "Subject"
rm("subject_test")
rm("subject_train")
```
read features file (which contains the measures names and is to be used as the label for the metric of table "X")
```
features <- read.table("./UCI HAR Dataset/features.txt", quote = " ")
Vfeatures <- features$V2
Vfeatures <- as.character(features$V2)
```
assign features as colnames of data table "X" and remove the names vector to clear up memory space.
```
colnames(X) <- Vfeatures  
rm("Vfeatures")
```

Since I am going to use the dplyr, we nee to upload the package. (please install the package on your machine if you do not have it already installed with function install.packages()).
```
library(dplyr)
``` 
cbind now the three data tables "X" + "Y" + "Subject" and remove the individual data tables to clare up memory space.
```
Table <- cbind(subject, Y, X)
Table_DF <- tbl_df(Table)
rm("features", "subject", "Table", "X", "Y")
```
## TASK 2: Extracts only the measurements on the mean and standard deviation for each measurement. This will narrow down the number of measure from 561 to 86.
There are invalid column names in the table: before selecting subsest of columns, must apply make.names func()
```
valid_column_names <- make.names(names=names(Table_DF), unique=TRUE, allow_ = TRUE)
names(Table_DF) <- valid_column_names

Table_DF_select <- select (Table_DF, Subject, ActivityCode, contains("mean"), contains("std"))
rm("Table_DF")
```
## TASK 3: Uses descriptive activity names to name the 6 activities in the data set, we use the "activity_label.txt" file provided to convert the levels of this factor from number to descriptive label.
```
Table_DF_select$ActivityCode <- factor(Table_DF_select$ActivityCode, 
                                      levels = c(1, 2, 3, 4, 5, 6), 
                                      labels = c("WALKING", "WALKING_UPSTAIRS", "WALKING_DOWNSTAIRS", "SITTING", "STANDING", "LAYING"))
```

## TASK 4: Appropriately labels the data set with descriptive variable names. Repeatedly using the "gsub"function we first remove dots from column names and then replace abbreviations with full "self explanatory" names. 
This will result in having the 86 measures renamed with (long) but descriptive names. 
```
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
```

## TASK 5: From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject. 
We will use "reshape2" package for this task, hence upload the library (please install the package on your machine if you do not have it already installed).
We first melt the wide data table to a long data table of 4 columns and 15.480 rows. Our id key will be Subject and Activity. The result is a table in which each row corresponds to the subject -> activity performed -> measure name -> mean of the value measured, offering a compact view and an average value of 86 measures derived from observing of the same subject performing that specific physical activity.
The code will also generate a .txt file called submission the captures the code's output.
```
library("reshape2")
output <- melt(Table_DF_select, id.vars = c("Subject", "ActivityCode"))
final <- output %>% group_by(Subject, ActivityCode, variable) %>% summarize(mean(value))
names(final) <- c("Subject", "Activity", "Measure", "Measure Mean")
write.table(final, file = "submission.txt", row.names = FALSE)
```
***
# APPENDIX: list of variable and respective decriptions:
# variable names have been processed in R to provide be self-explanatory, nonetheless, here below a list of all variable you will find in the final Tidy data set.
