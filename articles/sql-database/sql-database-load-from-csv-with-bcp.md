<properties
   pageTitle="Cargar datos desde un archivo CSV en Databaase de SQL Azure (bcp) | Microsoft Azure"
   description="Tamaño de datos pequeños, utiliza bcp para importar datos en la base de datos de SQL Azure."
   services="sql-database"
   documentationCenter="NA"
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/13/2016"
   ms.author="carlrab"/>


# <a name="load-data-from-csv-into-azure-sql-data-warehouse-flat-files"></a>Cargar datos de CSV en el almacén de datos de SQL Azure (archivos planos)

Puede usar la herramienta de línea de comandos bcp para importar datos desde un archivo CSV a la base de datos de SQL Azure.

## <a name="before-you-begin"></a>Antes de empezar

### <a name="prerequisites"></a>Requisitos previos

Paso a través de este tutorial, necesita:

- Un servidor de base de datos de SQL Azure lógico y la base de datos
- La herramienta de línea de comandos bcp instalada
- La utilidad de línea de comandos sqlcmd instalada

Puede descargar las herramientas bcp y sqlcmd desde el [Centro de descarga de Microsoft][].

### <a name="data-in-ascii-or-utf-16-format"></a>Datos en formato ASCII o UTF-16

Si está tratando de este tutorial con sus propios datos, los datos se deben usar la codificación de ASCII o UTF-16 ya bcp no admite UTF-8. 

## <a name="1-create-a-destination-table"></a>1. crear una tabla de destino

Defina una tabla de base de datos SQL como la tabla de destino. Las columnas de la tabla deben corresponden a los datos de cada fila de su archivo de datos.

Para crear una tabla, abra un símbolo del sistema y usar sqlcmd.exe para ejecutar el comando siguiente:


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
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


## <a name="next-steps"></a>Pasos siguientes

Para migrar una base de datos de SQL Server, consulte [migración de base de datos de SQL Server](sql-database-cloud-migrate.md).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Centro de descarga de Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433
