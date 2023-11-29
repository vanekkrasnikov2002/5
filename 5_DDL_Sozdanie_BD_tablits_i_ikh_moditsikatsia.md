## Тема 5. DDL Создание БД, таблиц  и их модификация 

## Управление таблицами: создание, изменение, удаление

### Создание таблицы
Для создания таблицы в PostgreSQL используется оператор `CREATE TABLE`.

```sql
CREATE TABLE employees (
    employee_id serial PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    hire_date DATE
);
```

### Изменение таблицы
Для изменения структуры существующей таблицы в PostgreSQL используется оператор `ALTER TABLE`. 
```sql
ALTER TABLE employees
ADD email VARCHAR(100);
```



### Удаление таблицы
Чтобы удалить таблицу, используется оператор `DROP TABLE`. Например:

```sql
DROP TABLE employees;
```


## Создание первичного ключа (PRIMARY KEY)

Первичный ключ (PRIMARY KEY) используется для уникальной идентификации записей в таблице. Для создания первичного ключа, можно добавить ключевое слово `PRIMARY KEY` к одному или нескольким столбцам при создании таблицы.

```sql
DROP TABLE chair;
CREATE TABLE chair
(
	cathedra_id serial PRIMARY KEY,
	chair_name varchar,
	dean varchar
);

INSERT INTO chair
VALUES (1, 'name', 'dean');


INSERT INTO chair
VALUES (1, 'name', 'dean');


INSERT INTO chair
VALUES (NULL, 'name', 'dean');


INSERT INTO chair
VALUES (2, 'name', 'dean');


DROP TABLE chair;
CREATE TABLE chair
(
	cathedra_id serial UNIQUE NOT NULL,
	chair_name varchar,
	dean varchar
);
ALTER TABLE chair
DROP CONSTRAINT chair_cathedra_id_key

select constraint_name
from information_schema.key_column_usage
where table_name = 'chair'
  and table_schema = 'public'
  and column_name = 'cathedra_id';
  
ALTER TABLE chair
ADD PRIMARY KEY(chair_id);
```

## Создание внешнего ключа (FOREIGN KEY)

Внешний ключ (FOREIGN KEY) используется для связи двух таблиц. 

```sql
CREATE TABLE public.publisher
(
    publisher_id integer NOT NULL,
    publisher_name varchar(128) NOT NULL,
    address text NOT NULL,
    
	CONSTRAINT PK_publisher_id PRIMARY KEY(publisher_id)
);

CREATE TABLE public.book
(
    book_id integer NOT NULL,
    title text NOT NULL,
    isbn character varying(32) NOT NULL,
    publisher_id integer NOT NULL,
	
	CONSTRAINT PK_book_book_id PRIMARY KEY(book_id)
);



INSERT INTO publisher 
VALUES 
(1, 'Everyman''s Library', 'NY'),
(2, 'Oxford University Press', 'NY'),
(3, 'Grand Central Publishing', 'Washington'),
(4, 'Simon & Schuster', 'Chicago');


INSERT INTO book
VALUES 
(1, 'The Diary of a Young Girl', '0199535566', 10); 

SELECT *
FROM book;

TRUNCATE TABLE book;

ALTER TABLE book
ADD CONSTRAINT fk_books_publisher FOREIGN KEY(publisher_id) REFERENCES publisher(publisher_id);


insert into book
values 
(1, 'The Diary of a Young Girl', '0199535566', 10);


DROP TABLE book;

CREATE TABLE public.book
(
    book_id integer NOT NULL,
    title text NOT NULL,
    isbn character varying(32) NOT NULL,
    publisher_id integer NOT NULL,
	
	CONSTRAINT PK_book_book_id PRIMARY KEY(book_id),
	CONSTRAINT FK_book_publisher FOREIGN KEY (publisher_id) REFERENCES publisher(publisher_id)
);


ALTER TABLE book
DROP CONSTRAINT FK_book_publisher;
```

## Ограничение CHECK

Ограничение CHECK позволяет задать условие, которое должно выполняться для значений в столбце.

```sql
DROP TABLE IF EXISTS book;

CREATE TABLE public.book
(
    book_id integer NOT NULL,
    title text NOT NULL,
    isbn character varying(32) NOT NULL,
    publisher_id integer NOT NULL,
	  
	CONSTRAINT PK_book_book_id PRIMARY KEY(book_id)
);

ALTER TABLE book
ADD COLUMN price decimal CONSTRAINT CHK_book_price CHECK (price > 0);

INSERT INTO book
VALUES
(1, 'title', 'isbn', 1, -1.5);
```

## DEFAULT - значения по умолчанию

