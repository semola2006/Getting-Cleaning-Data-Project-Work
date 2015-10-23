# Getting-Cleaning-Data-Project-Work
Repository for Code, Readme and Code book that complete the Project Work for Coursera 

=======================================================
Project Work - Getting and Cleaning data online Course
Author: Alberto Pellegata
email:alberto.pellegata@gmail.com
=======================================================

The data used for this project represents data collected from the accelerometers from the Samsung Galaxy S smartphone. 
A full description is available at the site where the data was obtained: 

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones 

datasets were dowloaded from this address:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip
======================================================
Starting from the data downloaded the project aimed at achieving the following:
1) create a tidy data set, 
2) a link to a Github repository with your script for performing the analysis, and 
3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md

Data available for the project consists basically of measures captured by the smartphone's accelerometer of 30 different subjects while performing a range of activity 
(walking, walking upstairs, sitting, etc..). you can find the full list of activities along with a description of the measures in the Code book.

The number of measures captured by smartphone device is massive and there needs to be some aggregation function to provide a more compact view.

In the final Tidy dataset generated (filename: "submission.txt") you will find an aggregation of mean of the relevant measures considered per each activiy per each subject. For example: subject 1, walking, bodyacceleration, mean VALUE of all observation of this subject performing that specific activity.
