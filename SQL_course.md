<a name="Tabla-de-contenido1"></a>
# Tabla de Contenido [![Texto](https://user-images.githubusercontent.com/116538899/231064143-c080de13-8be9-4321-8694-e62539263f5a.png)](#Tabla-de-contenido1)
- [Análisis Exploratorio](#Análisis-Exploratorio)
  - [Función Case](#Función-case)
  - [Función Case](#Función-concat)
  - [Función Case](#Función-replace)
  - [Análisis Previo](#Análisis-Previo2)   
  - [Análisis de Ventas](#Análisis-de-Ventas2)
  - [Análisis de Tráfico Web](#Análisis-de-Tráfico-Web2)
  - [Visualización en Looker](#Visualización-en-Looker2)   

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
- Fecha:rango razonale o no, comprar fechas, si las fechas futuras son 
- Fecha: rango razonable o no, comparar fechas, si las fechas futuras son razonables o no  
- Columnas numéricas: el rango de valores está dentro de lo esperado, busque valores atípicos,calcule STDDEV  
- Texto: si es categórico => debe ser una pequeña cantidad de valores distintos, si es en gran medida único => los valores deben estar cerca del número de filas en el conjunto de datos, verifique la longitud y busque valores inusualmente largos o cortos (verifique al menos 10 superiores/ valores atípicos más bajos)  

### **Función Case**

### **Función Concat**

### **Función Replace**
