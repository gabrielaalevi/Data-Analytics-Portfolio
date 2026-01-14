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
