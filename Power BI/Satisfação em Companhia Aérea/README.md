## :pushpin: Descrição do Projeto

Este projeto consiste no desenvolvimento de um dashboard interativo em Power BI para a análise de dados sobre as vendas de uma rede de lojas de biciletas fictícia. O principal objetivo foi colocar em prática e aprimorar minhas habilidades com Power BI, especialmente na análise e visualização de dados de vendas, buscando transformar dados brutos em informações claras e visuais para tomada de decisão.

O banco de dados original conta com 9 tabelas relacionadas, que descrevem o sistema de vendas da rede. As tabelas são: clientes/customers (contendo informações como nome e endereço de cada cliente), funcionarios/staff (contendo informações como nome e loja de atuação para cada funcionário), pedidos/orders (número do pedido, data, data do envio, e o cliente que fez o pedido em questão), lojas/stores (endereço e informações de contato para cada loja da rede), produtos/products (lista de todos os produtos vendidos pela loja, e seu preço), categorias/categories (categorias em que os produtos vendidos se encaixam), itens de um pedido/order items (informações extras de pedidos, como quais produtos foram vendidos e em qual quantidade), estoques/stocks (informações sobre os estoques de cada loja) e marcas/brands (lista de todas as marcas que são vendidas pela loja).

## :file_folder: Arquivos Disponíveis

Dashboard em Power BI (.pbix): versão interativa do relatório.

Arquivo PDF: contém capturas de tela de todos os painéis do dashboard para visualização rápida.

## :triangular_ruler: Estrutura do Dashboard

O dashboard é composto por 6 painéis, organizados da seguinte forma:

1. Vendas, contendo informações sobre faturamento, volume de vendas, número de clientes atendidos e preço médio
2. Estoque, comparando a quantidade de produtos em estoque e o que foi efetivamente vendido em 2018
3. Região, contendo uma análise das vendas por estado
4. Categoria, com dados sobre o desempenho de vendas por categoria de produto
5. Marca, identificando as marcas mais vendidas
6. Funcionários, avaliando o desempenho dos vendedores ao longo do ano

Todos os painéis contam com filtros dinâmicos, permitindo a exploração dos dados por diferentes categorias e a realização de análises mais detalhadas e personalizadas.

## :briefcase: Análises de negócios

A análise conduzida a partir do dashboard possibilitou a identificação de padrões relevantes em diferentes áreas da rede. Os principais insights obtidos foram os seguintes:

- Em 2018, as vendas apresentaram uma queda de quase 50% em relação a 2017, enquanto o número de clientes atendidos diminuiu 60% no mesmo período.
- O preço médio por produto vendido aumentou 20%; entretanto, o volume de vendas caiu 57%, o que resultou em uma redução significativa do faturamento.
- Abril foi o mês de maior faturamento em 2018. A partir de julho, o faturamento tornou-se praticamente inexistente.
- A loja Baldwin Bikes concentrou 62% das vendas da rede ao longo de todo o período analisado (2016 a 2018) e foi também a unidade que registrou a maior queda nas vendas.
- A rede mantém cerca de 13 mil itens em estoque, um volume extremamente elevado quando comparado às vendas realizadas em 2018.
- O estoque encontra-se distribuído de forma equilibrada entre as três lojas.
- As marcas Trek e Electra representam os maiores volumes em estoque, enquanto as categorias com maior quantidade de itens são Cruisers Bikes e Mountain Bikes.
- O estado de Nova Iorque apresentou o maior faturamento, o que está alinhado com a localização da loja Baldwin Bikes. No entanto, também foi o estado que sofreu a maior queda de faturamento em 2018 em comparação a 2017.
- Todos os estados exibiram um comportamento semelhante nas vendas mensais, com pico em abril e queda acentuada a partir de julho.
- As categorias que registraram as maiores reduções de faturamento foram Road Bikes e Mountain Bikes.
- Electric Bikes foi a categoria com maior preço médio, enquanto Children's Bikes apresentou o menor.
- Apesar do alto preço médio, a categoria Electric Bikes esteve entre as menos vendidas em 2018.
- Produtos da marca Trek sofreram a maior queda de faturamento, ao passo que os da marca Electra mantiveram valores praticamente estáveis.
- A marca Trek apresentou o maior preço médio entre os produtos analisados.
- Com excessão de Trek e Electra, as demais marcas apresentaram volumes de venda significativamente inferiores.
- As funcionárias com maior número de vendas foram Marcelene e Venita.
- Em termos de preço médio das vendas, as vendedoras Mireya, Kali e Layla superaram Marcelene.

Desta forma, podemos definir alguns objetivos-chave (OKRs) e indicadores de desempenho (KPIs), como:

Objetivo-chave 1: Recuperar o faturamento e o volume de vendas da rede. Para alcançar esse objetivo, é fundamental reduzir a dependência da loja Baldwin Bikes, limitando sua participação a, no máximo, 50% do total de vendas. O acompanhamento poderá ser realizado por meio de KPIs como faturamento total mensal e anual, volume de vendas, número de clientes atendidos, ticket médio, além do faturamento por loja e por estado.

Objetivo-chave 2: Aumentar a eficiência da gestão de estoque. Este objetivo visa reduzir o volume total de estoque e elevar seu giro, garantindo maior alinhamento entre estoque e demanda. A prioridade é concentrar maiores volumes nas categorias e marcas com maior potencial de venda. Os KPIs definidos incluem giro de estoque, dias médios em estoque, volume total estocado, percentual de estoque parado e distribuição do estoque por categoria e por marca.

Objetivo-chave 3: Otimizar o mix de produtos e marcas. A proposta é impulsionar as vendas das categorias Electric Bikes, Road Bikes e Mountain Bikes, além de ampliar a participação de outras marcas, de modo a reduzir a dependência das marcas Trek e Electra para, no máximo, 60% das vendas totais. Os KPIs associados a esse objetivo são faturamento por categoria e por marca, margem média por categoria, preço médio por categoria e marca, e participação percentual de cada marca no faturamento.

Objetivo-chave 4: Reduzir a sazonalidade e equilibrar as vendas ao longo do ano. Para mitigar a concentração de vendas em poucos meses, será necessário implementar campanhas comerciais no segundo semestre, estimulando o faturamento nesse período e reduzindo a diferença entre o melhor e o pior mês do ano. Os KPIs definidos são faturamento mensal, crescimento mês a mês, desvio padrão do faturamento mensal e desempenho das campanhas nos meses de menor venda.

Objetivo-chave 5: Desenvolver a performance comercial da equipe de vendas. Este objetivo busca elevar o ticket médio e diminuir a disparidade de resultados entre os vendedores, por meio de capacitação em técnicas de vendas e abordagem consultiva. Os KPIs incluem número de vendas por vendedor, faturamento e ticket médio individual, taxa de conversão (vendas por clientes atendidos) e participação de cada vendedor no faturamento total.
