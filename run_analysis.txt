###Direction
Uci_har <- "UCI HAR Dataset"
activity_labels <- paste(Uci_har, "/activity_labels.txt", sep= "")
features <- paste(Uci_har, "/features.txt", sep="")
x_test <- paste(Uci_har, "/test/X_test.txt", sep= "")
y_test <- paste(Uci_har, "/test/y_test.txt", sep= "")
subject_test <- paste(Uci_har, "/test/subject_test.txt", sep= "") 
x_train <- paste(Uci_har, "/train/X_train.txt", sep= "")
y_train <- paste(Uci_har, "/train/y_train.txt", sep= "")
subject_train <- paste(Uci_har, "/train/subject_train.txt", sep= "") 

##Reading
activity <- read.table(activity_labels)
feature <- read.table(features)
x.test <- read.table(x_test, header= F)
y.test <- read.table(y_test, header= F)
subject.test <- read.table(subject_test, header= F) 
x.train <- read.table(x_train, header= F)
y.train <- read.table(y_train, header= F)
subject.train <- read.table(subject_train, header=F)

##Merges the training and the test sets to create one data set.
tableFeat <- rbind(x.test, x.train)                 ##data set
tableActiv <- rbind(y.test, y.train)                 ##labels
tableSub <- rbind(subject.test, subject.train)

##Name the activities
names(tableSub)<- c("subject")
names(tableActiv)<- c("activity")
names(tableFeat) <- feature$V2
comb <- cbind(tableSub, tableActiv)
Data <- cbind(tableFeat, comb)

##Extracts only the measurements on the mean and standard deviation for each measurement. 
exst <- feature$V2[grep("-mean\\(\\)|-std\\(\\)", feature$V2)]
tablenew <- Data[ ,exst]
selectedNames<- c(as.character(exst), "subject", "activity" )
Data<- subset(Data, select=selectedNames)

##Appropriately labels the data set with descriptive variable names. 
names(Data)<- gsub("^t", "time", names(Data))
names(Data)<- gsub("^f", "frequency", names(Data))
names(Data)<- gsub("Acc", "Accelerometer", names(Data))
names(Data)<- gsub("Gyro", "Gyroscope", names(Data))
names(Data)<- gsub("Mag", "Magnitude", names(Data))
names(Data)<- gsub("BodyBody", "Body", names(Data))

##From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
library(plyr)
Dataf<- aggregate(. ~subject + activity, Data, mean)
Dataf<- Dataf[order(Dataf$subject, Dataf$activity), ]
write.table(Dataf, file = "tidydata.txt",row.name=FALSE)











