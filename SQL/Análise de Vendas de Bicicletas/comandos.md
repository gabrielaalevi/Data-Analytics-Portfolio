Este projeto tem como objetivo a análise de dados de vendas de uma loja de bicicletas utilizando SQL como principal ferramenta e o PostgreSQL como plataforma. Ao longo do trabalho, são explorados dados relacionados a produtos, clientes e pedidos, com o intuito de extrair insights relevantes para o negócio e apoiar a tomada de decisão.

O arquivo reúne os códigos SQL executados, capturas de tela dos resultados obtidos e respostas a perguntas de negócio relevantes, como identificação de produtos mais vendidos, desempenho de vendas ao longo do tempo e comportamento dos clientes. O foco do projeto é demonstrar habilidades práticas em manipulação de dados, escrita de consultas SQL e interpretação de resultados no contexto de um problema real de negócio.

Primeiramente, é necessário criar as nove tabelas relacionadas no PostgreSQL:

```
CREATE TABLE brands (
	brand_id INT PRIMARY KEY,
	brand_name VARCHAR(255)
);
```
```
CREATE TABLE categories(
	category_id INT PRIMARY KEY,
	category_name VARCHAR(255)
);
```
```
CREATE TABLE products(
	product_id INT PRIMARY KEY,
	product_name VARCHAR(255) NOT NULL,
	brand_id INT REFERENCES brands(brand_id),
	category_id INT REFERENCES categories(category_id),
	model_year INT,
	list_price FLOAT
);
```
```
CREATE TABLE customers(
	customer_id BIGINT PRIMARY KEY,
	first_name VARCHAR(255) NOT NULL,
	last_name VARCHAR(255) NOT NULL,
	phone VARCHAR(255),
	email VARCHAR(255) NOT NULL,
	street VARCHAR(255) NOT NULL,
	city VARCHAR(255) NOT NULL,
	state_adress VARCHAR(255) NOT NULL,
	zip_code VARCHAR(255) NOT NULL
);
```
```
CREATE TABLE stores(
	store_id BIGINT PRIMARY KEY,
	store_name VARCHAR(255) UNIQUE,
	phone VARCHAR(255),
	email VARCHAR(255) NOT NULL,
	street VARCHAR(255) NOT NULL,
	city VARCHAR(255) NOT NULL,
	state_adress VARCHAR(255) NOT NULL,
	zip_code VARCHAR(255) NOT NULL
);
```
```
CREATE TABLE staff(
	staff_id BIGINT PRIMARY KEY,
	first_name VARCHAR(255) NOT NULL,
	last_name VARCHAR(255) NOT NULL,
	email VARCHAR(255) NOT NULL,
	phone VARCHAR(255),
	active BOOL NOT NULL,
	store_id BIGINT REFERENCES stores(store_id),
	manager_id BIGINT
);
```
```
CREATE TABLE orders(
	order_id BIGINT PRIMARY KEY,
	customer_id BIGINT REFERENCES customers(customer_id),
	order_status VARCHAR(255) NOT NULL,
	order_date DATE NOT NULL,
	required_date DATE,
	shipped_date VARCHAR(255),
	store_id BIGINT REFERENCES stores(store_id),
	staff_id BIGINT REFERENCES staff(staff_id)
);
```
```
CREATE TABLE stocks(
	store_id BIGINT REFERENCES stores(store_id),
	product_id BIGINT REFERENCES products(product_id),
	quantity INT DEFAULT 0
);
```
```
CREATE TABLE order_items(
	order_id BIGINT REFERENCES orders(order_id),
	item_id BIGINT,
	product_id BIGINT REFERENCES products(product_id),
	quantity INT NOT NULL,
	list_price FLOAT,
	discount FLOAT
);
```

Então, inserimos os dados dos arquivos .csv no banco de dados:

```
COPY brands FROM 'C:\Program Files\PostgreSQL\Database\brands.csv' delimiter ',' CSV HEADER;
COPY categories FROM 'C:\Program Files\PostgreSQL\Database\categories.csv' delimiter ',' CSV HEADER;
COPY products FROM 'C:\Program Files\PostgreSQL\Database\products.csv' delimiter ',' CSV HEADER;
COPY customers FROM 'C:\Program Files\PostgreSQL\Database\customers.csv' delimiter ',' CSV HEADER;
COPY stores FROM 'C:\Program Files\PostgreSQL\Database\stores.csv' delimiter ',' CSV HEADER;
COPY staff FROM 'C:\Program Files\PostgreSQL\Database\staffs.csv' delimiter ',' CSV HEADER;
COPY orders FROM 'C:\Program Files\PostgreSQL\Database\orders.csv' delimiter ',' CSV HEADER;
COPY stocks FROM 'C:\Program Files\PostgreSQL\Database\stocks.csv' delimiter ',' CSV HEADER;
COPY order_items FROM 'C:\Program Files\PostgreSQL\Database\order_items.csv' delimiter ',' CSV HEADER;
```

Antes de iniciar a análise, é importante verificar a consistência da base de dados. O preço dos produtos está presente em duas tabelas distintas: products e order_items. Dessa forma, é necessário validar se os valores registrados nesses dois campos são consistentes para cada produto. 

Para realizar essa verificação, foi executada a seguinte consulta:

```
SELECT
	p.list_price,
	items.list_price,
	ABS(p.list_price - items.list_price) AS diff
FROM products AS p
INNER JOIN order_items AS items
ON p.product_id = items.product_id
WHERE ABS(p.list_price - items.list_price)>0;
```

Esta query retorna uma tabela vazia, o que significa que todos os preços estão concordando entre as tabelas. Agora, podemos iniciar nossa análise, respondendo algumas perguntas de negócio relevantes.

**Pergunta de Negócio 1: quais são os itens que mais venderam nos últimos 3 anos?**

Para responder esta questão, executamos a consulta:

```
SELECT o.product_id,
	p.product_name,
	SUM(o.quantity) AS total_sold
FROM order_items AS o
INNER JOIN products AS p
ON o.product_id = p.product_id
GROUP BY o.product_id, p.product_name
ORDER BY total_sold DESC;
```

O output dado pelo nosso banco de dados é:

