## Project Overview

In this project, we work with the client dataset from a fictional bank. Our goal is to predict which clients have left the bank (i.e. churned) and which remained, using data such as credit score, location, tenure duration, account balance and etc. This allows the bank to implement retention programs targeted to clients with higher risk of leaving.

The dataset contains the following columns:

| Variable | Type | Description |
| -------- | ---- | ----------- |
|**Customer Id** | int | Number to designate each client.|
|**Surname** | str | Client's surname.|
|**Credit Score**| int | Client's credit score in the bank.|
|**Geography**| str | Country where the client resides.|
|**Gender**| str | Client's gender.|
|**Age**| int | Client's age.|
|**Tenure**| int | Number of years the client's account has existed for.|
|**Balance**| float| Client's account balance.|
|**Number of Products**| int | Number of products the client has purchased through the bank.|
|**Has Credit Card**| int | If client has a credit card (equals 1) or does not have a credit card (equals 0).|
|**Is Active Member**| int | If the client is an active member of the bank (equals 1) or not (equals 0).|
|**Estimated Salary**| float| Client's estimated salary.|
|**Exited**| int | If the client has left the bank (equals 1) or not (equals 0). This is our target variable.|
|**Complain**| int | If the client has complained to the bank customer services (equals 1) or not (equals 0).|
|**Satisfaction Score**| int | Client's evaluation of their satisfaction with the bank services, varying from 1 (very displeased) to 5 (very pleased).|
|**Card Type**| str | Client's card type. Possible values are Silver, Gold, Platinum or Diamond.|
|**Points Earned**| int | Points earned by the client due to their credit card usage.|

## Project Outline

### Pre-processing

In this section, we standardize column names and string entries, setting them all to lowercase and removing blank spaces. We drop the 'Customer Id' and 'Surname' columns, since they are not relevant for machine learning algorithms. We check if there are any missing values in the dataset that may need to be handled, which is not the case. We also see there are no duplicated rows. Lastly, we convert all categorical variables to the 'category' type. We also convert discrete integer variables, such as 'Exited', 'Complained, and 'Satisfaction Score', to categorical as well.

### Exploratory Data Analysis

We analyse the dataset to understand its main characteristics and relations between features. Firstly, we begin by plotting frequency plots (for categorical variables), histograms and box plots (for numerical variables), to understand the variability in our data. This analysis allows for some conclusions:

- **Credit Score** (Mean: 650.53, Median: 652.0, Skewness: -0.07, Standard Deviation: 96.65) has a nearly normal distribution, which is confirmed by the low skewness. We see there are a few outliers with low credit score, which could be an indicator for churning. The only transformation required for this feature is scaling/standardizing.
- **Age** (Mean: 38.92, Median: 37.0, Skewness: 1.01, Standard Deviation: 10.48) has a highly skewed distribution, showing the client dataset tends towards younger customers between 30 and 40 years old. There could be a relation between age and churning, with younger clients having a larger tendency to exit the bank. It is necessary to apply a logarithmic transformation to the data before standardizing, to reduce skewness.
- **Tenure** (Mean: 5.01, Median: 5.0, Skewness: 0.01, Standard Deviation: 2.89) is uniformally distributed, showing even spread among the possible values. It is necessary only to standardize the data.
- **Balance** (Mean: 76485.89, Median: 97198.54, Skewness: -0.14, Standard Deviation: 62397.40) shows a huge peak at zero, showing many accounts have a null balance. This could be a huge predictor of churning, since it points to clients that do not use their account. The amount of zero-balanced accounts reduce the mean and create a discrepancy between the mean and the median. In this case, we can apply the same principle behind a hurdle model: we create a new binary variable 'HasZeroBalance'. It is 1 for clients with zero balance, and 1 for clients with non-zero balances. This allows us to remove the zero balanced accounts from the Balance distribution. Subsetting the dataset to include only clients with non-zero balance, we find the Balance feature now presents a normal distribution, with 0.02 skewness. Therefore, after creating the 'HasZeroBalance' feature, we will only need to standardize the Balance column.
- **Estimated Salary** (Mean: 100090.24, Median: 100193.92, Skewness: 0.00, Standard Deviation: 57510.49) shows a highly uniform distribution. The client dataset does not favors clients of specific income. The only necessary transformation is standardization.
- **Points Earned**  (Mean: 606.52, Median: 605.0, Skewness: 0.01, Standard Deviation: 225.92) is also uniformly distributed, and does not present outliers. The only transformation necessary is standardization.
- **Geography** shows the majority of bank clients reside in France. It may be an interesting indicator for churning.
- **Gender** is almost evenly split, with a little bit more male clients than female clients.
- **Number of Products** has a high peak at 1, and decreases until 4. The majority of clients does not show intense engagement.
- **Has Credit Card** indicates more than 2/3 of the clients in the dataset have a credit card. The public without a credit card could present a high potential for churning.
- **Is Active Member** is evenly split in the dataset. Inactive clients are more likely to churn.
- **Exited** is our target variable. Around 20% of clients churn, while 80% remain in the bank. This representation imbalance could be harmful to our model, leading to an overrepresentation bias. It will be necessary to oversample the dataset or stratify the training and testing sets.
- **Complain** shows 20% of clients complained to the bank. Clients complaining show a high potential for churning.
- **Satisfaction Score** is evenly distributed, with a small peak at 3. Low scores can be great predictors of churning, but the uniform distribution may harm its predictive power.
- **Card Type** is uniformly split, and is unlikely to be a high predictor of churning.

