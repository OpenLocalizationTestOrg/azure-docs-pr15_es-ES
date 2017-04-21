<properties
   pageTitle="Esquemas definidos por el usuario en el almacén de datos de SQL | Microsoft Azure"
   description="Sugerencias para usar los esquemas de Transact-SQL en el almacén de datos de SQL Azure para desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="user-defined-schemas-in-sql-data-warehouse"></a>Esquemas definidos por el usuario en el almacén de datos de SQL

Datawarehouses tradicional suelen usar bases de datos independientes para crear los límites de la aplicación en función de la carga de trabajo, dominio o seguridad. Por ejemplo, un almacén de datos de SQL Server tradicional puede incluir una base de datos provisional, una base de datos del almacén de datos y algunas bases de datos de puesto de datos. En esta topología de cada base de datos funciona como una carga de trabajo y límite de seguridad en la arquitectura.

Por el contrario, el almacén de datos SQL ejecuta la carga de trabajo del almacén de datos completa dentro de una base de datos. Entre la base de datos no se permiten combinaciones. Por lo tanto, el almacén de datos SQL espera todas las tablas que se usa el almacén para almacenar en la base de datos de una.

> [AZURE.NOTE] No admite las consultas de base de datos entre cualquier tipo de almacén de datos de SQL. Por lo tanto, implementaciones de almacén de datos que aprovechen este patrón deberá revisarse.

## <a name="recommendations"></a>Recomendaciones

Estos son recomendaciones para consolidar cargas de trabajo, seguridad, dominio y límites funcionales utilizando esquemas definidos por el usuario

1. Usar una base de datos de almacén de datos de SQL para ejecutar la carga de trabajo del almacén de datos completa
2. Consolidar su entorno de almacén de datos existente para usar una base de datos de almacén de datos SQL
3. Aproveche **esquemas definidos por el usuario** para proporcionar el límite previamente ha implementado con bases de datos.

Si no se han utilizado previamente esquemas definidos por el usuario tiene una pizarra. Simplemente use el nombre de la base de datos anterior como base para los esquemas definidos por el usuario en la base de datos de almacén de datos de SQL.

Si ya se han utilizado esquemas tiene varias opciones:

1. Quitar los nombres de esquema heredados y empezar desde cero
2. Conservar los nombres de esquema heredados por anteponga el nombre del esquema heredados al nombre de tabla
3. Conservar los nombres de esquema heredados implementando vistas sobre la tabla en un esquema adicional para volver a crear la estructura del esquema antiguo.

> [AZURE.NOTE] En la primera inspección opción 3 puede parecer la opción más atractiva. Sin embargo, el problema radica en los detalles. Vistas se leen solo en el almacén de datos de SQL. Cualquier modificación de datos o tabla tendría que se realicen en la tabla base. Opción 3 también presenta una capa de vistas en el sistema. Desea realizar esto algunas consideraciones adicionales si usa vistas en la arquitectura ya.


### <a name="examples"></a>Ejemplos:

Implementar los esquemas definidos por el usuario basados en los nombres de base de datos

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Conservar los nombres de esquema heredados por anteponga al nombre de tabla. Utilizar esquemas para el límite de carga de trabajo.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Conservar los nombres de esquema heredados con vistas

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [AZURE.NOTE] Cualquier cambio en la estrategia de esquema necesita una revisión del modelo de seguridad de la base de datos. En muchos casos, es posible que pueda simplificar el modelo de seguridad al asignar permisos en el nivel de esquema. Si se necesitan permisos más granulares puede usar funciones de base de datos.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más sugerencias de desarrollo, vea [Introducción al desarrollo][].

<!--Image references-->

<!--Article references-->
[Introducción al desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
