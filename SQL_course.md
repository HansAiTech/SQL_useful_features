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
- [SQL Avanzado - subconsultas y cte's](#SQL-Avanzado-subconsultas-y-ctes)  
  - [¿Que son las subqueries?](#Subqueries)

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
