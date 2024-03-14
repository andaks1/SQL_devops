# Домашнее задание к занятию 2. «SQL»

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/blob/virt-11/additional/README.md).

## Задача 1

Используя Docker, поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose-манифест.

#### Ответ на задание 1.

Описание файла docker-compose:

```YAML
version: '3.3'

services:
  pg_db:
    image: postgres:12
    environment:
      POSTGRES_USER: "andaks"
      POSTGRES_PASSWORD: "AAAbbb123"
    volumes:
      - /.andaks/docker/postgresql/pg_data:/var/lib/postgresql/data
      - /.andaks/docker/postgresql/pg_backup:/var/lib/postgresql
    ports:
      - "5432:5432"
```

```BASH
# ll /.andaks/docker/postgresql/pg_data/
total 124
drwx------ 6 logstash systemd-journal  4096 Mar 13 20:01 base
drwx------ 2 logstash systemd-journal  4096 Mar 13 20:01 global
drwx------ 2 logstash systemd-journal  4096 Mar 13 20:00 pg_commit_ts
drwx------ 2 logstash systemd-journal  4096 Mar 13 20:00 pg_dynshmem
-rw------- 1 logstash systemd-journal  4782 Mar 13 20:01 pg_hba.conf
-rw------- 1 logstash systemd-journal  1636 Mar 13 20:00 pg_ident.conf
drwx------ 4 logstash systemd-journal  4096 Mar 13 20:01 pg_logical
drwx------ 4 logstash systemd-journal  4096 Mar 13 20:00 pg_multixact
drwx------ 2 logstash systemd-journal  4096 Mar 13 20:01 pg_notify
drwx------ 2 logstash systemd-journal  4096 Mar 13 20:00 pg_replslot
drwx------ 2 logstash systemd-journal  4096 Mar 13 20:00 pg_serial
drwx------ 2 logstash systemd-journal  4096 Mar 13 20:00 pg_snapshots
drwx------ 2 logstash systemd-journal  4096 Mar 13 20:01 pg_stat
drwx------ 2 logstash systemd-journal  4096 Mar 13 20:01 pg_stat_tmp
drwx------ 2 logstash systemd-journal  4096 Mar 13 20:00 pg_subtrans
drwx------ 2 logstash systemd-journal  4096 Mar 13 20:00 pg_tblspc
drwx------ 2 logstash systemd-journal  4096 Mar 13 20:00 pg_twophase
-rw------- 1 logstash systemd-journal     3 Mar 13 20:00 PG_VERSION
drwx------ 3 logstash systemd-journal  4096 Mar 13 20:00 pg_wal
drwx------ 2 logstash systemd-journal  4096 Mar 13 20:00 pg_xact
-rw------- 1 logstash systemd-journal    88 Mar 13 20:00 postgresql.auto.conf
-rw------- 1 logstash systemd-journal 26810 Mar 13 20:00 postgresql.conf
-rw------- 1 logstash systemd-journal    36 Mar 13 20:01 postmaster.opts
-rw------- 1 logstash systemd-journal    94 Mar 13 20:01 postmaster.pid
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

#### Ответ на задание 2.

- Итоговый список БД:
```SQL
andaks=# \l
                                             List of databases
   Name    | Owner  | Encoding |  Collate   |   Ctype    | ICU Locale | Locale Provider | Access privileges 
-----------+--------+----------+------------+------------+------------+-----------------+-------------------
 andaks    | andaks | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            | 
 postgres  | andaks | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            | 
 template0 | andaks | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            | =c/andaks        +
           |        |          |            |            |            |                 | andaks=CTc/andaks
 template1 | andaks | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            | =c/andaks        +
           |        |          |            |            |            |                 | andaks=CTc/andaks
 test_db   | andaks | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            | 
(5 rows)
```

- Описание таблиц:
```SQL
test_db=# \dt+
                                    List of relations
 Schema |  Name   | Type  | Owner  | Persistence | Access method |  Size   | Description 
