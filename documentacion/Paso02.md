# Documentación del Esquema y Configuración de la Base de Datos integrador

<p><img src="../img/documentacionp2.png" alt="documentacionp2" height="125%" width="125%" /></p>

### Introducción

Este documento proporciona una descripción detallada de los scripts SQL utilizados para crear y gestionar una base de datos externa llamada integrador. El esquema de la base de datos 
incluye varias tablas, cada una diseñada para almacenar diferentes tipos de datos relevantes para las operaciones de un sistema de negocios integrado. Las tablas se almacenan como tablas 
externas en Apache Hive, utilizando OpenCSVSerde para manejar datos en formato CSV. Esta documentación detallará el propósito de cada tabla, el esquema y las configuraciones específicas
utilizadas.

### Inicialización de la Base de Datos
- **Paso 1:** Crear y Seleccionar la Base de Datos

```
CREATE DATABASE IF NOT EXISTS integrador;
USE integrador;

```
Estos comandos aseguran que la base de datos llamada integrador exista y la establecen como la base de datos actual para las operaciones posteriores.

- **Paso 2:** Eliminar Tablas Existentes
Antes de crear nuevas tablas, se eliminan las tablas existentes con los mismos nombres para evitar conflictos:

```
DROP TABLE IF EXISTS compra;
DROP TABLE IF EXISTS gasto;
DROP TABLE IF EXISTS tipo_gasto;
DROP TABLE IF EXISTS venta;
DROP TABLE IF EXISTS canal_venta;
DROP TABLE IF EXISTS cliente;
DROP TABLE IF EXISTS producto;
DROP TABLE IF EXISTS empleado;
DROP TABLE IF EXISTS sucursal;
DROP TABLE IF EXISTS calendario;
DROP TABLE IF EXISTS proveedor;

```

##  Esquemas de las Tablas
### Tabla compra
Propósito: Almacena información sobre las compras realizadas por la empresa.

Esquema:

- **IdCompra:** INTEGER, ID de la compra
- **Fecha:** DATE, Fecha de la compra
- **IdProducto:** INTEGER, ID del producto
- **Cantidad:** INTEGER, Cantidad comprada
- **Precio:** FLOAT, Precio de compra
- **IdProveedor:** INTEGER, ID del proveedor
Configuración:

```
CREATE EXTERNAL TABLE IF NOT EXISTS compra (
  IdCompra INTEGER,
  Fecha DATE,
  IdProducto INTEGER,
  Cantidad INTEGER,
  Precio FLOAT,
  IdProveedor INTEGER
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
    'separatorChar'=',',
    'skip.header.line.count'='1'
)
LOCATION '/data/compra';

```


### Tabla gasto
Propósito: Registra varios gastos incurridos por las diferentes sucursales de la empresa.

Esquema:

- IdGasto: INTEGER, ID del gasto
- IdSucursal: INTEGER, ID de la sucursal
- IdTipoGasto: INTEGER, ID del tipo de gasto
- Fecha: DATE, Fecha del gasto
- Monto: FLOAT, Monto del gasto
Configuración:

```
CREATE EXTERNAL TABLE IF NOT EXISTS gasto (
  IdGasto INTEGER,
  IdSucursal INTEGER,
  IdTipoGasto INTEGER,
  Fecha DATE,
  Monto FLOAT
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
    'separatorChar'=',',
    'skip.header.line.count'='1'
)
LOCATION '/data/gasto';
```
### Tabla tipo_gasto
Propósito: Almacena información sobre los diferentes tipos de gastos.

Esquema:

- IdTipoGasto: INTEGER, ID del tipo de gasto
- Descripcion: VARCHAR(50), Descripción del tipo de gasto
- Monto_Aproximado: FLOAT, Monto aproximado para el tipo de gasto
Configuración:

