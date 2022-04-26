{\rtf1\ansi\ansicpg1251\cocoartf2580
\cocoatextscaling0\cocoaplatform0{\fonttbl\f0\fswiss\fcharset0 Helvetica;}
{\colortbl;\red255\green255\blue255;}
{\*\expandedcolortbl;;}
\paperw11900\paperh16840\margl1440\margr1440\vieww11520\viewh8400\viewkind0
\pard\tx566\tx1133\tx1700\tx2267\tx2834\tx3401\tx3968\tx4535\tx5102\tx5669\tx6236\tx6803\pardirnatural\partightenfactor0

\f0\fs24 \cf0 CREATE VIEW analysis.orders as \
with sub_status as(\
SELECT ao.order_id, ao.status_id as status\
FROM production.orderstatuslog ao\
INNER join (select order_id, max(dttm) as dttm\
FROM production.orderstatuslog\
GROUP by order_id) as t\
ON t.order_id=ao.order_id\
AND t.dttm=ao.dttm)\
select ao.order_id, ao.order_ts, ao.user_id, ao.bonus_payment, ao.payment, ao.cost, ao.bonus_grant, sub_status.status\
from production.orders ao\
left join sub_status\
on sub_status.order_id=ao.order_id;}