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

  


