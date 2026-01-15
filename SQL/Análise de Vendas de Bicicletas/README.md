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

## :bulb: Principais Insights

Os principais insights obtidos na análise foram:

1. A loja Baldwin Bikes, localizada em Nova York, é a loja que gerou mais vendas (e mais lucro) nos últimos 3 anos. Ela é responsável por quase 68% das vendas totais da rede. Coincidentemente, a maioria dos clientes registrados na base moram no estado de Nova York.
3. Apesar da loja Baldwin Bikes vender mais produtos, as lojas Rowlett Bikes e Santa Cruz Bikes vendem mais produtos por cliente.
4. A loja Rowlett Bikes é a que gera maior lucro por venda.
5. A funcionária Marcelene foi quem realizou mais vendas nos últimos 3 anos. No entanto, a funcionária Kali é quem gerou maior lucro por venda.
6. Diversos descontos foram dados na compra de produtos. No entanto, somando os descontos dados por produto, o lucro 'perdido' por descontos máximo é de apenas 19 doláres. Logo, vemos que, apesar de vários descontos serem realizados, o valor individual por desconto é muito baixo.
7. Comparando os anos de 2017 e 2018, vemos que as vendas decaíram por volta de 60% nas 3 lojas.
8. O declínio nas vendas afetou todas as categorias de produtos vendidas pela rede.
9. A loja Baldwin Bikes foi a loja que mais perdeu vendas. Em 2018, a quantidade de produtos vendidos foi menos da metade da quantidade de vendas de 2017.
10. Para a categoria Cruisers Bikes, a mais popular em todas as lojas, a queda foi de aproximadamente 60% para a Baldwin Bikes e para a Rowlett Bikes, e de 42% para a Santa Cruz Bikes. Portanto, este resultado aponta para duas possibilidades: ou a queda nas vendas foi causada por uma política interna implementada em todas as lojas, ou o declínio foi gerado por mudanças espontâneas no interesse do público. Dados extras seriam necessários para confirmar uma ou outra hipótese.
11. A categoria de produtos com maior estoque é a Cruisers Bike. Comparando com os dados de 2018, o estoque em todas as lojas é muito maior do que as vendas previstas. É indicado realizar programas para estimular a venda de produtos desta categoria, como promoções.