--------+---------+-------+--------+-------------+---------------+---------+-------------
 public | clients | table | andaks | permanent   | heap          | 0 bytes | 
 public | orders  | table | andaks | permanent   | heap          | 0 bytes | 
(2 rows)
```

```SQL
test_db=# \d orders;
                                    Table "public.orders"
 Column |          Type          | Collation | Nullable |              Default               
--------+------------------------+-----------+----------+------------------------------------
 id     | integer                |           | not null | nextval('orders_id_seq'::regclass)
 name   | character varying(100) |           |          | 
 price  | integer                |           |          | 
Indexes:
    "orders_pkey" PRIMARY KEY, btree (id)


test_db=# \d clients;
                                     Table "public.clients"
  Column  |          Type          | Collation | Nullable |               Default               
----------+------------------------+-----------+----------+-------------------------------------
 id       | integer                |           | not null | nextval('clients_id_seq'::regclass)
 surname  | character varying(100) |           |          | 
 country  | character varying(25)  |           |          | 
 order_id | integer                |           |          | 
Indexes:
    "clients_pkey" PRIMARY KEY, btree (id)
    "clients_country_idx" btree (country)
Foreign-key constraints:
    "clients_order_id_fkey" FOREIGN KEY (order_id) REFERENCES orders(id)
```

- список пользователей с правами над таблицами БД test_db:
```SQL
test_db=# select table_catalog, table_schema, table_name, privilege_type, grantee from information_schema.table_privileges where table_name='orders' or table_name='clients';
 table_catalog | table_schema | table_name | privilege_type |     grantee      
---------------+--------------+------------+----------------+------------------
 test_db       | public       | orders     | INSERT         | andaks
 test_db       | public       | orders     | SELECT         | andaks
 test_db       | public       | orders     | UPDATE         | andaks
 test_db       | public       | orders     | DELETE         | andaks
 test_db       | public       | orders     | TRUNCATE       | andaks
 test_db       | public       | orders     | REFERENCES     | andaks
 test_db       | public       | orders     | TRIGGER        | andaks
 test_db       | public       | orders     | INSERT         | test-admin-user
 test_db       | public       | orders     | SELECT         | test-admin-user
 test_db       | public       | orders     | UPDATE         | test-admin-user
 test_db       | public       | orders     | DELETE         | test-admin-user
 test_db       | public       | orders     | TRUNCATE       | test-admin-user
 test_db       | public       | orders     | REFERENCES     | test-admin-user
 test_db       | public       | orders     | TRIGGER        | test-admin-user
 test_db       | public       | orders     | INSERT         | test-simple-user
 test_db       | public       | orders     | SELECT         | test-simple-user
 test_db       | public       | orders     | UPDATE         | test-simple-user
 test_db       | public       | orders     | DELETE         | test-simple-user
 test_db       | public       | clients    | INSERT         | andaks
 test_db       | public       | clients    | SELECT         | andaks
 test_db       | public       | clients    | UPDATE         | andaks
 test_db       | public       | clients    | DELETE         | andaks
 test_db       | public       | clients    | TRUNCATE       | andaks
 test_db       | public       | clients    | REFERENCES     | andaks
 test_db       | public       | clients    | TRIGGER        | andaks
 test_db       | public       | clients    | INSERT         | test-admin-user
 test_db       | public       | clients    | SELECT         | test-admin-user
 test_db       | public       | clients    | UPDATE         | test-admin-user
 test_db       | public       | clients    | DELETE         | test-admin-user
 test_db       | public       | clients    | TRUNCATE       | test-admin-user
 test_db       | public       | clients    | REFERENCES     | test-admin-user
 test_db       | public       | clients    | TRIGGER        | test-admin-user
 test_db       | public       | clients    | INSERT         | test-simple-user
 test_db       | public       | clients    | SELECT         | test-simple-user
 test_db       | public       | clients    | UPDATE         | test-simple-user
 test_db       | public       | clients    | DELETE         | test-simple-user
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

#### Ответ на задание 3