![Imagem 1](https://github.com/gabrielaalevi/Data-Analytics-Portfolio/blob/main/SQL/Análise%20de%20Vendas%20de%20Bicicletas/Imagens/img_1.png)

Com estas informações, concluimos que os produtos mais vendidos são aqueles com IDs 7, 12, 11, 25 e 10.

**Pergunta de Negócio 2: quais são as lojas que venderam mais itens nos últimos 2 anos?**

Para responder esta pergunta, a consulta a ser executada é:

```
SELECT 
	o.store_id,
	s.store_name,
	SUM(items.quantity) AS total_sold
FROM orders AS o
INNER JOIN order_items AS items
ON o.order_id = items.order_id
INNER JOIN stores AS s
ON o.store_id = s.store_id
GROUP BY o.store_id, s.store_name
ORDER BY total_sold DESC;
```

E obtemos como output:

![Imagem 2](https://github.com/gabrielaalevi/Data-Analytics-Portfolio/blob/main/SQL/Análise%20de%20Vendas%20de%20Bicicletas/Imagens/img_2.png)

Portanto, vemos que a Loja Baldwin Bikes é de longe a que mais vende produtos.

**Pergunta de Negócio 3: quais lojas geraram mais lucro para a empresa nos últimos 3 anos?**

Enquanto a loja Baldwin Bikes é que mais vende em termos de quantidade, é possível que o lucro não seja distribuído da mesma forma. Talvez as outras lojas direcionem seus clientes para produtos mais caros. Desta forma, enquanto a quantidade de produtos vendidos é relativamente baixa, o lucro da loja é alto. Para checar esta hipótese, executamos:

```
SELECT 
	o.store_id,
	s.store_name,
	SUM(items.list_price * items.quantity - items.discount) AS total_profit
FROM orders AS o
INNER JOIN order_items AS items
ON o.order_id = items.order_id
INNER JOIN stores AS s
ON o.store_id = s.store_id
GROUP BY o.store_id, s.store_name
ORDER BY total_profit DESC;
```
E obtemos o output:

![Imagem 3](https://github.com/gabrielaalevi/Data-Analytics-Portfolio/blob/main/SQL/Análise%20de%20Vendas%20de%20Bicicletas/Imagens/img_2_2.png)

Vemos que a loja Baldwin Bikes é a responsável pelo maior lucro também. Por fim, podemos analisar o lucro médio de cada loja, executando:

```
SELECT
	o.store_id,
	s.store_name,
	SUM(items.list_price * items.quantity - items.discount)/SUM(items.quantity) AS lucro_medio
FROM orders AS o
INNER JOIN order_items AS items
ON o.order_id = items.order_id
INNER JOIN stores AS s
ON o.store_id = s.store_id
GROUP BY o.store_id, s.store_name
ORDER BY lucro_medio DESC;
```

Para obter:

![Imagem 3.5](https://github.com/gabrielaalevi/Data-Analytics-Portfolio/blob/main/SQL/Análise%20de%20Vendas%20de%20Bicicletas/Imagens/imagem_3_2_2.png)

Neste caso, vemos que a loja Rowlett Bikes é a loja que possui maior lucro médio (ou seja, a loja que vende os produtos mais caros).

**Pergunta de Negócio 4: onde moram a maioria dos clientes da rede?**

Para responder esta pergunta, olhamos na nossa base de dados qual o estado com maior número de clientes:

```
SELECT
	state_adress,
	COUNT(*) AS total_customers
FROM customers
GROUP BY state_adress
ORDER BY total_customers DESC;
```
E obtemos:

![Imagem 4](https://github.com/gabrielaalevi/Data-Analytics-Portfolio/blob/main/SQL/Análise%20de%20Vendas%20de%20Bicicletas/Imagens/img_4.png)

Vemos que o estado com maior número de clientes é Nova York. É relevante notar que a loja Baldwin Bikes está localizada em Nova York. Logo, o estado com maior número de clientes é a localização da loja com maior número de vendas, uma conexão importante e previsível.

**Pergunta de Negócio 5: quais estados compraram mais itens nos últimos três anos?**

Neste caso, executamos:

```
SELECT
	cust.state_adress,
	SUM(quantity) AS total_sold
FROM customers AS cust
INNER JOIN orders AS ord
ON cust.customer_id = ord.customer_id
INNER JOIN order_items AS items
ON ord.order_id = items.order_id
GROUP BY cust.state_adress
ORDER BY total_sold DESC;
```
E obtemos o output:

![Imagem 5](https://github.com/gabrielaalevi/Data-Analytics-Portfolio/blob/main/SQL/Análise%20de%20Vendas%20de%20Bicicletas/Imagens/img_5.png)

Novamente, o estado de Nova York lidera a quantidade de vendas, devido à maior quantidade de clientes.

**Pergunta de Negócio 6: quantos produtos um cliente compra em média, por estado/por loja?**

Para esta pergunta, executamos a consulta:

```
SELECT
	st.store_name,
	st.state_adress,
	(SUM(items.quantity)/COUNT(DISTINCT ord.customer_id)) AS produtos_comprados_medio
FROM stores AS st
INNER JOIN orders AS ord
ON st.store_id = ord.store_id
INNER JOIN order_items AS items
ON ord.order_id = items.order_id
GROUP BY st.state_adress, st.store_name
ORDER BY produtos_comprados_medio DESC;
```

E obtemos:

![Imagem 6](https://github.com/gabrielaalevi/Data-Analytics-Portfolio/blob/main/SQL/Análise%20de%20Vendas%20de%20Bicicletas/Imagens/img_6_2.png)

Neste caso, vemos que os clientes das lojas Rowlett Bikes e Santa Cruz Bikes tendem a comprar mais produtos do que os clientes da loja Baldwin Bikes. Portanto, a loja Baldwin Bikes produz o maior lucro por ter o maior número de clientes, não por vender os produtos mais caros ou por vender mais produtos por cliente.

**Pergunta de Negócio 7: quais funcionários venderam mais itens nos últimos 3 anos?**

Neste caso, a query indicada é:

```
SELECT
	staff.first_name,
	SUM(items.quantity) AS total_sold
FROM staff AS staff
INNER JOIN orders AS ord
ON staff.staff_id = ord.staff_id
INNER JOIN order_items AS items
ON ord.order_id = items.order_id
GROUP BY staff.staff_id
ORDER BY total_sold DESC;
```

![Imagem 7](https://github.com/gabrielaalevi/Data-Analytics-Portfolio/blob/main/SQL/Análise%20de%20Vendas%20de%20Bicicletas/Imagens/img_7.png)

Vemos que Marcelene é a funcionária que vendeu a maior quantidade de produtos nos últimos 2 anos. Infelizmente, nossa base de dados não possui informações sobre quando cada funcionário foi contratado. Caso contrário, seria possível calcular o número de produtos vendidos por dia, de forma a não favorecer os funcionários mais antigos da empresa.

**Pergunta de Negócio 8: quais funcionários geraram mais lucro nos últimos 2 anos?**

```
SELECT
	staff.first_name,
	SUM(items.list_price * items.quantity) AS lucro_total
FROM staff AS staff
INNER JOIN orders AS ord
ON staff.staff_id = ord.staff_id
INNER JOIN order_items AS items
ON ord.order_id = items.order_id
GROUP BY staff.staff_id
ORDER BY lucro_total DESC;
```

E o output devolvido pelo SQL:

![Imagem 8](https://github.com/gabrielaalevi/Data-Analytics-Portfolio/blob/main/SQL/Análise%20de%20Vendas%20de%20Bicicletas/Imagens/img_8_2.png)

Neste caso, o lucro total gerado segue a mesma ordem do número de produtos vendidos. Porém, podemos analisar qual o valor médio de cada venda feita, para ver qual funcionário vende produtos mais caros.

```
SELECT
	staff.first_name,
	SUM(items.list_price * items.quantity)/SUM(items.quantity) AS lucro_medio
FROM staff AS staff
INNER JOIN orders AS ord
ON staff.staff_id = ord.staff_id
INNER JOIN order_items AS items
ON ord.order_id = items.order_id
GROUP BY staff.staff_id
ORDER BY lucro_medio DESC;
```

Esta consulta retorna:

![Imagem 8-2](https://github.com/gabrielaalevi/Data-Analytics-Portfolio/blob/main/SQL/Análise%20de%20Vendas%20de%20Bicicletas/Imagens/img_8_2_2.png)

Agora, vemos que Kali e Venita são as vendedoras que vendem produtos mais caros. Na tabela anterior, vimos que Kali era uma das vendedoras com menor lucro total. Neste caso, é possível que Kali tenha recebido instruções para se especializar em categorias mais caras, que normalmente recebem menos vendas. Com isso, seu lucro total será impactado, mas não seu lucro médio, como foi observado.

**Pergunta de Negócio 9: quais itens receberam descontos mais vezes nos últimos 3 anos?**

A consulta:

```
SELECT
	prod.product_name,
	COUNT(items.discount) AS discount_times
FROM order_items AS items
INNER JOIN products AS prod
ON items.product_id = prod.product_id
GROUP BY prod.product_name
ORDER BY discount_times DESC;
```

gera como output:

![Imagem 9](https://github.com/gabrielaalevi/Data-Analytics-Portfolio/blob/main/SQL/Análise%20de%20Vendas%20de%20Bicicletas/Imagens/img_9.png)

Podemos ver que os itens Electra Townie Original 21D - 2016 e Electra Cruiser 1 (24-Inch) - 2016 receberam descontos 193 vezes neste período. No entanto, não sabemos o valor exato dos descontos recebidos.

**Pergunta de Negócio 10: qual item recebeu o maior desconto total nos últimos 3 anos?**

Para ver qual foi o valor total dado de desconto por item neste período, executamos a consulta:

```
SELECT
	prod.product_name,
	SUM(items.discount) AS total_discount
FROM order_items AS items
INNER JOIN products AS prod
ON items.product_id = prod.product_id
GROUP BY prod.product_name
ORDER BY total_discount DESC;
```

E obtemos:

![Imagem 10](https://github.com/gabrielaalevi/Data-Analytics-Portfolio/blob/main/SQL/Análise%20de%20Vendas%20de%20Bicicletas/Imagens/img_10.png)

Neste caso, vemos que os itens com maior desconto também foram da linha Electra, mas não os mesmos da pergunta anterior. No entanto, o desconto total recebido por estes itens foi de 19 reais no total, o que não representa uma porcentagem significativa dos lucros da empresa.

**Pergunta de Negócio 11: qual o lucro total por ano, no período analisado?**

Executamos a consulta:

```
SELECT
	EXTRACT(YEAR FROM ord.order_date) AS year,
	SUM(items.list_price * items.quantity - items.discount) AS total_sales
FROM orders AS ord
INNER JOIN order_items AS items
ON ord.order_id = items.order_id
GROUP BY year
ORDER BY year;
```

E obtemos:


Notamos que as vendas diminuíram muito quando comparamos os anos de 2018 e 2017. Portanto, é interessante tentar descobrir o motivo desta queda. Podemos estudar a variação no número de produtos vendidos, executando:

```
SELECT
	EXTRACT(YEAR FROM ord.order_date) AS year,
	SUM(items.quantity) AS itens_sold
FROM orders AS ord
INNER JOIN order_items AS items
ON ord.order_id = items.order_id
GROUP BY year
ORDER BY year;
```

de forma a obter:

(IMG 11-1)

Vemos que a quantidade de produtos vendidos caiu bastante no ano de 2018, o que justifica a diferença entre o lucro total encontrada. É possível separar esta diferença em categorias, para entender quais foram os tipos de produto cujas vendas foram mais afetadas:

```
SELECT
	cat.category_name,
	SUM(CASE WHEN(EXTRACT(YEAR FROM ord.order_date)) = 2016 THEN items.quantity ELSE 0 END) AS sales_2016,
	SUM(CASE WHEN(EXTRACT(YEAR FROM ord.order_date)) = 2017 THEN items.quantity ELSE 0 END) AS sales_2017,
	SUM(CASE WHEN(EXTRACT(YEAR FROM ord.order_date)) = 2018 THEN items.quantity ELSE 0 END) AS sales_2018
FROM orders AS ord
INNER JOIN order_items AS items
ON ord.order_id = items.order_id
INNER JOIN products AS prod
ON items.product_id = prod.product_id
INNER JOIN categories AS cat
ON prod.category_id = cat.category_id
GROUP BY category_name;
```

(IMG 11-2)

Neste caso, vemos que as categorias Comfort Bicycles, Cruisers Bicycles, Mouintain Bikes, Cyclocross Bikes e Childrens Bikes foram as mais afetadas pela queda. Isto pode ser causado por mudanças de interesse no público geral. Já analisando as vendas por loja, executando:

```
SELECT
	stores.store_name,
	SUM(CASE WHEN(EXTRACT(YEAR FROM ord.order_date)) = 2016 THEN items.quantity ELSE 0 END) AS sales_2016,
	SUM(CASE WHEN(EXTRACT(YEAR FROM ord.order_date)) = 2017 THEN items.quantity ELSE 0 END) AS sales_2017,
	SUM(CASE WHEN(EXTRACT(YEAR FROM ord.order_date)) = 2018 THEN items.quantity ELSE 0 END) AS sales_2018
FROM orders AS ord
INNER JOIN order_items AS items
ON ord.order_id = items.order_id
INNER JOIN stores
ON ord.store_id = stores.store_id
GROUP BY stores.store_name;
```

(IMG 11-3)

Enquanto todas as lojas registraram quedas nas vendas de 2018, a loja Baldwin Bikes recebeu, em 2018, menos da metade das vendas de 2017. Portanto, seria interessante verificar o que foi alterado na loja (como corpo de funcionários, organização das lojas, propagandas) para entender o que causou esta queda.


