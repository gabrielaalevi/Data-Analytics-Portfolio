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

A análise realizada por meio do dashboard permitiu identificar padrões relevantes em diversas áreas da rede. Os principais insights foram:

- As vendas caíram quase 50% em 2018, quando comparadas com o volume de vendas do ano de 2017. O número de clientes atendidos caiu em 60% no mesmo período.
- O preço médio por produto vendido aumentou em 20%. No entanto, as vendas diminuíram em 57%, o que explica a queda no faturamento.
- O mês com maior faturamento em 2018 foi Abril. A partir de julho, o faturamento se tornou quase nulo.
- A loja Baldwin Bikes é responsável por 62% das vendas da rede em todos os anos analisados (2016 - 2018), e foi quem sofreu a maior queda em vendas.
- A rede possui 13 mil itens em estoque, uma diferença gigantesca quando comparada com as vendas de 2018.
- Os itens em estoque estão igualmente distribuídos entre as 3 lojas.
- Trek e Electra são as marcas com maior volume em estoque, enquanto Cruisers Bikes e Mountain Bikes são as categorias com maior volume.
- O estado de Nova Iorque é o que possui maior faturamento, coincidindo com a localização da loja Baldwin Bikes. Este também foi o estado com maior queda no faturamento em 2018, quando comparado com 2017.
- Todos os estados apresentaram um comportamento semelhante em relação às vendas mensais, com um pico em Abril e uma queda brusca em julho.
- 

- Clientes que possuem um maior número de produtos ou serviços contratados apresentam maior probabilidade de encerrar suas contas, indicando possíveis desafios na experiência do cliente ou na adequação dos produtos oferecidos.
- Clientes residentes na Alemanha demonstram uma taxa de churn superior quando comparados aos demais países analisados.
- Aproximadamente 99% dos clientes que registraram reclamações no serviço de atendimento ao cliente acabaram encerrando suas contas, evidenciando o impacto crítico da qualidade do suporte no processo de retenção.
- O churn se concentra principalmente entre clientes com idade entre 36 e 60 anos, sugerindo a necessidade de estratégias específicas para esse perfil.
- Clientes com maior tempo de contrato apresentam probabilidade de churn semelhante à de clientes mais recentes, indicando que a longevidade do relacionamento, por si só, não garante maior retenção.

Desta forma, podemos definir alguns objetivos-chave (OKRs) e indicadores de desempenho (KPIs), como:

Objetivo-chave 1: Reavaliar a estratégia de vendas e o portfólio de produtos, garantindo maior satisfação para clientes que contratam múltiplos serviços. Neste caso, empregamos KPIs como o número médio de produtos por cliente, a taxa de churn por número de produtos, a razão entre taxa de adesão e cancelamento, e o número de reclamações por quantidade de produtos.

Objetivo-chave 2: Implementar estratégias de retenção de clientes que residem na Alemanha, de forma a reduzir a taxa de churn neste país. Para este objetivo, podemos utilizar como KPI a taxa de churn por país de residência.

Objetivo-chave 3: Melhorar a experiência dos clientes que entram em contato com o atendimento, utilizando KPIs como o percentual de clientes que registram reclamações, a taxa de churn pós-reclamação, o tempo médio de resolução e o índice de satisfação do cliente (CSAT).

Objetivo-chave 4: Desenvolver e aplicar estratégias de retenção direcionadas a clientes com idade entre 36 e 60 anos, com o objetivo de reduzir a taxa de churn nesse segmento específico. Empregamos como KPI a taxa de churn por faixa etária.

Objetivo-chave 5: Implementar ações de retenção voltadas a clientes com maior tempo de contrato, por meio da oferta de novos benefícios e incentivos personalizados, visando aumentar a fidelização desse público. Neste caso, os KPIs indicados são a taxa de sucesso das campanhas de retenção, a redução do churn em clientes de alto risco, o custo por cliente retido e o ROI das iniciativas de retenção.
