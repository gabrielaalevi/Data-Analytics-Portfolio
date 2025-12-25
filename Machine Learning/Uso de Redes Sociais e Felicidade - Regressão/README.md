## Descrição do Projeto

Neste projeto, trabalhamos com um banco de dados que contém informações de uma pesquisa fictícia aplicada a usuários de redes sociais. Os usuários responderam perguntas relacionadas ao tempo de uso de mídias sociais, qualidade de sono, níveis de stress, hábitos de exercício e níveis de felicidade. Nosso objetivo é criar um modelo de Machine Learning que consiga prever qual o nível de felicidade de uma pessoa com base em características da sua rotina.

Os dados contém as seguintes colunas:

| Variable | Type | Description |
| -------- | ---- | ----------- |
|**User_ID** (ID do usuário)| int | Número que designa o participante na pesquisa.|
|**Age** (Idade)| int | Idade do participante.|
|**Gender** (Gênero)| str| Gênero do participante (Masculino, Feminino or Outro).|
|**Daily Screen Time (hrs)** (Tempo  de tela diário, em horas)| int | Número médio de horas que o participante passa diariamente utilizando aparelhos eletrônicos.|
|**Sleep Quality** (Qualidade do sono)| int | Nota que o participante deu para sua qualidade do sono, de 1 (terrível) a 10 (perfeito).|
|**Stress Level** (Nível de stress)| int| Nota que o participante deu para seu nível de stress, de 1 a 10. Na fonte dos dados, não há explicação sobre como estes dados foram obtidos. Para este projeto, assumiremos que 1 é um nível muito baixo de stress, e 10 é um nível muito alto. |
|**Days without Social Media** (Dias sem redes sociais) | int | Período mais longo que o usuário passou sem utilizar suas redes sociais, em dias.|
|**Exercise Frequency** (Frequência de exercício) | int | Número de vezes que o participante se exercita em uma semana. |
|**Social Media Platform** (Rede social preferida) | str | Plataforma de rede social que o participante mais utiliza.|
|**Happiness Index** (Nível de Felicidade)| int | Nota que o participante deu para seu nível de felicidade, de 1 (extremamente infeliz) a 10 (extremamente feliz). Esta é a nossa variável alvo para predição.|

## Organização do Projeto

### Pré-processamento de variáveis

Nesta fase, começamos por padronizar os nomes de colunas e as entradas de texto, deixando-as todas em letras minúsculas e substituindo espaços em branco por _. Descartamos as colunas 'User ID' (ID do usuário), uma vez que ela não é relevante para nossa análise. Por fim, transformamos as variáveis 'Gender' (Gênero) e 'Social Media Platform' (Rede social preferida) para o tipo 'category'. 

### Análise Exploratória de Dados

Começamos nossa análise checando se existem valores em branco ou linhas duplicadas nos dados, que necessitam de transformação. Vemos que este não é o caso deste dataset, então seguimos para a próxima parte. Observamos os valores númericos nos dados, para checar se eles concordam com o senso comum e com os pressupostos que temos sobre nossos valores. O número máximo de vezes que um participante se exercitou em uma semana foi 7, o que é possível. Diversas variáveis possuem, como pressuposto, valores no intervalo de 1 a 10. Vemos que os valores mínimos e máximos destas colunas estão realmente dentro deste intervalo, e nosso pressuposto está correto. As idades dos participantes variam de 16 a 49, um intervalo razoável. Portanto, os dados aparentam estarem corretos, sem valores que foram alterados devido a erros.

Então, checamos os valores presentes nas colunas categóricas, para ver se não temos nenhum caso de erros de digitação. Vemos 3 opções para gênero (Masculino, Feminino e Outro), e diversas opções válidas para a rede social preferida.

Performamos uma análise univariacional, para entender a distribuição de variáveis no dataset. Nossas principais conclusões são:

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
