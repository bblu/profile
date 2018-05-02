---
title: Different Join queries in postgresql9.6
date: 2018-04-26 21:17
categories: blog
tags: [sql,postgresql]
---
> PostgreSQL is a powerful, open source object-relational database system with over 30 years of active development that has earned it a strong reputation for reliability, feature robustness, and performance. It's postgis plugin is powerful for special data and that's the reason why I used it in my work.talk is cheap show you the code now.

### 0.create tables about customer、books and order
```sql
CREATE TABLE IF NOT EXISTS  customer(
    id integer NOT NULL,
    name varchar NOT NULL
);

CREATE TABLE IF NOT EXISTS book(
    id integer NOT NULL,
    name varchar NOT NULL
);
-- or use CREATE TABLE "order" if you like
CREATE TABLE IF NOT EXISTS bookorder(
    customer integer NOT NULL,
    book integer NOT NULL
);

CREATE UNIQUE INDEX IF NOT EXISTS idx_cid ON customer (id);
CREATE UNIQUE INDEX IF NOT EXISTS idx_bid ON book (id);
```

### 1.insert demo data into tables
```sql
INSERT INTO public.customer(id, name)
    VALUES 
    (0, 'customer0'),
    (1, 'customer1'),
    (2, 'customer2'),
    (3, 'customer3'),
    (4, 'customer4');
    
INSERT INTO public.book(id, name)
    VALUES 
    (0, 'book0'),
    (1, 'book1'),
    (2, 'book2'),
    (3, 'book3'),
    (4, 'book4');
    
INSERT INTO public.bookorder(book,customer)
    VALUES 
    (0, 1),
    (1, 1),
    (4, 3);
```

### 2.let's see a picture first
and we will learn about the join clause in general 
![join table](../assets/in-post/2018-04-26-sql-joins.png)

### 3.different between INNER and ORTER Join

#### 3.1.INNER JOIN query which bookes in ordertable
> Inner Join does not distinguish between left and right,It's the common part of the left and right tables of join clause.

```sql
SELECT b.name AS book,o.book bookId
FROM public.book AS b
INNER JOIN public.bookorder o ON o.book = b.id

--book bookid
-------------
--"book0";0
--"book1";1
--"book4";4

```

> if you use outer join the left or right Can not be ignored

#### 3.2.RIGHT OUTER JOIN 
```sql
SELECT o.book id,b.name AS book
FROM public.book AS b
RIGHT OUTER JOIN public.bookorder o ON o.book = b.id

--id  book
--------------
--0; "book0"
--1; "book1"
--4; "book4"
```

#### 3.3.LEFT OUTER JOIN 
```sql
query which bookes did not been ordered
SELECT b.name AS book,o.book bookId
FROM public.book AS b
LEFT OUTER JOIN public.bookorder o ON o.book = b.id

--book bookid
--------------
--"book0";0
--"book1";1
--"book4";4
--"book2";
--"book3";
```

### 4.different between LEFT and RIGHT Join

#### 4.1.right join use right table keys to join
```sql
-- RITHT JOIN according to the rows in rear/RITHT table
-- the table has 3 orders and that is what you want
SELECT c.name AS custorm,o.book bookId
FROM public.customer AS c
RIGHT JOIN public.bookorder o ON o.customer = c.id

--customer, bookId
-------------------
--"customer1";0
--"customer1";1
--"customer3";4
---------------------------------------------------------
--let's just exchange the tables order and see the result
SELECT c.name AS custorm,o.book bookId
FROM public.bookorder AS o
RIGHT JOIN public.customer c ON o.customer = c.id

--customer, bookId
------------------
--"customer1";0
--"customer1";1
--"customer3";4
--"customer0";
--"customer2";
--"customer4";
```

#### 4.2.left join use left table keys to join
```sql
-- RITHT JOIN according to the rows in rear/RITHT table
-- the table has 3 orders and that is what you want
SELECT c.name AS custorm,o.book bookId
FROM public.customer AS c
LEFT JOIN public.bookorder o ON o.customer = c.id

--customer, bookId
-------------------
"customer1";    0
"customer1";    1
"customer3";    4
"customer0";
"customer2";
"customer4";

```

### 5.query people who erver bought a book
```sql
SELECT c.name AS custorm, o.book bid
FROM public.customer AS c
RIGHT JOIN public.bookorder o on c.id = o.customer

-- customer  bid|
----------------|
--"customer1";1 |
--"customer1";0 |
--"customer3";4 |
```

### 6.query people who did not buy a book
```sql
SELECT c.name customer, count(o.book) bookcount
FROM public.bookorder o
INNER JOIN public.customer c on o.customer =  c.id
group by c.name

-- customer  bookcount
----------------------
--"customer1";  2
--"customer3";  1
```

### 7.query people who bought a book and what it is
```sql
-- RITHT JOIN according to the rows in rear table
-- and that is what you want
SELECT c.id, c.name AS customer,b.name book
FROM public.customer AS c
JOIN public.bookorder o ON o.customer = c.id
JOIN public.book AS b ON b.id = o.book

-- id customer    book
-------------------------
-- 1;"customer1";"book0"
-- 1;"customer1";"book1"
-- 3;"customer3";"book4"
```