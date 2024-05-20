# Documentacion Guía para Crear y Desplegar un Clúster de Hadoop con Docker Compose

<p><img src="../img/documentacionp2.png" alt="documentacionp2" height="125%" width="125%" /></p>

Esta guía te ayudará a crear un archivo docker-compose-v1.yml para desplegar un clúster de Hadoop usando Docker Compose. Además, se incluye una explicación detallada de cada componente y cómo funciona.

### Prerrequisitos
Antes de comenzar, asegúrate de tener instalados los siguientes programas en tu sistema:

- Docker
- Docker Compose

### Paso 1: Crear el Archivo docker-compose-v1.yml
Crea un archivo llamado docker-compose-v1.yml en tu directorio de trabajo. El contenido del archivo debe ser el siguiente:

```
version: "3"

services:
  namenode:
    image: bde2020/hadoop-namenode:2.0.0-hadoop3.2.1-java8
    container_name: namenode
    restart: always
    ports:
      - 9870:9870
      - 9010:9000
    volumes:
      - hadoop_namenode:/hadoop/dfs/name
      - hadoop_datasets:/home
    environment:
      - CLUSTER_NAME=test
      - CORE_CONF_fs_defaultFS=hdfs://namenode:9000
    env_file:
      - ./hadoop.env

  datanode:
    image: bde2020/hadoop-datanode:2.0.0-hadoop3.2.1-java8
    container_name: datanode
    restart: always
    volumes:
      - hadoop_datanode:/hadoop/dfs/data
    environment:
      - SERVICE_PRECONDITION: "namenode:9870"
      - CORE_CONF_fs_defaultFS: hdfs://namenode:9000
    ports:
      - "9864:9864"
    env_file:
      - ./hadoop.env

  resourcemanager:
    image: bde2020/hadoop-resourcemanager:2.0.0-hadoop3.2.1-java8
    container_name: resourcemanager
    restart: always
    environment:
      - SERVICE_PRECONDITION: "namenode:9000 namenode:9870 datanode:9864"
    env_file:
      - ./hadoop.env

  nodemanager1:
    image: bde2020/hadoop-nodemanager:2.0.0-hadoop3.2.1-java8
    container_name: nodemanager
    restart: always
    environment:
      - SERVICE_PRECONDITION: "namenode:9000 namenode:9870 datanode:9864 resourcemanager:8088"
    env_file:
      - ./hadoop.env

  historyserver:
    image: bde2020/hadoop-historyserver:2.0.0-hadoop3.2.1-java8
    container_name: historyserver
    restart: always
    environment:
      - SERVICE_PRECONDITION: "namenode:9000 namenode:9870 datanode:9864 resourcemanager:8088"
    volumes:
      - hadoop_historyserver:/hadoop/yarn/timeline
    env_file:
      - ./hadoop.env
     
volumes:
  hadoop_namenode:
  hadoop_datasets:
  hadoop_datanode:
  hadoop_historyserver:

```

## Explicación del Archivo docker-compose-v1.yml

version: "3"

Este campo especifica la versión de Docker Compose que se está utilizando. La versión 3 es adecuada para la mayoría de los casos de uso.

services

Esta sección define todos los servicios (contenedores) que se van a crear. En este caso, estamos definiendo cinco servicios: **namenode**, **datanode**, **resourcemanager**, **nodemanager1**, y **historyserver**.

