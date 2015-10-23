# Getting-Cleaning-Data-Project-Work
Repository for Code, Readme and Code book that complete the Project Work for Coursera 

Author: Alberto Pellegata, email:alberto.pellegata@gmail.com


## Preambule and sources:

The data used for this project represents data collected from the accelerometers from the Samsung Galaxy S smartphone. 
A full description is available at the site where the data was obtained: 

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones 

source datasets were dowloaded from this address:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

## Project deliverables and high level description:

Starting from the data downloaded the project aimed at achieving the following:
  1. create a tidy data set, 
  2. a link to a Github repository with your script for performing the analysis, and 
  3. a code book that describes the variables, the data, and any transformations or work that you performed to clean up the   data called CodeBook.md

Data available for the project consists basically of measures captured by the smartphone's accelerometer of 30 different subjects while performing 6 different physical activities ( e.g. walking, walking upstairs, sitting, etc..) you can find the full list of activities along with a description of the measures in the Code book.
The original datasets provided us with 561 different measures and we decided to narrow down the amount to 86 keeping only measures of mean and standard deviation of the accelerometer. Therefore, we needed to execute some aggregation functions to provide a more compact view.

In the final Tidy Dataset generated (filename: "submission.txt") you will find an aggregation of mean of the relevant measures considered per each activiy per each subject. For example: subject 1, walking, bodyacceleration, mean VALUE of all observation of this subject performing that specific activity. This resulted in a Data table of 4 variables and 15.480 observaations obtained from 30 subjects performing 6 activities and monitored over 86 measures (6*30*86=15.480)
