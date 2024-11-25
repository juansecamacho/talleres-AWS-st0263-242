# Lab Spark

###  Configuración y Carga de Datos

1. Primero vamos a descargar el dataset de COVID-19 en Colombia:

```bash
# Descargar desde datos abiertos de Colombia
wget https://www.datos.gov.co/api/views/gt2j-8ykr/rows.csv?accessType=DOWNLOAD -O Casos_positivos_de_COVID-19_en_Colombia.csv
```

2. Ahora se va a subir el Dataset a HDFS:

```bash
hdfs dfs -put Casos_positivos_de_COVID-19_en_Colombia.csv /user/hadoop/datasets/covid19/
```

### Análisis Exploratorio en PySpark

1. Vamos a cargar y explorar el dataset. Iniciamos PySpark en el nodo master de EMR y crea un DataFrame:

```bash
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("CovidAnalysis").getOrCreate()
df = spark.read.csv("hdfs:///user/hadoop/datasets/covid19/Casos_positivos_de_COVID-19_en_Colombia.csv", header=True, inferSchema=True)
df.printSchema() 
```
2. Filtrar y analizar datos. seleccionar y filtrar datos de interés:

```bash
df_filtered = df.select("ID de caso", "Ciudad de ubicación", "Estado", "Edad").filter(df["Estado"] == "Activo")
df_filtered.show(10)  # Muestra los primeros 10 registros

df.groupBy("Estado").avg("Edad").show()

```

### Guardar Datos en AWS S3 y Google Drive

1. Guardar el DataFrame en AWS S3 y Google Drive:

```bash
df_filtered.write.mode("overwrite").parquet("s3://jscamachobucket/covid-data")

from google.colab import drive
drive.mount('/content/drive')
df_filtered.write.csv("/content/drive/MyDrive/covid-data", header=True)
```

Si queremos ejecutar el análisis como script, crea covid_analysis.py con el siguiente contenido:

```bash
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("CovidAnalysis").getOrCreate()
df = spark.read.csv("hdfs:///user/hadoop/datasets/covid19/Casos_positivos_de_COVID-19_en_Colombia.csv", header=True, inferSchema=True)
df_filtered = df.select("ID de caso", "Ciudad de ubicación", "Estado", "Edad").filter(df["Estado"] == "Activo")

df_filtered.write.mode("overwrite").parquet("hdfs:///tmp/covid-data")
df_filtered.write.mode("overwrite").parquet("s3://jscamachobucket/covid-data")
```

Ahora se ejecuta el script en EMR:

```bash
spark-submit covid_analysis.py
```

### Verificar Resultados en HDFS:

consultar los resultados guardados en HDFS:

```bash
hdfs dfs -ls /tmp/covid-data
hdfs dfs -cat /tmp/covid-data/part-00000 | head
```