Servicio namenode
```
namenode:
  image: bde2020/hadoop-namenode:2.0.0-hadoop3.2.1-java8
  container_name: namenode
  restart: always
  ports:
    - 9870:9870
    - 9010:9000
  volumes:
    - hadoop_namenode:/hadoop/dfs/name
    - hadoop_datasets:/home
  environment:
    - CLUSTER_NAME=test
    - CORE_CONF_fs_defaultFS=hdfs://namenode:9000
  env_file:
    - ./hadoop.env

```
- image: Especifica la imagen de Docker a utilizar para el contenedor. Aquí estamos usando una imagen de Hadoop NameNode.
- container_name: Asigna un nombre al contenedor. En este caso, namenode.
- restart: Define la política de reinicio. always significa que el contenedor se reiniciará siempre que se detenga.
- ports: Mapea los puertos del contenedor a los puertos del host. 9870:9870 significa que el puerto 9870 del host se conecta al puerto 9870 del contenedor.
- volumes: Monta volúmenes en el contenedor. hadoop_namenode es el volumen para los datos del NameNode.
- environment: Define variables de entorno específicas para este servicio.
- env_file: Especifica un archivo de entorno adicional (hadoop.env) que contiene más variables de entorno.

Esto se repite para los 5 servicios con cambios ligeros cambios

volumes
```
volumes:
  hadoop_namenode:
  hadoop_datasets:
  hadoop_datanode:
  hadoop_historyserver:

```
- hadoop_namenode: Volumen para almacenar datos del NameNode.
- hadoop_datasets: Volumen para almacenar datasets.
- hadoop_datanode: Volumen para almacenar datos del DataNode.
- hadoop_historyserver: Volumen para almacenar datos del HistoryServer.


### Paso 2: Crear el Archivo de Entorno hadoop.env
Crea un archivo llamado hadoop.env en el mismo directorio que el archivo docker-compose-v1.yml. Define las variables de entorno necesarias como se muestra a continuación:
```
CORE_CONF_fs_defaultFS=hdfs://namenode:9000
CORE_CONF_hadoop_http_staticuser_user=root
CORE_CONF_hadoop_proxyuser_hue_hosts=*
CORE_CONF_hadoop_proxyuser_hue_groups=*
CORE_CONF_io_compression_codecs=org.apache.hadoop.io.compress.SnappyCodec

HDFS_CONF_dfs_webhdfs_enabled=true
HDFS_CONF_dfs_permissions_enabled=false
HDFS_CONF_dfs_namenode_datanode_registration_ip___hostname___check=false

YARN_CONF_yarn_log___aggregation___enable=true
YARN_CONF_yarn_log_server_url=http://historyserver:8188/applicationhistory/logs/
YARN_CONF_yarn_resourcemanager_recovery_enabled=true
YARN_CONF_yarn_resourcemanager_store_class=org.apache.hadoop.yarn.server.resourcemanager.recovery.FileSystemRMStateStore
YARN_CONF_yarn_resourcemanager_scheduler_class=org.apache.hadoop.yarn.server.resourcemanager.scheduler.capacity.CapacityScheduler
YARN_CONF_yarn_scheduler_capacity_root_default_maximum___allocation___mb=8192
YARN_CONF_yarn_scheduler_capacity_root_default_maximum___allocation___vcores=4
YARN_CONF_yarn_resourcemanager_fs_state___store_uri=/rmstate
YARN_CONF_yarn_resourcemanager_system___metrics___publisher_enabled=true
YARN_CONF_yarn_resourcemanager_hostname=resourcemanager
YARN_CONF_yarn_resourcemanager_address=resourcemanager:8032
YARN_CONF_yarn_resourcemanager_scheduler_address=resourcemanager:8030
YARN_CONF_yarn_resourcemanager_resource__tracker_address=resourcemanager:8031
YARN_CONF_yarn_timeline___service_enabled=true
YARN_CONF_yarn_timeline___service_generic___application___history_enabled=true
YARN_CONF_yarn_timeline___service_hostname=historyserver
YARN_CONF_mapreduce_map_output_compress=true
YARN_CONF_mapred_map_output_compress_codec=org.apache.hadoop.io.compress.SnappyCodec
YARN_CONF_yarn_nodemanager_resource_memory___mb=16384
YARN_CONF_yarn_nodemanager_resource_cpu___vcores=8
YARN_CONF_yarn_nodemanager_disk___health___checker_max___disk___utilization___per___disk___percentage=98.5
YARN_CONF_yarn_nodemanager_remote___app___log___dir=/app-logs
YARN_CONF_yarn_nodemanager_aux___services=mapreduce_shuffle

MAPRED_CONF_mapreduce_framework_name=yarn
MAPRED_CONF_mapred_child_java_opts=-Xmx4096m
MAPRED_CONF_mapreduce_map_memory_mb=4096
MAPRED_CONF_mapreduce_reduce_memory_mb=8192
MAPRED_CONF_mapreduce_map_java_opts=-Xmx3072m
MAPRED_CONF_mapreduce_reduce_java_opts=-Xmx6144m
MAPRED_CONF_yarn_app_mapreduce_am_env=HADOOP_MAPRED_HOME=/opt/hadoop-3.2.1/
MAPRED_CONF_mapreduce_map_env=HADOOP_MAPRED_HOME=/opt/hadoop-3.2.1/
MAPRED_CONF_mapreduce_reduce_env=HADOOP_MAPRED_HOME=/opt/hadoop-3.2.1/

```
### Explicación del Archivo hadoop.env
Este archivo contiene variables de entorno que configuran diversos aspectos de Hadoop. Aquí hay algunas de las variables más importantes:

