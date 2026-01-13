## :pushpin: Descrição do Projeto

Este projeto foi construído com o intuito de colocar em prática minhas habilidades com a linguagem SQL. O banco de dados original conta com 9 tabelas relacionadas, que descrevem o sistema de vendas de uma loja de bicicletas. As tabelas são: clientes/customers (contendo informações como nome e endereço de cada cliente), funcionarios/staff (contendo informações como nome e loja de atuação para cada funcionário), pedidos/orders (número do pedido, data, data do envio, e o cliente que fez o pedido em questão), lojas/stores (endereço e informações de contato para cada loja da rede), produtos/products (lista de todos os produtos vendidos pela loja, e seu preço), categorias/categories (categorias em que os produtos vendidos se encaixam), itens de um pedido/order items (informações extras de pedidos, como quais produtos foram vendidos e em qual quantidade), estoques/stocks (informações sobre os estoques de cada loja) e marcas/brands (lista de todas as marcas que são vendidas pela loja).

O objetivo do projeto é transferir os dados do banco, que estão armazenados em arquivos .csv, para o SQL, e então encontrar respostas para as principais perguntas de negócio empregando o SQL.

## :file_folder: Arquivos Disponíveis

Arquivo comandos (.md): arquivo de texto com os comandos executados no SQL, junto com uma explicação.

Arquivos do Banco de Dados (.csv): tabelas utilizadas para o projeto.

## :triangular_ruler: Estrutura do Projeto

O projeto foi desenvolvido em etapas, organizadas de forma que:

1. Criação de tabelas e schemas no PostgreSQL: criação de cada uma das tabelas dos dados. Para isso, foi necessário definir o melhor tipo de dado para cada coluna, e criar as relações entre tabelas utilizando chaves primárias e estrangeiras.

2. Inserção de valores: importação dos dados brutos dos arquivos .csv para o PostgreSQL.

3. Análise Exploratória: análise dos dados nas tabelas, com o objetivo de conhecer melhor o dataset.

4. Insights: obtenção de respostas para principais perguntas de negócio, utilizando comandos de SQL.
5. 
