## Project Overview

In this project, we work with a datased that correlates social media usage, sleep quality, stress levels and exercise habits to a Happiness Index, which ranges from 1 to 10. We aim to build a Machine Learning that can predict a person's happiness levels by analysing their routines. 

The dataset contains the following columns:

| Variable | Type | Description |
| -------- | ---- | ----------- |
|**User_ID**| int | number to designate each participant in the survey.|
|**Age**| int | participant's age.|
|**Gender**| str| participant's gender (Male, Female or Other).|
|**Daily Screen Time (hrs)**| int | average number of hours the participant uses digital screens.|
|**Sleep Quality**| int | how the participant rated their sleep quality, ranging from 1 (terrible) to 10 (perfect).|
|**Stress Level**| int| how the participant rated their stress level, ranging from 1 to 10. In the source material, there is no explanation on how the score was calculated. Therefore, we do not know for sure if 1 is a very low stress level or a very high stress level. For this work, we will assume 1 is very low stress, and 10 is a terrible stress level.|
|**Days without Social Media**| int | largest period the user has gone without checking their social media profiles, in days.|
|**Exercise Frequency**| int | number of times the participant exercises in a week.|
|**Social Media Platform** | str | participant's most used social media platform.|
|**Happiness Index**| int | how the participant rated their happiness level, ranging from 1 (very unhappy) to 10 (very happy). This is our target feature to predict.|

## Project Outline

### Preprocessing

In this phase, we standardize column names and string entries, transforming them to lowercase and replacing blank spaces by underscores. We drop the 'User ID' column, since it is not relevant for our analysis. Lastly, we set the 'Gender' and 'Social Media Platform' to the 'category' type.

### Exploratory Data Analysis

We begin our analysis by checking if there are any missing values in our dataset, or any duplicated rows. We find no missing values or duplicated rows. We then take a look at our numerical variables and see if they agree with our common sense, as to avoid errors. The maximum amount of times a participant exercised in a week was 7, which sounds possible. All the variables that range from 1 to 10 are between these values, as seen by their minimum and maximum values. The participants' ages vary from 16 to 49, which is reasonable. Therefore, our dataset seems to be ok, and not present any errors caused by wrong values.

Then, we check the unique values present in our categorical features. This information will be relevant in next sessions, when we need to encode this information. We see 3 options for gender (Male, Female and Other), and many different options for the preferred Social Media Platform. 

We follow by performing an univariate analysis, to understand how our variables are distributed in our dataset. Our main takeaways are:

- **Age** (Mean: 32.98, Median: 34, Standard Deviation: 9.96, Skewness: -0.12) follows an almost uniform distribution, with no outliers. Younger users may be more prone to high social media usage, and therefore may present a lower happiness index. This could be an important feature for our predictive models. This relation will be further analysed soon. The only necessary transformation for this data is scaling/standardizing.
- **Daily Screen Time** (Mean: 5.53, Median: 5.6, Standard Deviation: 1.73, Skewness: 0.03) follows an almost perfect normal distribution, confirmed by the graph and the low skewness value. We see one outlier with screen time higher than 10 hours a day, which we will maintain in the dataset. In a real-life situation, it would be interesting to separate screen time at work (doing work related tasks), and screen time at leisure time. Participants that work with computers may show a high daily screen time, but not necessarily a high social media usage. It would be interesting to see if it is only social media usage that impacts the participant's happiness levels, or if it is the daily screen time as a whole. There is no need to perform any transformation in this feature.
- **Sleep Quality** (Mean: 6.3, Median: 6, Standard Deviation: 1.52, Skewness: 0.03) follows a normal distribution, without outliers. There is no need to perform transformations in this variable.
- **Stress Levels** (Mean: 6.61, Median: 7, Standard Deviation: 1.54, Skewness: -0.09) also follows an almost normal distribution, but with a slightly longer left tail caused by an outlier that described their stress levels as 2. There is no need for transformations in this column.
- **Days without Social Media** (Mean: 3.13, Median: 3, Standard Deviation: 1.85, Skewness: 0.079) has a right-skewed normal distribution, with the left tail larger than the right one. While this is not ideal, we will not implement any transformation for this variable.
- **Exercise Frequency** (Mean: 2.45, Median: 2, Standard Deviation: 1.42, Skewness: 0.23) has a right-skewed distribution. However, attempting to apply a square-root transformation or a log1p transformation actually increases the skewness of this variable, therefore we choose to not apply any transformation.
- **Happiness Index** (Mean: 8.37, Median: 9, Standard Deviation: 1.52, Skewness: -0.68) has a highly left-skewed distribution. In this case, we will use a SMOTE technique to oversample the dataset.
- **Gender** has an almost even distribution between Male and Female, with a small number of participants identifying as Other.
- **Social Media Platform** has uniform results for all the platforms analysed.

Then, we do a bi-variate analysis, to see how each feature impacts the Happiness Index of a participant with a line plot. We find:

- **Age** does not seem to show any correlation with the Happiness Index. Therefore, this variable will not be a good predictor for our target feature.
- **Daily Screen Time** shows an interesting relation with happiness levels. We notice the average Happiness Index decreases as a participant's daily screen time increases. This is likely a good predictor for happiness.
- **Sleep Quality** increases tend to increase happiness levels. This is also an interesting predictor. We can also investigate to see if there is any relation between sleep quality and screen time, exercise frequency and stress levels, since recent research in the medical field hints all these factors could be related. 
- **Stress Level** has an inverse relationship to happiness index, which confirm our hypothesis that 1 represents the lowest level of stress in this dataset.
- **Days without Social Media** does not seem to have a strong relation with happiness levels.
- **Exercise Frequency** has a weird relation with happiness levels. Participants who do not exercise seem to have a higher Happiness Index than those who exercise 1, 2 or 3 times a week. However, looking at the y axis, we see the difference between these categories' happiness levels are less than half a point. Therefore, there is a chance this could be a statistical fluke, and not a relevant difference. To check this hypothesis, we perform two-sampled Z-tests between these categories. For all of them, our null hypothesis is that there is no difference between these groups mean happiness index (i.e. the difference in happiness levels is not statistically relevant). Between the group who does not exercise and the group that exercises once a week, the p-value is 0.6873. Hence, we reject our null hypothesis and find the difference in results is relevant. The same pattern goes for the other comparisons: between those who exercise once a week and those who exercise twice a week, the p-value is 0.3693. Between those who exercise twice a week, and those who exercise 3 times a week, the p-value is equal to 0.8220. Now, we can investigate the relation between exercise frequency and other variables, to try to understand why its relation with happiness index does not fit our expectations.

Next, we compare happiness levels between our categorical features:
- **Gender** does not seem to affect the happiness index distribution greatly. We see a similar pattern for all 3 options.
- **Social Media Platform** also has a similar pattern for all the options considered. At first, we see there is a great difference between the number of participants with a 10 happiness index between platforms. However, when we compare the percentages they possess in the dataset, we notice that platforms with more users in the dataset have higher counts of high happiness index. Once this is a count plot, it is expected that platforms with more users in the dataset will present higher counts. With this information in mind, it does not seem the social media platform of preference affects the participants happiness index significantly.

Using a correlation heat map, we see the variables that are the most correlated to Happiness Index are Stress Level, Sleep Quality and Daily Screen Time. As mentioned previously, we also notice these 3 features are also well correlated among themselves. This should be expected, since research show that high screen time can increase stress levels and reduce sleep quality, while high stress levels can also worsen sleep. Therefore, it is interesting to do a multivariate analysis to see these relations. The analysis confirms our hypothesis: lower values of daily screen time correlate to lower levels of stress, and higher Happiness Index values; high values of daily screen time correlate with low sleep quality and lower happiness levels; high stress levels correlate to lower sleep quality and lower happiness ratings.

In face of the information above, we have some variables that may not be relevant for our analysis: age, gender, social media platform and days without social media. While exercise frequency has a low correlation with the happiness index, we still see a trend with people that exercise more than 3 times a week are happier than those that exercise less. We will keep this feature in our dataset for now, and maybe later train models without it to check its impact on performance. To better understand which variables are better to drop from the data, we do a multivariate analysis with Happiness Index and the variables we do intend to keep (stress level, sleep quality and daily screen time). This would allow us to see if, for example, younger women tend to have lower happiness levels.

For social media platform, age and days without social media, there was no substantial correlation with the other variables. Therefore, it is safe to affirm these variables are not good predictors and will be dropped of our data. However, the gender variable carries some interesting information. For participants who execise more than 3 times a week, women tend to have significantly higher happiness levels than men. Hence, we keep this column. 

### Label Encoding

We encode the 'gender' feature using one-hot encoding.

### Data Split

In this section, we split our data into training and testing sets. In the EDA session, we noticed our dataset presents more high happiness index values than lower instances. This could lead to an overrepresentation bias in our models. Hence, we use a SMOTE technique to oversample the dataset and guarantee we will have equal proportions of each index present. We also stratify our training and testing sets by happiness index, so both of them will have an equal amount of each happiness level value.

All the numerical features we employ in the model range between 1 and 10, therefore there is no need to scale our data.

### Model Training

Finally, we train our models. We employ RMSE and NRMSE to evaluate our models, since this is the best metric for regression problems without outliers. We also employ cross-validation for all models, and grid search to fine tune some hyperparameters. The models trained are:

- Linear Regression: RMSE of 0.9581.
- Linear Regression with Ridge Regularization: RMSE of 0.9579.
- Linear Regression with Lasso Regularization: RMSE of 0.9557.
- Decision Tree Regressor: RMSE of 0.9965.
- Random Forest Regressor: RMSE of 0.9507.
- Gradient Boosting Regressor: RMSE of 0.9584.

Therefore, we see the Random Forest Regressor was the best model in the training set. 

### Hyperparameter fine-tuning

In the previous section, we did a small grid search for the best parameters for each model, in order to find the most effective one. Now, we do another grid search for values close to the ones selected previously, to fine-tune the model. With this, we reduce the RMSE in the training test to 0.9503. Finally, we use this model to make predictions in the test set, and achieve a RMSE of 0.8953. The model has a better performance in the test set, therefore we do not need to worry about overfitting.
