<a name="Tabla-de-contenido1"></a>
# Tabla de Contenido [![Texto](https://user-images.githubusercontent.com/116538899/231064143-c080de13-8be9-4321-8694-e62539263f5a.png)](#Tabla-de-contenido1)
- [Análisis Exploratorio](#Análisis-Exploratorio)
  - [Función Case](#Función-case)
  - [Función Concat](#Función-concat)
  - [Función replace](#Función-replace)
 

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

### **Función Replace**