- Запросы и результаты их работы:
```SQL
test_db=# insert into clients(surname, country) values('Иванов Иван Иванович', 'USA'),
('Петров Петр Петрович', 'Canada'),
('Иоганн Себастьян Бах', 'Japan'),
('Ронни Джеймс Дио', 'Russia'),
('Ritchie Blackmore', 'Russia')
;
INSERT 0 5
test_db=# select * from clients;
 id |       surname        | country | order_name 
----+----------------------+---------+------------
  1 | Иванов Иван Иванович | USA     | 
  2 | Петров Петр Петрович | Canada  | 
  3 | Иоганн Себастьян Бах | Japan   | 
  4 | Ронни Джеймс Дио     | Russia  | 
  5 | Ritchie Blackmore    | Russia  | 
(5 rows)

test_db=# select count(*) from orders;
 count 
-------
     5
(1 row)

test_db=# select count(*) from clients;
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

#### Ответ на задание 4.

- запросы на выполнение операций:
```SQL
test_db=# update clients set order_id=3 where surname='Иванов Иван Иванович';
test_db=# update clients set order_id=4 where surname='Петров Петр Петрович';
test_db=# update clients set order_id=5 where surname='Иоганн Себастьян Бах';

test_db=# select * from clients;
 id |       surname        | country | order_id 
----+----------------------+---------+----------
  4 | Ронни Джеймс Дио     | Russia  |         
  5 | Ritchie Blackmore    | Russia  |         
  1 | Иванов Иван Иванович | USA     |        3
  2 | Петров Петр Петрович | Canada  |        4
  3 | Иоганн Себастьян Бах | Japan   |        5
(5 rows)


test_db=# select clients.surname, orders.name from clients join orders on orders.id=clients.order_id;
       surname        |  name   
----------------------+---------
 Иванов Иван Иванович | Книга
 Петров Петр Петрович | Монитор
 Иоганн Себастьян Бах | Гитара
```



## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните, что значат полученные значения.

#### Ответ на задание 5.

- Запрос через EXPLAIN:
```SQL
test_db=# explain select clients.surname, orders.name from clients join orders on orders.id=clients.order_id;
                             QUERY PLAN                              
---------------------------------------------------------------------
 Hash Join  (cost=1.11..15.56 rows=5 width=436)
   Hash Cond: (orders.id = clients.order_id)
   ->  Seq Scan on orders  (cost=0.00..13.20 rows=320 width=222)
   ->  Hash  (cost=1.05..1.05 rows=5 width=222)
         ->  Seq Scan on clients  (cost=0.00..1.05 rows=5 width=222)
(5 rows)
```

По сути EXPLAIN это планировщик запросов. Он показывает каким именно образом будет выполняться сам запрос, дает оценку его производительности (показатели параметра cost) и обозначает другие параметры. Например: rows - приблизительно возвращаемое количество строк; width - средний размер одной строки в байтах.
Если вы используете индексы, EXPLAIN может показать вам их использование. Вы будете лучше представлять анатомию запроса.
У EXPLAIN есть параметр ANALYZE, который наряду с планом запроса покажет статистику уже реального его выполнения.

## Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. задачу 1).

Остановите контейнер с PostgreSQL, но не удаляйте volumes.

Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления. 

#### Ответ на задание 6.

- Список команд, которые я применял для резервного копирования и восстановления БД:
```SQL
# Резервируем базу данных:
root@debian-andaks:/home/andaks# pg_dump -U andaks -W -h 127.0.0.1 -p 5432 > test_db /.andaks/docker/postgresql/pg_backup/

останавливаем докер компос и поднимаем чистый контейнер с простгресом:
# docker-compose down
# docker run --rm --name pgdocker -e POSTGRES_PASSWORD=AAAbbb123 -e POSTGRES_USER=andaks -d -p 5432:5432 -v /.andaks/docker/postgresql/pg_backup/:/var/lib/postgresql/ postgres:12

Заливаем дамп базы в чистый контейнер. Предварительно пришлось создать БД:
andaks=# create database test_db;
CREATE DATABASE
andaks=# q
andaks-# \q

# psql -h 127.0.0.1 -U andaks -W -d test_db < /.andaks/docker/postgresql/pg_backup/test_db_dump
```

---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---


