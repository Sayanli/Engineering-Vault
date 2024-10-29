![[Pasted image 20240921121816.png]]

![[Pasted image 20240921121830.png]]

```sql
// user
id | firstname | lastname | birth
1  | Ivan      | Petrov   | 1996-05-01
2  | Anna      | Petrova  | 1999-06-01      
3  | Anna      | Petrova  | 1990-10-02
      
// purchase
sku| price | user_id | date
1  | 5500  | 1       | 2021-02-15
1  | 5700  | 1       | 2021-01-15
2  | 4000  | 1       | 2021-02-14
3  | 8000  | 2       | 2021-03-01
4  | 400   | 2       | 2021-03-02

// ban_list
user_id | date_from   
1       | 2021-03-08


-- Вывести уникальные комбинации пользователя и id товара для всех покупок, совершенных пользователями до того, как их забанили.
-- Отсортировать сначала по имени пользователя, потом по SKU (id товара)

SELECT 
    user.id,
    user.firstname,
    user.lastname,
    purchase.sku
    FROM purchase
    JOIN user ON purchase.user_id = user.id
    LEFT JOIN ban_list AS bl ON user.id = bl.user_id
DISTINCT ON
    (user.id, purchase.sku)
WHERE
    bl.date_from > purchase.date
    OR bl IS NULL
ORDER BY
    user.firstname, user.lastname, purchase.sku
;

-- Найти пользователей, которые совершили покупок на сумму больше 5000р. Вывести их имена в формате id пользователя | имя | фамилия | сумма покупок
SELECT
    user.id,
    user.firstname,
    user.lastname,
    subquery.sumprice
    JOIN (
        SELECT
            user.id AS user_id,
            SUM(purchase.price) AS sumprice
        GROUP BY
            user.id
        HAVING
            SUM(purchase.price) > 5000
    ) AS subquery ON subquery.user_id = user.id
;


SELECT
    user.id,
    user.firstname,
    user.lastname,
    SUM(purchase.price)
GROUP BY
    user.id, user.firstname, user.lastname
HAVING
    SUM(purchase.price) > 5000
;
```

![[Attachments/Pasted image 20240929184318.png]]

```sql
Есть 3 сущности: "Автор", "Книга", "Читатель"
Физически книга только одна и может быть только у одного читателя.
Нужно составить таблицы для библиотеки так что бы это учесть.

-- is_active BOOL DEFAULT TRUE
-- updated_at TIMESTAMP DEfAULT NOW()

CREATE TABLE IF NOT EXISTS author (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL
);

CREATE TABLE IF NOT EXISTS reader (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL
);

-- book UNIQUE(name) + book_item
CREATE TABLE IF NOT EXISTS book (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    reader_id BIGINT REFERECES reader(id)
);

CREATE TABLE IF NOT EXISTS author_book (
    book_id BIGINT NOT NULL REFERENCES book(id),
    author_id BIGINT NOT NULL REFERENCES author(id),
    CONSTRAINT author_book_pk PRIMARY KEY (book_id, author_id)
);

1) Написать запрос - выбрать названия всех книг которые на руках
SELECT
    book.name,
    author.name
FROM
    book
JOIN
    author_book ab ON ab.book_id = book.id
JOIN
    author ON ab.author_id = author.id
WHERE
    reader_id IS NOT NULL
;

2) Написать запрос - выбрать названия всех книг в библиотеке у которых больше 3 авторов
SELECT
    book.id,
    book.name
FROM
    book
JOIN
    author_book ab ON ab.book_id = book.id
GROUP BY
    book.id, book.name
HAVING
    COUNT(*) > 3
ORDER BY
    book.id
;

3) Написать запрос - выбрать имена топ 3 читаемых авторов на данный момент

SELECT
    author.id,
    author.name
FROM
    author
JOIN
    author_book ab ON ab.author_id = author.id
JOIN (
    SELECT
        book.name,
        author.id AS author_id
    FROM
        book
    JOIN
        author_book ab ON ab.book_id = book.id
    JOIN
        author ON ab.author_id = author.id
    WHERE
        reader_id IS NOT NULL
) AS sub ON sub.author_id = author.id
GROUP BY
    author.id,
    author.name
ORDER BY
    SUM(author.id)
LIMIT
    3
;


SELECT
    author.id,
    author.name
FROM
    author
JOIN
    author_book ab ON ab.author_id = author.id
JOIN
    book ON ab.book_id = book.id 
WHERE
     book.reader_id IS NOT NULL
GROUP BY
    author.id,
    author.name
ORDER BY
    COUNT(author.id) DESC
LIMIT
    3
;



3) Построить оптимальный индекс для

SELECT * FROM employee WHERE sex = 'm' AND salary > 300000 AND age = 20 ORDER BY created_at;

CREATE INDEX ON employee USING btree (age, salary);
-- (age, created_at)


SELECT * FROM employee WHERE sex = 'w' AND name like '$1%';
-- Допустим у нас в IT-шная контора и в ней мало женщин в ней. Как построить индекс, чтобы не занимать много места?
-- 'w' < 5%
CREATE INDEX ON employee USING btree (sex, name) WHERE sex = 'w';
```