### Feature Engeneering

Before continuing with our Exploratory Data Analysis, it is important to create the new 'HasZeroBalance' feature, so we can analyse its relation with other features.

### Exploratory Data Analysis Part II

Now, it is possible to conduct a bivariate analysis, focusing on how related are the features in the dataset. Firstly, we study churning rate variation depending on each feature. We begin by plotting a correlation map between numerical and binary variables. We use the Spearman correlation method, once we have many variables with an uniform distribution. This map hints that the most important variables to predict churning are Complain, HasZeroBalance, IsActiveMember, Number of Products, Balance and Age. However, it is still interesting to take a look at each feature individually.

- **Credit Score**: we find a median credit score of 646 among churning clients, and 653 among non-churning clients. Therefore, we see there are no significant data pointing that low credit score users are more likely to churn. For this variable, we chose to use the median instead of the mean due to the presence of many outliers in the credit score distribution, which affects the mean more harshly than the median.
- **Age**: the median age for churning clients is 45 years old, while it is 36 years old for non-churning clients. There seems to be a relation between these features, signaling that older clients are more likely to churn. Once again, we use the median instead of the mean due to the presence of outliers in the age distribution.
- **Tenure**: the mean tenure among churning clients is 4.93 years, while the mean tenure among non-churning clients is 5.03. This seems to imply a relation between these features. However, looking at the box plot, we see the churning quartiles go from 2 years to 8 years of tenure, while the non-churning quartiles go from 3 to 7 years of tenure. Both categories also have the same value of median. This, added to the fact the difference between the mean tenures between both groups is pretty small, makes it safe to assume there is no significant relation between tenure duration and the likely of churning.
- **Balance**: our correlation plot hints at a correlation between the balance value and the churning rate. However, plotting a histrogram of balance divided by clients who churned and clients who didn't, shows the same distribution for both variables. Therefore, a client with a higher balance is not less likely to churn than a client with a moderate balance. The only difference resides in the zero peak, whose information is already encoded in the 'HasZeroBalance' feature. Hence, it is useful to drop the 'Balance' column and employ solely the 'HasZeroBalance' one, to simplify our model.
- **Estimated Salary**: the mean salary among churning users is U$101509.90, while for non-churning customers is U$99726.85. We see no significant relation between Estimated Salary and the likelihood of churning.
- **Points Earned**: clients who churned have a mean of 604.4 earned points, whereas clients who didn't churn have a mean of 607.0 earned points. This implies no predictive power lies in this feature.
- **Geography**: we notice a higher churning rate for customers residing in Germany (0.32 churning rate, against 0.16 churning rate for both France and Spain). Therefore, a client living in Germany has double the chance to churn.
- **Gender**: female clients have a churning rate of 0.25, while male clients present a churning rate of 0.16, even though we have slightly more male customers than female ones. There seems to be a correlation between this feature and the chance of churning.
- **Number of Products**: while clients owning 2 products has a lower churning rate than customers owning only 1 product (0.07 versus 0.28, respectively), clients owning more than 2 products have very high chances of churning; the churning rate for clients owning 3 products is 0.83, and it increases to 1 for clients who own 4 products. This variable holds high predictive power for churning likelihood.
- **Has Credit Card**: while this may seem a highly important feature at first glance, the churning rate for clients who own a credit card is very close to the churning rate for clients who do not own a credit card (0.202 versus 0.208, respectively). This is not a relevant feature for churning prediction.
- **Is Active Member**: the churning rate for active members is 0.14, while for non-active users it is 0.27. This is an important feature.
- **Complain**: the churning rate for clients who have complained is 1. Therefore, every client who churned has complained before leaving the bank. While this variable holds extreme predictive power, it can be hurtful to our model due to the introduction of separation. This is highly problematic for logistic regression models, which we will test in the last section. To avoid statistical instabilities due to this correlation, we will train models both including and excluding this variable, in our Model Selection section.
- **Satisfaction Score**: the churning rate is almost the same for all values of satisfaction score. Hence, it is not a relevant feature for our analysis.
- **Card type**: all categories of card types showed uniform churning rates, and we conclude this is not a relevant variable. This could be expected, since Card Types are often related to the client's Balance (a higher Balance leads to more important card types). Once the Balance feature does not hold predictive power, it is compreehensible the Card Type variable also doesn't.

