## Descrição do Projeto

Neste projeto, trabalhamos com um banco de dadaos de clientes de um banco fictício. Nosso objetivo é prever quais clientes deixaram o banco (realizaram o churn) e quais mantiveram suas contas, usando dados como score de crédito, país de residência, tempo de contrato e balanço da conta, entre outras. Com isto, é possível implementar um programa de retenção de clientes focado em especificamente em indivíduos com altas chances de deixar o banco.

O banco de dados contém as seguintes colunas:

| Variável | Tipo | Descrição |
| -------- | ---- | ----------- |
|**Customer Id** (Id do cliente) | int | Número que identifica cada cliente. |
|**Surname** (Sobrenome) | str | Sobrenome do cliente.|
|**Credit Score** (Score de crédito) | int | Score de crédito do cliente no banco.|
|**Geography** (Geografia)| str | País de residência do cliente.|
|**Gender** (Gênero)| str | Gênero do cliente.|
|**Age** (Idade) | int | Idade do cliente.|
|**Tenure** (Tempo de contrato)| int | Tempo de contrato do cliente com o banco.|
|**Balance** (Saldo) | float| Saldo da conta do cliente.|
|**Number of Products** (Número de produtos)| int | Número de serviços do banco contratados pelo cliente.|
|**Has Credit Card** (Tem cartão de crédito) | int | Se o cliente possui um cartão de crédito ou não. Esta variável já vem com one-hot encoding, onde 1 representa clientes com cartão de crédito e 0 clientes que não possuem cartão de crédito. |
|**Is Active Member** (É membro ativo) | int | Se o cliente é um membro ativo (representado por 1) ou não (representado por 0).|
|**Estimated Salary** (Salário estimado) | float| Salário estimado do cliente.|
|**Exited** (Saiu) | int | Se o cliente encerrou seu contrato com o banco (representado por 1) ou não (representado por 0). Esta é a variável que gostaríamos de prever. |
|**Complain** (Reclamou) | int | Se o cliente registrou reclamação no SAC do banco (representado por 1) ou não (representado por 0).|
|**Satisfaction Score** (Nível de Satisfação) | int | Avaliação do cliente sobre os serviços do banco, de 1 (muito insatisfeito) a 5 (muito satisfeito).|
|**Card Type** (Tipo de Cartão) | str | Tipo de cartão do cliente. Os tipos possíveis são Prata (Silver), Ouro (Gold), Platina (Platinum) e Diamante (Diamond).|
|**Points Earned** (Pontos obtidos)| int | Pontos obtidos pelo cliente ao utilizar o cartão de crédito.|

## Organização do Projeto

### Pré-processamento de variáveis