```
CREATE EXTERNAL TABLE IF NOT EXISTS tipo_gasto (
  IdTipoGasto INTEGER,
  Descripcion VARCHAR(50),
  Monto_Aproximado FLOAT
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
    'separatorChar'=',',
    'skip.header.line.count'='1'
)
LOCATION '/data/tipodegasto';
```
### Tabla venta
Propósito: Captura las transacciones de ventas, incluyendo detalles de entrega.

Esquema:

- IdVenta: INTEGER, ID de la venta
- Fecha: DATE, Fecha de la venta
- Fecha_Entrega: DATE, Fecha de entrega
- IdCanal: INTEGER, ID del canal de ventas
- IdCliente: INTEGER, ID del cliente
- IdSucursal: INTEGER, ID de la sucursal
- IdEmpleado: INTEGER, ID del empleado
- IdProducto: INTEGER, ID del producto
- Precio: FLOAT, Precio de venta
- Cantidad: INTEGER, Cantidad vendida
Configuración:

```
CREATE EXTERNAL TABLE IF NOT EXISTS venta (
  IdVenta INTEGER,
  Fecha DATE,
  Fecha_Entrega DATE,
  IdCanal INTEGER, 
  IdCliente INTEGER, 
  IdSucursal INTEGER,
  IdEmpleado INTEGER,
  IdProducto INTEGER,
  Precio FLOAT,
  Cantidad INTEGER
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
    'separatorChar'=',',
    'skip.header.line.count'='1'
)
LOCATION '/data/venta';
```
### Tabla canal_venta
Propósito: Detalla los diferentes canales de venta utilizados por la empresa.

Esquema:

- IdCanal: INTEGER, ID del canal de ventas
- Canal: VARCHAR(50), Descripción del canal de ventas
Configuración:

```
CREATE EXTERNAL TABLE IF NOT EXISTS canal_venta (
  IdCanal INTEGER,
  Canal VARCHAR(50)
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
    'separatorChar'=',',
    'skip.header.line.count'='1'
)
LOCATION '/data/canaldeventa';
```
### Tabla cliente
Propósito: Almacena información de los clientes.

Esquema:

- ID: INTEGER, ID del cliente
- Provincia: VARCHAR(50), Provincia
- Nombre_y_Apellido: VARCHAR(80), Nombre y apellido
- Domicilio: VARCHAR(150), Domicilio
- Telefono: VARCHAR(30), Teléfono
- Edad: VARCHAR(5), Edad
- Localidad: VARCHAR(80), Localidad
- X: VARCHAR(30), Coordenada X
- Y: VARCHAR(30), Coordenada Y
- col10: VARCHAR(1), Columna adicional
Configuración:

```
CREATE EXTERNAL TABLE IF NOT EXISTS cliente (
  ID INTEGER,
  Provincia VARCHAR(50),
  Nombre_y_Apellido VARCHAR(80),
  Domicilio VARCHAR(150),
  Telefono VARCHAR(30),
  Edad VARCHAR(5),
  Localidad VARCHAR(80),
  X VARCHAR(30),
  Y VARCHAR(30),
  col10 VARCHAR(1)
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
    'separatorChar'=';',
    'skip.header.line.count'='1'
)
LOCATION '/data/cliente';
```
### Tabla producto
Propósito: Contiene detalles de los productos.

Esquema:

- IdProducto: INTEGER, ID del producto
- Concepto: VARCHAR(100), Concepto del producto
- Tipo: VARCHAR(50), Tipo de producto
- Precio: FLOAT, Precio del producto
Configuración:

```
CREATE EXTERNAL TABLE IF NOT EXISTS producto (
  IdProducto INTEGER,
  Concepto VARCHAR(100),
  Tipo VARCHAR(50),
  Precio FLOAT
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
    'separatorChar'=';',
    'skip.header.line.count'='1'
)
LOCATION '/data/producto';
```
### Tabla empleado
Propósito: Almacena información de los empleados.

Esquema:

