Neste arquivo, apresento todos os comandos executados no PostgreSQL, junto com explicações sobre seus objetivos.

Como primeiro passo, começamos criando as nove tabelas relacionadas:
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
Com as nove tabelas criadas, podemos inserir os dados dos nossos arquivos .csv no databse:

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

Então, é interessante checar se nossa base de dados é consistente. Primeiro, notamos que o preço dos produtos aparecem em duas tabelas diferentes: products e order_items. Então, podemos checar se os valores são iguais nos dois campos para cada produto. Para isso, rodamos:

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
Esta query retorna uma tabela vazia, o que significa que todos os preços estão concordando entre as tabelas.

Agora, podemos iniciar nossa análise, tentando responder algumas perguntas de negócio relevantes.

**Pergunta de Negócio 1: quais são os itens que mais venderam nos últimos 2 anos?**

Para responder esta questão, rodamos o seguinte código:

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

E obtemos como resposta:

(IMG 1)

**Pergunta de Negócio 2: quais são as lojas que venderam mais itens nos últimos 2 anos?**

Para esta questão, rodamos:

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
E obtemos como resposta:

(IMG 2)

**Pergunta de Negócio 3: quais lojas geraram mais lucro para a empresa nos últimos 2 anos?**

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
(IMG 3)

**Pergunta de Negócio 4: onde moram a maioria dos clientes das lojas?**

```
SELECT
	state_adress,
	COUNT(*) AS total_customers
FROM customers
GROUP BY state_adress
ORDER BY total_customers DESC;
```
(IMG 4)

**Pergunta de Negócio 5: quais estados compraram mais itens nos últimos dois anos?**

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

(IMG 5)

**Pergunta de Negócio 6: quais estados geraram mais lucro nos últimos 2 anos?**

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

(IMG 6)

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


