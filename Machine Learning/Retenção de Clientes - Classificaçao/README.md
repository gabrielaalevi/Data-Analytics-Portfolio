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
- **Balance** (Saldo): nosso gráfico de correlação aponta para uma relação significativa entre o saldo da conta e a probabilidade do cliente deixar o banco. No entanto, criando um histograma do saldo dividido por clientes que encerraram seus serviços e clientes que se mantiveram na empresa, vemos a mesma distribuição para ambas as categorias. Analisando as porcentagens, vemos que ambas as categorias a mesma proporção para clientes com o mesmo saldo. A diferença principal é causada pelo pico no zero, cuja informação foi codificada na variável 'HasZeroBalance' (Possui saldo nulo). Logo, podemos descartar a variável Balance (Saldo) e manter somente a variável Has Zero Balance (Possui saldo nulo), para simplificar nosso modelo.
- **Estimated Salary** (Salário estimado): o salário médio entre clientes que deixaram o banco é de U$101509.90, enquanto para clientes que permaneceram no banco o salário médio é de U$99726.85. Não vemos uma relação significativa entre esta variável e a probabilidade de um cliente encerrar sua conta.
- **Points Earned** (Pontos obtidos): clientes que realizaram o churning possuem uma média de 604.4 pontos obtidos, enquanto clientes que se mantiveram no banco possuem uma média de 607.0 pontos obtidos. Logo, esta variável não possui poder preditivo.
- **Geography** (Geografia): notamos que a taxa de churning é maior para clientes que vivem na Alemanha (taxa de 0.32, contra taxa de churning de 0.16 para clientes residindo na França e na Espanha). Logo, um cliente que reside na Alemanha possui o dobro de chance de encerrar sua conta do que clientes de outros países.
- **Gender** (Gênero): clientes do gênero feminino possuem taxa de churning de 0.25, enquanto clientes do gênero masculino possuem taxa de churning igual a 0.16, apesar do banco possuir clientes masculinos em quantidade um pouco mais elevada do que clientes femininos. Podemos encontrar uma relação entre esta variável e a probabilidade do cliente deixar o banco.
- **Number of Products** (N° de produtos): enquanto clientes que possuem 2 produtos possuem menor probabilidade de encerrarem suas contas do que clientes que obtiveram somente 1 dos serviços do banco (taxa de churning de 0.07 e 0.28, respectivamente), clientes que contraram mais de 2 produtos do banco possuem probabilidade muito alta de abandonarem seus serviços; a taxa de churning para clientes que contraram 3 serviços é de 0.83, e aumenta para 1 para clientes que obtiveram 4 produtos. Esta variável possui alto poder preditivo para o nosso modelo.
- **Has Credit Card** (Possui cartão de crédito): enquanto esta variável aparenta ser muito importante a princípio, a taxa de churning para clientes que possuem cartão de crédito é muito próxima do valor para clientes que não possuem cartão (0.202 contra 0.208, respectivamente). Esta não é uma característica relevante para nossa predição.
- **Is Active Member** (É membro ativo): a taxa de churning para membros ativos é de 0.14, enquanto para membros inativos a taxa é igual a 0.27. Esta é uma variável importante para nossa análise.
- **Complain** (Reclamou): a taxa de churning para clientes que reclamaram é 1. Portanto, todos os clientes que saíram do banco reclamaram ao SAC antes de encerrarem seus contratos. Enquanto esta variável possui poder preditivo altíssimo, ela pode ser prejudicial ao modelo devido à introdução de separação. Esta característica é extremamente problemática para modelos de regressão logística, que serão testados na última sessão. Para evitar instabilidades estatísticas devido à esta correlação, treinaremos modelos com e sem esta variável.
- **Satisfaction Score** (Nível de satisfação): a taxa de churning é quase igual para todos os valores de satisfação. Logo, esta não é uma coluna relevante para nossa análise.
- **Card type** (Tipo de cartão): todas as categorias de cartão possuem taxa de churning uniforme, e portanto esta variável pode ser descartada. Como explicado anteriormente, o tipo de cartão de um cliente está normalmente associado ao seu saldo em conta. Se a variável Balance (Saldo) não possui poder preditivo, é compreensível que o tipo de cartão também não seja significativo.

