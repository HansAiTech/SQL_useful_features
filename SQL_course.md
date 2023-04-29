<a name="Tabla-de-contenido1"></a>
# Tabla de Contenido [![Texto](https://user-images.githubusercontent.com/116538899/231064143-c080de13-8be9-4321-8694-e62539263f5a.png)](#Tabla-de-contenido1)
- [Análisis Exploratorio](#Análisis-Exploratorio)
  - [Función Case](#Función-case)
  - [Función Concat](#Función-concat)
  - [Función Left-Right](#Función-Left-Right)
  - [Función Now-Datediff](#Función-Now-Datediff)  
  - [Función Year-Month](#Función-Year-Month)  
  - [Función Monthname-Dayname](#Función-Monthname-Dayname)  
  - [Función Ceiling-Floor-Round-Formate](#Función-Ceiling-Floor-Round-Format)  
  - [Función IFNULL-ISNULL](#Función-IFNULL-ISNULL)  
  - [Caso: Call center verde](#Caso-Call-center-verde)
- [SQL Avanzado - subconsultas y cte's](#SQL-Avanzado-subconsultas-y-ctes)    - [¿Que son las subqueries?](#Subqueries)
  - [¿Que son las CTE's?](#CTEs)
  - [CTE vs Subconsultas](#CTEvsSubconsultas)  
  - [Windows Functions](#WindowsFunctions)  
  - [Caso práctico](#Casopractico)
  - [Caso Data Bank](#Caso5)
  
   
## Análisis Exploratorio
1) EDA  
_¿Por qué explorar datos?_     
  - Ayuda a comprender la calidad de datos  
  - Identificar valores inusuales o inesperados  
  - Buscar valores inusuales  
  - Busquedatos inconsistentes, especialmente con respecto a las reglas comerciales  
  - Comprobar la distribución de los datos, forma  
  - Entender subgrupos usando histogramas  
  - Verificar correlaciones entre variables, usando el Coeficinete de correlación de Pearson    

2) Comprobaciones de calidad de datos
_¿Por qué chequear datos?_  
  - Valores faltantes
  - Valores fuera de rango esperado (edades menores a 0 mayores a 120, etc)
  - Valores únicos
  - Valores mal formados (nombres y descripciones truncados, fecha, etc)
  - Valors que violan las reglas comerciales (total del pedido > límite de crédito del cliente)
  - Valores incoherentes entre tablas, como ID de cliente

### Tipos de controles de calidad
**Comprobación de valores faltantes**
```sql
SELECT * FROM store_values FROM units_sold IS NULL
```

**Valores fuera de rango**
```sql
SELECT * FROM empleados WHERE (edad < 0) AND (edad >40)
SELECT * FROM store_sales WHERE empleado_turno >10 OR empleado_turno <0
```

**Comprobación de datos inconsistentes**  
- Comprobación de código postal  
- Código postal (010-027) están en Massachusetts  

**Lógica de negocios**
Verificar para asegurarse de que ningún afiliado en un plan de seguro tenga datos de inscripción anteriores a la fecha en que se creó el plan la fecha de inicio del empleado no puede ser anterior a la fecha de inicio de la empresa, etc.  

**Imputación de valores faltantes**    
- Continuar sin reemplazar los valores faltantes  
- Definir un valor predeterminado  
- Calcular el valor en función de las filas vecinas  
  + Filas de grupo por algunos criterios y valor promedio usando filas cercanas   
  + Ordenar las filas en algún orden para que las filas similares estén una al lado de la otra, y luego copie el valor de la columna faltante de la fila anterior / fila posterior calcular el valor basado en la regresión.  

**Identificación de comprobaciones de lógica empresarial**  
- Fecha: rango razonable o no, comparar fechas, si las fechas futuras son razonables o no  
- Columnas numéricas: el rango de valores está dentro de lo esperado, busque valores atípicos,calcule STDDEV    
- Texto: si es categórico => debe ser una pequeña cantidad de valores distintos, si es en gran medida único => los valores deben estar cerca del número de filas en el conjunto de datos, verifique la longitud y busque valores inusualmente largos o cortos (verifique al menos 10 superiores/ valores atípicos más bajos)  

**Comprobación de desviación estándar**  
- Calcular la media de una columna
- Calcular la desviacipon estándar
- Establecer el límite superior (media + 3 * desviación estándar)
- Establecer el límite inferior (media - 3t * std-dev)

### **Función Case**

Ejemplo 1)
```sql
SELECT 
*,
CASE WHEN purch_amt BETWEEN 0 AND 1000 THEN 'Bronze'
     WHEN purch_amt BETWEEN 1001 AND 2000 THEN 'Platinium'
     WHEN purch_amt BETWEEN 2001 AND 3000 THEN 'Gold'
     ELSE 'Sin Ranking'
     END AS ranking_ventas
FROM salesman.orders;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/232181984-14fdde5e-7bb1-470f-9329-e48a26405a84.png"></p>    

Ejemplo 2)
```sql
SELECT *,
CASE WHEN ord_date >= '2012-08-08' AND ord_date < '2012-08-02' THEN 'Periodo 1'
	 WHEN ord_date >= '2012-08-02' AND ord_date <= '2012-09-01' THEN 'Periodo 2'
	 WHEN ord_date > '2012-09-01' AND ord_date <= '2012-11-01' THEN 'Periodo 3'
     ELSE 'Sin periodo'
     END AS ranking_periodo
FROM salesman.orders;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/232229034-89761de6-4c17-4764-bc2d-3e3b1b3d64c6.png"></p>  

```sql
SELECT
CASE WHEN ord_date >= '2012-06-01' AND ord_date < '2012-08-02' THEN 'Periodo 1'
	 WHEN ord_date >= '2012-08-02' AND ord_date <= '2012-09-01' THEN 'Periodo 2'
	 WHEN ord_date > '2012-09-01' AND ord_date <= '2012-11-01' THEN 'Periodo 3'
     ELSE 'Sin periodo'
     END AS ranking_periodo,
     SUM(purch_amt) as ventas
FROM salesman.orders
GROUP BY ranking_periodo
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/232228548-6d16056e-cc51-4247-9bf9-21fd0350740b.png"></p>  


### **Función Concat**  

Ejemplo 1)
```sql
SELECT 
*,
CONCAT(salesman_id,' - ',name,' - ',city) as concatenado
FROM salesman.salesman;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234083200-b30c80ac-3bc1-4bdc-a0b8-335fc4ea45f2.png"></p>    

Ejemplo 2)
```sql
SELECT 
*,
CONCAT(cust_name, ' ** ', grade) as concatenado
FROM salesman.customer;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234083457-0aba5742-4632-4b3a-bf7d-d3fd7b91fbc1.png"></p>  


### **Función Replace**  

Ejemplo 1)
```sql
SELECT
*,
REPLACE (grade,100,80) campo_reemplazado
FROM salesman.customer;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234085500-4b176f45-0b0f-4f80-8c03-c689cd16c884.png"></p>    

Ejemplo 2)
```sql
SELECT
*,
REPLACE (cust_name,' ',' - ') nombre_nuevo
FROM salesman.customer;	
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234085590-6a076930-1835-45ff-8a0e-b42b870ddb67.png"></p>    

### **Función Left-Right**  

Ejemplo 1)
```sql
SELECT 
*,
LEFT(cust_name,3) iniciales
FROM salesman.customer;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234086306-eeaeb8f5-30bb-4637-beb1-6fe4ddc52c17.png"></p> 


Ejemplo 2)
```sql
SELECT 
*,
RIGHT(cust_name,3) iniciales
FROM salesman.customer;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234086391-b35e32e8-f10c-4063-97e1-2ed35e531b8d.png"></p>    

### **Función Now-Datediff**  

Ejemplo 1)
```sql
SELECT
*,
DATEDIFF(NOW(),ord_date) dias_desde_ventas
FROM salesman.orders;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234088062-9594feda-b2f0-4187-a755-e62e590cc011.png"></p> 

### **Función Year-Month**  

Ejemplo 1)
```sql
SELECT
MONTH(ord_date) mes,
ROUND(SUM(purch_amt),2) ventas
FROM salesman.orders
GROUP BY mes;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234117691-f024f187-2f30-4797-9ddf-cfcfa683516b.png"></p> 


Ejemplo 2)
```sql
SELECT
YEAR(ord_date) año,
SUM(purch_amt) ventas
FROM salesman.orders
GROUP BY año;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234117802-814114e4-90c9-4756-8f61-cc7db53ac6ab.png"></p>      

### **Función Monthname-Dayname**  

Ejemplo 1)
```sql
SELECT
MONTH(ord_date) mes,
MONTHNAME(ord_date) nombre_mes,
ROUND(SUM(purch_amt),2) ventas
FROM salesman.orders
GROUP BY mes, nombre_mes
ORDER BY SUM(purch_amt) DESC;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234120458-915aa8c8-48d6-477e-9048-5408e1bf9b2b.png"></p> 


Ejemplo 2)
```sql
SELECT
DAYNAME(ord_date) dia_semana,
ROUND(SUM(purch_amt),2) ventas
FROM salesman.orders
GROUP BY dia_semana;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234120558-4a3e1a83-8c99-4cc5-81de-90f71423a6fc.png"></p>    


### **Función Ceiling-Floor-Round-Format**  

Ejemplo 1)
```sql
SELECT
*,
ROUND(purch_amt,1) ventas_round,
FORMAT(purch_amt,1) ventas_format,
CEILING(purch_amt) ventas_ceil,
FLOOR(purch_amt) ventas_floor
FROM salesman.orders;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234126796-f89c1230-6377-401c-8c16-8ffba1e37119.png"></p> 

    
### **Función IFNULL-ISNULL**  
```sql
-- Paso previo
UPDATE salesman.salesman
SET city=null
WHERE city='';
```

Ejemplo 1)
```sql
SELECT 
*,
IFNULL(city,'Sin ciudad') city_complete
FROM salesman.salesman;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234129916-26f6b6f2-03a4-4d9b-a65a-7e57c0b5eb0f.png"></p> 
  
Ejemplo 2)
```sql
SELECT 
*,
CASE WHEN city IS NULL THEN 'Sin ciudad'
ELSE city END city_complete
FROM salesman.salesman;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234130018-3071af78-b38c-4665-a5e2-c89ac69c81d3.png"></p>    


## Caso Call Center Verde
Call Center Verde es un call center que brinda servicios a una empresa externa. Principalmente opera en la región de estados unidos y realiza llamada diversas para conocer si el iente está conforme 0 no.  

Problema  
Almudena, la gerenta , te ha pedido que la ayudes a pasar los datos del csva la base de datos, debes pasar la tabla a mysql, luego crear una nueva con los datos
limpios apartir de la tabla cruda y por ultimo que realizar un pequeño paso de análisis exploratorio de los mismos.  

**Práctica**  
### Parte I - Importación de datos  
1. En primer lugar debemos crear la nueva base de datos que contendrá los datos de las llamadas del Call Center Verde. (Nombre base de datos = call_center_verde

```sql
CREATE SCHEMA call_center_verde;
```
    
2. Crear la tabla “cruda” que contendrá nuestros datos del call center sin ningun proceso de limpieza o transformación sino en crudo.  
a. Criterios para la creación de la tabla llamada “calls”: 
```sql
CREATE TABLE call_center_verde.calls (    	
    ID CHAR(50),
    cust_name CHAR (50),
    sentiment CHAR (20),
   	csat_score INT,
   	call_timestamp CHAR (10),
   	reason CHAR (20),
   	city CHAR (20),
   	state CHAR (20),
   	channel CHAR (20),
   	response_time CHAR (20),
   	call_duration_minutes INT,
   	call_center CHAR (20)
   	)
;
```
**Análisis inicial**
```sql
SELECT
*
FROM call_center_verde.calls; 
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234169913-2d6d8433-aa98-4e5f-b7c2-471720f843f6.png"></p>    
  
  
### Parte II - Análisis Exploratorio
1) Chequear rango de tiempo de llamadas, min fecha y max fecha.
```sql
-- Fecha minima, fecha máxima y diferencia
SELECT
MIN(STR_TO_DATE(call_timestamp, '%m/%d/%Y')) fecha_minima,
MAX(STR_TO_DATE(call_timestamp, '%m/%d/%Y')) fecha_maxima,
DATEDIFF(MAX(STR_TO_DATE(call_timestamp, '%m/%d/%Y')),MIN(STR_TO_DATE(call_timestamp, '%m/%d/%Y'))) rango_fecha,
MIN(call_duration_minutes) Min_llamada,
MAX(call_duration_minutes) Max_llamada
FROM call_center_verde.calls;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234417484-edec3a67-c247-4146-a7c8-355adfe4b71f.png"></p>   


2) Chequear la cantidad de columnas y filas que tenemos en nuestros datos. 

```sql
SELECT
COUNT(ID) registros
FROM call_center_verde.calls;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234417594-4d096958-8436-4081-b20c-485f6ea6791c.png"></p>    
  
```sql
SELECT
COUNT(*) cols_num
FROM information_schema.columns
WHERE table_name = 'calls';
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234417728-47ceeb14-f5ab-4023-938a-bec420b541ae.png"></p>    
  
3) Chequear los valores únicos de las siguientes columnas: sentiment,reason,channel,response_time,call_center, state  

```sql
SELECT 
DISTINCT sentiment
FROM call_center_verde.calls;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234414192-01e4b24c-d98f-44d8-9aae-48be40268682.png"></p>    
      
```sql
SELECT 
DISTINCT reason
FROM call_center_verde.calls;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234416601-e148eab0-daa8-418c-826a-c00f27e9ccea.png"></p>    
     
```sql
SELECT 
DISTINCT `channel`
FROM call_center_verde.calls;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234416704-7fe8490e-7070-4b37-aeb1-b608be07ea42.png"></p>    
  
```sql
SELECT 
DISTINCT response_time
FROM call_center_verde.calls;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234416810-0b146efe-de01-4f8a-a347-6fce02c0d0b6.png"></p>    
  
```sql
SELECT 
DISTINCT call_center
FROM call_center_verde.calls;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234416875-1b7337f9-b2e3-4589-b335-6b154b4ec6ba.png"></p>    
  
```sql
SELECT 
DISTINCT state
FROM call_center_verde.calls;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234417067-306974dc-2beb-4f14-8200-c3bb13c15e3b.png"></p>    
  
4) Chequear ahora la cantidad de registros y porcentajes de las columnas que miramos los valores únicos 
  ```sql
SELECT 
sentiment,
FORMAT(COUNT(ID),'es_ESP') Cantidad_registros,
CONCAT(FORMAT(COUNT(ID)/ (SELECT COUNT(ID) FROM call_center_verde.calls)*100,2,'es_ESP'),'%') Porcentaje
FROM call_center_verde.calls
GROUP BY sentiment
ORDER BY COUNT(ID) DESC;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234418075-19cbd98c-da88-4817-8e1b-1b08328995ea.png"></p>    

### Parte III - Limpieza de datos
1) Del campo id se debe extraer el código de 8 dígitos que viene luego de las letras en un nuevo campos que se llame green_code
```sql
SELECT
ID,
SUBSTRING(ID,5,8) green_code
FROM call_center_verde.calls;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234418493-1a169c2d-3c18-49a3-831d-0b64fdfa3080.png"></p>  
  
2) Debemos actualizar el formato de las fechas que se encuentran como string a Fecha 
```sql
SELECT 
call_timestamp,
STR_TO_DATE(call_timestamp, '%m/%d/%Y') fecha 
FROM call_center_verde.calls;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234418751-96696df5-24fe-417c-9861-17e88e466865.png"></p>  
  
3) Aquellas llamadas que tengan csat_score = 0 deben convertirse a nulas.
```sql
csat_score,
CASE WHEN csat_score=0 THEN csat_score = NULL
ELSE csat_score END csat_score
FROM call_center_verde.calls;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234419134-df091a43-d0a0-4b33-9183-dbbf8cbf2e19.png"></p> 
  
4) La ciudad de Nueva York se ha descargado mal por un problema con el software y hay que pasar aquellos que no tengan el formato adecuado al que corresponde.
```sql
SELECT 
state,
CASE 
	WHEN state like 'NY' THEN 'New York'
	WHEN state like 'NewYork' THEN 'New York'
    	ELSE state END state
FROM call_center_verde.calls
WHERE state like 'NY' OR 'NewYork';
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234424007-08c3adc6-06a4-4d20-8c1b-ae9566f539b9.png"></p> 
  
5) Hay que convertir los minutos que están como enteros a hh:mm:ss
```sql
SELECT
call_duration_minutes,
SEC_TO_TIME(call_duration_minutes*60) call_duration
FROM call_center_verde.calls;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234424200-4aa105e6-972c-45f7-9fb0-7484cef121fe.png"></p> 

6) Crear nueva tabla con los campos limpios: call_verde
```sql
CREATE TABLE 
	call_verde (
	green_code CHAR(50),
	fecha CHAR(10),
   	csta_score INT,
    	state CHAR(20),
	call_duration INT
); 
```
  
```sql
INSERT INTO call_verde
SELECT
SUBSTRING(ID,5,8) green_code,
STR_TO_DATE(call_timestamp, '%m/%d/%Y') fecha,
CASE WHEN csat_score=0 THEN csat_score = NULL ELSE csat_score END csta_score,
CASE 
	WHEN state like 'NY' THEN 'New York'
	WHEN state like 'NewYork' THEN 'New York'
   	ELSE state END state,
SEC_TO_TIME(call_duration_minutes*60) call_duration
FROM call_center_verde.calls;
```  
```sql  
  SELECT * FROM call_center_verde.call_verde;
```
<p align="center"><img src="https://user-images.githubusercontent.com/116538899/234425104-9cc2e5b6-e82c-42c7-b0eb-658a4e420373.png"></p> 
   
## SQL Avanzado-subconsultas y ctes 
<a name="Subqueries"></a>      
### ¿Que son las subqueries?
<p align='justify'>Las subconsultas (también conocidas como consultas internas o consultas anidadas) son una herramienta para realizar operaciones en varios pasos. Por ejemplo, si quisiera tomar las sumas de varias columnas y luego promediar todos esos valores, necesitaría hacer cada agregación en un paso distinto.
Las subconsultas se pueden usar en varios lugares dentro de una consulta, pero es más fácil comenzar con la instrucción FROM. Aquí hay un ejemplo de una subconsulta básica:</p>

```sql
SELECT sub.*
  FROM (
        SELECT 
	*
        FROM tutorial.sf_crime_incidents_2014_01
        WHERE day_of_week = 'Friday'
       ) sub
 WHERE sub.resolution = 'NONE'
```  

Analicemos lo que sucede cuando ejecuta la consulta anterior:  
Primero, la base de datos ejecuta la "consulta interna", la parte entre paréntesis:  
```sql
SELECT 
*
FROM tutorial.sf_crime_incidents_2014_01
WHERE day_of_week = 'Friday'
```       
<p align='justify'>
Si tuviera que ejecutar esto por sí solo, produciría un conjunto de resultados como cualquier otra consulta. Puede sonar como una obviedad, pero es importante: su consulta interna debe ejecutarse por sí sola, ya que la base de datos la tratará como una **consulta independiente**. Una vez que se ejecuta la consulta interna, la **consulta externa se ejecutará utilizando los resultados de la consulta interna como su tabla subyacente:**</p>   

```sql
SELECT sub.*
  FROM (
       <<results from inner query go here>>
       ) sub
 WHERE sub.resolution = 'NONE'
```        
Las subconsultas deben tener nombres, que se agregan después de los paréntesis de la misma manera que agregaría un alias a una tabla normal. En este caso, hemos utilizado el nombre "sub".

#### Uso de subconsultas para agregar en múltiples etapas     
<p align='justify'>¿Qué pasaría si quisiera averiguar cuántos incidentes se informan cada día de la semana? Mejor aún, ¿qué pasaría si quisiera saber cuántos incidentes ocurren, en promedio, un viernes de diciembre? ¿En Enero? Este proceso consta de dos pasos: contar el número de incidentes cada día (consulta interna) y luego determinar el promedio mensual (consulta externa):</p>  
  
```sql
SELECT LEFT(sub.date, 2) AS cleaned_month,
       sub.day_of_week,
       AVG(sub.incidents) AS average_incidents
  FROM (
        SELECT day_of_week,
               date,
               COUNT(incidnt_num) AS incidents
          FROM tutorial.sf_crime_incidents_2014_01
          GROUP BY 1,2
       ) sub
 GROUP BY 1,2
 ORDER BY 1,2
```    
   
Tip: Si tienes problemas para entender lo que pasa, siempre intenta ejecutar por partes. Primero la sub query interna y luego la externa.
  
#### Subconsultas en condicional lógica
<p align='justify'>Puede usar subconsultas en lógica condicional (junto con WHERE, JOIN/ON o CASE). La siguiente consulta devuelve todas las entradas desde la fecha más antigua en el conjunto de datos (teóricamente, el formato deficiente de la columna de fecha en realidad hace que devuelva el valor que se ordena primero alfabéticamente):</p>  

```sql
SELECT *
  FROM tutorial.sf_crime_incidents_2014_01
 WHERE Date = (SELECT MIN(date)
                 FROM tutorial.sf_crime_incidents_2014_01
              )
```  

<p align='justify'>  
La consulta anterior funciona porque el resultado de la subconsulta es solo una celda. La mayoría de la lógica condicional funcionará con subconsultas que contengan resultados de una celda. Sin embargo, IN es el único tipo de lógica condicional que funcionará cuando la consulta interna contenga varios resultados: </p>

```sql
SELECT *
  FROM tutorial.sf_crime_incidents_2014_01
 WHERE Date IN (SELECT date
                 FROM tutorial.sf_crime_incidents_2014_01
                ORDER BY date
                LIMIT 5
              )
```  

<p align='justify'>  
Tenga en cuenta que no debe incluir un alias cuando escribe una subconsulta en una declaración condicional. Esto se debe a que la subconsulta se trata como un valor individual (o un conjunto de valores en el caso IN) en lugar de como una tabla.</p>  

#### Uniendo subconsultas  
<p align='justify'>  
Puede recordar que puede filtrar consultas en uniones. Es bastante común unirse a una subconsulta que llega a la misma tabla que la consulta externa en lugar de filtrar en la cláusula WHERE. La siguiente consulta produce los mismos resultados que el ejemplo anterior:</p>   

```sql
SELECT *
  FROM tutorial.sf_crime_incidents_2014_01 incidents
  JOIN ( SELECT date
           FROM tutorial.sf_crime_incidents_2014_01
          ORDER BY date
          LIMIT 5
       ) sub
    ON incidents.date = sub.date
```   

<p align='justify'>  
Esto puede ser particularmente útil cuando se combina con agregaciones. Cuando se une, los requisitos para la salida de su subconsulta no son tan estrictos como cuando usa la cláusula WHERE. Por ejemplo, su consulta interna puede generar múltiples resultados. La siguiente consulta clasifica todos los resultados según la cantidad de incidentes informados en un día determinado. Lo hace agregando el número total de incidentes cada día en la consulta interna y luego usando esos valores para ordenar la consulta externa:</p>  

```sql
SELECT incidents.*,
       sub.incidents AS incidents_that_day
  FROM tutorial.sf_crime_incidents_2014_01 incidents
  JOIN ( SELECT date,
          COUNT(incidnt_num) AS incidents
           FROM tutorial.sf_crime_incidents_2014_01
          GROUP BY 1
       ) sub
    ON incidents.date = sub.date
 ORDER BY sub.incidents DESC, time
```  

#### Subconsultas y UNIONs  

Para la siguiente sección, tomaremos prestado directamente de la lección sobre UNION, nuevamente utilizando los datos de Crunchbase:
```sql
SELECT *
  FROM tutorial.crunchbase_investments_part1

 UNION ALL

 SELECT *
   FROM tutorial.crunchbase_investments_part2
```   
<p align='justify'>  
Ciertamente, no es raro que un conjunto de datos se divida en varias partes, especialmente si los datos pasaron a través de Excel en algún momento (Excel solo puede manejar ~1 millón de filas por hoja de cálculo). Las dos tablas utilizadas anteriormente se pueden considerar como partes diferentes del mismo conjunto de datos; lo que seguramente le gustaría hacer es realizar operaciones en todo el conjunto de datos combinado en lugar de en las partes individuales.<br>
Puedes hacer esto usando una subconsulta:</p>  

```sql
SELECT COUNT(*) AS total_rows
  FROM (
        SELECT *
          FROM tutorial.crunchbase_investments_part1

         UNION ALL

        SELECT *
          FROM tutorial.crunchbase_investments_part2
       ) sub
```  

<a name="CTEs"></a>      
### ¿Que son las CTE's?
<p align='justify'>Escribir consultas SQL limpias, legibles y eficientes es un aspecto importante de cualquier proceso analítico o de ingeniería en un equipo. Dichas consultas se mantengan de manera efectiva y se escalen bien cuando llegue el momento. Una construcción de SQL que tanto los desarrolladores como los analistas pueden adoptar fácilmente para que esto suceda es Common Table Expression (CTE).</p>

#### Expresiones de tabla comunes  
<p align='justify'>Una expresión de tabla común (CTE) es una construcción que se utiliza para almacenar temporalmente el conjunto de resultados de una consulta específica de modo que las consultas posteriores puedan hacer referencia a ella. El resultado de un CTE no se conserva en el disco, sino que su vida útil dura hasta la ejecución de la consulta (o consultas) que hace referencia a él.<br><br>
Los usuarios pueden aprovechar las CTE de modo que las consultas complejas se dividan en subconsultas más fáciles de mantener y leer. Además, se puede hacer referencia a las Expresiones de tabla comunes varias veces dentro de una sola consulta, lo que significa que no tiene que repetirse. Dado que los CTE se nombran, también significa que los usuarios pueden dejar en claro al lector qué expresión en particular se supone que debe devolver como resultado.</p>  

**Construcción de expresiones de tabla comunes**  
Cada CTE se puede construir usando la cláusula WITH <cte-name> AS  
	
```sql
WITH sessions_per_user_per_month AS (
    SELECT
      user_id,
      COUNT(*) AS no_of_sessions,
      EXTRACT (MONTH FROM session_datetime) AS session_month,
      EXTRACT (YEAR FROM session_datetime) AS session_year
    FROM user_sessions
    GROUP BY user_id
)
```  
	
Se pueden especificar varios CTE dentro de una sola consulta, cada uno separado por comas de los demás. Los CTE también pueden hacer referencia a otros CTE:

```sql
WITH sessions_per_user_per_month AS (
    SELECT
      user_id,
      COUNT(*) AS no_of_sessions,
      EXTRACT (MONTH FROM session_datetime) AS session_month,
      EXTRACT (YEAR FROM session_datetime) AS session_year
    FROM user_sessions
    GROUP BY user_id
),
running_sessions_per_user_per_month AS (
    SELECT
      user_id, 
      SUM(no_of_sessions) OVER (
        PARTITION BY 
          user_id, 
          session_month, 
          session_year
      ) AS running_sessions
    FROM sessions_per_user_per_month
)
```  
	
Luego, las consultas posteriores pueden hacer referencia a CTE como cualquier tabla o vista:
 
```sql
WITH sessions_per_user_per_month AS (
    SELECT
      user_id,
      COUNT(*) AS no_of_sessions,
      EXTRACT (MONTH FROM session_datetime) AS session_month,
      EXTRACT (YEAR FROM session_datetime) AS session_year
    FROM user_sessions
    GROUP BY user_id
),
running_sessions_per_user_per_month AS (
    SELECT
      user_id, 
      SUM(no_of_sessions) OVER (
        PARTITION BY 
          user_id, 
          session_month, 
          session_year
      ) AS running_sessions
    FROM sessions_per_user_per_month
)

SELECT 
  u.username,
  u.email
  u.country,
  s.running_sessions
FROM users u
LEFT JOIN sessions_per_user_per_month s
  ON u.user_id = s.user_id
WHERE country = 'US';
```  	

<a name="CTEvsSubconsultas"></a> 
### CTE vs Subconsultas  

#### La diferencia entre Subconsulta y CTE  
**Ventajas de usar CTE**  
- <p align='justify'><strong>CTE puede ser reutilizable:</strong> una ventaja de usar CTE es que CTE es reutilizable por diseño. En lugar de tener que declarar la misma subconsulta en cada lugar donde necesite usarla, puede usar CTE para definir una tabla temporal una vez y luego consultarla cuando la necesite.</p>  
- <p align='justify'><strong>CTE puede ser más legible:</strong> otra ventaja de CTE es que CTE es más legible que las subconsultas. Dado que CTE puede ser reutilizable, puede escribir menos código usando CTE que usando una subconsulta. Además, las personas tienden a seguir la lógica y las ideas más fácilmente en secuencia que de manera anidada. Cuando escribe una consulta, es más fácil dividir una consulta compleja en partes más pequeñas usando CTE.</p>      
- <p align='justify'><strong>os CTE pueden ser recursivos:</strong> un CTE puede ejecutarse recursivamente, lo que no puede hacer una subconsulta. Esto lo hace especialmente adecuado para estructuras de árbol, en las que la información de una fila determinada se basa en la información de la(s) fila(s) anterior(es). La función de recursividad se puede implementar con RECURSIVE y UNION ALL.</p>  

**Ventajas de usar subconsulta**  
<p align='justify'>Se puede usar una subconsulta en la cláusula WHERE: Podemos usar una subconsulta para devolver un valor y luego usarla en la cláusula WHERE.<br>
En el siguiente ejemplo, nos gustaría devolver empleados que tienen un salario superior al salario promedio. Sería fácil de implementar con una subconsulta, que calcula el salario promedio.</p>  

```sql
SELECT
   employee_name, salary 
FROM sample
WHERE
   salary > (SELECT AVG(salary) FROM sample)
```

<p align='justify'>Una subconsulta puede actuar como una columna con un solo valor: también puede usar una subconsulta como una nueva columna. La única restricción es que la subconsulta debe devolver solo un valor. En el siguiente ejemplo, nos gustaría agregar una nueva columna que contenga el salario promedio.<br>
Podemos usar una subconsulta para calcular el salario promedio y luego incluirlo en la instrucción SELECT.  


```sql
SELECT
   employee_name,
   salary,
   (SELECT AVG(salary) FROM sample) AS average_salary
FROM sample
```    

<p align='justify'> 
Se puede usar una subconsulta con SUBCONSULTA CORRELADA: A diferencia de CTE, podemos usar una subconsulta interna como una subconsulta correlacionada. <strong>Eso significa que por cada registro procesado por una consulta externa, se ejecutará una consulta interna.</strong><br>
En el siguiente ejemplo, nos gustaría devolver al empleado con el segundo salario más alto. Esto es lo que vamos a hacer para resolver este problema 
usando una subconsulta correlacionada. Para cada empleado (en la consulta externa (a)), calculamos el número de empleados (en la consulta interna (b)), que tienen salarios más altos que un empleado determinado. Si solo hay otro empleado que tiene un salario más alto que este empleado en particular, mantenemos a este empleado.  


```sql
SELECT 
   employee_name, salary
FROM sample a
WHERE 1 = (SELECT COUNT(DISTINCT(salary)) FROM sample b WHERE a.salary < b.salary)
```  
  
Pero tenga en cuenta que debido a que la subconsulta interna se evaluaría cada vez que la consulta externa procese cada fila, podría ser lento.

<a name="WindowsFunctions"></a>  
### Windows Functions  

#### ¿Qué es una función de ventana?
<p align='justify'>
Las funciones de ventana se introdujeron por primera vez en SQL estándar en 2003. Según la documentación de PostgresSQL:<br>
"Una función de ventana <strong>realiza un cálculo</strong> en un <strong>conjunto de filas</strong> de la tabla que de alguna manera están <strong>relacionadas con la fila actual.</strong>. Detrás de escena, la función de ventana puede <strong>acceder a más que solo la fila</strong> actual del resultado de la consulta".<br><br> 
Las funciones de ventana son similares a la <strong>agregación realizada en la cláusula GROUP BY</strong>. Sin embargo, las filas <strong>no se agrupan en una sola fila</strong>, cada fila <strong>conserva su identidad separada</strong>. Es decir, una función de ventana puede devolver <strong>un solo valor para cada fila</strong>. Aquí hay una buena visualización de lo que quiero decir con eso.  

<p align='center'><img src="https://user-images.githubusercontent.com/116538899/234939700-b0a152f6-ccda-4462-b9f0-71f8774075c5.png"></p>

<p align='justify'> 
Observe cómo la agregación GROUP BY en el lado izquierdo de la imagen agrupa las tres filas en una sola fila. La función de ventana en el lado derecho de la imagen puede generarcada fila con un valor de agregación. Esto puede ahorrarle tener que hacer una unión después de GROUP BY.
</p>

**Ejemplo: GROUP BY versus función de ventana**  
<p align='justify'> 
Aquí hay un ejemplo rápido para darle una idea de lo que hace una función de ventana. Digamos que tenemos algunos datos de salarios y queremos encontrar para crear una columna que nos dé el salario promedio para cada título de trabajo.
</p>
<p align='center'>
<img src="https://user-images.githubusercontent.com/116538899/234940848-ed5c0ab9-835b-4821-b6c4-4413ca7baaef.png"> 
</p>
<br>
<p align='center'>
<img src="https://user-images.githubusercontent.com/116538899/234941012-5f3754a0-1c0e-467a-8c36-36554028046f.png"> 
</p>  

#### Sintaxis de la función de ventana  
Así es como se ve la sintaxis genérica para una función de ventana en la cláusula SELECT.

<p align='center'>
<img src="https://user-images.githubusercontent.com/116538899/234941410-dbee9fc9-7379-48ee-8edc-b97cf6b1528f.png"> 
</p>   

<p align='justify'>
Hay muchas palabras aquí, así que veamos algunas definiciones: 
<br>
<strong>window_function</strong>es el nombre de la función de ventana que queremos usar; por ejemplo, sum, avg o row_number 
<br>
<strong>expresión</strong> es el nombre de la columna en la que queremos que opere la función de ventana.
<br>
Esto puede no ser necesario dependiendo de qué window_function se use 
<br>
<strong>OVER</strong>es solo para indicar que esta es una función de ventana
<br>
<br>
<strong>PARTITION BY</strong> divide las filas en particiones para que podamos especificar qué filas usar para calcular la función de ventana lista_partición es el nombre de la(s) columna(s) por la(s) que queremos particionar.
<br>
<br>
<strong>ORDER BY</strong>se usa para que podamos ordenar las filas dentro de cada partición. <strong>Esto es opcional</strong> y no tiene que ser especificado order_list es el nombre de la(s columna(s) por las que queremos ordenar.
<br>
<br>
<strong>ROWS</strong>se puede usar si queremos limitar aún más las filas dentro de nuestra partición. Esto es opcional y generalmente no se usa. frame_clause define cuánto compensar de nuestra fila actual No se preocupen por memorizar las definiciones y la sintaxis o incluso por comprender completamente lo que significa exactamente en este momento.
</p>

**Ejemplo rápido**
<p align='justify'>
Para ayudarlo a tener una mejor idea de cómo funciona realmente la sintaxis, a continuación se muestra un ejemplo de cómo se vería una función de ventana en la práctica.<br>
Esta es la consulta que habría generado el resultado que vimos anteriormente con respecto al salario por cargo. 
</p>

<p align='center'>
<img src="https://user-images.githubusercontent.com/116538899/234947208-3236377a-971d-43c2-8d94-f25b8212a70d.png">
</p>

<p align='justify'>
Aquí, AVG() es el nombre de la función de ventana, SALARY es la expresión y JOB_TITLE es nuestra lista de particiones. No usamos <strong>ORDER BY</strong> porque no es necesario y no queremos usar FILAS porque no queremos limitar más nuestra partición.
</p>

<p align='center'>
<img src="https://user-images.githubusercontent.com/116538899/234947670-cd7e0384-6d03-433e-9be5-9b7eaadc81da.png" height = "30%" width= "30%">
</p> 
  
#### Principales Window Functions  
<p align='justify'>
Hay tres tipos principales de funciones de ventana disponibles para usar: funciones de agregación, clasificación y valor. En la imagen a continuación, puede ver algunos de los nombres de las funciones que se encuentran dentro de cada grupo.
</p>


<p align='center'>
<img src="https://user-images.githubusercontent.com/116538899/234948203-f62a5d7a-be6a-46a9-9602-53bdd79ea38e.png">
</p>  
 
Aquí hay una descripción general rápida de para qué es útil cada tipo de función de ventana  
<p align='justify'>
<strong>Funciones agregadas:</strong> podemos usar estas funciones para calcular varias agregaciones, como el promedio, el número total de filas, los valores máximos o mínimos o la suma total dentro de cada ventana o partición.
<br>
<br>
<strong>Funciones de clasificación:</strong> estas funciones son útiles para clasificar filas dentro de su partición.
<br>
<br>
<strong>Funciones de valor:</strong> estas funciones le permiten comparar valores de filas anteriores o siguientes dentro de la partición o el primer o último valor dentro de la partición.
</p>

<a name=casopractico></a>
#### Caso práctico  
Ejemplo 1) Subsonculta   
```sql
SELECT 
*
FROM salesman.orders
WHERE  salesman_id IN (SELECT salesman_id FROM salesman WHERE city = 'Paris'
```

<p align='center'><img src="https://user-images.githubusercontent.com/116538899/235189468-b093aa33-6baa-470e-87ab-c029670b3193.png"></p> 
  
Ejemplo 2) Creación de CTE 
```sql
WITH comision_1 as (
SELECT *,
ROUND(purch_amt*0.05,2) as comision -- Aquellas ventas que superen los 200 USD llevan un 5% de comisión
FROM salesman.orders
WHERE purch_amt>200
)
,comision_2 as (
SELECT *,
ROUND(purch_amt*0.01,2) as comision -- Aquellas ventas que No superen los 200 USD llevan un 1% de comisión
FROM salesman.orders
WHERE purch_amt<=200
)
SELECT *
FROM comision_1
UNION
SELECT *
FROM comision_2
;
```

<p align='center'>
<img src="https://user-images.githubusercontent.com/116538899/235190186-6382fb68-b3bd-4e1d-a687-ac38525dc236.png">
</p> 
  
Ejemplo 3) Windows Function 
```sql
SELECT
*,
ROUND(AVG(purch_amt) OVER (PARTITION BY salesman_id),2) AS avg_comercial
FROM salesman.orders;
```

<p align='center'><img src="https://user-images.githubusercontent.com/116538899/235190459-cc425cb5-eff2-4ce8-85d5-38b294463143.png"></p> 

<a name=Caso5></a>
  
### Caso Data Bank

#### Introducción   
<p align='justify'>
Hay algunas advertencias interesantes que acompañan a este modelo de negocio, ¡y aquí es donde el equipo de Data Bank necesita su ayuda! El equipo de administración de Data Bank quiere aumentar su base total de clientes, pero también necesita ayuda para rastrear cuánto almacenamiento de datos necesitarán sus clientes.
<br>
Este caso de uso se trata de calcular métricas, crecimiento y ayudar a la empresa a analizar sus datos de una manera inteligente para pronosticar y planificar mejor sus desarrollos futuros!	
</p>

#### Descripción de Datos
El equipo de Data Bank ha preparado un modelo de datos para este estudio de caso, así como algunas filas de ejemplo del conjunto de datos completo a continuación para que se familiarice con sus tablas.  

<p align='center'><img src="https://user-images.githubusercontent.com/116538899/235194568-92881f85-d2c7-4bbe-8222-d6bc97f30a68.png"></p> 

#### Práctica
Ten en cuenta que las transaccinoes deben netearse. Un deposito es ingreso de dinero, withdraw es extracción de dinero y purchase es una compra por lo tengo extracción de dinero.
  
**A. Exploración de nodos de clientes**
1. ¿Cuántos nodos únicos hay en el sistema del banco de datos?  
```sql
SELECT
COUNT(DISTINCT node_id) nodos_unicos
FROM data_bank.customer_nodes;
```
<p align='center'>
<img src="https://user-images.githubusercontent.com/116538899/235327306-6074e80d-0b6c-4c25-9eec-c1066e8635bc.png">
</p>
   
2. ¿Cuál es el número de nodos por nombre de región?    
```sql
SELECT
region_name Region,
COUNT(DISTINCT node_id) Nodos
FROM data_bank.customer_nodes c
LEFT JOIN data_bank.regions r ON c.region_id = r.region_id
GROUP BY Region; 
```
<p align='center'>
<img src="https://user-images.githubusercontent.com/116538899/235327343-987d0be0-3b31-4cde-becd-8301ecbb34ee.png">
</p>

3. ¿Cuántos clientes únicos se asignan a cada nombre de región?    
```sql
SELECT
region_name Region, 
COUNT(DISTINCT customer_id) Clientes_unicos
FROM data_bank.customer_nodes c
LEFT JOIN data_bank.regions r ON c.region_id = r.region_id
GROUP BY Region
ORDER BY Clientes_unicos DESC;
```
<p align='center'>
<img src="https://user-images.githubusercontent.com/116538899/235327409-5272d3ba-9774-4d68-a2e7-d01f260c8ad2.png">
</p>  

4. ¿Cuántos días en promedio se reasignan los clientes a un nodo diferente?(Resuelve éste ejercicio con una CTE para calcular los dias de rotacion)   
```sql
WITH reasignacion AS( 
SELECT 
*,
DATEDIFF(end_date, start_date) dias_reasignacion
FROM data_bank.customer_nodes
)
SELECT
AVG(dias_reasignacion) dias_promedio
FROM reasignacion; 
```
<p align='center'>
<img src="https://user-images.githubusercontent.com/116538899/235327445-3147f481-9c73-478e-913b-07ae366a6f61.png">
</p>   

5. ¿Que puedes observar del resultado? ¿Esta bien el numero que te da? Hay algun outlier (valor fuera de lo normal) de fechas? ¿Como volverías a calular el ejercicio 4 sin estos valores?   
Como pudimos observar el anterior resultado generó un número promedio de días extremo por lo cual analizaremos a fondo el campo end_date con el siguiente código sql. 

```sql
SELECT 
*,
COUNT(txn_amount) OVER (PARTITION BY customer_id) Total_transaccion
FROM data_bank.customer_transactions;
``` 
<p align='center'>
<img src="https://user-images.githubusercontent.com/116538899/235327525-ed07980e-9718-4590-aed7-a337b0dfd5c8.png">
</p>   

Se observa que el problema en el campo end_date es la fecha 9999-12-31 con lo cual procedemos a excluirla de la instrucción del ejericicio 4. 

```sql
WITH reasignacion AS( 
SELECT 
*,
DATEDIFF(end_date, start_date) dias_reasignacion
FROM data_bank.customer_nodes
WHERE end_date <> '9999-12-31'
)
SELECT
AVG(dias_reasignacion) dias_promedio
FROM reasignacion; 
``` 
<p align='center'>
<img src="https://user-images.githubusercontent.com/116538899/235327553-f3eb950d-6a0e-45f5-9952-59a98f2dd10f.png">
</p>   


6. ¿Puedes filtrar devolver los nodos de clientes que tienen como nombre de region australia pero con una subconsulta condicional?  
```sql
SELECT 
*
FROM data_bank.customer_nodes
WHERE region_id IN (SELECT region_id FROM regions WHERE region_name = 'Australia');
```
<p align='center'>
<img src="https://user-images.githubusercontent.com/116538899/235327434-67c07a67-3706-4885-86b4-eefc8d0ce3f8.png">
</p>  


B. Transacciones de clientes  

1. ¿Cuál es el recuento único y el monto total para cada tipo de transacción?  
```sql
SELECT 
txn_type Transaccion, 
FORMAT(COUNT(txn_type),'es_ESP') Recuento,
FORMAT(SUM(txn_amount),2,'es_ESP') Monto_total
FROM data_bank.customer_transactions
GROUP BY Transaccion;
```
<p align='center'>
<img src="https://user-images.githubusercontent.com/116538899/235327632-acdbbb02-90bf-4861-93f2-ce79ba92e0f1.png">
</p>  
 

2. ¿Cuál es el total histórico promedio de recuentos y montos de depósitos para todos los clientes?  

3. Para cada mes, ¿cuántos clientes de Data Bank hacen más de 1 depósito y 1 compra o 1 retiro en un solo mes?  

4. ¿Puedes a la tabla de transacciones original incluir una columna que devuelva el total de cantidad de transacciones de cada cliente con una función ventana?  

5. Para el id de cliente = 1 puedes traer todas las transacciones y luego en una columna aparte la cantidad de sus transacciones por mes con una función ventana?  

6. ¿Puedes calcular el saldo de cada cliente de cada mes son una subconsulta?  
