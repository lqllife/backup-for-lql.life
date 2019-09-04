---
title: SQL赏析
date: 2019-05-23 15:21:11
tags: 原创
categories: MySQL
---

**整理了一些sql，比较高效...**
<!--more-->

### 获取订单表中各类状态的条数
```mysql
SELECT
	sum( CASE WHEN order_status IN ( 1, 2 ) THEN 1 ELSE 0 END ) AS wait,
	sum( CASE WHEN order_status = 3 THEN 1 ELSE 0 END ) AS shipping,
	sum( CASE WHEN order_status = 4 THEN 1 ELSE 0 END ) AS receiving,
	sum( CASE WHEN order_status IN ( 5, 6 ) THEN 1 ELSE 0 END ) AS finish,
	sum( CASE WHEN order_status IN ( 7, 90, 91, 92, 93, 94 ) THEN 1 ELSE 0 END ) AS closed,
	count( order_status ) AS `total`
FROM
	table_name
WHERE
	user_id = 47 
	AND delete_time = 0
```

### 