Por fim, investigamos a relação entre Number of Products (N° de produtos) e Balance (Saldo). De acordo com nosso gráfico de correlação, existe uma relação significativa entre estas variáveis. No entanto, quando criamos um gráfico entre elas e separamos os dados pelas categorias de churning (clientes que saíram do banco versus clientes que permaneceram), vemos que a correlação entre o N° de produtos e o Saldo só existe para clientes que mantiveram suas contas no banco. Para estes clientes, o número de produtos adquiridos diminui conforme seu saldo aumenta. Isto não é um resultado trivial, uma vez que seria esperado que clientes com grandes saldos em conta possuíssem maior probabilidade de contratar mais serviços. Já para clientes que deixaram o banco, o saldo médio se mantém constante conforme o número de produtos adquiridos aumenta. Na vida real, seria interessante coletar mais dados para investigar de forma mais profunda esta relação.

Também podemos estudar este relacionamento criando um gráfico do número de produtos adquiridos pela variável Has Zero Balance (Possui saldo nulo). A maioria dos clientes que possuem somente um produto do banco possuem contas com saldo não-nulo. O raciocínio é análogo para clientes que obtiveram 3 ou 4 produtos. No entanto, a maioria dos clientes que possuem dois produtos são clientes com saldo nulo. Isto pode ser resultado de políticas do banco para oferecer benefícios melhores para clientes com saldo zero, para incentivá-los a utilizar suas contas. Mais estudos seriam necessários para confirmar esta hipótese. Os resultados do gráfico apontam para a relação observada na análise bivariacional do número de produtos obtido e no mapa de correlação: clientes que possuem 2 produtos do banco possuem maiores chances de possuir saldo zero, e também maiores chances de deixar o banco.

### Processamento dos Dados

Agora, aplicamos os insights obtidos acima nos dados. Primeiro, aplicamos uma transformação logarítmica em Age (Idade), devido à sua distribuição assimétrica. Checamos a nova distribuição após a transformação, e vemos que a Assimetria de Age (Idade) agora é igual a 0.18. Portanto, nosso objetivo de normalizar esta variável foi atingido.

Então, aplicamos one-hot encoding para as variáveis 'Geography' (Geografia) e 'Gender' (Gênero), para permitir sua análise pelo nosso modelo preditivo, e separamos os dados entre grupos de treino e de teste. Fazemos esta separação de forma estratificada pelos resultados de 'Exited' (Saiu), uma vez que há mais dados de clientes que permaneceram no banco do que dados de clientes que encerraram suas contas. Logo, a estratificação garantirá que ambos os grupos possuem frações iguais de clientes que saíram do banco.

No entanto, isto pode não ser o suficiente para balancear nossos dados. Então, aplicamos uma técnica de SMOTE de oversampling, que irá replicar exemplos aleatórios da classe minoritária (neste caso, clientes que deixaram o banco). Agora, temos certeza que nossos dados estão balanceados entre as duas categorias. Por fim, padronizamos nossos dados, para evitar problemas causados pelas diferentes ordens de grandeza entre eles.

## Seleção de Modelos

