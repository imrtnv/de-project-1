# Проект 1
Опишите здесь поэтапно ход решения задачи. Вы можете ориентироваться на тот план выполнения проекта, который мы предлагаем в инструкции на платформе.

1 Сбор требований:
 - 1.1 Куда сохранить - Витрина должна располагаться в той же базе в схеме analysis,
 - 1.2 Структура витрины - user_id, recency - char(5) , frequency - char (5), monetary_value - char (5)
 - 1.3 Глубина витрины - В витрине нужны данные с начала 2021 года
 - 1.4 Название витрины - Назовите витрину dm_rfm_segments
 - 1.5 Частота обновлений - Не требуется
 - 1.6 Примечание - Успешно выполненый заказ - статус 'Closed'
 
2 Изучение структуры исходных данных: Посмотрим наличие источников в таблице (select * from pg_catalog.pg_tables where schemaname = 'production'), доступно 6 таблиц:
 - orderitems (id int4 NOT NULL GENERATED ALWAYS AS IDENTITY, product_id int4 NOT NULL, order_id int4 NOT NULL, "name" varchar(2048) NOT NULL, price numeric(19, 5) NOT NULL DEFAULT 0, discount numeric(19, 5) NOT NULL DEFAULT 0, quantity int4 NOT NULL)
 - orders (	order_id int4 NOT NULL, order_ts timestamp NOT NULL, user_id int4 NOT NULL, bonus_payment numeric(19, 5) NOT NULL DEFAULT 0, payment numeric(19, 5) NOT NULL DEFAULT 0, "cost" numeric(19, 5) NOT NULL DEFAULT 0, bonus_grant numeric(19, 5) NOT NULL DEFAULT 0, status int4 NOT NULL)
 - orderstatus (id int4 NOT NULL, "key" varchar(255) NOT NULL)
 - orderstatuslog (id int4 NOT NULL GENERATED ALWAYS AS IDENTITY, order_id int4 NOT NULL, status_id int4 NOT NULL, dttm timestamp NOT NULL)
 - product (id int4 NOT NULL, 	"name" varchar(2048) NOT NULL, price numeric(19, 5) NOT NULL DEFAULT 0)
 - users (id int4 NOT NULL, "name" varchar(2048) NULL, login varchar(2048) NOT NULL)
