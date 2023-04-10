# SQL_useful_features
In this page I'm going to share different tools that could be usefull in data analytics  
SELECT * FROM ositofeliz.order_items;

-- SQL code
-- Obtener el número total de registros en la tabla
SELECT COUNT(*) FROM order_items;

-- Obtener el rango de fechas de los pedidos
SELECT MIN(created_at) AS 'Fecha inicial', MAX(created_at) AS 'Fecha final' FROM order_items;
