# amazon-product-rating-model

This project is a group assignment for DSC 102: Systems for Scalable Analytics. The goal of the project is to conduct feature engineering for an Amazon prouduct dataset and use extracted features to train machine learning models using Apache Spark on an DSMLP cluster. The project is separated into eight tasks. The purpose of each task is outlined below. 


## Dataset description

The datasets are no longer available. The schmea of the data is outlined below for the purpose of understanding the code. 

<b> The review table is for extracting the rating information for each product in Task 1. <\b> 

review
|-- asin: string, same as above < br / >
|-- reviewerID: string, the reviewer id, e.g., ‘A1MIP8H7G33SHC’
|-- overall: float, the rating associated with the review, e.g., 5.0

<b> The product table is used mainly throughout Task 1-4. <\b> 

product
|-- asin: string, the product id, e.g., ‘B00I8HVV6E’
|-- salesRank: map, a map between category and sales rank, e.g., {‘Home & Kitchen’: 796318}
| |-- key: string, category, e.g., ‘Home & Kitchen’
| |-- value: integer, rank, e.g., 796318
|-- categories: array, list of list of categories, e.g., [[‘Home & Kitchen’, ’Artwork’]]
| |-- element: array, list of categories, e.g., [‘Home & Kitchen’, ’Artwork’]
| | |-- element: string, category, e.g., ‘Home & Kitchen’
|-- title: string, title of product, e.g., ‘Intelligent Design Cotton Canvas’
|-- price: float, price of product, e.g., 27.9
|-- related: map, related information, e.g., {‘also_viewed’: [‘B00I8HW0UK’]}
| |-- key: string, the attribute name of the information, e.g., ‘also_viewed’
| |-- value: array, array of product ids, e.g., [‘B00I8HW0UK’]
| | |-- element: string product id , e.g., ‘B00I8HW0UK’

<b> The product processed table is used mainly for Task 5-6. <\b> 

product_processed
|-- asin: string, same as above
|-- title: string, title column after imputation, e.g., ‘Intelligent Design Cotton Canvas’
|-- category: string, category column after extraction, e.g., ‘Home & Kitchen’

<b> The following tables are for training and testing <\b>

ml_features_train
|-- features: SparseVector(float), SparseVector of concatenated
features from user and product data (all features are continuous
features)
|-- overall: int, review rating
2. ml_features_test
|-- features: SparseVector(float), same as above
|-- overall: int, same as above


## Purpose of each task 

The focus of tasks 1-6 is to feature engineer the dataset. The focus of tasks 7-8 is to develop and tune a model using these engineered features to predict the user rating for a specific product. 

### Task 1 

The purpose of task 1 is to aggregate and extract some information from the user review table. We want to know the mean and the number of ratings for each product. 

### Task 2 

The purpose of task 2 is to flatten the hierarchical variable 'categories' and the key-value pair structured variable 'salesRank'. 

### Task 3 

The purpose of task 3 is to flatten the variable 'related', which is stored as a map with four keys/attributes. 

### Task 4 

The purpose of task 4 is to imputate the null values in the dataset. For the 'price' variable, we created two new columns to imputate the nulls, using the mean and median of all the non-null values. For teh 'title' variable, we imputated the nulls using the special string "unknown".

### Task 5 

The purpose of task 5 is to transform the 'title' variable into a fixed-length vector via word2vec. We did this by converting each title to lowercase, splitting it by whitespace to an array of strings, stored as a new column, and training a word2vec model out of the new column. For each of the three words inputted, we found the 10 closest synonyms and their similarity scores by using the cosine similarity of word vectors. 

### Task 6 

The purpose of task 6 is to one-hot-encode the categorical features and PCA on these categories. To perform PCA, we reduced the dimension of each one-hot vector to 15 and put the transformed vecture into a new column. 

### Task 7 

The purpose of task 7 is to use the training data and the newly engineered features to train a Decision Tree Regression model to predict the user rating for a product. The max tree depth parameter is set to 5 and all other parameters of the model are left to default values. After generating predictions of the test data, we checked accuracy by calculating the root mean square error (RSME) of the test predictions. 

### Task 8 

The purpose of task 8 is to perform hyperparameter tuning to select the best max tree depth for the model developed in task 7. We created new training and validation data from the original training data using a random split of 75/25. We then trained the models with max tree depth values of 5, 7, 9, and 12 and calculated the RMSE of the validation data predictions of each of the models. We picked the best model using the recorded RMSEs and used the best model to generate predictions on the test data. 
