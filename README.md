# Getting-and-Cleaning-Data-Course-Project

The script works as the following:

1.) Load library
```
library(dplyr)
```

2.) Get a list of features name from features.txt and filter only features containing "mean" or "std"
```
features <- read.table("features.txt")
number_of_features <- dim(features)[1]
selected_column <- rep("NULL", number_of_features)
included_features <- grep("mean|std", features$V2)
included_features_name <- grep("mean|std", features$V2, value = TRUE)
selected_column[included_features] <- "numeric"
```

3.) Get a list of training and testing data and merge them together
```
data_train <- read.table("train/X_train.txt", colClasses = selected_column)
data_test <- read.table("test/X_test.txt", colClasses = selected_column)
data_all <- rbind(data_train, data_test)
```

4.) Modify descriptive variable names (e.g., t to Time, f to Freq, -mean()- to Mean)
```
included_features_name <- gsub("^t", "Time", included_features_name)
included_features_name <- gsub("^f", "Freq", included_features_name)
included_features_name <- gsub("-mean\\(\\)-?", "Mean", included_features_name)
included_features_name <- gsub("-meanFreq\\(\\)-?", "MeanFreq", included_features_name)
included_features_name <- gsub("-std\\(\\)-?", "Std", included_features_name)
names(data_all) <- included_features_name
```

5.) Get a list of training and testing label and merge them together
```
activity_train <- read.table("train/y_train.txt")
activity_test <- read.table("test/y_test.txt")
activity_all <- rbind(activity_train, activity_test)
names(activity_all) <- "Activity"
```

6.) Replace activity number (1, 2, 3) with the label ("WALKING", "WALKING_UPSTAIRS", "WALKING_DOWNSTAIRS")
```
activity_labels <- read.table("activity_labels.txt", colClasses = c("NULL", "character"))
activity_labels <- unlist(activity_labels, use.names = FALSE)
activity_all$Activity <- activity_labels[activity_all$Activity]
```

7.) Get a list of training and testing subject and merge them together
```
subject_train <- read.table("train/subject_train.txt")
subject_test <- read.table("test/subject_test.txt")
subject_all <- rbind(subject_train, subject_test)
names(subject_all) <- "Subject"
```

8.) Combine everything together (subject, activity, data)
```
result <- cbind(subject_all, activity_all, data_all)
```

9.) Group them by subject and activity, and calculate mean
```
result <- group_by(result, Subject, Activity)
summarized_result <- summarize_each(result, funs(mean))
```

10.) Write table to file
```
write.table(summarized_result, "summarized_result.txt", row.names=FALSE)
```
