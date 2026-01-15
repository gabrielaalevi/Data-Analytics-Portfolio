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

Os principais insights identificados a partir da análise são os seguintes:

1. A loja Baldwin Bikes, localizada em Nova York, foi a que apresentou o maior volume de vendas e de lucro nos últimos três anos, sendo responsável por aproximadamente 68% das vendas totais da rede. Esse resultado está alinhado ao fato de que a maior parte dos clientes cadastrados reside no estado de Nova York.

2. Embora a Baldwin Bikes seja a loja com maior volume de vendas, as lojas Rowlett Bikes e Santa Cruz Bikes apresentam um número maior de produtos vendidos por cliente.

3. A Rowlett Bikes se destaca como a loja com maior lucro médio por venda.

4. Entre os funcionários, Marcelene foi quem realizou o maior número de vendas nos últimos três anos. Entretanto, a funcionária Kali obteve o maior lucro médio por venda.

5. Foram concedidos diversos descontos na venda de produtos. Contudo, ao somar o valor total dos descontos por produto, observa-se que o lucro máximo “perdido” foi de apenas 19 dólares. Isso indica que, apesar da frequência de descontos, o impacto financeiro individual é bastante reduzido.

6. Ao comparar os anos de 2017 e 2018, observa-se uma queda aproximada de 60% nas vendas nas três lojas da rede.

7. Esse declínio afetou todas as categorias de produtos comercializadas.

8. A loja Baldwin Bikes foi a mais impactada pela redução nas vendas, registrando em 2018 um volume de produtos vendidos inferior à metade do observado em 2017.

9. A categoria Cruisers Bikes, a mais popular em todas as lojas, apresentou uma queda de cerca de 60% nas lojas Baldwin Bikes e Rowlett Bikes, e de aproximadamente 42% na Santa Cruz Bikes. Esse cenário sugere duas possíveis causas: a implementação de alguma política interna comum às lojas ou uma mudança espontânea no interesse dos consumidores. Para confirmar qualquer uma dessas hipóteses, seriam necessários dados adicionais.

10. A categoria com maior volume de estoque é a Cruisers Bikes. Ao comparar o estoque disponível com as vendas realizadas em 2018, percebe-se que o nível de estoque em todas as lojas está significativamente acima da demanda. Recomenda-se, portanto, a adoção de ações para estimular as vendas dessa categoria, como campanhas promocionais.
