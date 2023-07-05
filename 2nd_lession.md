## Задача 1

Используя Docker, поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose-манифест.
```Yaml
root@sqlvm:~/.docker/cli-plugins# cat postgre.yml
# Use postgres/example user/password credentials
version: '3.1'


services:

  db:
    image: postgres:12
    volumes:
      - /home/amaksimov/base:/base
      - /home/amaksimov/backup:/backup
    restart: always
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: 12345
      PGDATA: /base
  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
volumes:
  base:
  backup:

```

## Задача 2

В БД из задачи 1: 

- создайте пользователя test-admin-user и БД test_db;
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже);
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db;
- создайте пользователя test-simple-user;
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE этих таблиц БД test_db.

Таблица orders:

- id (serial primary key);
- наименование (string);
- цена (integer).

Таблица clients:

- id (serial primary key);
- фамилия (string);
- страна проживания (string, index);
- заказ (foreign key orders).

Приведите:

- итоговый список БД после выполнения пунктов выше;
- описание таблиц (describe);
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db;
- список пользователей с правами над таблицами test_db.

```
1. test_db=# \l
                             List of databases
   Name    | Owner | Encoding |  Collate   |   Ctype    | Access privileges
-----------+-------+----------+------------+------------+-------------------
 admin     | admin | UTF8     | en_US.utf8 | en_US.utf8 |
 postgres  | admin | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | admin | UTF8     | en_US.utf8 | en_US.utf8 | =c/admin         +
           |       |          |            |            | admin=CTc/admin
 template1 | admin | UTF8     | en_US.utf8 | en_US.utf8 | =c/admin         +
           |       |          |            |            | admin=CTc/admin
 test_db   | admin | UTF8     | en_US.utf8 | en_US.utf8 |
(5 rows)

2. test_db=# \dt+
                    List of relations
 Schema |  Name   | Type  | Owner |  Size   | Description
--------+---------+-------+-------+---------+-------------
 public | clients | table | admin | 0 bytes |
 public | orders  | table | admin | 0 bytes |
(2 rows)

3. SELECT table_catalog,table_name,grantee,privilege_type FROM information_schema.table_privileges WHERE table_schema = 'public';

4. test_db=# SELECT table_catalog,table_name,grantee,privilege_type FROM information_schema.table_privileges WHERE table_schema = 'public';
 table_catalog | table_name |     grantee      | privilege_type
---------------+------------+------------------+----------------
 test_db       | orders     | admin            | INSERT
 test_db       | orders     | admin            | SELECT
 test_db       | orders     | admin            | UPDATE
 test_db       | orders     | admin            | DELETE
 test_db       | orders     | admin            | TRUNCATE
 test_db       | orders     | admin            | REFERENCES
 test_db       | orders     | admin            | TRIGGER
 test_db       | orders     | test-admin-user  | INSERT
 test_db       | orders     | test-admin-user  | SELECT
 test_db       | orders     | test-admin-user  | UPDATE
 test_db       | orders     | test-admin-user  | DELETE
 test_db       | orders     | test-admin-user  | TRUNCATE
 test_db       | orders     | test-admin-user  | REFERENCES
 test_db       | orders     | test-admin-user  | TRIGGER
 test_db       | orders     | test-simple-user | INSERT
 test_db       | orders     | test-simple-user | SELECT
 test_db       | orders     | test-simple-user | UPDATE
 test_db       | orders     | test-simple-user | DELETE
 test_db       | clients    | admin            | INSERT
 test_db       | clients    | admin            | SELECT
 test_db       | clients    | admin            | UPDATE
 test_db       | clients    | admin            | DELETE
 test_db       | clients    | admin            | TRUNCATE
 test_db       | clients    | admin            | REFERENCES
 test_db       | clients    | admin            | TRIGGER
 test_db       | clients    | test-admin-user  | INSERT
 test_db       | clients    | test-admin-user  | SELECT
 test_db       | clients    | test-admin-user  | UPDATE
 test_db       | clients    | test-admin-user  | DELETE
 test_db       | clients    | test-admin-user  | TRUNCATE
 test_db       | clients    | test-admin-user  | REFERENCES
 test_db       | clients    | test-admin-user  | TRIGGER
 test_db       | clients    | test-simple-user | INSERT
 test_db       | clients    | test-simple-user | SELECT
 test_db       | clients    | test-simple-user | UPDATE
 test_db       | clients    | test-simple-user | DELETE
(36 rows)


```


## Задача 3