- CORE_CONF_fs_defaultFS: Define el sistema de archivos predeterminado de Hadoop.
- HDFS_CONF_dfs_webhdfs_enabled: Habilita WebHDFS, una extensión de HDFS.
- YARN_CONF_yarn_resourcemanager_hostname: Define el nombre del host del ResourceManager.
- MAPRED_CONF_mapreduce_framework_name: Especifica que el marco de trabajo para MapReduce es YARN.

### Paso 3: Desplegar el Clúster de Hadoop
Ejecuta el siguiente comando en tu terminal desde el directorio donde se encuentran los archivos docker-compose-v1.yml y hadoop.env:

```
docker-compose -f docker-compose-v1.yml up -d
```

Este comando descargará las imágenes necesarias y creará los contenedores Docker definidos en el archivo docker-compose-v1.yml.
Explicación de los Servicios y Volúmenes

## Servicios
### NameNode
- Imagen: bde2020/hadoop-namenode:2.0.0-hadoop3.2.1-java8
- Descripción: Administra el sistema de archivos HDFS.
- Puertos:
9870:9870: Interfaz web del NameNode.
9010:9000: Servicio HDFS.
- Volúmenes:
hadoop_namenode: Persistencia de metadatos del NameNode.
hadoop_datasets: Almacenamiento de datasets

### DataNode
- Imagen: bde2020/hadoop-datanode:2.0.0-hadoop3.2.1-java8
- Descripción: Almacena los datos reales en HDFS.
- Puertos:
9864:9864: Interfaz web del DataNode.
- Volúmenes:
hadoop_datanode: Persistencia de datos del DataNode.

### ResourceManager
- Imagen: bde2020/hadoop-resourcemanager:2.0.0-hadoop3.2.1-java8
- Descripción: Administra los recursos del clúster y coordina la ejecución de aplicaciones.

### NodeManager
- Imagen: bde2020/hadoop-nodemanager:2.0.0-hadoop3.2.1-java8
- Descripción: Gestiona la ejecución de contenedores individuales en cada nodo.

### HistoryServer
- Imagen: bde2020/hadoop-historyserver:2.0.0-hadoop3.2.1-java8
- Descripción: Proporciona información sobre los trabajos completados.
- Volúmenes:
hadoop_historyserver: Persistencia de datos del HistoryServer.

### Acceso a las Interfaces Web
NameNode Web UI: http://localhost:9870
DataNode Web UI: http://localhost:9864

### Conclusión
Este archivo docker-compose-v1.yml facilita el despliegue de un clúster de Hadoop utilizando Docker Compose. Podrás configurar y ejecutar tu propio clúster de Hadoop de manera eficiente.