- ID_empleado: INTEGER, ID del empleado
- Apellido: VARCHAR(50), Apellido
- Nombre: VARCHAR(80), Nombre
- Sucursal: VARCHAR(150), Sucursal
- Sector: VARCHAR(30), Sector
- Cargo: VARCHAR(30), Cargo
- Salario: FLOAT, Salario
- Configuración:

```
CREATE EXTERNAL TABLE IF NOT EXISTS empleado (
  ID_empleado INTEGER,
  Apellido VARCHAR(50),
  Nombre VARCHAR(80),
  Sucursal VARCHAR(150),
  Sector VARCHAR(30),
  Cargo VARCHAR(30),
  Salario FLOAT
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
    'separatorChar'=';',
    'skip.header.line.count'='1'
)
LOCATION '/data/empleado';
```
### Tabla sucursal
Propósito: Contiene información sobre las sucursales de la empresa.

Esquema:

- IdSucursal: INTEGER, ID de la sucursal
- Sucursal: VARCHAR(40), Nombre de la sucursal
- Domicilio: VARCHAR(150), Domicilio
- Localidad: VARCHAR(80), Localidad
- Provincia: VARCHAR(50), Provincia
- Latitud: FLOAT, Latitud
- Longitud: FLOAT, Longitud
Configuración:

```
CREATE EXTERNAL TABLE IF NOT EXISTS sucursal (
  IdSucursal INTEGER,
  Sucursal VARCHAR(40),
  Domicilio VARCHAR(150),
  Localidad VARCHAR(80),
  Provincia VARCHAR(50),
  Latitud FLOAT,
  Longitud FLOAT
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
    'separatorChar'=';',
    'skip.header.line.count'='1'
)
LOCATION '/data/sucursal';
```
### Tabla calendario
Propósito: Proporciona una estructura de calendario para análisis relacionados con fechas.

Esquema:

- id: INTEGER, ID del calendario
- fecha: DATE, Fecha
- anio: INTEGER, Año
- mes: INTEGER, Mes
- dia: INTEGER, Día
- trimestre: INTEGER, Trimestre (1 a 4)
- semana: INTEGER, Semana (1 a 52/53)
- dia_nombre: VARCHAR(9), Nombre del día ('Lunes', 'Martes', etc.)
- mes_nombre: VARCHAR(9), Nombre del mes ('Enero', 'Febrero', etc.)
- Configuración:

```
CREATE EXTERNAL TABLE calendario (
  id INTEGER,
  fecha DATE,
  anio INTEGER,
  mes INTEGER,
  dia INTEGER,
  trimestre INTEGER,
  semana INTEGER,
  dia_nombre VARCHAR(9),
  mes_nombre VARCHAR(9)
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
    'separatorChar'=',',
    'skip.header.line.count'='1'
)
LOCATION '/data/calendario';
```
### Tabla proveedor
Propósito: Almacena información sobre los proveedores.

Esquema:

- IdProveedor: INTEGER, ID del proveedor
- Nombre: VARCHAR(40), Nombre del proveedor
- Direccion: VARCHAR(150), Dirección
- Ciudad: VARCHAR(80), Ciudad
- Provincia: VARCHAR(50), Provincia
- Pais: VARCHAR(20), País
- Departamento: VARCHAR(50), Departamento
- Configuración:

```
CREATE EXTERNAL TABLE IF NOT EXISTS proveedor (
  IdProveedor INTEGER,
  Nombre VARCHAR(40),
  Direccion VARCHAR(150),
  Ciudad VARCHAR(80),
  Provincia VARCHAR(50),
  Pais VARCHAR(20),
  Departamento VARCHAR(50)
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
    'separatorChar'=',',
    'skip.header.line.count'='1'
)
LOCATION '/data/proveedor';
```
Esta documentación detalla el propósito y la configuración de cada tabla dentro de la base de datos integrador, proporcionando una guía completa para la creación y
gestión de las tablas en Apache Hive.








