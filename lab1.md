# Laboratorio 1: Hadoop y S3

## Conexion por SSH al cluster

1. Abrimos nuestra consola y en la carpeta donde este nuestra llave del cluster vamos a correr el siguiente comando.

```bash
ssh -i <your-key-pair.pem> hadoop@<master-public-dns-name>

```

![image](https://github.com/user-attachments/assets/91213f7a-7aff-439a-bce7-04737dd4a475)

Nos deberia salir esto cuando nos hayamos conectado correctamente a la instancia

## Setup del cluster

1. Lo primero es descargar git para poder clonar el repositorio con el cual vamos a trabajar.

```bash
sudo yum install git
```

![image](https://github.com/user-attachments/assets/3ce09502-b44e-4740-92bd-3d7478782232)

2. Ahora vamos a clonar el repositorio de la materia con el siguiente comando:

```bash
git clone https://github.com/st0263eafit/st0263-242.git
```

![image](https://github.com/user-attachments/assets/8072ff2e-3be7-457e-ab2a-b3dd819bb4df)

## Creacion de directorios

1. Para listar los directorios vamos a usar los siguientes comandos:

```bash
hdfs dfs -ls /
hdfs dfs -ls /user
hdfs dfs -ls /user/hadoop
hdfs dfs -ls /user/hadoop/datasets
```

2. Para crear los directorios: datasets y guternberg usamos los siguientes comandos

```bash
hdfs dfs -mkdir /user/hadoop/datasets
hdfs dfs -mkdir /user/hadoop/datasets/gutenberg
```

![image](https://github.com/user-attachments/assets/5c27337f-93a5-4c65-ad98-4c540254d620)

3. Vamos a descargar los archivos desde HDFS a nuestra maquina local con los siguientes comandos

```bash
hdfs dfs -get /user/hadoop/datasets/gutenberg-small/*.txt ~hadoop/mis_datasets/
hdfs dfs -copyToLocal /user/hadoop/datasets/gutenberg-small/*.txt ~hadoop/mis_datasets/
```

Y verificamos

![image](https://github.com/user-attachments/assets/d4ce9c71-0f5c-4eb3-bf50-7581a491f963)

4. ahora vamos a crear carpetas en HDFS y a copiar los archivos necesarios

```bash
hdfs dfs -mkdir /user/hadoop/datasets
hdfs dfs -mkdir /user/hadoop/datasets/gutenberg-small
hdfs dfs -put /local-path-to-cloned-repo/* /user/hadoop/datasets/gutenberg-small
```
Ahora vamos a cargar los archivos en HDFS para que puedan ser utilizados por Hive

```bash
hdfs dfs -put /root/st0263-242/bigdata/datasets/gutenberg-small/*.txt /user/hadoop/datasets/gutenberg-small/
```

![image](https://github.com/user-attachments/assets/a826558f-666c-4284-b548-dabea5137ca4)
![image](https://github.com/user-attachments/assets/75cffab8-878c-4b42-a3e3-a37dd7ad994a)


## Copiar archivos a S3

```bash
hadoop fs -put /root/st0263-242/bigdata/datasets/ s3://jscamachobucket/

```

Y por ultimo enviamos los archivos a s3 por medio de Hadoop a traves de SSH

```bash
hadoop fs -put /root/st0263-242/bigdata/datasets/ s3://jscamachobucket/
```













