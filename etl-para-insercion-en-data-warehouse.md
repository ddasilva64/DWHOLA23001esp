# ETL para inserción en DWH

## Documento de mapeo
 
El mapeo de datos es el paso inicial de cualquier proceso de datos, incluidos ETL y la integración de datos. Extrae campos de datos de uno o varios archivos de origen y los compara con sus campos de destino relacionados en el destino. El objetivo principal del mapeo de datos es garantizar que los datos de una fuente se puedan transformar o transferir con precisión y eficacia a otro destino, manteniendo su integridad, consistencia y significado.

<p><br></p> 

![Transformaciones](https://i.imgur.com/VydCMmT.png)
_Transformaciones_

<p><br></p> 

![ImTransformacionesgur](https://i.imgur.com/cJiLbiM.png)
_Transformaciones_

<p><br></p> 

**_Mi propuesta_**:

Necesito un sistema para generar, de manera rápida y eficiente, un **_mapeo de datos_**. Además, necesito que esa herramienta me ayude a generar el **_ETL_** y este puede ser que lo haga en **_Python_** o en **_PDI_**.

He cogido el resultado que obtuve en **_Pentaho_** en un proyecto anterior y he intentado hacer ingeniería inversa.
Para ello he especificado en lenguaje natural lo que quiero obtener (especificaciones).

Le he pedido a **_ChatGPT-3.5_** que genere:

- **_Documento de mapeo_**
- **_ETL_** en **_Python_**
- **_ETL_** (pasos) en **_Pentaho_**
- El resultado es que, habiendo hecho un análisis exploratorio de los datos, viendo como se relacionan las fuentes (**_raw_**) de datos y sabiendo lo que queremos en la salida, el resultado es muy muy bueno y en total he tardado unos minutos.

Si no me convence el resultado, repito el proceso hasta que me convenza (itero).

El resultado de esta prueba, en inglés, estará integrado en el proyecto del curso.

<p><br></p> 

### Especificación en lenguaje natural

Te voy a dar las columnas de una serie de ficheros, te daré también las columnas del stage file de salida XML. 

Quiero que me hagas una tabla bidimensional del mapeo de datos (en formato md), donde las columnas se correspondan a los ficheros y cada fila se corresponda al mismo campo en los diferentes ficheros. Además especifica las transformaciones necesarias en cada campo. Finalmente indica solo los campos que sí van al stage file de salida. 

De la tabla bidimensional anterior genera un fichero md.

Fichero raw 1 (fichero .XLSX):
- Country Code (en mayúsculas y con espacios a izquierda y derecha)
- Region
- IncomeGroup
- SpecialNotes
- TableName

Fichero raw 2 (fichero .CSV):
- id
- alpha2
- alpha3 (en minúsculas y sin espacios a izquierda y derecha)
- name

Country Code y alpha3 tienen el mismo contenido, pero a Country Code le tienes que hacer las siguientes transformaciones:
1. Conviételo en minúsculas.
2. Quítale espacios por la derecha y por la izquierda.

Se deben coger las filas en las que Country Code y alpha3 coincidan (solamente si existe alpha3). Lo anterior es un right join.

Fichero raw 3 (fichero .CSV):
- iso_code
- country
- 2021_last_updated
- 2020_population
- area
- density_sq_km
- growth_rate
- world_%
- rank
- alpha2
- name
- alpha3
- id

iso_code tiene el mismo contenido que Country Code y alpha3 (de la unión anterior), pero a iso_code le tienes que hacer las siguientes transformaciones:
1. Conviételo en minúsculas.
2. Quítale espacios por la derecha y por la izquierda.

A area le tienes que hacer las siguientes transformaciones:
1. Quítale espacios por la derecha y por la izquierda.
2. Extrae únicamentete el subestring que va de las posiciones 6 a 25.

Haz un left join entre iso_code y el join de raw 1 y raw 2.

Fichero raw 4 (fichero .XML):
- country_code
- area

country_code de raw 4 tiene el mismo contenido que iso_code de raw 3, alpha3 de raw2 y Country Code de raw 1.

Haz un left join entre country_code y el join de raw 4 con el resultado del join anterior.

Para cada fila en raw 4, debemos mirar si en raw 3 la columna area tiene valor nulo. En ese caso cogemos la columna area de raw 4.

Si area de raw 3 es nulo, debemos coger area de raw 4 y el resultado de lo anterior se debe llamar area.

stage file de salida XML:
- CountryCode
- Country
- Area
- Region

<p><br></p> 

### Tabla de Mapeo de Datos

| Campo            | Fichero raw 1      | Fichero raw 2      | Fichero raw 3      | Fichero raw 4      | Transformaciones                        | Va al Stage File |
|------------------|--------------------|--------------------|--------------------|--------------------|------------------------------------------|------------------|
| CountryCode      | Country Code       | alpha3             | iso_code           | country_code       | Minúsculas, Sin espacios izq/der         | Sí               |
| Country          | -                  | -                  | country            | -                  | -                                        | Sí               |
| Area             | -                  | -                  | area               | area               | Sin espacios izq/der, Substring(6, 25)  | Sí               |
| Region           | Region             | -                  | -                  | -                  | -                                        | Sí               |

<p><br></p> 

### Especificación en lenguaje natural (PDI)

De lo anterior quiero los resultados, con el máximo detalle, en Pentaho Data Integration. Todos los joins deben estar especificados (right,left o inner).

<p><br></p> 

### Flujo en Pentaho Data Integration

1. **Fichero raw 1 (XLSX) Input:**
   - Lee el fichero .XLSX con las siguientes columnas: Country Code, Region, IncomeGroup, SpecialNotes, TableName.

2. **Fichero raw 2 (CSV) Input:**
   - Lee el fichero .CSV con las siguientes columnas: id, alpha2, alpha3, name.

3. **Transformación para raw 1 y raw 2:**
   - Aplica las transformaciones necesarias a "Country Code" y "alpha3":
     1. Convierte ambos campos a minúsculas.
     2. Elimina espacios en los extremos.

4. **Join entre raw 1 y raw 2:**
   - Realiza un right join entre "Country Code" transformado y "alpha3" transformado.

5. **Fichero raw 3 (CSV) Input:**
   - Lee el fichero .CSV con las siguientes columnas: iso_code, country, 2021_last_updated, 2020_population, area, density_sq_km, growth_rate, world_%, rank, alpha2, name, alpha3, id.

6. **Transformación para raw 3:**
   - Aplica las transformaciones necesarias a "iso_code":
     1. Convierte el campo a minúsculas.
     2. Elimina espacios en los extremos.

   - Aplica la transformación a "area":
     1. Elimina espacios en los extremos.
     2. Extrae el substring de las posiciones 6 a 25.

7. **Join entre iso_code y raw 1-raw 2 Join:**
   - Realiza un left join entre "iso_code" transformado y el resultado del join entre raw 1 y raw 2.

8. **Fichero raw 4 (XML) Input:**
   - Lee el fichero .XML con las siguientes columnas: country_code, area.

9. **Join entre country_code y raw 3-raw 1-raw 2 Join:**
   - Realiza un left join entre "country_code" y el resultado del join entre raw 3 y el join anterior (raw 1-raw 2 join).

10. **Filtro y transformación para determinar "Area" final:**
    - Agrega un paso que verifique si "area" en raw 3 es nulo.
    - Si "area" es nulo, utiliza el valor de "area" en raw 4.

11. **Salida a XML:**
    - Genera el stage file de salida XML con las columnas CountryCode, Country, y Area (desde el paso anterior), y agrega la columna Region.

<p><br></p> 

### Solución aportada anteriormente en mi proyecto (PROWPI001)

![Solución en PROWPI001](https://i.imgur.com/CrNtwlA.png)
_Solución en PROWPI001_

<p><br></p> 

### Especificación en lenguaje natural (Python para verificación)

De lo anterior quiero los resultados, con el máximo detalle, en Python. Todos los joins deben estar especificados (right,left o inner).

verificación en 

### Código Python

````Python
import pandas as pd

# Cargar los datos de los ficheros
raw1 = pd.read_excel("raw1.xlsx")
raw2 = pd.read_csv("raw2.csv")
raw3 = pd.read_csv("raw3.csv")
raw4 = pd.read_xml("raw4.xml")

# Transformaciones para raw 1 y raw 2
raw1["Country Code"] = raw1["Country Code"].str.strip().str.lower()
raw2["alpha3"] = raw2["alpha3"].str.strip().str.lower()

# Join entre raw 1 y raw 2 (right join)
join_raw1_raw2 = pd.merge(raw1, raw2, left_on="Country Code", right_on="alpha3", how="right")

# Transformaciones para raw 3
raw3["iso_code"] = raw3["iso_code"].str.strip().str.lower()
raw3["area"] = raw3["area"].str.strip().str[6:25]

# Join entre iso_code y raw 1-raw 2 join (left join)
join_iso_raw1_raw2 = pd.merge(raw3, join_raw1_raw2, left_on="iso_code", right_on="Country Code", how="left")

# Join entre country_code y raw 3-raw 1-raw 2 join (left join)
join_country_raw3_raw1_raw2 = pd.merge(raw4, join_iso_raw1_raw2, left_on="country_code", right_on="Country Code", how="left")

# Filtro y transformación para determinar "Area" final
join_country_raw3_raw1_raw2["Area_final"] = join_country_raw3_raw1_raw2.apply(lambda row: row["area_y"] if pd.isnull(row["area_x"]) else row["area_x"], axis=1)

# Seleccionar columnas para el stage file de salida XML
output_columns = ["Country Code", "country", "Area_final", "Region"]

# Crear el DataFrame de salida
output_data = join_country_raw3_raw1_raw2[output_columns]

# Guardar el DataFrame en un archivo XML
output_data.to_xml("output.xml", root_name="root", row_name="row")

# Imprimir el DataFrame resultante
print(output_data)

````

<p><br></p> 

**_¡Aviso!_**: Deberemos regenerar los resultados, tantas veces como sea necesario, si el resultado no nos satisface.

<p><br></p> 

[ChatGPT usage](CHATGPT_USAGE.md) 

## Modelo físico

**_Modelo físico propuesto en el curso_**

--drop table dim_clientes;
create table dim_clientes
(
	id_cliente	integer
	,codigo_cliente	varchar(20)
	,nombre	varchar(50)
	,apellido	varchar(50)
	,nombre_completo	varchar(100)
	,numero_telefono_celular	varchar(20)
	,numero_telefono_casa	varchar(20)
	,numero_telefono_trabajo	varchar(20)
	,ciudad_casa	varchar(50)
	,fecha_carga timestamp
	,fecha_actualizacion timestamp
	,primary key (id_cliente)
) 
;


--drop table dim_productos;
create table dim_productos
(
	id_producto	integer
	,codigo_producto	varchar(20)
	,nombre	varchar(50)
	,color	varchar(50)
	,tamanio	varchar(50)
	,categoria	varchar(50)
	,fecha_carga timestamp
	,fecha_actualizacion timestamp
	,primary key (id_producto)
) 
;


--drop table dim_territorios;
create table dim_territorios
(
	id_territorio	integer
	,codigo_territorio	varchar(20)
	,nombre	varchar(50)
	,continente	varchar(50)
	,fecha_carga timestamp
	,fecha_actualizacion timestamp
	,primary key (id_territorio)
) 
;


--drop table dim_vendedores;
create table dim_vendedores
(
	id_vendedor	integer
	,codigo_vendedor	varchar(20)
	,identificación	varchar(20)
	,nombre	varchar(50)
	,apellido	varchar(50)
	,nombre_completo	varchar(50)
	,rol	varchar(50)
	,fecha_nacimiento	date
	,genero	varchar(10)
	,ind_activo	boolean
	,fecha_inicio	date
	,fecha_fin	date
	,version integer
	,fecha_carga timestamp
	,primary key (id_vendedor)
) 
;


--drop table fact_ventas;
CREATE TABLE dwh_adventureworks.fact_ventas (
	id_venta integer NOT NULL,
	codigo_venta_detalle varchar(10) NOT NULL,
	codigo_venta_encabezado varchar(10) NOT NULL,
	id_fecha integer NULL,
	id_territorio integer NULL,
	id_cliente integer NULL,
	id_vendedor integer NULL,
	id_producto integer NULL,
	cantidad integer NULL,
	valor numeric(18,2) NULL,
	descuento numeric(18,2) NULL,
	fecha_carga timestamp NULL,
	fecha_actualizacion timestamp NULL,
	CONSTRAINT fact_ventas_pkey PRIMARY KEY (id_venta)
)


--drop table dim_tiempo;
create table dim_tiempo
(
    id_fecha int not null,
    fecha date not null, 
    dia smallint not null,
    mes smallint not null,
    anio smallint not null,
    dia_semana smallint not null,
    dia_anio smallint not null,
	PRIMARY KEY (id_fecha)
)



--Ejecutar luego de realizar la primera carga de datos en las dimensiones con Pentaho!!!!

INSERT INTO dwh_adventureworks.dim_clientes
(id_cliente, codigo_cliente, nombre, apellido, nombre_completo, numero_telefono_celular, numero_telefono_casa, numero_telefono_trabajo, ciudad_casa, fecha_carga, fecha_actualizacion)
VALUES(-1, '-1', 'Sin Información', 'Sin Información', 'Sin Información', '', '', '', '', '1900/01/01 00:00:00', '1900/01/01 00:00:00');


INSERT INTO dwh_adventureworks.dim_productos
(id_producto, codigo_producto, nombre, color, tamanio, categoria, fecha_carga, fecha_actualizacion)
VALUES(-1, '-1', 'Sin Información', '', '', '', '1900/01/01 00:00:00', '1900/01/01 00:00:00');


INSERT INTO dwh_adventureworks.dim_territorios
(id_territorio, codigo_territorio, nombre, continente, fecha_carga, fecha_actualizacion)
VALUES(-1, '-1', 'Sin Información', '', '1900/01/01 00:00:00', '1900/01/01 00:00:00');


INSERT INTO dwh_adventureworks.dim_vendedores
(id_vendedor, codigo_vendedor, identificación, nombre, apellido, nombre_completo, rol, fecha_nacimiento, genero, ind_activo, fecha_inicio, fecha_fin, version, fecha_carga)
VALUES(-1, '-1', null, 'Sin Información', 'Sin Información', 'Sin Información', null, '1900/01/01 00:00:00', null, true, '1900/01/01 00:00:00', '9999/12/31 00:00:00', 1, '1900/01/01 00:00:00');

## Extracción: querys en SQL

**_Extracción propuesta en el curso_**

--Extraer del transaccional

select 
	c.customerid as cod_cliente
	, p.firstname as nombre
	, p.lastname as apellido
	, p.firstname||' '||p.lastname as nombre_completo
	, case when p2.phonenumbertypeid = 1 then p2.phonenumber else null end as numero_celular
	, case when p2.phonenumbertypeid = 2 then p2.phonenumber else null end as numero_casa
	, case when p2.phonenumbertypeid = 3 then p2.phonenumber else null end as numero_trabajo
	, a.city as ciudad
from sales.customer c
left join person.person p
	on(c.personid=p.businessentityid)
left join person.personphone p2 
	on(p.businessentityid = p2.businessentityid)
left join person.businessentity b
	on(p.businessentityid = b.businessentityid)
left join person.businessentityaddress b2 
	on(b.businessentityid = b2.businessentityid and b2.addresstypeid = 2)
left join person.address a 
	on (b2.addressid = a.addressid)
	

	
--Extraer del dwh
SELECT
	  id_cliente
	, codigo_cliente
	, fecha_actualizacion
FROM dwh_adventureworks.dim_clientes

## Extracción en Pentaho

![Query de la tabla transaccional](https://i.imgur.com/71FIUHF.png)  
_Query de la tabla transaccional_

<p><br></p> 

![Conexión a la tabla del DWH](https://i.imgur.com/EXmkxbF.png)  
_Conexión a la tabla del DWH_

<p><br></p> 

![Query de la tabla del DWH](https://i.imgur.com/vUnyQVl.png)  
_Query de la tabla del DWH_

De momento la tabla del DWH está vacía.

<p><br></p> 

![Comparación de los registros de las dos tablas](https://i.imgur.com/xuNnGlt.png)  
_Comparación de los registros de las dos tablas_

## Transformación: dimensión de cliente

![Captura de la fecha de carga - del sistema -](https://i.imgur.com/pmUHWt6.png)  
_Captura de la fecha de carga - del sistema -_

![Indicador para saber si es Insert o Update](https://i.imgur.com/IpGxpqh.png)  
_Indicador para saber si es Insert o Update_

![Asignamos el ID del cliente](https://i.imgur.com/cjGse3w.png)  
_Asignamos el ID del cliente_

![Comprobamos si ID cliente es nuevo](https://i.imgur.com/mExtfPD.png)  
_Comprobamos si ID cliente es nuevo_

![Creamos una constante para ID cliente existentes](https://i.imgur.com/uIlSPrp.png)
_Creamos una constante para ID cliente existentes_

![A la variable le asignamos el ID del cliente](https://i.imgur.com/JZNwJV8.png)
_A la variable le asignamos el ID del cliente_

![Creamos una clave autoincrementada para los nuevos registros](https://i.imgur.com/EvdEfiL.png)  
_Creamos una clave autoincrementada para los nuevos registros_

![Unimos los dos pasos](https://i.imgur.com/PcepmGO.png)  
_Unimos los dos pasos_

![Fórmula final para asignar el ID de cliente](https://i.imgur.com/ZTAo6Eh.png)  
_Fórmula final para asignar el ID de cliente_

Clic con el botón derecho del ratón en la fórmula, entonces preview 

![Clic con el botón derecho del ratón en la fórmula, entonces preview ](https://i.imgur.com/uN8OZhZ.png)  
_Clic con el botón derecho del ratón en la fórmula, entonces preview_

![Configure, variables y podemos establecer el valor mínimo de la variable que incrementaremos](https://i.imgur.com/ILqe0ZF.png)  
_Configure, variables y podemos establecer el valor mínimo de la variable que incrementaremos_

![Corregimos asignación de variables mal hecha](https://i.imgur.com/39HVmyJ.png)  
_Corregimos asignación de variables mal hecha_

![Ejecución correcta del flujo del ETL](https://i.imgur.com/nCdpUYU.png)  
_Ejecución correcta del flujo del ETL_

## Carga: dimensión de cliente

![Update de clientes existentes](https://i.imgur.com/J7FK8m6.png)  
_Update de clientes existentes_

![En la solución propuesta, los registros para el Insert se dejan en un bucket de S3](https://i.imgur.com/yZWi05Y.png)  
_En la solución propuesta, los registros para el Insert se dejan en un bucket de S3_

![Solución](https://i.imgur.com/Sgssh8c.png)  
_Solución_

![Copiamos el archivo plano del bucket a la tabla de S3, para hacer el Insert](https://i.imgur.com/E9aQVnq.png)  
_Copiamos el archivo plano del bucket a la tabla de S3, para hacer el Insert_

## Soluciones ETL de las tablas de dimensiones y hechos

