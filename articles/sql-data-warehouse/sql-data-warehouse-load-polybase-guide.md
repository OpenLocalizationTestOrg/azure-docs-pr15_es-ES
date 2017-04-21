<properties
   pageTitle="Guía para el uso de PolyBase en el almacén de datos de SQL | Microsoft Azure"
   description="Directrices y recomendaciones para utilizar PolyBase en escenarios de almacén de datos de SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>Guía para el uso de PolyBase en el almacén de datos de SQL

Esta guía le ofrece información práctica para usar PolyBase en el almacén de datos de SQL.

Para obtener más información, consulte el tutorial de [cargar datos con PolyBase][] .


## <a name="rotating-storage-keys"></a>Rotación de claves de almacenamiento

De vez en cuando desee cambiar la tecla de acceso para el almacenamiento de blobs por motivos de seguridad.

La manera más elegante para realizar esta tarea es siga un proceso conocido como "girar las teclas". Es podrán que haya observado tiene dos claves de almacenamiento para su cuenta de almacenamiento de blobs. Se trata de modo que puede realizar la transición

Girar las claves de la cuenta de almacenamiento de Azure es un proceso de tres pasos simples

1. Crear la segunda credencial de ámbito de la base de datos en función de la tecla de acceso de almacenamiento secundario
2. Crear esta nueva credencial se basa en segundo origen de datos externos
3. Quitar y crear las tablas externas señalando el nuevo origen de datos externos

Cuando haya migrado todos su externo tablas para el nuevo origen de datos externos, a continuación, puede realizar las tareas de limpieza:

1. Coloque el primer origen de datos externos
2. Credenciales en función de la tecla de acceso de almacenamiento principal de ámbito de la primera base de datos de lista
3. Inicie sesión en Azure y regenerar la clave de acceso principal lista para la próxima vez

## <a name="query-azure-blob-storage-data"></a>La consulta de datos de almacenamiento de blobs de Windows Azure
Consultas en tablas externas simplemente use el nombre de tabla como si fuera una tabla relacional.

```sql
-- Query Azure storage resident data via external table.
SELECT * FROM [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE] Una consulta en una tabla externa puede fallar con el error *"Se ha alcanzado consulta anulado--el máximo rechazar umbral al leer desde un origen externo"*. Esto indica que los datos externos contienen registros *sucio* . Un registro de datos se considera 'sucio' Si el número o tipos de datos reales de columnas no coinciden con las definiciones de columna de la tabla externa o si los datos no se ajustan al formato de archivo externo especificado. Para corregir este error, asegúrese de que su tabla externa y las definiciones de formato de archivo externo son correctas y sus datos externos se ajustan a estas definiciones. En caso de un subconjunto de registros de datos externos dirty, puede elegir rechazar estos registros de las consultas con las opciones de rechazar en crear externos DDL de tabla.


## <a name="load-data-from-azure-blob-storage"></a>Cargar datos desde el almacenamiento de blobs de Windows Azure
Este ejemplo carga los datos de almacenamiento de blobs de Windows Azure a base de datos de almacén de datos de SQL.

Almacenar los datos directamente, elimina el tiempo de transferencia de datos de consultas. Almacenar los datos con un índice columnstore mejora el rendimiento de la consulta para consultas de análisis de hasta 10 veces.

Este ejemplo usa la instrucción CREATE TABLE AS SELECT para cargar los datos. La nueva tabla hereda las columnas con el nombre de la consulta. Hereda los tipos de datos de las columnas de la definición de tabla externa.

CREATE TABLE AS SELECT es un alto rendimiento instrucción Transact-SQL que carga los datos en paralelo en todos los nodos de cálculo de su almacén de datos de SQL.  Fue desarrollado originalmente para el motor de procesamiento enormemente paralelo (MPP) en el sistema de la plataforma de análisis y está ahora en el almacén de datos de SQL.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Consulte [Crear tabla como seleccionar (Transact-SQL)][].

## <a name="create-statistics-on-newly-loaded-data"></a>Crear estadísticas de los datos recién cargadas

Almacén de datos de SQL Azure hace aún no automática de soporte técnico, crear o actualizará las estadísticas automáticamente.  Para obtener el mejor rendimiento de las consultas, es importante que estadísticas crearse en todas las columnas de todas las tablas después de la primera carga o se producen cambios importantes en los datos.  Para obtener una explicación detallada de las estadísticas, vea el tema de [estadísticas][] en el grupo de desarrollo de temas.  A continuación se muestra un ejemplo de cómo crear estadísticas en los cuadros y cargado en este ejemplo rápido.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-to-azure-blob-storage"></a>Exportar datos a almacenamiento de blobs de Windows Azure
Esta sección muestra cómo exportar datos de almacén de datos de SQL con el almacenamiento de blobs de Windows Azure. Este ejemplo usa crear externos tabla AS SELECT que es un alto rendimiento instrucción Transact-SQL para exportar los datos en paralelo de todos los nodos de cálculo.

En el ejemplo siguiente se crea una tabla externa Weblogs2014 con definiciones de columna y los datos de dbo. Tabla de blogs. La definición de tabla externa se almacena en el almacén de datos de SQL y se exportan los resultados de la instrucción SELECT en el directorio "/ Archivar/log2014 /" en el contenedor de blob especificado por el origen de datos. Se exportan los datos en el formato de archivo de texto especificado.

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```


## <a name="working-around-the-polybase-utf-8-requirement"></a>Evitar el requisito de PolyBase UTF-8
En presentar PolyBase admite la carga de archivos de datos que se han codificada UTF-8. Como UTF-8 utiliza la misma codificación de caracteres que ASCII PolyBase también le soporte cargar datos es ASCII codificada. Sin embargo, PolyBase no admite otra codificación de caracteres como UTF-16 / Unicode o extendido de caracteres ASCII. ASCII extendido incluye los caracteres con acentos como diéresis que es común en alemán.

Para evitar este requisito es volver a escribir en codificación UTF-8 la mejor respuesta.

Existen varias formas de hacerlo. A continuación encontrará dos enfoques con Powershell:

### <a name="simple-example-for-small-files"></a>Ejemplo sencillo para archivos pequeños

A continuación se muestra una línea simple de un script de Powershell que crea el archivo.

```PowerShell
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

Sin embargo, aunque es una forma sencilla de volver a codificar los datos no es más eficaz. El ejemplo de transmisiones de io siguiente es mucho, mucho más rápido y consigue el mismo resultado.

### <a name="io-streaming-example-for-larger-files"></a>Ejemplo de transmisión de IO para archivos más grandes

En el siguiente ejemplo de código es más complejo pero medida que se transmite las filas de datos de origen a destino es mucho más eficaz. Use este método para archivos de mayor tamaño.

```PowerShell
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path\","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path\","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de cómo mover los datos al almacén de datos de SQL, vea la [información general sobre la migración de datos][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Cargar los datos con PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Estadísticas]: ./sql-data-warehouse-tables-statistics.md
[información general sobre la migración de datos]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[Crear formato de archivo externo (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026) .aspx [crear una tabla externa (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspxx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[Crear tabla AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->
