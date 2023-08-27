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

