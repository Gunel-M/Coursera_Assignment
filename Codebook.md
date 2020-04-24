

# load all individual files into variables
 The features variable contains the serial number and functions pertaining to the dataset. The activities variable describes the behaviour of the individual with respect to the activity code
```
    features <- read.table("./UCI HAR DATASET/features.txt", col.names=c("no","functions"))
    activities <- read.table("./UCI HAR DATASET/activity_labels.txt", col.names=c("actv","label"))
```
    
# loading training variables
```
    subject_train <- read.table("./UCI HAR DATASET/train/subject_train.txt", col.names="subject")
    Xtrain <- read.table("./UCI HAR DATASET/train/X_train.txt", col.names=features$functions)
    Ytrain <- read.table("./UCI HAR DATASET/train/Y_train.txt", col.names="code")
```

# loading testing variables
```
    subject_test <- read.table("./UCI HAR DATASET/test/subject_test.txt", col.names="subject")
    Xtest <- read.table("./UCI HAR DATASET/test/X_test.txt", col.names=features$functions)
    Ytest <- read.table("./UCI HAR DATASET/test/Y_test.txt", col.names="code")
```

# row bind train value and test values
Since test and train values belong to the same attributes we must row bind these two data frames.
```
    X_data <-rbind(Xtrain,Xtest)
    Y_data <-rbind(Ytrain,Ytest)
    subject <-rbind(subject_train,subject_test)
```

# column bind the different attributes
```
    dataframe<-cbind(subject,X_data,Y_data)
```

# selecting only the mean and std devitaion variables
```

## run_analysis.R Description
# Load library - dplyr

library(dplyr)
# Enter file name
```
  filename <- "UCI_HAR_Dataset.zip"
```
# check if zip file exits 
```
  if(!file.exists(filename))
    {
      #download and unzip zip file
      download.file("https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip",filename,method="curl")
      unzip(filename)
    }
```



# read train data
# loading training variables
```
X_train <- read.table("./UCI HAR Dataset/train/X_train.txt")
Y_train <- read.table("./UCI HAR Dataset/train/Y_train.txt")
Sub_train <- read.table("./UCI HAR Dataset/train/subject_train.txt")

```

# read test data
# loading testing data
```
X_test <- read.table("./UCI HAR Dataset/test/X_test.txt")
Y_test <- read.table("./UCI HAR Dataset/test/Y_test.txt")
Sub_test <- read.table("./UCI HAR Dataset/test/subject_test.txt")
```

# read data description
variable_names <- read.table("./UCI HAR Dataset/features.txt")

# read activity labels
activity_labels <- read.table("./UCI HAR Dataset/activity_labels.txt")

# 1. Merges the training and the test sets to create one data set.
X_total <- rbind(X_train, X_test)
Y_total <- rbind(Y_train, Y_test)
Sub_total <- rbind(Sub_train, Sub_test)

# 2. Extracts only the measurements on the mean and standard deviation for each measurement.
selected_var <- variable_names[grep("mean\\(\\)|std\\(\\)",variable_names[,2]),]
X_total <- X_total[,selected_var[,1]]

# 3. Uses descriptive activity names to name the activities in the data set
colnames(Y_total) <- "activity"
Y_total$activitylabel <- factor(Y_total$activity, labels = as.character(activity_labels[,2]))
activitylabel <- Y_total[,-1]

# 4. Appropriately labels the data set with descriptive variable names.
colnames(X_total) <- variable_names[selected_var[,1],2]

# 5. From the data set in step 4, creates a second, independent tidy data set with the average
# of each variable for each activity and each subject.
colnames(Sub_total) <- "subject"
total <- cbind(X_total, activitylabel, Sub_total)
total_mean <- total %>% group_by(activitylabel, subject) %>% summarize_each(funs(mean))
write.table(total_mean, file = "./UCI HAR Dataset/tidydata.txt", row.names = FALSE, col.names = TRUE)