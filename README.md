# Cement-Strength-Regression: 

## Table of Content  
  * [Overview](#overview)
  * [Data Description](#Data-Description)
  * [Data Validation](#Data-Validation)
  * [Data Insertion in Database](#Data-Insertion-in-Database)
  * [Model Training](#Model-Training)
  * [Prediction Data Description](#Prediction-Data-Description)
  * [Prediction Data Validation](#Prediction-Data-Validation)
  * [Prediction](#Prediction)
  * [Installation](#Installation)
  * [Technologies & Tools Used](#Technologies-&-Tools-Used)

## Overview
To build a regression model to predict the concrete compressive strength based on the given different features in the training data. 

## Data Description
The client will send data in multiple sets of files in batches at a given location. Data will contain 24 columns: 

| Column Name | Description|
| ----------- |:-------------|
|Cement (component 1) | kg in a m3 mixture |
|Blast Furnace Slag (component 2) | kg in a m3 mixture |
|Fly Ash (component 3) |kg in a m3 mixture|
|Water (component 4) |kg in a m3 mixture|
|Superplasticizer (component 5) |kg in a m3 mixture|
|Coarse Aggregate (component 6) |kg in a m3 mixture|
|Fine Aggregate (component 7) |kg in a m3 mixture|
|Age |Day (1~365) |
|Concrete compressive strength |MPa -- Output Variable |

Apart from training files, we also require a "schema" file from the client, which contains all the relevant information about the training files such as:
Name of the files, Length of Date value in FileName, Length of Time value in FileName, Number of Columns, Name of the Columns, and their datatype.

## Data Validation 
In this step, we perform different sets of validation on the given set of training files. 
 
1. Name Validation- We validate the name of the files based on the given name in the schema file. We have created a regex pattern as per the name given in the schema file to use for validation. After validating the pattern in the name, we check for the length of date in the file name as well as the length of time in the file name. If all the values are as per requirement, we move such files to "Good_Data_Folder" else we move such files to "Bad_Data_Folder."
1. Number of Columns - We validate the number of columns present in the files, and if it doesn't match with the value given in the schema file, then the file is moved to "Bad_Data_Folder."
1. Name of Columns - The name of the columns is validated and should be the same as given in the schema file. If not, then the file is moved to "Bad_Data_Folder".
1. The datatype of columns - The datatype of columns is given in the schema file. This is validated when we insert the files into Database. If the datatype is wrong, then the file is moved to "Bad_Data_Folder".
1. Null values in columns - If any of the columns in a file have all the values as NULL or missing, we discard such a file and move it to "Bad_Data_Folder".

## Data Insertion in Database 
1. Database Creation and connection - Create a database with the given name passed. If the database is already created, open the connection to the database. 
1. Table creation in the database - Table with name - "Good_Data", is created in the database for inserting the files in the "Good_Data_Folder" based on given column names and datatype in the schema file. If the table is already present, then the new table is not created and new files are inserted in the already present table as we want training to be done on new as well as old training files.     
1. Insertion of files in the table - All the files in the "Good_Data_Folder" are inserted in the above-created table. If any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to "Bad_Data_Folder".

## Model Training 
1. Data Export from Db - The data in a stored database is exported as a CSV file to be used for model training.
1. Data Preprocessing   
   1. Check for null values in the columns. If present, impute the null values using the KNN imputer.
   1. transform the features using log transformation.
   1. Apply scaling, remove multi collinearity if any.
   1. Scale the training and test data separately.
1. Clustering - KMeans algorithm is used to create clusters in the preprocessed data. The optimum number of clusters is selected by plotting the elbow plot, and for the dynamic selection of the number of clusters, we are using "KneeLocator" function. The idea behind clustering is to implement different algorithms
   To train data in different clusters. The Kmeans model is trained over preprocessed data and the model is saved for further use in prediction.

1. Model Selection - After clusters are created, we find the best model for each cluster. We are using two algorithms, `"Random forest Regressor"` and `“Linear Regression”`. For each cluster, both the algorithms are passed with the best parameters derived from GridSearch. We calculate the Rsquared scores for both models and select the model with the best score. Similarly, the model is selected for each cluster. All the models for every cluster are saved for use in prediction. 

## Prediction Data Description 
Client will send the data in multiple set of files in batches at a given location.Data will contain 23 columns and we have to predict whether an order will become back order or not. 
Apart from prediction files, we also require a "schema" file from client which contains all the relevant information about the training files such as:
Name of the files, Length of Date value in FileName, Length of Time value in FileName, Number of Columns, Name of the Columns and their datatype.

## Prediction Data Validation  
In this step, we perform different sets of validation on the given set of training files.  
1. Name Validation- We validate the name of the files on the basis of given Name in the schema file. We have created a regex pattern as per the name given in schema file, to use for validation. After validating the pattern in the name, we check for length of date in the file name as well as length of time in the file name. If all the values are as per requirement, we move such files to "Good_Data_Folder" else we move such files to "Bad_Data_Folder". 
1. Number of Columns - We validate the number of columns present in the files, if it doesn't match with the value given in the schema file then the file is moved to "Bad_Data_Folder". 
1. Name of Columns - The name of the columns is validated and should be same as given in the schema file. If not, then the file is moved to "Bad_Data_Folder". 
1. Datatype of columns - The datatype of columns is given in the schema file. This is validated when we insert the files into Database. If dataype is wrong then the file is moved to "Bad_Data_Folder". 
1. Null values in columns - If any of the columns in a file has all the values as NULL or missing, we discard such file and move it to "Bad_Data_Folder".  

## Data Insertion in Database 
1. Database Creation and connection - Create database with the given name passed. If the database is already created, open the connection to the database. 
1. Table creation in the database - Table with name - "Good_Data", is created in the database for inserting the files in the "Good_Data_Folder" on the basis of given column names and datatype in the schema file. If table is already present then new table is not created, and new files are inserted the already present table as we want training to be done on new as well old training files.     
1. Insertion of files in the table - All the files in the "Good_Data_Folder" are inserted in the above-created table. If any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to "Bad_Data_Folder".

## Prediction  
1. Data Export from Db - The data in a stored database is exported as a CSV file to be used for model training.
1. Data Preprocessing   
   1. Check for null values in the columns. If present, impute the null values using the KNN imputer.
   1. transform the features using log transformation.
   1. Apply scaling, remove multi collinearity if any.
   1. Scale the training and test data separately.
1. Clustering - KMeans model created during training is loaded, and clusters for the preprocessed prediction data is predicted.
   To train data in different clusters. The Kmeans model is trained over preprocessed data and the model is saved for further use in prediction.
1. Prediction - Based on the cluster number, the respective model is loaded and is used to predict the data for that cluster.
1. Once the prediction is made for all the clusters, the predictions along with the original names before label encoder are saved in a CSV file at a given location and the location is returned to the client.

## Installation
The Code is written in Python 3.7.5. If you don't have Python installed you can find it [here](https://www.python.org/downloads/). If you are using a lower version of Python you can upgrade using the pip package, ensuring you have the latest version of pip. To install the required packages and libraries, run this command in the project directory after [cloning](https://www.howtogeek.com/451360/how-to-clone-a-github-repository/) the repository:
```bash
pip install -r requirements.txt
```

## Technologies & Tools Used
1. Pandas
1. SKlearn
1. Imbalanced-learn
1. Xgboost
1. Autoimpute
1. Kneed
1. Flask
1. Postman
1. Pycharm