Nesta sessão, nós padronizamos o nome das colunas e as variáveis de texto, deixando todas com letra minúscula e substituindo os espaços em branco por _. Também removemos as colunas "Customer Id" (Id do cliente) e "Surname" (Sobrenome), uma vez que elas não são relevantes para nosso algoritmo de Machine Learning. Checamos os dados para ver se existem valores em branco ou linhas duplicadas nos dados, que precisam ser transformados. Descobrimos que este não é o caso, então seguimos nossa análise. Por último, convertemos todas as variáveis categóricas para o tipo 'category', além de colunas com números inteiros mas que representam categorias usando one-hot encoding ('Exited' (Saiu), 'Complained' (Reclamou' e 'Satisfation Score' (Nível de Satisfação)).

### Análise Exploratória de Dados

Para esta parte, analisamos nossos dados para entender suas principais características e as relações entre variáveis. Primeiro, começamos fazendo uma análise univariacional, criando gráficos de frequência para variáveis categóricas e histogramas e gráficos de caixa para variáveis numéricas. Esta análise nos permite chegar em algumas conclusões:

- **Credit Score** (Score de crédito) (Média: 650.53, Mediana: 652.0, Assimetria: -0.07, Desvio Padrão: 96.65) possui uma distribuição quase normal, o que é confirmado pelo baixo valor da assimetria e pela pequena diferença entre a média e a mediana. Vemos alguns casos extremos com score muito baixo, o que pode ser um indicador de churning. A única transformação necessária para nossa análise é a padronização (standardization) dos dados.
- **Age** (Idade) (Média: 38.92, Mediana: 37.0, Assimetria: 1.01, Desvio Padrão: 10.48) tem uma distribuição altamente assimétrica, mostrando que a base de clientes do banco tende para clientes mais novos, entre 30 e 40 anos. Pode existir uma relação entre idade e churning, com clientes mais novos possuindo uma tendência maior de deixar o banco. Para termos certeza, precisaremos fazer uma análise bivariacional. Neste caso, devido à assimetria da distribuição, será necessário aplicar uma transformação logarítmica nesta coluna, para deixar sua distribuição mais perto da distribuição normal.
- **Tenure** (Tempo de contrato) (Média: 5.01, Mediana: 5.0, Assimetria: 0.01, Desvio Padrão: 2.89) possui uma distribuição uniforme entre os valores possíveis. Será necessário somente padronizar os dados.
- **Balance** (Saldo) (Média: 76485.89, Mediana: 97198.54, Assimetria: -0.14, Desvio Padrão: 62397.40) possui um pico ao redor de zero, mostrando que vários clientes possuem saldo nulo. Isto pode ser um grande indicador de churning, uma vez que aponta para clientes que não utilizam suas contas. O número de contas com saldo nulo reduz a média e cria uma discrepância entre a média e a mediana. Neste caso, podemos pensar em análises de hurdle model: criamos uma variável binária chamada 'HasZeroBalance' (Possui saldo nulo). Ela será 1 para clientes com saldo zero, e 0 para clientes com saldo não-nulo. Isto nos permite remover as contas com saldo zero da distribuição de Balance (Saldo). Com isso, vemos que agora a variável Balance (Saldo) apresenta uma distribuição normal, com assimetria de 0.02. Isto é necessário, uma vez que diversos modelos de Machine Learning trabalham com a suposição que as variáveis seguem uma distribuição normal. Logo, após criar a variável 'Has Zero Balance', só será necessário padronizar esta coluna.
- **Estimated Salary** (Salário estimado) (Média: 100090.24, Mediana: 100193.92, Assimetria: 0.00, Desvio Padrão: 57510.49) possui uma distribuição altamente uniforme, mostrando que a base de clientes do banco possui uma variedade de salários diferentes. A única transformação necessária é a padronização.
- **Points Earned** (Pontos obtidos) (Média: 606.52, Mediana: 605.0, Assimetria: 0.01, Desvio Padrão: 225.92) também possui distribuição uniforme, e não apresenta nenhum caso extremo (outlier). Novamente, a única transformação necessária é a padronização.
- **Geography** (Greografia) mostra que a maioria dos clientes do banco reside na França. Pode ser um indicator interessante para churning.
- **Gender** (Gênero) é distribuído de forma quase uniforme, tendendo um pouco mais para clientes do gênero masculino do que feminino.
- **Number of Products** (N° de produtos) apresenta um pico de frequência em 1, e diminui até chegar em 4. A maioria dos clientes não apresenta um engajamento muito grande com os serviços do banco.
- **Has Credit Card** (Tem cartão de crédito) indica que mais de 2/3 dos clientes possuem cartão de crédito. O público que não possui cartão de crédito talvez possua maiores chances de deixar o banco.
- **Is Active Member** (É membro ativo) é distribuído igualmente. Clientes inativos possuem maior chance de encerrarem seus contratos.
- **Exited** (Saiu) é nossa variável-alvo para predição. Em torno de 20% dos clientes fecham suas contas, enquanto 80% mantém seus contratos ativos. Esta diferença na representação pode ser um problema para nosso modelo preditivo, criando um viés para prever clientes que continuarão com o banco (overrepresentation bias). Será necessário utilizar técnicas de oversampling nos dados ou estratificar os grupos de treino e teste, para evitar este viés.
- **Complain** (Reclamou) mostra que 20% dos clientes registraram uma reclamação no SAC do banco. Esta é uma coluna com alto poder preditivo, uma vez que registra a insatisfação do cliente.
- **Satisfaction Score** (Nível de Satisfação) é distribuído uniformemente, com um pequeno pico em 3. Valores baixos podem ser bons preditores de clientes que pretendem deixar os serviços do banco, mas a distribuição uniforme pode reduzir seu poder preditivo. 
- **Card Type** (Tipo de cartão) é distribuído uniformemente, porém é improvável que esta variável carregue informações extras além do que está contido na variável Balance (Saldo), uma vez que o tipo de cartão que o cliente possui é normalmente determinado pelo saldo em conta.

### Engenharia de variáveis (Feature engenieering)

Antes de continuarmos com nossa Análise Exploratória, é importante criar a variável 'HasZero Balance', de forma a estudá-la em comparação com as outras colunas.


### Análise Exploratória de Dados Parte II

Agora, podemos conduzir uma análise bivariacional, focando nas relações entre colunas do banco de dados. Primeiro, começamos estudando a variação na taxa de churning (taxa de saída de clientes) causada por mudanças em cada coluna do dataset. Criamos também um mapa de correlação entre as variáveis numéricas e binárias, o que nos aponta quais serão as principais variáveis relacionadas à taxa de churning. Este gráfico indica que as variáveis que possuem maior poder preditivo são Complain (Reclamou), HasZeroBalance (Possui saldo nulo), IsActiveMember (É membro ativo), Number of Products (N° de produtos), Balance (Saldo) e Age (Idade). No entanto, ainda é interessante observar cada coluna individualmente.

- **Credit Score** (Score de crédito): observamos que a mediana do Score é de 646 para clientes que deixaram o banco, e 653 para clientes que mantiveram suas contas abertas. Portanto, não vemos indicativos que usuários com baixo score possuem maior probabilidade de deixar o banco. Para esta variável, utilizamos a mediana ao invés da média devido à presença de casos extremos (outliers), que afetam a média de forma mais forte do que a mediana. 
- **Age** (Idade): a idade mediana para clientes que encerram suas contas é de 45 anos, enquanto para clientes que continuaram com os serviços a idade mediana é 36 anos. Aparentemente, nossa ideia inicial na parte I da análise exploratória estava errada, e na verdade são os clientes mais velhos que possuem maior probabilidade de encerrar seus contratos. Mais uma vez, utilizamos a mediana devido à presença de casos extremos.
- **Tenure** (Tempo de contrato): o tempo médio de contrato para clientes que deixaram o banco é de 4.93 anos, enquanto o tempo médio para clientes que continuaram com suas contas é de 5.03. A princípio, estes dados aparentam estar correlacionados. No entanto, quando observamos o gráfico de caixa, vemos que o 1° quartil para clientes que encerraram suas contas está em 2 anos de contrato, enquanto o 3° quartil se localiza em 8 anos. Já para clientes que se mantiveram no banco, o 1° quartil se encontra em 3 anos de contrato, enquanto o 3° quartil está localizado em 7 anos. Logo, vemos que os clientes que abandonam o banco possuem uma variedade de tempos de contrato maior do que a outra opção. Ambas as categorias possuem o mesmo valor de mediana. Isto, adicionado ao fato que a diferença entre o tempo médio de contrato entre os grupos é muito pequena, indica que não existe uma relação significative entre o tempo de contrato e a probabilidade do cliente deixar o banco.
- **Balance**: our correlation plot hints at a correlation between the balance value and the churning rate. However, plotting a histrogram of balance divided by clients who churned and clients who didn't, shows the same distribution for both variables. Therefore, a client with a higher balance is not less likely to churn than a client with a moderate balance. The only difference resides in the zero peak, whose information is already encoded in the 'HasZeroBalance' feature. Therefore, it is useful to drop the 'Balance' column and employ solely the 'HasZeroBalance' one, to simplify our model.
- **Estimated Salary**: the mean salary among churning users is U$101509.90, while for non-churning customers is U$99726.85. We see no significant relation between Estimated Salary and the likelihood of churning.
- **Points Earned**: clients who churned have a mean of 604.4 earned points, whereas clients who didn't churn have a mean of 607.0 earned points. This implies no predictive power lies in this feature.
- **Geography**: we notice a higher churning rate for customers residing in Germany (0.32 churning rate, against 0.16 churning rate for both France and Spain). Therefore, a client living in Germany has double the chance to churn.
- **Gender**: female clients have a churning rate of 0.25, while male clients present a churning rate of 0.16, even though we have slightly more male customers than female ones. There seems to be a correlation between this feature and the chance of churning.
- **Number of Products**: while clients owning 2 products has a lower churning rate than customers owning only 1 product (0.07 versus 0.28, respectively), clients owning more than 2 products have very high chances of churning; the churning rate for clients owning 3 products is 0.83, and it increases to 1 for clients who own 4 products. This variable holds high predictive power for churning likelihood.
- **Has Credit Card**: while this may seem a highly important feature at first glance, the churning rate for clients who own a credit card is very close to the churning rate for clients who do not own a credit card (0.202 versus 0.208, respectively). This is not a relevant feature for churning prediction.
- **Is Active Member**: the churning rate for active members is 0.14, while for non-active users it is 0.27. This is an important feature.
- **Complain**: the churning rate for clients who have complained is 1. Therefore, every client who churned has complained before leaving the bank. While this variable holds extreme predictive power, it can be hurtful to our model due to the introduction of separation. This is highly problematic for logistic regression models, which we will test in the last section. To avoid statistical instabilities due to this correlation, we will train models both including and excluding this variable, in our Model Selection section.
- **Satisfaction Score**: the churning rate is almost the same for all values of satisfaction score. Therefore, it is not a relevant feature for our analysis.
- **Card type**: all categories of card types showed uniform churning rates, and therefore this is not a relevant variable. This could be expected, since Card Types are often related to the client's Balance (a higher Balance leads to more important card types). Once the Balance feature does not hold predictive power, it is compreehensible the Card Type variable also doesn't.

Lastly, we investigate the relation between Number of Products and Balance. According to our correlation map, there is a certain level of correlation between these features. However, when we plot their relation divided by the categories of churning, we see the correlation only exists for non-churning customers. For non-churning clients, the number of products owned decreases as their account balance grows larger. This is not trivial, since it would be expected high-earning clients to be more likely to acquire new products. Besides, this relationship does not apply for churning customers. Their balance remains almost constant as the number of products owned increases. In a real-life scenario, it would be interesting to collect more data and investigate further into this relation.

This relation can also be studied by plotting the number of products owned categorized by the 'HasZeroBalance' variable. The majority of people who only own one product have non-zero balances. The same applies for those who own 3 or 4 products. However, the majority of clients who own one product are people with zero-balance. This could be a result of the bank offering better benefits to customers with zero balances, to incentivize these clients to use their accounts more. Better studies would be necessary to confirm this hypothesis. The results from this graph points towards the relation observed in the bivariate analysis of the number of products owned and the correlation map: clients who own 2 products are more likely to have zero balances, and are also more likely to churn.

### Data Processing

Now, we apply the insigths obtained above to the data. Firstly, we apply a logarithmic transformation to Age, due to its imbalanced distribution. We check the transformation, and see the skewness of the logarithmic distribution of age is now 0.18. Therefore, we have achieved our goal to normalize this variable. Then, we apply one-hot encoding to the 'Geography' and 'Gender' variables, and split the data into training and testing sets. We make sure to stratify the groups by 'Exited', since there is an overrepresentation of customers who did not churn in the dataset. Therefore, stratification will guarantee both groups have equal fractions of clients who did churn. However, this may not be enough to balance our dataset. So, we also apply a SMOTE oversampling technique, that will replicate random examples from the minority class (in this case, clients that churned). Now, we are sure our dataset is balanced between both categories. Lastly, we standardize our data, as to avoid problems caused by the different scales between features.

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


