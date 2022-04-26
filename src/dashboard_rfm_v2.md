-- Удаление представлений
DROP view if exists analysis.orderitems;
DROP view if exists analysis.orders;
DROP view if exists analysis.orderstatuses;
DROP view if exists analysis.orderstatuslog;
DROP view if exists analysis.products;
DROP view if exists analysis.users;

-- Создание представлений
CREATE VIEW analysis.orderitems as SELECT * from production.orderitems;
CREATE VIEW analysis.orders as SELECT * from production.orders;
CREATE VIEW analysis.orderstatuses as SELECT * from production.orderstatuses;
CREATE VIEW analysis.orderstatuslog as SELECT * from production.orderstatuslog;
CREATE VIEW analysis.products as SELECT * from production.products;
CREATE VIEW analysis.users as SELECT * from production.users;


--1.4.2
-- Удаление таблицы
drop table if exists analysis.rfm;

--  Создание Таблицы
CREATE TABLE analysis.rfm (
	id integer UNIQUE,
	recency smallint NOT null check (recency <= 5 and recency >= 1),
	frequency smallint NOT null check (frequency <= 5 and frequency >= 1),
	monetary_value smallint NOT null check (monetary_value <= 5 and monetary_value >= 1));


-- очистка таблицы
truncate table analysis.rfm;

-- загрузка данных в таблицу
INSERT into analysis.rfm

with sub as(
select au.id as user_id, coalesce(t.order_ts,'2022-02-12') as order_ts, coalesce (t.count_order,0) as count_order, coalesce (t.sum_cost,0) as sum_cost
from analysis.users au
left join (
	select ao.user_id, max(ao.order_ts)::date as order_ts, count (distinct ao.order_id) as count_order, sum(ao.cost) as sum_cost
	from analysis.orders ao
	where status = 4
	group by ao.user_id) as t
on au.id=t.user_id)

select user_id, ntile(5) over(order by order_ts ) as recency, ntile(5) over(order by count_order ) as frequency, ntile(5) over(order by sum_cost ) as monetary
from sub