Используя SQL-синтаксис, наполните таблицы следующими тестовыми данными:

Таблица orders

|Наименование|цена|
|------------|----|
|Шоколад| 10 |
|Принтер| 3000 |
|Книга| 500 |
|Монитор| 7000|
|Гитара| 4000|

Таблица clients

|ФИО|Страна проживания|
|------------|----|
|Иванов Иван Иванович| USA |
|Петров Петр Петрович| Canada |
|Иоганн Себастьян Бах| Japan |
|Ронни Джеймс Дио| Russia|
|Ritchie Blackmore| Russia|

Используя SQL-синтаксис:
- вычислите количество записей для каждой таблицы.

Приведите в ответе:

    - запросы,
    - результаты их выполнения.

```
INSERT INTO orders (Наименование, Цена) VALUES ('Шоколад', 10), ('Принтер', 3000), ('Книга', 500), ('Монитор', 7000), ('Гитара', 4000);

INSERT INTO clients (Фамилия, "Страна проживания") VALUES ('Иванов Иван Иванович', 'USA'), ('Петров Петр Петрович', 'USA'), ('Иоганн Себастьян Бах', 'Japan'), ('Ронни Джеймс Дио', 'Russia'), ('Ritchie Blackmore', 'Russia');

test_db=# select count(*) from clients;
 count
-------
     5
(1 row)

test_db=# select count(*) from orders;
 count
-------
     5
(1 row)

```

## Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys, свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения этих операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод этого запроса.
 
Подсказка: используйте директиву `UPDATE`.

```
UPDATE clients SET Заказ = (SELECT id FROM orders WHERE "Наименование" = 'Книга') WHERE "Фамилия" = 'Иванов Иван Иванович';
UPDATE clients SET Заказ = (SELECT id FROM orders WHERE "Наименование" = 'Монитор') WHERE "Фамилия" = 'Петров Петр Петрович';
UPDATE clients SET Заказ = (SELECT id FROM orders WHERE "Наименование" = 'Гитара') WHERE "Фамилия" = 'Иоганн Себастьян Бах';

test_db=# select * from clients where Заказ IS NOT Null;
 id |       Фамилия        | Страна проживания | Заказ
----+----------------------+-------------------+-------
  1 | Иванов Иван Иванович | USA               |     3
  2 | Петров Петр Петрович | USA               |     4
  3 | Иоганн Себастьян Бах | Japan             |     5
(3 rows)

```

## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните, что значат полученные значения.

```
test_db=# EXPLAIN SELECT * FROM clients;
                         QUERY PLAN
------------------------------------------------------------
 Seq Scan on clients  (cost=0.00..15.30 rows=530 width=124)
(1 row)

Этот запрос не содержит предложения WHERE, поэтому он должен просканировать все строки таблицы, так что планировщик выбрал план простого последовательного сканирования. Числа, перечисленные в скобках (слева направо), имеют следующий смысл:

    Приблизительная стоимость запуска. Это время, которое проходит, прежде чем начнётся этап вывода данных, например для сортирующего узла это время сортировки.

    Приблизительная общая стоимость. Она вычисляется в предположении, что узел плана выполняется до конца, то есть возвращает все доступные строки. На практике родительский узел может досрочно прекратить чтение строк дочернего.

    Ожидаемое число строк, которое должен вывести этот узел плана. При этом так же предполагается, что узел выполняется до конца.

    Ожидаемый средний размер строк, выводимых этим узлом плана (в байтах).

```

## Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. задачу 1).

Остановите контейнер с PostgreSQL, но не удаляйте volumes.

Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления. 

```
pg_dump -U admin test_db > /backup/test_db.sql

root@642e04d6a02e:/backup# psql -U admin -d test_db -f /backup/test_db.sql
SET
SET
SET
SET
SET
 set_config
------------

(1 row)

SET
SET
SET
SET
SET
SET
CREATE TABLE
ALTER TABLE
CREATE SEQUENCE
ALTER TABLE
ALTER SEQUENCE
CREATE TABLE
ALTER TABLE
CREATE SEQUENCE
ALTER TABLE
ALTER SEQUENCE
ALTER TABLE
ALTER TABLE
COPY 5
COPY 5
 setval
--------
      7
(1 row)

 setval
--------
      5
(1 row)

ALTER TABLE
ALTER TABLE
CREATE INDEX
ALTER TABLE
GRANT
GRANT
GRANT
GRANT
root@642e04d6a02e:/backup#

```
