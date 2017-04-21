<properties
   pageTitle="Cargar datos de SQL Server en el almacén de datos de SQL Azure (PolyBase) | Microsoft Azure"
   description="Utiliza bcp para exportar datos de SQL Server a archivos sin formato, AZCopy para importar datos con el almacenamiento de blobs de Windows Azure y PolyBase para recopilar los datos en el almacén de datos de SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-azcopy"></a>Cargar datos de SQL Server en el almacén de datos de SQL Azure (AZCopy)

Usar bcp y utilidades de línea de comandos AZCopy para cargar los datos de SQL Server con el almacenamiento de blobs de Windows Azure. A continuación, use PolyBase o generador de datos de Azure para cargar los datos en el almacén de datos de SQL Azure. 


## <a name="prerequisites"></a>Requisitos previos

Paso a través de este tutorial, necesita:

- Una base de datos de almacén de datos SQL
- La herramienta de línea de comandos bcp instalada
- La utilidad de línea de comandos SQLCMD instalada

>[AZURE.NOTE] Puede descargar las utilidades bcp y sqlcmd desde el [Centro de descarga de Microsoft][].

## <a name="import-data-into-sql-data-warehouse"></a>Importar datos en el almacén de datos SQL

En este tutorial, se crea una tabla en el almacén de datos de SQL Azure e importar datos en la tabla.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Paso 1: Crear una tabla en el almacén de datos de SQL Azure

Desde el símbolo del sistema, use sqlcmd para ejecutar la consulta siguiente para crear una tabla en la instancia:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

>[AZURE.NOTE] Para obtener más información sobre cómo crear una tabla en el almacén de datos de SQL y las opciones disponibles en la cláusula WITH, vea [Información general de la tabla][] o la [sintaxis de CREATE TABLE][] .

### <a name="step-2-create-a-source-data-file"></a>Paso 2: Crear un archivo de datos de origen

Abra el Bloc de notas y copie las siguientes líneas de datos en un nuevo archivo de texto y, a continuación, guardar este archivo en el directorio temporal local, C:\Temp\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [AZURE.NOTE] Es importante recordar que bcp.exe no admite la codificación UTF-8 del archivo. Usar archivos ASCII o UTF-16 codificados cuando se usen bcp.exe.

### <a name="step-3-connect-and-import-the-data"></a>Paso 3: Conectar e importar los datos
Utilizar bcp, puede conectar e importar los datos mediante el comando siguiente reemplazando los valores según corresponda:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Puede comprobar que los datos se cargan, ejecute la consulta siguiente con sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Esto debe devolver los resultados siguientes:

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Paso 4: Crear estadísticas en los datos recién cargados

Almacén de datos de SQL Azure hace aún no automática de soporte técnico, crear o actualizará las estadísticas automáticamente. Para obtener el mejor rendimiento de las consultas, es importante que estadísticas crearse en todas las columnas de todas las tablas después de la primera carga o se producen cambios importantes en los datos. Para obtener una explicación detallada de las estadísticas, vea el tema de [estadísticas][] en el grupo de desarrollo de temas. A continuación se muestra un ejemplo de cómo crear estadísticas en los cuadros y cargado en este ejemplo rápido

Ejecute las siguientes instrucciones de crear estadísticas desde un símbolo del sistema de sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Exportar datos de almacén de datos SQL
En este tutorial, creará un archivo de datos de una tabla en el almacén de datos de SQL. Se exportarán los datos que se creó anteriormente a un nuevo archivo de datos denominado DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Paso 1: Exportar los datos

Con la herramienta bcp, puede conectar y exportar datos con el comando siguiente reemplazando los valores según corresponda:

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Puede comprobar que los datos se exportan correctamente al abrir el archivo nuevo. Los datos en el archivo deben coincidir con el texto siguiente:

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

>[AZURE.NOTE] Debido a la naturaleza de los sistemas distribuidos, el orden de datos no puede ser el mismo en todas las bases de datos de almacén de datos de SQL. Otra opción es utilizar la función **queryout** de bcp para escribir un extracto de la consulta, en lugar de exportar la tabla completa.

## <a name="next-steps"></a>Pasos siguientes
Para obtener información general de la carga, vea [cargar datos en el almacén de datos de SQL][].
Para obtener más sugerencias de desarrollo, vea [información general sobre el desarrollo de almacén de datos de SQL][].

<!--Image references-->

<!--Article references-->

[Cargar datos en el almacén de datos SQL]: ./sql-data-warehouse-overview-load.md
[Información general sobre el desarrollo de almacén de datos SQL]: ./sql-data-warehouse-overview-develop.md
[Información general de la tabla]: ./sql-data-warehouse-tables-overview.md
[Estadísticas]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[Sintaxis de CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Centro de descarga de Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433