Lastly, we investigate the relation between Number of Products and Balance. According to our correlation map, there is a certain level of correlation between these features. However, when we plot their relation divided by the categories of churning, we see the correlation only exists for non-churning customers. For non-churning clients, the number of products owned decreases as their account balance grows larger. This is not trivial, since it would be expected high-earning clients to be more likely to acquire new products. Besides, this relationship does not apply for churning customers. Their balance remains almost constant as the number of products owned increases. In a real-life scenario, it would be interesting to collect more data and investigate further into this relation.

This relation can also be studied by plotting the number of products owned categorized by the 'HasZeroBalance' variable. The majority of people who only own one product have non-zero balances. The same applies for those who own 3 or 4 products. However, the majority of clients who own two products are people with zero-balance. This could be a result of the bank offering better benefits to customers with zero balances, to incentivize these clients to use their accounts more. Better studies would be necessary to confirm this hypothesis. The results from this graph points towards the relation observed in the bivariate analysis of the number of products owned and the correlation map: clients who own 2 products are more likely to have zero balances, and are also more likely to churn.

### Data Processing

Now, we apply the insigths obtained above to the data. Firstly, we apply a logarithmic transformation to Age, due to its imbalanced distribution. We check the transformation, and see the skewness of the logarithmic distribution of age is now 0.18. Therefore, we have achieved our goal to normalize this variable. Then, we apply one-hot encoding to the 'Geography' and 'Gender' variables, and split the data into training and testing sets. We make sure to stratify the groups by 'Exited', since there is an overrepresentation of customers who did not churn in the dataset. Hence, stratification will guarantee both groups have equal fractions of clients who did churn. However, this may not be enough to balance our dataset. So, we also apply a SMOTE oversampling technique, that will replicate random examples from the minority class (in this case, clients that churned). Now, we are sure our dataset is balanced between both categories. Lastly, we standardize our data, as to avoid problems caused by the different scales between features.

## Model Selection

In this section, we begin training our models and evaluating their perfomance. While accuracy would be the first choice to evaluate a classification algorithm, it is not indicated for this case, where we have an imbalance in the target value distribution. Instead, it is necessary to employ other metrics, such as precision and recall. For this example, a high recall but low precision means the model will emphasize correctly predicting which customers will NOT churn, at cost of letting clients who will churn pass unoticed. We will not lose money by giving benefits to customers who will not churn, but we may lose clients that were not flagged by the model as potential churners. On the other hand, a high precision but low recall will emphasize correctly predicting which customers WILL churn, on the expanse of labeling as potential churners some clients that would remain in the service anyway. Therefore, we may lose money by giving benefits and discounts to clients who would not leave the service regardless. In real life, it is possible executives will rather one situation or the other, in which case a specific metric would be preferable. However, to be more neutral in our analysis, we evaluate models using their f1-score, as to balance the precision/recall tradeoff.

The models trained were:

- *Logistic Regression*, with a small grid search testing Ridge regularizations with some values of C. We apply regularization as to avoid overfitting to the 'Complain' variable. F1-score of this model is 0.998834.
- *Decision Tree Classifier*, also with a small grid search testing the minimum number of samples in a leaf. F1-score of 0.999103.
- *Random Forest Classifier*, testing the number of trees and the minimum number of samples in a leaf. F1-score of 0.999103.
- *SVC* with a Ridge regularization, testing some values of C. F1-score of 0.997581.
- *Ada Boosting Classifier*, using Decision Trees. We do a small grid search for the number of trees and the learning rate. F1-score of 0.999.
- *Gradient Boosting Classifier* with small grid search for the number of estimators and the learning rate. F1-score of 0.999.

The best models are the Decision Tree Classifier and the Random Forest Classifier. We pick both and test them in our testing dataset. Both models generated f1-score of 0.9992, which highlights they are not overfitting to the training data.

We notice our models present great predictive power, with a really high f1-score for all instances. Once the Complain variable has an almost perfect correlation with the Exited variable, it could be expected that the models would do really well on this. However, it is possible our models are relying to much on the Complain feature, and not flagging clients who churned without complaining, or clients who complained but remained.

So, we re-train our models without the Complain variable, to understand how our algorithms would work without this information. The results were:

- *Logistic Regression* produced a f1-score of 0.709803.
- *Decision Tree Classifier* produced a f1-score of 0.856745.
- *Random Forest Classifier* produced a f1-score of 0.852764.
- *SVC* produced a f1-score of 0.725223.
- *Ada Boosting Classifier* produced a f1-score of 0.831.
- *Gradient Boosting Classifier* produced a f1-score of 0.8619.

As expected, the f1-scores of our models have been greatly reduced by removing the Complain variable. In a real-life situation, it would be interesting to gather more data, specially focusing on clients who churned without complaing or complained without churning, as to increase our models capabilities. The Gradient Boosting Classifier is the best model for this case, and generates a f1-score of 0.8796 in the test set. Therefore, we see there is no overfitting to the training data.