Можно указать значения по умолчанию для столбцов, которые будут использоваться, если не указано другое значение при вставке.

```sql
CREATE TABLE customer
(
	customer_id serial,
	full_name text,	
	status char DEFAULT 'r',
	
	CONSTRAINT PK_customer_id PRIMARY KEY(customer_id),
	CONSTRAINT CHK_customer_status CHECK (status = 'r' or status = 'p')
);

INSERT INTO customer
VALUES
(1, 'name');

SELECT *
FROM customer;

INSERT INTO customer
VALUES
(1, 'name', 'd');

ALTER TABLE customer
ALTER COLUMN status DROP DEFAULT;

ALTER TABLE customer
ALTER COLUMN status SET DEFAULT 'r';
```

## Последовательности в PostgreSQL - CREATE SEQUENCE

Последовательности (sequences) используются для автоматической генерации уникальных числовых значений. 

```sql
CREATE SEQUENCE seq;

SELECT nextval('seq');
SELECT currval('seq');
SELECT lastval();

--
SELECT setval('seq', 10);
SELECT currval('seq');
SELECT nextval('seq');

SELECT setval('seq', 16, false);
SELECT currval('seq');
SELECT nextval('seq');

--
CREATE SEQUENCE IF NOT EXISTS seq2 INCREMENT 16;
SELECT nextval('seq2');

--
CREATE SEQUENCE IF NOT EXISTS seq3
INCREMENT 16
MINVALUE 0
MAXVALUE 128
START WITH 0

SELECT nextval('seq3');

ALTER SEQUENCE seq3 RENAME TO seq4
ALTER SEQUENCE seq4 RESTART WITH 16
SELECT nextval('seq4');

DROP SEQUENCE seq4;

DROP TABLE IF EXISTS book;

CREATE TABLE public.book
(
    book_id int NOT NULL,
    title text NOT NULL,
    isbn varchar(32) NOT NULL,
    publisher_id int NOT NULL,
	  
	CONSTRAINT PK_book_book_id PRIMARY KEY(book_id)
);

CREATE SEQUENCE IF NOT EXISTS book_book_id_seq
START WITH 1 OWNED BY book.book_id;

INSERT INTO book (title, isbn, publisher_id)
VALUES ('title', 'isbn', 3);


ALTER TABLE book
ALTER COLUMN book_id SET DEFAULT nextval('book_book_id_seq');

INSERT INTO book (title, isbn, publisher_id)
VALUES ('title', 'isbn', 3);

SELECT * 
FROM book;

INSERT INTO book (title, isbn, publisher_id)
VALUES ('title2', 'isbn2', 3);

SELECT * 
FROM book;


INSERT INTO book
VALUES ('title2', 'isbn2', 3);

INSERT INTO book (title, isbn, publisher_id)
VALUES ('title3', 'isbn3', 3)
RETURNING book_id;
```

## Последовательности и таблицы - SERIAL

Тип данных SERIAL в PostgreSQL используется для создания столбцов, автоматически заполняемых уникальными числовыми значениями.

```sql
CREATE TABLE employees (
    employee_id serial PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50)
);
```



## INSERT - Вставка данных в таблицу

Чтобы вставить данные в таблицу, используется оператор `INSERT INTO`.

```sql
INSERT INTO author
VALUES (10, 'John Silver', 4.5);

SELECT * FROM author;

INSERT INTO author(author_id, full_name)
VALUES
(12, 'Name 1'),
(13, 'Name 2'),
(14, 'Name 3');

SELECT *
INTO best_authors
FROM author
WHERE rating > 4.5;

SELECT * FROM best_authors;
```

## UPDATE, DELETE, RETURNING - обновление и удаление данных из таблицы

Для обновления данных в таблице используется оператор `UPDATE`, а для удаления данных - оператор `DELETE`.

```sql
SELECT * FROM author;

UPDATE author
SET full_name = 'Elias', rating = 5
WHERE author_id = 1;

DELETE FROM author
WHERE rating < 4.5;

DELETE FROM author;

TRUNCATE TABLE author;

DROP TABLE book;

CREATE TABLE book
(
	book_id serial,
	title text NOT NULL,
	isbn varchar(32) NOT NULL,
	publisher_id int NOT NULL,
	
	CONSTRAINT PK_book_book_id PRIMARY KEY(book_id)
);

INSERT INTO book(title, isbn, publisher_id)
VALUES ('title', 'isbn', 3)
RETURNING *;

UPDATE author
SET full_name = 'Walter', rating = 5
WHERE author_id = 1
RETURNING author_id;

DELETE FROM author
WHERE rating = 5
RETURNING *;
```