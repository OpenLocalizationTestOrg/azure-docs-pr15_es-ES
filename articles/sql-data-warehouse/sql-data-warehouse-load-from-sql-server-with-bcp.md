<properties
   pageTitle="Cargar datos de SQL Server en el almacén de datos de SQL Azure (bcp) | Microsoft Azure"
   description="Tamaño de datos pequeños, usa bcp para exportar datos de SQL Server a archivos planos e importar los datos directamente en el almacén de datos de SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-flat-files"></a>Cargar datos de SQL Server en el almacén de datos de SQL Azure (archivos planos)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)

Conjuntos de datos pequeños, puede usar la herramienta de línea de comandos bcp para exportar los datos de SQL Server y, a continuación, se carga directamente al almacén de datos de SQL Azure.

En este tutorial, usará bcp:

- Exportar una tabla a partir de SQL Server mediante el comando bcp (o crear un archivo de ejemplo sencillo)
- Importar la tabla de un archivo plano al almacén de datos de SQL.
- Crear estadísticas en los datos cargados.

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## <a name="before-you-begin"></a>Antes de empezar

### <a name="prerequisites"></a>Requisitos previos

Paso a través de este tutorial, necesita:

- Una base de datos de almacén de datos SQL
- La herramienta de línea de comandos bcp instalada
- La utilidad de línea de comandos sqlcmd instalada

Puede descargar las herramientas bcp y sqlcmd desde el [Centro de descarga de Microsoft][].

### <a name="data-in-ascii-or-utf-16-format"></a>Datos en formato ASCII o UTF-16

Si está tratando de este tutorial con sus propios datos, los datos se deben usar la codificación de ASCII o UTF-16 ya bcp no admite UTF-8. 

PolyBase es compatible con UTF-8 pero aún no es compatible con UTF-16. Tenga en cuenta que si desea combinar bcp con PolyBase debe transformar los datos a UTF-8 después de que se exportó desde SQL Server. 


## <a name="1-create-a-destination-table"></a>1. crear una tabla de destino

Definir una tabla en el almacén de datos de SQL que será la tabla de destino para la carga. Las columnas de la tabla deben corresponden a los datos de cada fila de su archivo de datos.

Para crear una tabla, abra un símbolo del sistema y usar sqlcmd.exe para ejecutar el comando siguiente:


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


## <a name="2-create-a-source-data-file"></a>2. crear un archivo de datos de origen

Abra el Bloc de notas y copie las siguientes líneas de datos en un nuevo archivo de texto y, a continuación, guardar este archivo en el directorio temporal local, C:\Temp\DimDate2.txt. Estos datos están en formato ASCII.

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

(Opcional) Para exportar sus propios datos desde una base de datos de SQL Server, abra un símbolo del sistema y ejecute el siguiente comando. Reemplace el nombre de tabla, nombre_de_servidor, DatabaseName, nombre de usuario y contraseña con su propia información.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```



## <a name="3-load-the-data"></a>3. cargar los datos
Para cargar los datos, abra un símbolo del sistema y ejecute el comando siguiente, reemplazando los valores para el nombre de servidor, nombre de la base de datos, nombre de usuario y contraseña con su propia información.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

Utilice este comando para comprobar que los datos se cargan correctamente

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Los resultados deben tener este aspecto:

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

## <a name="4-create-statistics"></a>4. crear estadísticas

Almacén de datos SQL hace aún no crear automáticamente o actualización automática de estadísticas de soporte técnico. Para obtener el mejor rendimiento de la consulta, es importante crear estadísticas de todas las columnas de todas las tablas después de la primera carga o después de que se producen cambios importantes en los datos. Para obtener una explicación detallada de las estadísticas, consulte [estadísticas][]. 

Ejecute el comando siguiente para crear estadísticas en la tabla recién cargada.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="5-export-data-from-sql-data-warehouse"></a>5. exportar los datos de almacén de datos SQL
Por diversión, puede exportar los datos que acabo de cargar del almacén de datos de SQL.  El comando Exportar es exactamente lo mismo que exportar desde SQL Server.

Sin embargo, hay una diferencia en los resultados. Puesto que los datos se almacenan en ubicaciones distribuidas en el almacén de datos de SQL, al exportar datos cada nodo de cálculo escribe datos en el archivo de salida. El orden de los datos en el archivo de salida es probable que sea distinto el orden de los datos en el archivo de entrada.

### <a name="export-a-table-and-compare-exported-results"></a>Exportar una tabla y comparar los resultados exportados

Para ver los datos exportados, abra un símbolo del sistema y ejecute este comando con sus propios parámetros. Nombre de servidor es el nombre de su servidor de SQL Azure lógico.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Puede comprobar que los datos se exportan correctamente al abrir el archivo nuevo. Los datos en el archivo deben coincidir con el texto siguiente, pero es probable que se va a ordenar en un orden diferente:

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

### <a name="export-the-results-of-a-query"></a>Exportar los resultados de una consulta

Puede usar la función **queryout** de bcp para exportar los resultados de una consulta en lugar de exportar la tabla completa. 

## <a name="next-steps"></a>Pasos siguientes
Para obtener información general de la carga, vea [cargar datos en el almacén de datos de SQL][].
Para obtener más sugerencias de desarrollo, vea [información general sobre el desarrollo de almacén de datos de SQL][].
Para obtener más información sobre cómo crear una tabla en el almacén de datos de SQL, vea [Información general de la tabla][] o la [sintaxis de CREATE TABLE][] .

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
