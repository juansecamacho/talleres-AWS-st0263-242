# laborario 0: Configuracion del cluster EMR en AWS

## Parte1: Creacion del cluster EMR en AWS
1. Acceder a la consola de aws.
   
![image](https://github.com/user-attachments/assets/8a8e70cd-e150-48ff-b629-6c69dd65a245)

3. Buscar en la lupa el servicio de **EMR**.
   
![image](https://github.com/user-attachments/assets/714262e3-1ed7-4cdd-967c-6faa7406ad96)

5. Darle al boton de **Create cluster**.
   
![image](https://github.com/user-attachments/assets/999483d5-63a2-4205-9f03-951b8ef50223)

7. Darle nombre al cluster (el que tu quieras), la version y lo que va a tener instalado el cluster, en nuestro caso vamos a poner custom por que vamos a tener varias aplicaciones descargadas. Tambien vamos a seleccionar para que Hive y Spark puedan ver las tablas AWS en EMR.
   
![image](https://github.com/user-attachments/assets/29f20f10-a714-42f3-adf7-b35537ea6fb2)

9. Vamos a seleccionar las maquinas del cluster, por default viene m5.xlarge, pero esta maquina a veces puede tener problemas al inciar el cluster, por lo tanto trabajaremos con m4.xlarge en todas las maquinas del cluster.
    
![image](https://github.com/user-attachments/assets/7129ff67-d8df-45e9-bb44-19768f5e987c)
![image](https://github.com/user-attachments/assets/3e5bd268-437d-43fc-99cd-a8e6e3950e4f)

11. Vamos a dejar las siguientes opciones por default.
    
 ![image](https://github.com/user-attachments/assets/11b9ad19-fb55-42c4-ab91-5da30fa1aec6)
 
13. Ahora vamos a hacer que la unica forma en que se acabe el cluster es de forma manual para evitar problemas.
     
![image](https://github.com/user-attachments/assets/5eb07418-7b69-45a8-9311-9679b976ce3c)

15. Configuraremos el bucket (si no lo tienes crealo antes de inicar el cluster **muy importante**) para que guarde los notebooks de jupyter y no se pierdan cuando se apague el cluster

![image](https://github.com/user-attachments/assets/61e00631-fbf6-41fc-848a-aa2dcd26efb0)

Nota: cambiar el nombre del bucket por el que creaste antes de empezar a crear el cluster **aca esta la configuracion**
```bash
[
  {
    "Classification": "jupyter-s3-conf",
    "Properties": {
      "s3.persistence.bucket": "jscamachobucket",
      "s3.persistence.enabled": "true"
    }
  }
]
```
11. Crar la clave para la configuracion
    
![image](https://github.com/user-attachments/assets/084d3e4e-86c9-4df9-9c52-9f38ebc1cf77)

13. Seleccionar los roles de la siguiente forma
    
![image](https://github.com/user-attachments/assets/b23eccdd-7e25-485d-a804-bcac69c040af)

15. darle al boton de **crear cluster** (el cluster se demora en inicar entre 15 y 20 minutos, paciencia plox).

NOTA: El cluster no es permanente, asi que cuando quieras volver a usarlo tienes que clonar el cluster, seleccionas el cluster que quieres clonar y le das al boton de **clone**

![image](https://github.com/user-attachments/assets/5b9617cc-3359-4b0b-891e-cc0e4ffa611e)


Luego al final no te va a salir **create cluster** sino que te va a salir **clone cluster**, le das a **clone cluster** y esperas a que en el estado te salga **waiting** y al lado haya un chulito verde

15. Abrir todos los puertos TCP para acceso del cluster, esto solo se tiene que hacer una vez.

![image](https://github.com/user-attachments/assets/4dfeeb73-a767-471f-a4a9-ae9cb8ce74cc)

16. Ahora vamos a ubicar el nodo master para abrir los puertos de las aplicaciones que vamos a usar: Hadoop/Spark, ¿como sabemos cuales son los puertos que toca abrir? Nos vamos a aplicaciones dentro del cluster y ahi vamos a poder ver cuales son los puertos de: Hue, Jupyter y Zeppelin

![image](https://github.com/user-attachments/assets/9cf9b1cb-815d-44cc-afe2-584210e24295)

Los puertos son: 22, 8888, 8090, 9443, 9870 y 14000

17. Nos vamos para las instancias de EC2, que deberian haber 3 instancias, miramos cual es la que dice **master** e ingresamos

![image](https://github.com/user-attachments/assets/e67afbfb-7405-48ac-9827-780cf754ccf2)

18. Entramos a la pestaña se **Security** y despues nos camos a **Security groups**

![image](https://github.com/user-attachments/assets/5f85701b-7a97-48b3-aa68-f8f7b8b5b9d3)

19. Adicionamos los puertos que se mencionaron previamente y al final de todo le damos al boton de **add rules**


![image](https://github.com/user-attachments/assets/db5ea4f5-4d56-41a4-bf93-d0a207dc18b2)
![image](https://github.com/user-attachments/assets/9ac7e98a-12f7-4faf-9851-13d18d4bd86a)
![image](https://github.com/user-attachments/assets/035b48e2-c0a3-41f2-b129-4f0d45c6cfd1)
![image](https://github.com/user-attachments/assets/bcc7468b-5cef-4017-bfd2-0bef33175dad)

20. Ahora vamos a iniciar las aplicaciones de Hue y Jupyter

Primerio Hue, **muy importante el usuario debe ser Hadoop**, la contraseña ya puede ser la que uno quiera

![image](https://github.com/user-attachments/assets/ddbab723-d94c-4a88-ac12-3533f48e05df)
![image](https://github.com/user-attachments/assets/0b5f1615-513a-468b-82a4-a7115d89a771)

Y ahora hacemos lo mismo en Jupyter, donde el usuario en jovyan y la contraseña es Jupyter

![image](https://github.com/user-attachments/assets/1684950c-70f1-44c9-852e-68168c47d62f)

21. Creamos un nuevo cuarderno, donde va a correr PySpark

![image](https://github.com/user-attachments/assets/35989374-624a-43cc-82d9-9d539257ae02)

Y corremos el siguiente codigo 

![image](https://github.com/user-attachments/assets/1917ab1f-32e7-49ab-8741-806935743096)
























