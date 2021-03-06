# Mercari Price Suggestion Challenge

## 1.	Problem statement

<p align="justify"> A Regression problem: Construct a trained model with optimized algorithm, which can automatically predict the most accurate price of a given product based on the specific features/information provided about this product.  </p>

## 2.	Background

<p align="justify"> Several online trading companies or online shopping websites which sell a wide range of items need to suggest the prices automatically to their items. This process need to be automated because the items are just so many to be done by human beings and also getting it done by human being is very slow and expensive. So Artificial Intelligence and Machine Learning has a big application here. The Biggest Challenge here is that the price of items is dependent on the type of item and there are tens of thousands of different types of item. Added to this complexity prices of similar items can vary a lot based on their brand, condition of item, specifications of the item and so on. </p>

<p align="justify"> The most useful variables for predicting the price (item name, brand name, category, and description) all of them are unstructured text data and the target variable is a continuous numerical variable. Thus, due to this complication this problem becomes even more challenging. So, to deal with this here I have used the text analytics tools to derive the useful term frequency related new variables from this unstructured text data which can help us predict the prices more accurately and also the training and prediction steps can be done in a time and computation efficient manner. The text data where the complete text is important (e.g. brand name) they were label encoded so that the processing and computation is faster with these new label encoded variables.  After basic pre-processing steps many different algorithms, ranging from basic statistical method such as generalized linear model to advanced neural networks, which were suitable for regressions were employed and tested. The algorithm selection was done using n-fold cross-validation method to obtain the minimum bias and minimum variance of the trained algorithm. Then tuning of different parameters for the selected model was performed to select the most optimum parameters. Finally the most optimum model with best performance was build and tested on the given test dataset.  </p>

## 3.	Evaluation metric or Error metric 

<p align="justify"> Since it is a regression problem, we will have a squared error related metrics as the error metric to minimize for making accurate predictions. As our product price has a very broad range from tens to thousands, we have to use a transformed version of squared error metric so that we do not get biased for accuracy on the products with high price. Thus, I have used the “Root Mean Squared Logarithmic Error” (RMSLE) as the error metric to minimize (also suggested at the kaggle competition page) for this particular project. The mathematical formula for this error metric is the following:  </p>