Nesta sessão, treinamos nossos modelos e avaliamos suas performances. Enquanto a acurácia (accuracy) seria a primeira escolha para avaliar um algoritmo de classificação como este, ela não é indicada para este caso, devido ao desbalanço original entre as categorias da variável-alvo. Ao invés disso, é necessário empregar outras métricas, como a precisão e o recall. Para este exemplo, um recall alto mas precisão baixa indica que o modelo irá enfatizar corretamente a predição de clientes que NÃO sairão do banco, com o custo de errar na predição de clientes com altas chances de deixar os serviços. O banco não perderá dinheiro dando benefícios para clientes que manteriam seus serviços ativos de qualquer jeito, mas podemos perder clientes que não foram corretamente nomeados como potenciais churners. Por outro lado, uma precisão alta mas recall baixo irá enfatizar clientes que sairão do banco, com o custo de errar nas predições de clientes que se manterão. Portanto, podemos perder dinheiro dando benefícios melhores para clientes que continuariam no banco de qualquer forma, mas conseguiremos garantir que a maioria dos clientes que são churners em potencial serão nomeados como tal. Na vida real, é possível que executivos escolham uma situação ou a outra, e neste caso uma métrica específica seria preferível. No entanto, para uma análise mais neutra, avaliaremos nossos modelos usando a métria f1-score, que leva em conta tanto a precisão quanto o recall para seu cálculo. Assim, possuímos uma solução intermediária em relação às citadas anteriormente.

Os modelos treinados foram:

- *Logistic Regression* (Regressão Logística), com uma pequena busca testando alguns valores de C para aplicar a regularização de Ridge. Utilizamos a regularização para evitar overfitting com a variável 'Complain' (Reclamou). Sua f1-score é de 0.998834.
- *Decision Tree Classifier* (Árvore de Decisão), também com uma pequena busca para encontrar o número mínimo de samples em uma folha mais efetivo. F1-score de 0.999103.
- *Random Forest Classifier* (Floresta de Decisão), testando o número de árvores ideal e o número mínimo de samples em uma folha mais efetivo. F1-score de 0.999103.
- *SVC* com regularização de Ridge, com uma pequena busca para o valor de C ideal. F1-score de 0.997581.
- *Ada Boosting Classifier*, usando Árvores de Decisão. Realizamos uma pequena busca para o número de árvores ideal e a taxa de aprendizado (learning rate) mais efetivo. F1-score de 0.999.
- *Gradient Boosting Classifier* com uma pequena busca para os valores ideais do número de estimadores e a taxa de aprendizado (learning rate). F1-score de 0.999.

Logo, vemos que os melhores modelos para este caso são a Árvore de Decisão e a Floresta de Decisão. Utilizamos estes dois modelos para realizar predições no grupo de teste. Ambos geram métricas f1-score de 0.9992, o que nos mostra que os modelos não estão realizando overfitting nos dados de treino.

Nossos modelos possuem alto poder preditivo, com valores de f1-score muito altos para todas as instâncias. Como a variável 'Complain' (Reclamou) possui uma correlação praticamente perfeita com a variável 'Exited' (Saiu), era esperado que nossos modelos fossem muito competentes nesta predição. No entanto, é possível que os modelos valorizem somente o valor de 'Complain' (Reclamou) na hora de realizar a predição. Neste caso, os algoritmos não seriam eficientes para clientes que saem do banco sem reclamar, ou que reclamam e não encerram suas contas.

Então, treinamos novos modelos sem a variável 'Complain' (Reclamou), para entender como nossos algoritmos respondem sem esta informação. Os resultados são:

- *Logistic Regression* (Regressão Logística) produziu uma métrica f1-score de 0.709803.
- *Decision Tree Classifier* (Árvore de Decisão) produziu uma métrica f1-score de 0.856745.
- *Random Forest Classifier* (Floresta de Decisão) produziu uma métrica f1-score de 0.852764.
- *SVC* produziu uma métrica f1-score de 0.725223.
- *Ada Boosting Classifier* produziu uma métrica f1-score de 0.831.
- *Gradient Boosting Classifier* produziu uma métrica f1-score de 0.8619.

Como esperado, os valores de f1-score de nossos modelos foram reduzidos devido à remoção da variável 'Complain' (Reclamou). Na vida real, seria interessante obter mais dados, especialmente focando em clientes que deixaram o banco sem reclamar ou que reclamaram sem sair, para aumentar a predição dos modelos. O modelo de Gradient Boosting Classifier foi o melhor modelo para este caso, e gera um valor de f1-score de 0.9796 nos dados de teste. Logo, vemos que não temos problemas com overfitting.


