# Проект 1
Описание проекта

Заказчик — компания, которая разрабатывает приложение по доставке еды.
Необходимо составить витрину для RFM-классификации пользователей приложения.

**Сбор требований:**
 - 1.1 Куда сохранить - Витрина должна располагаться в той же базе в схеме analysis,
 - 1.2 Структура витрины - user_id, recency - char(5) , frequency - char (5), monetary_value - char (5)
 - 1.3 Глубина витрины - В витрине нужны данные с начала 2021 года
 - 1.4 Название витрины - Назовите витрину dm_rfm_segments
 - 1.5 Частота обновлений - Не требуется
 - 1.6 Примечание - Успешно выполненый заказ - статус 'Closed'
 
**Структура исходных данных: Наличие источников в таблице (select * from pg_catalog.pg_tables where schemaname = 'production'), доступно 6 таблиц:**
 - orderitems (id int4 NOT NULL GENERATED ALWAYS AS IDENTITY, product_id int4 NOT NULL, order_id int4 NOT NULL, "name" varchar(2048) NOT NULL, price numeric(19, 5) NOT NULL DEFAULT 0, discount numeric(19, 5) NOT NULL DEFAULT 0, quantity int4 NOT NULL)
 - orders (	order_id int4 NOT NULL, order_ts timestamp NOT NULL, user_id int4 NOT NULL, bonus_payment numeric(19, 5) NOT NULL DEFAULT 0, payment numeric(19, 5) NOT NULL DEFAULT 0, "cost" numeric(19, 5) NOT NULL DEFAULT 0, bonus_grant numeric(19, 5) NOT NULL DEFAULT 0, status int4 NOT NULL)
 - orderstatus (id int4 NOT NULL, "key" varchar(255) NOT NULL)
 - orderstatuslog (id int4 NOT NULL GENERATED ALWAYS AS IDENTITY, order_id int4 NOT NULL, status_id int4 NOT NULL, dttm timestamp NOT NULL)
 - product (id int4 NOT NULL, 	"name" varchar(2048) NOT NULL, price numeric(19, 5) NOT NULL DEFAULT 0)
 - users (id int4 NOT NULL, "name" varchar(2048) NULL, login varchar(2048) NOT NULL)

**Необходимые поля **
- users - Таблица users
- recency - Таблица orders(order_ts, user_id, status) 
- frequency - Таблица orders(order_id, user_id, status)
- monetary_value - Таблица orders(cost, user_id, status)

- recency — чтобы рассчитать, давность покупки, достаточно идентификатора клиента user_id и времени заказа order_ts.
- frequency — чтобы рассчитать, количество покупок, достаточно идентификатора клиента user_id , order_id и status
- monetary_value — чтобы рассчитать, сумму покупок, достаточно идентификатора клиента user_id , cost и status

**Качество данных**
- Проверка дублей (select count(*), count(distinct order_id) from production.orders o) - не содержит дублей
- Поиск пропущенных значений (select sum(case when order_ts is null then 1 else 0 end) as case_order_ts,
	sum(case when order_id  is null then 1 else 0 end) as case_order_id,
	sum(case when user_id  is null then 1 else 0 end) as case_user_id,
	sum(case when cost  is null then 1 else 0 end) as case_cost
from production.orders o) - Пропусков не содержит
- Проверка адекватности в данных (select min(cost), max(cost), avg(cost)
from production.orders o ) - данные находятся в реальном диапазоне 
- Инструменты для оценки поддержки каччества таблицы (orders - все поля not null, проверка на поле cost (payment + bonus_payment) = cost
