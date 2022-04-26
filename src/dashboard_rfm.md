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
	recency smallint NOT null check (recency <= 5),
	frequency smallint NOT null check (frequency <= 5),
	monetary_value smallint NOT null check (monetary_value <= 5));


-- очистка таблицы
truncate table analysis.rfm;

-- загрузка данных в таблицу
INSERT into analysis.rfm

with sub as(
select *,
case when (cume_dist() over(order by order_ts) * 100 ) <=20 then 1
	when (cume_dist() over(order by order_ts) * 100 ) <=40 then 2
	when (cume_dist() over(order by order_ts) * 100 ) <=60 then 3
	when (cume_dist() over(order by order_ts) * 100 ) <=80 then 4
	when (cume_dist() over(order by order_ts) * 100 ) <=100 then 5 else 1 end recency,
case when (cume_dist() over(order by count_order) * 100 ) <=20 then 1
	when (cume_dist() over(order by count_order) * 100 ) <=40 then 2
	when (cume_dist() over(order by count_order) * 100 ) <=60 then 3
	when (cume_dist() over(order by count_order) * 100 ) <=80 then 4 else 5 end frequency,
case when (cume_dist() over(order by sum_cost) * 100 ) <=20 then 1
	when (cume_dist() over(order by sum_cost) * 100 ) <=40 then 2
	when (cume_dist() over(order by sum_cost) * 100 ) <=60 then 3
	when (cume_dist() over(order by sum_cost) * 100 ) <=80 then 4 else 5 end monetary
from (
select user_id, max(order_ts) as order_ts, count (distinct order_id) as count_order, sum(cost) as sum_cost
from analysis.orders
where status = 4
group by user_id) as t)


select au.id  as user_id, coalesce(recency,1) as recency, coalesce(frequency,1) as frequency, coalesce(monetary,1) as monetary
from analysis.users au
left join sub
on sub.user_id=au.id;