<h1>Herramientas Big Data</h1>

<p align="center">
    <img src="./img/foto.png" alt="imagen"  />
</p>

<h2>Introducción</h2>

Las herramientas Big Data son software diseñado para almacenar, procesar y analizar grandes volúmenes de datos de manera eficiente. Hadoop es un sistema de almacenamiento distribuido que permite procesar datos de forma paralela. Spark es un framework de procesamiento de datos en memoria que acelera el análisis de datos. Hive es una herramienta que facilita el análisis de datos estructurados mediante consultas estilo SQL sobre datos almacenados en Hadoop. Juntas, estas herramientas forman un ecosistema poderoso para gestionar y analizar grandes cantidades de información.

<p align="center"><img src="./img/foto1.png" alt="imagen1"  /></p>

<h2> Nota importante </h2>

En este proyecto se llevó a cabo en un entorno de desarrollo Windows. Se utilizó una máquina virtual alojada en VirtualBox con Ubuntu Server, debido a que Docker no es compatible directamente con Windows. En la máquina virtual se instaló Docker para la gestión de contenedores. Para establecer la conexión entre la máquina virtual y el sistema operativo original de Windows, se empleó PuTTY. Además, para simplificar la transferencia y gestión de archivos en Ubuntu, también se puede optar por utilizar WinSCP.Los links se proveen a continuacion

<p align="center"><img src="./img/foto2.png" alt="imagen2"  /></p>

<h2> Practica Integradora </h2>

Durante esta practica la idea es emular un ambiente de trabajo, desde un área de innovación solicitan construir un MVP(Producto viable mínimo) de un ambiente de Big Data donde se deban cargar unos archivos CSV que anteriormente se utilizaban en un datawarehouse en MySQl, pero ahora en un entorno de Hadoop.
Desde la gerencia de Infraestructura no están muy convencidos de utilizar esta tecnología por lo que no se asigno presupuesto alguna para esta iniciativa, de forma tal que por el momento no es posible utilizar un Vendor(Azure, AWS, Google) para implementar dicho entorno, es por esto que todo el MVP se deberá implementar utilizando Docker de forma tal que se pueda hacer una demo al sector de infraestructura mostrando las ventajas de utilizar tecnologías de Big Data.

<h2> Entorno Docker con Hadoop, Spark y Hive </h2>

<p>Se presenta un entorno Docker con Hadoop (HDFS) y la implementación de:</p>
<ul>
    <li>Spark</li>
    <li>Hive</li>
    <li>HBase</li>
    <li>MongoDB</li>
    <li>Neo4J</li>
    <li>Zeppelin</li>
    <li>Kafka</li>
</ul>

Es importante mencionar que el entorno completo consume muchos recursos de su equipo, motivo por el cuál, se propondrán ejercicios pero con ambientes reducidos, en función de las herramientas utilizadas.
Ejecute docker network inspect en la red (por ejemplo, docker-hadoop-spark-hive_default) para encontrar la IP en la que se publican las interfaces de hadoop. Acceda a estas interfaces con las siguientes URL:
```
Namenode: http://<IP_Anfitrion>:9870/dfshealth.html#tab-overview
Datanode: http://<IP_Anfitrion>:9864/
Spark master: http://<IP_Anfitrion>:8080/
Spark worker: http://<IP_Anfitrion>:8081/	
HBase Master-Status: http://<IP_Anfitrion>:16010
HBase Zookeeper_Dump: http://<IP_Anfitrion>:16010/zk.jsp
HBase Region_Server: http://<IP_Anfitrion>:16030
Zeppelin: http://<IP_Anfitrion>:8888
Neo4j: http://<IP_Anfitrion>:7474
```
Para implementar ejecute las siguientes lineas:

```
git clone https://github.com/lopezdar222/herramientas_big_data
```
Primero clonamos el repositorio que contiene lo nesesario para trabajar
```
cd herramientas_big_data
```
ingresamos en el directorio 
```
sudo docker-compose -f docker-compose-v1.yml up -d
```
levantaremos en el contenedor correspondiente
<h3> Nota </h3> 
en el codigo sudo docker-compose -f docker-compose-v1.yml up -d el contenedor tendra que ser ejecutado la version corepodiente al punto que se espesifica en cada punto en caso de tener que usar otro el anterior tiene que ser detenido y el comando vuelto a ser ejecurtar con la version acorde 

<h2> 1) HDFS </h2>

Utilizar el entorno docker-compose-v1.yml

Copiar los archivos ubicados en la carpeta Datasets, dentro del contenedor "namenode"
```
sudo docker exec -it namenode bash
```
iniciarmos el contenedor
```
cd home
mkdir Datasets
```
nos moveremos a home y crearemos el directoio Datasets
```
cd Datasets 
mkdir cliente venta tipodegasto sucursal proveedor producto compra empleado canaldeventa gasto calendario data_nvo
exit
```
Nos debemos posicionar en "Datasets" y crearemos multiples carpetas y luego salimos

a continuacion utilizaremos un shell script llamado Paso00.sh que se encuentra en la carpeta herramientas_big_data clonada previamente

```
chmod u+x Paso00.sh
```
le damos premiso de ejecucion

```
./Paso00.sh
```
ejecutamos




