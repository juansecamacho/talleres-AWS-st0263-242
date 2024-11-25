# Hive y Spark

## Ingresar al cluster

1. Para ingresar al cluster abrimos la terminal local y nos ubicamos donde este la clave para entrar al cluster, ahi usamos el comando: 

```bash
ssh -i <your-key-pair.pem> hadoop@<master-public-dns-name>
```
![image](https://github.com/user-attachments/assets/91213f7a-7aff-439a-bce7-04737dd4a475)

asi nos deberia salir en caso de que nos hayamos conectado correctamente

y nos metemos a hue por el link que nos sale cuando inicamos el cluster **importante que el usuario sea hadoop**

![image](https://github.com/user-attachments/assets/ddbab723-d94c-4a88-ac12-3533f48e05df)

## Archivos para el taller

1. Ubicaremos de forma temporal con HDFS los archivos `hdi-data.csv` y `export-data.csv` en la carpeta: `/user/hadoop/datasets/onu/`

## Gestion y consultas
 1. Nos tenemos que meter desde la consola, ya cuando estemos conectados al cluster a beeline con el comando:

```bash
beeline
!connect jdbc:hive2://localhost:10000
```

![image](https://github.com/user-attachments/assets/f05943b8-8aeb-4700-baab-cd89f183d495)

2. Ahora vamos a listar todas las bases de datos que tenemos en este momento con el comando:

```bash
SHOW DATABASES;
```

![image](https://github.com/user-attachments/assets/420a7802-f278-4c59-9b2a-27dd7a78547e)

3. luego vamos a usar la base de datos usernameb con el comando:
   
```bash
USE usernameb;
```

![image](https://github.com/user-attachments/assets/5838836f-b235-4a35-b8c5-e66c649e8cb8)

4. Vamos a crear la tabla HDI usando beeline

```bash
CREATE TABLE HDI (
    id INT,
    country STRING,
    hdi FLOAT,
    lifeex INT,
    mysch INT,
    eysch INT,
    gni INT
) 
ROW FORMAT DELIMITED 
FIELDS TERMINATED BY ',' 
STORED AS TEXTFILE;
```

## Datos a la tabla en HDFS

### Opcion 1

Copiar los datos directamente hacia HDFS con el siguiente comando:

```bash
$ hdfs dfs -put hdfs:///user/hadoop/datasets/onu/hdi-data.csv hdfs:///user/hive/warehouse/usernamedb.db/hdi
```

### Opcion 2

1. Cargar los datos desde Hive, pero antes de eso hay que darle permisos al directorio de la siguiente forma: 

```bash
$ hdfs dfs -chmod -R 777 /user/hadoop/datasets/onu/
$ beeline
0: jdbc:hive2://sandbox-hdp.hortonworks.com:2> LOAD DATA INPATH '/user/hadoop/datasets/onu/hdi-data.csv' INTO TABLE HDI;
```
2. Ahora vamos a crear una tabla extera en HDFS

```bash
use usernamedb;
CREATE EXTERNAL TABLE HDI (id INT, country STRING, hdi FLOAT, lifeex INT, mysch INT, eysch INT, gni INT)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION '/user/hadoop/datasets/onu';
```


### Consultas y Cálculos sobre la Tabla HDI

1. Mostrar Tablas y Describir la Tabla HDI

```bash
use usernamedb;
show tables;
describe hdi;
```

### Consultar Todos los Datos de HDI

1. Empezaremos con diferentes consultas con varios comandos, ña primera seria de todos los datos

```bash
select * from hdi;
```

2. Ahora vamos a consultar pasies con GNI Mayor a 2000

```bash
select country, gni from hdi where gni > 2000;
```

### Ejecutar un JOIN en Hive

1. Vamos a crear la tabla EXPO en Hive. Luego vamos a obtener los datos base export-data.csv y ubicarlos en 'datasets'.

```bash
use usernamedb;
CREATE EXTERNAL TABLE EXPO (
    country STRING,
    expct FLOAT
) 
ROW FORMAT DELIMITED 
FIELDS TERMINATED BY ',' 
STORED AS TEXTFILE 
LOCATION 's3://jscamachobucket/onu/export/';
```

2. Realizar el JOIN entre las Tablas HDI y EXPO

```bash
SELECT h.country, gni, expct 
FROM HDI h 
JOIN EXPO e ON (h.country = e.country) 
WHERE gni > 2000;
```

#### WordCount en Hive

Crear la Tabla docs en Hive para WordCount

Opcion 1: Usar HDFS

```bash
use usernamedb;
CREATE EXTERNAL TABLE docs (
    line STRING
) 
STORED AS TEXTFILE 
LOCATION 'hdfs://localhost/user/hadoop/datasets/gutenberg-small/';
```

Alternativa 2: Usar S3

```bash
CREATE EXTERNAL TABLE docs (
    line STRING
) 
STORED AS TEXTFILE 
LOCATION 's3://jscamachobucket/gutenberg-small/';
```

#### WordCount Ordenado por Palabra

```bash
SELECT word, count(1) AS count 
FROM (SELECT explode(split(line,' ')) AS word FROM docs) w 
GROUP BY word 
ORDER BY word DESC 
LIMIT 10;
```

#### WordCount Ordenado por Frecuencia de Menor a Mayor

```bash
SELECT word, count(1) AS count 
FROM (SELECT explode(split(line,' ')) AS word FROM docs) w 
GROUP BY word 
ORDER BY count DESC 
LIMIT 10;
```

#### Reto: Almacenar los Resultados de WordCount en una Tabla

Para almacenar los resultados de un WordCount en una tabla en Hive:

1. Se va a crear la tabla para almacenar resultados

```bash
CREATE TABLE wordcount_results (
    word STRING,
    count INT
)
STORED AS TEXTFILE;
```

2. Vamos a insertar resultados del WordCount en la tabla

```bash
INSERT INTO TABLE wordcount_results
SELECT word, count(1) AS count
FROM (SELECT explode(split(line, ' ')) AS word FROM docs) w
GROUP BY word;
```

3. Consultar la Tabla de Resultados de WordCount
```bash
SELECT * FROM wordcount_results
ORDER BY count DESC
LIMIT 10;
```

#### Resumen del Script Completo

```bash
CREATE TABLE wordcount_results (
    word STRING,
    count INT
)
STORED AS TEXTFILE;

INSERT INTO TABLE wordcount_results
SELECT word, count(1) AS count
FROM (SELECT explode(split(line, ' ')) AS word FROM docs) w
GROUP BY word;

SELECT * FROM wordcount_results
ORDER BY count DESC
LIMIT 10;
```