![Alt text](https://github.com/shubhamj1510112/Mercari-Price-Suggestion-Challenge/blob/master/error_metric_formula.png)

n = number of observations; pi = prediction from model for ith observation ; ai = actual price of ith observation.

**Please Note that the complete r code, figures, and tables are uploaded as separate files and are referened at appropriate places in this report**

## 4.	Variable encoding

<p align="justify"> Note: The complete r code for this section is provided as the variable_encoding.R file and figures/tables are uploaded separately. So after setting up the path you can run the complete r code but it may take some time to run (please check for r version or package availability related errors as they vary from system to system anyways the used versions are specified in the comments of r script). </p>

<p align="justify"> In this particular dataset I had four columns with unstructured text data like name, category, brand name, and item description. Moreover, these columns also had a lot of special characters which needed to be taken care. Also almost all of the variables had the missing values. Thus, to resolve these issues this particular step of label encoding was performed. Here I used many tools and techniques from text analytics and dummy variable encoding. Now let’s see this step in detail for each of the variable. </p>
 
Figure 1: Percent missing values plotted for each variable. Brand name has highest amount of missing values (>40%)

![Alt text](https://github.com/shubhamj1510112/Mercari-Price-Suggestion-Challenge/blob/master/Figure-1.jpeg)


<p align="justify"> Table 1: Missing value summary for each variable. </p>

![Alt text](https://github.com/shubhamj1510112/Mercari-Price-Suggestion-Challenge/blob/master/Table-1.JPG)


<p align="justify">  I observed that every predictor variable has missing values therefore; proper missing value treatment is required. In data there was an identifier number for each training observation (train_id). As this has no predictive power so it was just kept there for reference but not used in modelling. </p>

### 4.1 First variable: Item Name (predictor variable)

<p align="justify">  This variable had the name of the item written in the text format. This variable is very important as the name of the item is one of the main factors in deciding its price. For example a computer will have price Rs. 35,000 or more whereas a pen will have the price of Rs. 10 or more, so the name is deciding the price. So I had to use this variable for prediction and since it is a categorical variable I needed to perform the dummy encoding for it so that it can be used in regression modelling. This is because most regression algorithm has this requirement that they perform very well on numeric data (except a few like decision trees). 
The problem with this variable was that many item name appeared only one or very few times and in many cases the same item was given different names like pen, stationary pen, PEN and so on. Also the name had many special characters which may produce error at the time of training. So first I removed all the special characters using “gsub” command in r than I applied the text analytics tools to perform other preprocessing like covert to lower case, stemming, and lemmatization. Here I did not remove number because it has significance in predicting price like 16GB pen drive cost less than 32 GB pen drive. Then I used “term frequency” metric to extract the item names most frequent in the dataset and they were dummy encoded. Due to the computational limitations only top 50 item names were selected for now but same code can be used to select more or any desired number. This step was useful as now this will make our algorithm more focused on accurately predicting the prices of the items which are most frequent in the business/market.</p>

As a result of this step now we have 50 variables in our dataset for now: 
50 (item name dummy encoded) = 50

### 4.2 Second variable: item condition (predictor variable)

<p align="justify"> This variable tells about the condition of the item available for purchase and rates it from 1-5. This variable is very useful in predicting the price as the item in good condition will have more price in comparison to item with poor condition. This variable was already very well encoded and was in numeric data format, although it was kind of a factor ordinal variable. The only problem with this variable was missing values: >4000 observations had missing values for this variable. I used the imputation method as I cannot throw out this high amount of observations. I compared different methods like k-nearest neighbours, random forest, mean, median, and mode on the sample data. I found that slower methods like knn and random forest were performing similar to faster methods like mean, median, and mode. So I compared only the faster methods like the mean, median, and mode for the complete data and found that median imputation was performing better. Therefore, I imputed all the missing values for this variable with the median value of “2” item condition rating. Now this variable is ready to be used for modelling.  </p>

As a result of this step now we have 51 variables in our dataset for now: 
50 (item name dummy encoded) + 1 (item condition)= 51

### 4.3 Third variable: category name (predictor variable)

<p align="justify"> This variable defined the category for each item or observation. This had three different levels of categories and they were in the hierarchical order. Therefore, I could create three variables (one for each hierarchy) from this one variable. I named them as category-1 (cat1 in scripts), category-2 (cat2 in scripts), and category-3 (cat3 in scripts). This splitting of one column into three columns was performed using the “reshape2” library in r. Also the missing category values were imputed with the “unknown” value.
Now in for cat1 I performed the dummy variable encoding for this variable. After that as many categories had interactions which needed to be encoded for example in summers the winter clothes prices will be low for both men’s and women’s. Also I needed to control the number of predictor variables because if I use to many then training of our model will be very tedious and computationally expensive. Also many times having to many variables is not good for making accurate predictions. So to tackle all these problems I performed the Principal Component Analysis (PCA) on the cat1 dummy encoded variables. PCA will generate new variables from the input variables and good thing about PCA is that these new variables (called PCs in the script) are linearly independent and orthogonal and also encode the interaction between input variables. Usually we select the number of PCs using which we can explain the >80% of variance of our complete data, from the scree plot (pasted below) I found that eighth PCs were enough to explain >80% of variance, so I selected eight PCs for further analysis. </p>
 
<p align="justify"> Figure 2: Principal Components (PCs) were plotted against the cumulative variance of data explained. I observed that eight PCs are enough to explain the >80% variance present in the data </p>
 
 ![Alt text](https://github.com/shubhamj1510112/Mercari-Price-Suggestion-Challenge/blob/master/Figure-2.png)
 
 

As a result of this step now we have 60 variables in our dataset for now: 
1 (train_id) + 50 (item name dummy encoded) + 1 (item condition) + 8 (PCs encoding cat1)= 60

<p align="justify"> Now in for cat2 I could not perform the dichotomous dummy variable encoding because it had too many categories, so for this variable I used the label encoding strategy.  In this label encoding strategy each category value was assigned a numeric rank. To assign this rank for each category value the mean price value was calculated for each category and then sorted numerically. So you can say that these category levels were numerically encoded based on their mean price value. This same strategy was also applied to label encode the cat3 variable because this also had too many categories to be dummy coded.</p>

As a result of this step now we have 61 variables in our dataset for now: 
50 (item name dummy encoded) + 1 (item condition) + 8 (PCs encoding cat1) + 1 (label encoded cat2) + 1 (label encoded cat3) = 61

### 4.4 Fourth variable: brand name (predictor variable)

<p align="justify"> This variable defines the brand name for the items. This is also a text variable or categorical/nominal variable. The major problem with this variable is that it has a lot of missing values (>600,000 observations or >40% of training dataset). This is also expected as many items are not branded and hence, will not have brand name. So, all the missing values were imputed to “unknown” brand name. This variable had a lot of levels due to a lot of different brand name for different items, because of this dummy variable encoding was not possible for this variable. Thus, the label encoding strategy (as also used for cat2 and cat3) was utilized to encode this particular variable. In this label encoding strategy the brands were ranked based on the mean price values of their items and now these ranks were used as a numeric vector or a numeric predictor variable for predicting the price. </p>

<p align="justify"> As a result of this step now we have 62 variables in our dataset for now: 
50 (item name dummy encoded) + 1 (item condition) + 8 (PCs encoding cat1) + 1 (label encoded cat2) + 1 (label encoded cat3) +1 (label encoded brand_name)  = 62 </p>

### 4.5 Fifth variable: shipping (predictor variable)

<p align="justify"> This variable was easy to handle as this has only two possible values, one shipping cost from seller, and the other, shipping cost from buyer. This variable was already kind of dummy coded so no extra efforts were required. However, it had a lot of missing values (>4000). All these missing values were imputed with the most common shipping mode which was shipping cost paid by buyer.</p> 

<p align="justify"> As a result of this step now we have 63 variables in our dataset for now: 
50 (item name dummy encoded) + 1 (item condition) + 8 (PCs encoding cat1) + 1 (label encoded cat2) + 1 (label encoded cat3) +1 (label encoded brand_name) + 1 (shipping) = 63 </p>

### 4.6 Sixth variable: item description (predictor variable)

<p align="justify"> This variable was most difficult to handle, as this was the most unstructured form of text data. This also contained a lot special characters (including some emoji characters). So at first all the special characters were removed. Then text preprocessing tools were used to covert complete text into lower case and to remove punctuation marks and whitespaces. Then stemming and lemmatization was performed. Further, most important words which describe the quality of item, such as great condition, awesome, brand new, and so on, were extracted. Now term frequency of these selected words was used to derive the novel dummy coded variables for each of these words. In total, fifty most frequent words were selected so I had fifty new dummy coded variables extracted from this one item description variable.</p>

<p align="justify"> As a result of this step now we have 113 variables in our dataset for now: 
50 (item name dummy encoded) + 1 (item condition) + 8 (PCs encoding cat1) + 1 (label encoded cat2) + 1 (label encoded cat3) +1 (label encoded brand_name) + 1 (shipping) + 50 (item description dummy encoded) = 113</p>

### 4.7 Seventh variable: Price (target variable)

<p align="justify"> This is our target variable which we want that our final trained model should be able to predict with very high accuracy. As this is a continuous variable I have perform the regression modelling or this is a regression problem in machine learning terms. This variable had >4000 observations with missing values. As this is our target variable I could not impute it at the training stage, so I removed all the observations/items with missing price value. </p>

<p align="justify"> As a result of this step now we have 114 variables in our dataset for now: 
50 (item name dummy encoded) + 1 (item condition) + 8 (PCs encoding cat1) + 1 (label encoded cat2) + 1 (label encoded cat3) +1 (label encoded brand_name) + 1 (shipping) + 50 (item description dummy encoded) + 1 (Price) = 114 </p>

<p align="justify"> After this variable encoding stage in the final file we have 113 predictor variables (all numeric as either dummy encoded or label encoded) and 1 target variable. </p>

## 5.	Data Preprocessing

<p align="justify"> Note: The complete r code for this section is provided as the modeling.R file and figures/tables are uploaded separately. So after setting up the path you can run the complete r code but it may take some time to run (please check for r version or package availability related errors as they vary from system to system anyways the used versions are specified in the comments of r script). </p>

<p align="justify"> After the variable encoding I have all our data in numeric format (with all the information of categorical variables retained) which is very good as all the algorithm accepts numerical data and perform very well on the numeric data. Also computation with numerical data is much faster in comparison to categorical text data. Therefore, the variable encoding is always advisable for any machine learning project. Now this numerical data still need some preprocessing before I could use this for training like remove useless variables, remove multicollinearity, treat outliers, and variable selection. So in this section we talk about these preprocessing steps. </p>

### 5.1	 Splitting into train and test dataset

<p align="justify"> This step is very important to truly evaluate the trained algorithm especially in the context of bias and variance related issues. In this step I kept some part of our data as blind set which the algorithm will never see, not even at the cross-validation stage. Here I used the “random sampling without replacement” strategy to divide the complete data into train and test datasets. I kept 60% data for training and 40% data for testing as a blind set. This kind of split was done because already data was very large and I believe that a good algorithm should be able to learn the useful patterns (crucial for prediction) from reasonable amount (as minimum as possible) of data and 60% of the total data looked to be good enough.  </p>


### 5.2	 Remove useless variables

<p align="justify"> This step is performed to remove all the variables which will have nearly no predictive power towards the prediction of our target variable. This filtering is required as this will improve the performance of our model and it will reduce the time and computational cost of the project significantly. Here I removed all the variables which have the ratio of most frequent value to second most frequent value to be more than 20, as this value to be more than 20 indicate that there is a nearly constant value for that particular variable among all the observations. Also I removed the variables which do not show variability above a particular threshold (10%) across the training set observations. Some variables were kept immune to this filtering as they had only two or less than five levels and they should not be filtered with this method. Using this method I could filter 63 variables and for further processing I was left with only 50 predictor variables.</p>

### 5.3	 Remove multicollinearity

<p align="justify"> Multicollinearity occurs when one or more predictor variables are highly correlated with the other predictor variables. It means that two or more variables carry similar information regarding the prediction of target variables. This kind of redundant variables need to removed and only one representative should be kept. This step is essential because if multicollinearity problem is not treated well then especially in the regression modelling the results are not accurate and very unstable. This is predominantly because the coefficient estimation for the regression model cannot be performed with reasonable accuracy if two or more predictor variables carry redundant information. I used the VIF value (results not shown as they were very similar to results from caret package functions) and the different functions of “caret” package to detect the multicollinearity in our training data. After the filtering I found that our data did not have multicollinearity problem so after this stage I had 50 predictor variables for making predictions about our target variable. This observation will be clearer after visualising the “Correlogram” pasted below.</p>
 

<p align="justify"> Figure 3: Correlogram showing the correlation of different predictor variables with each other. Here the target variable is also included for the sake of visualization. None of the predictor variable showed correlation greater than 0.85 with any other predictor variable.</p>

![Alt Text](https://github.com/shubhamj1510112/Mercari-Price-Suggestion-Challenge/blob/master/Figure-3.jpeg)


### 5.4	 Outlier treatment

<p align="justify"> Outliers are the specific values in particular variable which has values very different from the general distribution of the variable. These values need proper treatment otherwise they will negatively affect the accuracy and predictive power of our regression model. First I detected the outliers using my custom created function: this function will iterate through values and will identify values which are outside the outer fence of variable distribution. After detection these outlier values were treated using a custom function, this function will iterate through all the outlier values and replace them with the outer fence values. In this approach, this information is still conserved that specific observations have very high values for specific variables but now these values will not affect our regression analysis. The variables which are label encoded and have more than 500 levels like brand name were not used for this outlier analysis. This is because we know that some brands are very rare and very expensive but they cannot be merged with other brands. Also, this outlier treatment was not performed on the “Price” variable as this is our target variable. Out of 50, 43 predictor variables had outliers and they were treated successfully in this step. </p>

### 5.5	 Variable Importance

<p align="justify"> In this step I evaluated the importance of each predictor variable in making accurate predictions for the target variable. This step is crucial to reduce the computational cost of the project and also avoid resources wastage. In literature, this step has also been shown to lead an improvement in the model’s accuracy and predictive power. I have used two methods of variable importance, one is “Backward feature selection” implemented as ‘rfe’ function in caret, and other using random forest. Only results for random forest are shown here as it performed better than the other method. After this step I observed that 22 variables out of the 50 were most important in making accurate predictions of target variable. Thus, I considered only these 22 predictor variable for our regression modelling step. The line chart displaying importance of different variables using random forest method is pasted below.</p>
 
<p align="justify"> Figure 4: Line plot displaying the overall importance value for the 50 predictor variables. The overall importance values were calculated based on the mean decrease in RMSE value and the mean decrease in Gini values using random forest in “caret” package in r.</p>

![Alt Text](https://github.com/shubhamj1510112/Mercari-Price-Suggestion-Challenge/blob/master/Figure-4.jpeg)


<p align="justify"> After completion of this step of data preprocessing, we now have excellent data for regression modelling with 22 predictor variables and one continuous target variable.</p>

## 6.	Regression modelling

<p align="justify"> Note: The complete r code for this section is provided as the modeling.R file and figures/tables are uploaded separately. So after setting up the path you can run the complete r code but it may take some time to run (please check for r version or package availability related errors as they vary from system to system anyways the used versions are specified in the comments of r script). </p>

<p align="justify"> Now as our data is completely pre-processed and well set for the regression modelling I needed to perform the selection of appropriate algorithm using sample data. Then I need to tune the selected algorithm on the sampled data so that best performance of this selected algorithm can be achieved. And both of these steps need to be done with 5-fold cross validation so that I can keep an eye on the bias and variance of the algorithm at each stage. Then I trained this algorithm on the complete training data and evaluated its performance on the blind dataset which I created in the previous step. As the algorithm has never seen this blind set data I could use its performance on this blind set to get an idea like how my algorithm will perform on the actual real world data. All these steps are discussed in detail below.</p>

### 6.1	 Algorithm Selection

<p align="justify"> As our task is a regression problem, accordingly, I made a list most optimal machine learning algorithms which represent different modelling strategies like decision trees, generalized modelling, bayesian modelling, neural networks and so on. These selected algorithms were: random forest, extreme gradient boosting, relaxed lasso, support vector machines with polynomial kernel, CART, k-nearest neighbours, bayesian additive regression trees, boosted generalized linear models, neural networks (multilayer perceptron), stochastic gradient boosting machines. These algorithms were compared for their performance based on RMSLE value across the 5-fold cross validation. Here the use of 5-fold cross validation was very important because this is the most efficient way I could compare these algorithms for their bias and variance. High bias of an algorithm represents that the algorithm has too many assumptions to be met while training and while making predictions which leads to poor performance. This is one of the major causes of underfitting by a particular algorithm. On the other hand, high variance shows that algorithm did not learn any pattern for the data but just memorized the training data and because of this it shows very good performance on training data but very poor performance on real data, this is a major cause of overfitting by a particular algorithm. The bias and variance can vary from algorithm to algorithm for the same training data because different algorithm has different parameters to be estimated during training and these parameter are crucial in defining the overfitting or underfitting by an algorithm. Therefore, I compared these algorithms to select the best algorithm with highest performance and very low bias and very low variance for our regression problem. I observed that SVM with polynomial kernel had the best performance with very low bias and very low variance but this algorithm could not be trained on the complete data due to computational limitations. So for fast training and fast implementation purposes the next best performing algorithm xgBoost was selected and this algorithm also had acceptable very low variance and very low bias. So for further analysis xgBoost algorithm was selected in this step. A box plot showing the performance of these different algorithms on 5-fold cross validation is pasted below.</p>
 
<p align="justify"> Figure 5: Box plot displaying the performance of different algorithms in the 5-fold cross validation testing. Size of the box and length of the whisker show the amount of variance, whereas the place of box according to the y-axis represents the bias.</p> 

![Alt Text](https://github.com/shubhamj1510112/Mercari-Price-Suggestion-Challenge/blob/master/Figure-5.jpeg)


### 6.2	 Algorithm tuning

<p align="justify"> As mentioned above xgBoost algorithm was selected for further analysis. We know that this algorithm can outcompete other algorithms because of its high accuracy, high efficiency, fast training and prediction, and high feasibility. Still I needed to fine tune this algorithm and select the most optimal parameters so that I can achieve highest performance from this algorithm using our training dataset. There are many parameters for this algorithm which could be tuned. Among the tree and linear version of this algorithm I used the tree version of this algorithm, this parameter is called as general parameter. Now among the booster parameters I could change many parameters like eta, gamma, max_depth, min_child_weight, max_delta_step, subsample, colsample_bytree, and nrounds. But I chose to optimize only max_depth and nrounds parameters because they affected the performance of the algorithm at the maximum in comparison to other parameters for this particular regression problem. We know that this parameter tuning is very much problem specific and I found that varying these two parameters could get me good results so I optimized these two parameters. After trying many different values I found that max_depth value of 5 and nrounds value of 25 gave the most optimal results in terms of RMSLE error metric value. Moreover, model at these two specific parameter values also showed acceptable low bias and low variance. Thus, I selected the max_depth value of 5 and nrounds value of 25 for further analysis and for building the final model on the complete training dataset. The box plot showing the comparison of different parameters for xgBoost is paste below.</p>
 
<p align="justify"> Figure 6: Box plot displaying the performance of xgBoost on different values of max_depth and nrounds in the 5-fold cross validation testing. On x-axis label the first value is max_depth parameter value and the next value is nrounds parameter value.</p>

![Alt Text](https://github.com/shubhamj1510112/Mercari-Price-Suggestion-Challenge/blob/master/Figure-6.jpeg)


### 6.3 Training of final xgBoost model

<p align="justify"> Now using compete training data (60% of total data for stage-1 of competition) I trained the xgBoost algorithm on the selected most optimal max_depth parameter value of 5 and nrounds parameter value of 25. The best thing about this algorithm and these optimized parameters is that it could be trained in less than one minute. This property is very important in the cases where data is very dynamic and added in real time or every hour and now the algorithm needs learn and update very quickly according to this new data. This final model was created using R version 3.3.2 so it can be loaded on any machine with R version 3.3.2 and can be used for making prediction on new data. As I have mentioned that this can be trained within one minute I am providing the script and data with this submission so within one minute this model can trained for may machine with any version of R software. The final trained model is saved and provided with this submission (as .RData file) which can be loaded and used for making prediction on any new data.</p>

### 6.4	 Evaluation of trained data

<p align="justify"> Now the final trained model needed to be evaluated for its performance on the real world data. So as we discussed before I kept the 40% data as the blind dataset which the algorithm has never seen before. Thus, I used this 40% data to make predictions and then compared their actual price value with the predicted price value. Now from these actual and predicted values I could calculate the RMSLE error metric value. In the script the code is shown for making predictions on the 10,000 observations but the same code can be used for making predictions on the complete blind dataset. This test data was also preprocessed (removing useless variables, selecting important variables, outlier treatment and so on) so that it matches to the data format used for the training of algorithm. The code for this preprocessing is also provided in the script. From the predictions on this blind dataset (sample: 10,000) and comparison with actual price values I obtained the RMSLE error metric values of 0.6236 which is acceptable. 
Further I preprocessed the complete stage-1 and stage-2 data (on a multicore cluster with high RAM: results not shown here) and used this complete data for model training except the blind set of only 10,000 observations. Now this time as the training set was larger our model could train really well. This time with this blind dataset of only 10,000 observations I obtained the RMSLE value of 0.401, which shows that I would have got silver medal if participated in the competition.</p>

<p align="justify"> This evaluation altogether shows that this strategy of variable encoding, data preprocessing, algorithm selection, algorithm tuning, and finally algorithm training was successful in making reasonably good predictions at a very high speed (as the prediction for 10,000 observations of blind set could be done in less than 30 seconds). This shows that this model can be trained in real time with the dynamic data (added every hour) and applied in real time for making prediction of prices for thousands of new items added on the shopping application every hour.</p>

## 7.	Predictions on kaggle test data for upload

<p align="justify"> The complete r code for this section is provided as the kaggle_actual_testdata.R file and figures/tables are uploaded separately. So after setting up the path you can run the complete r code but it may take some time to run (please check for r version or package availability related errors as they vary from system to system anyways the used versions are specified in the comments of r script). </p>
 
<p align="justify"> At kaggle, to obtain the leaderboard ranking, I needed to make prediction on the test data provided at kaggle. For this test data price variable is not provided and I needed to predict the prices using my trained model. Then, they will look at my file and calculate the RMSLE value and based on this value they will assign the leaderboard ranking. I downloaded this kaggle test data and preprocessed it so that I could create the same variables for this test dataset as were used at the time of training of algorithm. Then the predictions of price for this test data were made using my trained xgBoost model. Now as the competition is over I cannot upload my results on kaggle but I am attaching the same file with this submission (file name: test_results_for_kaggle.xlsx or test_results.csv). In this file format only two columns are mentioned the “test_id” and their “Predicted_Price” (as per the requirement of kaggle). However, the complete information about these test observations can be fetched from “test.tsv” file using the “test_id” values.  </p>

## 8.	Discussion

<p align="justify"> In this particular problem “Mercari Price Suggestion Challenge” I was asked to construct a model which can make the price prediction about any item for which particular information (variables) is provided. I made a trained xgBoost model which could make these price predictions. My model obtained a very good performance on the blind dataset and the real world dataset from kaggle. The typical features of this model is that it can be trained in less than one minute and it can make predictions for more than 10,000 observations in less than 30 seconds with reasonable accuracy. Therefore, this model can be used in real time where it can be updated every hour, as the more and more of new data is available and also it can be utilized in real time where it can keep predicting the price for all new items added to the shopping app.</p>
 
<p align="justify"> Still there are some improvements which could be done, and I could not implement them due to some time and resource limitations. Firstly, while performing dummy encoding for “item_name” and “item_descritption” variables I had to choose only top 50 terms for my further analysis because if I increase more, my system cannot handle such large data. This is where I felt the need of cloud computing were we can get a large RAM and huge processing power to analyse and model such large data. Given with this facility I can add these more variables and this will essentially improve the model performance. Additionally, I performed the complete coding in R which is sometimes very slow and not very memory efficient. Moreover, some algorithms like neural networks can be much customized to obtain good performance like by changing the number of hidden layers and trying out different activation functions and so on, but all this is not possible in R. For that I am now working on to perform this complete project in python so that I can have access to all these parameters of this powerful algorithm and also trying to use some of the more advance tools of deep learning to obtain better performance. Soon, I will also upload this python project on the Gihub repository.</p>

<p align="justify"> Another advantage of using python for this task is that deployment (online or integration to existing software) becomes very easy because python has very strong API versions which can be very easily exploited for deployment purposes. Also python has tools like ”flask” which can be used for the web based deployment this compete model which can be directly consumed by the end users.</p>

## 9.	Conclusion

<p align="justify"> In this project a machine learning model has be constructed which can predict the price of any item based on its particular features. The input data was very noisy and had a lot of unstructured text data, so the data filtering and preprocessing was performed and also some text analytics tools were utilized for this purpose. Further, the variables were filtered based on their predictability for price variable and also multicollinearity and other problems were taken care. Further, different algorithms were compared using their performance, bias, and variance on the training data to select the most appropriate algorithm for this task. Now the selected algorithm is further tuned for its most optimized parameters to obtain best possible performance. Then final model was trained using optimized parameters and its performance was evaluated on the blind dataset and the kaggle test dataset. On the blind set using only 60% of stage-1 data it showed RMSLE value of 0.62, whereas using the complete data of stage-1 and stage-2 (processed on a multicore cluster with very high RAM) for training, on the blindest it showed the RMSLE value of 0.40. All the supporting files and r scripts are attached with this submission some code is commented out as that was run on the cluster but its output is provided and loaded directly so that rest of the script can run smoothly. </p>

## 10. Declaration

<p align="justify"> I certify that this complete project was performed solely by me and the report presented here is novel and free from plagiarism </p>

Thanks, <br />
Yours sincerely, <br />
Shubham Kumar Jaiswal <br />
email: shubhamj.jaiswal89@gmail.com
  




