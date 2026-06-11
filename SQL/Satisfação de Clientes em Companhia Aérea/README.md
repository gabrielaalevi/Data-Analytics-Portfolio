## :pushpin: Descrição do Projeto

Este projeto foi criado com o intuito de praticar minhas habilidades em SQL e pandas. Para isso, utilizei um banco de dados fictício de uma companhia aérea, onde foram registradas características do cliente (gênero, idade, tipo de viagem), características do voo (distância, tempo de atraso na decolagem, tempo de atraso na chegada) e avaliações de satisfação realizadas pelo cliente sobre diferentes aspectos da viagem.

O objetivo desta parte do projeto foi preparar os dados utilizando pandas (checar a integridade do banco de dados, criar bins para algumas variáveis), e analisar os dados utilizando SQL.

## :file_folder: Arquivos Disponíveis

Arquivo comandos (.ipynb): notebook Jupyter Lab com os comandos utilizados para a preparação dos dados.

Arquivos do Banco de Dados (.csv): dados utilizados no projeto.

## :triangular_ruler: Estrutura do Projeto

O projeto foi desenvolvido em etapas, organizadas de forma que:

1. Garantir a integridade do banco de dados: checar se existiam dados nulos no banco e tratá-los.

2. Criação de bins: utilizar o pandas para criar categorias para as variáveis idade, atraso na decolagem e atraso na chegada, para facilitar sua análise no Power BI na próxima etapa do projeto.
   
3. Criação de variável: criação da variável Atraso no Percurso. Uma vez que as variáveis atraso na decolagem e atraso na chegada estão conectadas, criamos uma variável para analisar somente atrasos causados após a decolagem.

4. Análise Exploratória: análise dos dados nas tabelas, com o objetivo de conhecer melhor o dataset, empregando SQL.

5. Salvamento: salvar a tabela nova, e duas novas tabelas (de clientes satisfeitos e clientes insatisfeitos), para facilitar a visualização no Power BI.

## :bulb: Próximos Passos

Para a próxima parte do projeto, montarei um dashboard no Power BI para analisar os dados obtidos e facilitar sua visualização.


