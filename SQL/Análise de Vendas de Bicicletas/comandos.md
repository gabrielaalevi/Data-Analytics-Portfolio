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

**Pergunta de Negócio 1: quais são os itens que mais venderam nos últimos 2 anos?**

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

**Pergunta de Negócio 3: quais lojas geraram mais lucro para a empresa nos últimos 2 anos?**

Enquanto a loja Baldwin Bikes é que mais vende em termos de quantidade, é possível que o lucro não seja distribuído da mesma forma. Talvez as outras lojas direcionem seus clientes para produtos mais caros. Desta forma, enquanto a quantidade de produtos vendidos é relativamente baixa, o lucro da loja é alto. Para checar esta hipótese, executamos:

```
SELECT 
	o.store_id,
	s.store_name,
	SUM(items.list_price - items.discount) AS total_profit
FROM orders AS o
INNER JOIN order_items AS items
ON o.order_id = items.order_id
INNER JOIN stores AS s
ON o.store_id = s.store_id
GROUP BY o.store_id, s.store_name
ORDER BY total_profit DESC;
```
E obtemos o output:

![Imagem 3](https://github.com/gabrielaalevi/Data-Analytics-Portfolio/blob/main/SQL/Análise%20de%20Vendas%20de%20Bicicletas/Imagens/img_3.png)

Vemos que a loja Baldwin Bikes é a responsável pelo maior lucro também. Logo, é provável que as 3 lojas vendam produtos com a mesma distribuição de preço.

**Pergunta de Negócio 4: onde moram a maioria dos clientes das lojas?**

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

**Pergunta de Negócio 5: quais estados compraram mais itens nos últimos dois anos?**

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

**Pergunta de Negócio 6: quais estados geraram mais lucro nos últimos 2 anos?**

Para esta pergunta, executamos a consulta:

```
SELECT
	cust.state_adress,
	SUM(list_price) AS total_sold
FROM customers AS cust
INNER JOIN orders AS ord
ON cust.customer_id = ord.customer_id
INNER JOIN order_items AS items
ON ord.order_id = items.order_id
GROUP BY cust.state_adress
ORDER BY total_sold DESC;
```

E obtemos:



**Pergunta de Negócio 7: quais funcionários venderam mais itens nos últimos 2 anos?**

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

(IMG 7)

**Pergunta de Negócio 8: quais funcionários geraram mais lucro nos últimos 2 anos?**

```
SELECT
	staff.first_name,
	SUM(items.list_price) AS total_sold
FROM staff AS staff
INNER JOIN orders AS ord
ON staff.staff_id = ord.staff_id
INNER JOIN order_items AS items
ON ord.order_id = items.order_id
GROUP BY staff.staff_id
ORDER BY total_sold DESC;
```

(IMG 8)

**Pergunta de Negócio 9: quais itens receberam descontos mais vezes nos últimos 2 anos?**

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

(IMG 9)

**Pergunta de Negócio 10: qual item recebeu o maior desconto total nos últimos 2 anos?**

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

(IMG 10)


