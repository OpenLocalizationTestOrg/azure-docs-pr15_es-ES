<properties
   pageTitle="Desarrollar secuencias de comandos de SQL U con datos lago Tools para Visual Studio | Azure"
   description="Obtenga información sobre cómo instalar herramientas de lago de datos para Visual Studio, cómo desarrollar y secuencias de comandos de prueba U SQL. "
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>Tutorial: Introducción a lenguaje de datos de Azure lago análisis U-SQL

U-SQL es un idioma que unifica las ventajas de SQL con la potencia expresiva de su propio código para procesar todos los datos a cualquier escala. Capacidad de consulta distribuida scalable de SQL U permite eficazmente analizar datos en el almacén y a través de comercios relacional como base de datos de SQL Azure.  Permite al proceso de datos no estructurados aplicando esquema en leen, insertar lógica personalizada y UDF e incluyen capacidad de ampliación para habilitar un control minucioso sobre cómo ejecutar a escala. Para obtener más información sobre la filosofía de diseño de SQL U, consulte esta [entrada de blog de Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Existen algunas diferencias de ANSI SQL o T SQL. Por ejemplo, sus palabras clave como SELECT deben estar en mayúsculas.

Está idioma de sistema y la expresión de tipo dentro de las cláusulas select, ¿dónde están los predicados etcetera en C#.
Esto significa que los tipos de datos son los tipos de C# y los tipos de datos utilizan semántica C# nulo y las operaciones de comparación dentro de un predicado siguen la sintaxis de C# (por ejemplo, un == "foo").  Esto también significa, que los valores son objetos de .NET completos, lo que le permite utilizar fácilmente cualquier método para que funcione en el objeto (por ejemplo, "f o o o". Split(' ')).

Para obtener más información, vea [Referencia SQL de U](http://go.microsoft.com/fwlink/p/?LinkId=691348).

###<a name="prerequisites"></a>Requisitos previos

Debe completar [Tutorial: desarrollar secuencias de comandos de SQL U con datos lago Tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

En el tutorial, ha encontrado un trabajo de análisis de datos lago con el siguiente script U SQL:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

Esta secuencia de comandos no tiene todas las operaciones de transformación. Lee el archivo de origen denominado **SearchLog.tsv**, schematizes y envía el conjunto de filas en un archivo llamado **SearchLog-primera-u-sql.csv**.

Observe el signo de interrogación junto al tipo de datos del campo duración. Esto significa que el campo duración podría ser null.

Algunos conceptos y palabras clave que se encuentra en la secuencia de comandos:

- **Variables de conjunto de filas**: cada expresión de la consulta que genera un conjunto de filas se puede asignar a una variable. SQL U sigue el modelo de nomenclatura variable de T SQL, por ejemplo, **@searchlog** en la secuencia de comandos.
    Tenga en cuenta que la asignación no forzar la ejecución. Nombres de la expresión y le ofrece la capacidad de acumulación simplemente los expresiones más complejas.
- **Extraer** permite definir un esquema de lectura. Especifica el esquema de un nombre de columna y C# par de nombre de tipo por columna. Utiliza un llamados **extracción**, por ejemplo, **Extractors.Tsv()** para extraer archivos tsv. Puede desarrollar extractores personalizados.
- **Salida** toma un conjunto de filas y lo serializa. La Outputters.Csv() de salida de un archivo de valores separados por comas en la ubicación especificada. También puede desarrollar a Outputters personalizados.
- Observe que las dos rutas son rutas de acceso relativas. También puede usar rutas de acceso absolutas.  Por ejemplo

        adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Debe usar ruta de acceso absoluta para acceder a los archivos en las cuentas de almacenamiento vinculados.  La sintaxis de los archivos almacenados en cuenta vinculada de almacenamiento de Azure es:

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Contenedor de blobs de Windows Azure con BLOB público o permisos de acceso de los contenedores público no se admiten actualmente.

## <a name="use-scalar-variables"></a>Utilizar variables escalares

También puede usar las variables escalares para facilitar el mantenimiento de scripts. La secuencia de comandos de SQL U anterior también puede escribirse como la siguiente:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Transformar conjuntos de filas

Use **Seleccione** transformar conjuntos de filas:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

La cláusula WHERE usa [C# expresión booleana](https://msdn.microsoft.com/library/6a71f45d.aspx). Puede usar el lenguaje de expresiones de C# para hacer su propia expresiones y funciones. Incluso puede realizar un filtrado más complejo combinarlas con conjunciones lógicos (and) y disjunctions (OR).

La siguiente secuencia de comandos utiliza el método de DateTime.Parse() y conjunción.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();

Observe que la segunda consulta está funcionando con el resultado del primer conjunto de filas y, por tanto, el resultado es una composición de los dos filtros. También puede volver a usar un nombre de variable y los nombres están orientados instrucción.

## <a name="aggregate-rowsets"></a>Conjuntos de filas de agregado

U-SQL le ofrece la familiar **ORDER BY**, **GROUP BY** y agregados.

La siguiente consulta encuentra la duración total por región y, a continuación, genera la parte superior de 5 duraciones en orden.

Conjuntos de filas de SQL U no conservan su orden de la consulta siguiente. Por lo tanto, para un resultado, es necesario agregar ORDER BY a la instrucción de salida, como se muestra a continuación:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Cláusula ORDER BY U SQL tiene que combinar con la cláusula de capturar en una expresión SELECT.

Cláusula HAVING U SQL puede utilizarse para restringir los resultados a los grupos que cumplen la condición HAVING:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## <a name="join-data"></a>Combinar datos

U-SQL proporciona operadores de combinación comunes como combinación interna, combinación externa de completo, izquierda y derecha, SEMICOMBINACIÓN, unirse a las tablas, pero no sólo los conjuntos de filas (incluso los producidos a partir de archivos).

La siguiente secuencia de comandos unan a la searchlog con un registro de impresión de anuncio y proporciona los anuncios por la cadena de consulta para una fecha determinada.

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();

    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;

    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


SQL U solo es compatible con la sintaxis compatible con ANSI: predicado Rowset1 combinación Rowset2 activado. La sintaxis de la antigüedad de desde Rowset1, donde Rowset2 predicado no es compatible.
El predicado de una combinación tiene que ser una combinación de igualdad y sin expresión. Si desea usar una expresión, agregarlo a la cláusula select de un conjunto de filas anterior. Si desea hacer una comparación distinta, puede mover en la cláusula WHERE.


## <a name="create-databases-table-valued-functions-views-and-tables"></a>Crear tablas, vistas, funciones con valores de tabla y bases de datos

U-SQL le permite usar los datos en el contexto de una base de datos y el esquema. Así que no tiene siempre leer o escribir en archivos.

Todos los scripts SQL U se ejecución con una base de datos predeterminada (principal) y un esquema predeterminado (DBO) como su contexto predeterminado. Puede crear su propia base de datos o el esquema. Para cambiar el contexto, utilice la instrucción **USE** para cambiar el contexto.


### <a name="create-a-table-valued-function-tvf"></a>Crear una función con valores de tabla (TVF)

En la secuencia de comandos de SQL U anterior, repite con extraer la lectura del mismo archivo de origen. Función con valores de tabla de SQL U permite encapsulados los datos de uso en el futuro.   

El script siguiente crea una función TVF denominada *Searchlog()* en la base de datos predeterminada y esquema:

    DROP FUNCTION IF EXISTS Searchlog;

    CREATE FUNCTION Searchlog()
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;

La siguiente secuencia de comandos muestra cómo usar la función TVF definida en la secuencia de comandos anterior:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-views"></a>Crear vistas

Si solo tiene una expresión de consulta que desee resumir y no desea agregar parámetros a ella, puede crear una vista en lugar de una función con valores de tabla.

La siguiente secuencia de comandos crea una vista denominada *SearchlogView* en la base de datos predeterminada y esquema:

    DROP VIEW IF EXISTS SearchlogView;

    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

La siguiente secuencia de comandos muestra el uso de la vista definida:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-tables"></a>Crear tablas

Similar a la tabla de base de datos relacional, SQL U le permite crear una tabla con un esquema predefinido o crear una tabla y deducir el esquema de la consulta que rellena la tabla (también conocido como CREATE TABLE AS SELECT o CTAS).

La siguiente secuencia de comandos crear una base de datos y dos tablas:

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SeachLogDb
    USE DATABASE SearchLogDb;

    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;

    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,

                INDEX sl_idx CLUSTERED (UserId ASC)
                    PARTITIONED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                PARTITIONED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>Tablas de consulta

Puede consultar las tablas (creadas en la secuencia de comandos anterior) en la misma manera que la consulta sobre los archivos de datos. En lugar de crear un conjunto de filas con extraer, se ahora puede hacer referencia al nombre de tabla.

La secuencia de comandos de transformación que ha usado previamente se ha modificado para leer de las tablas:

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Tenga en cuenta que actualmente no puede ejecutar una instrucción SELECT en una tabla en la misma secuencia de comandos como la secuencia de comandos en la que crear tabla.


##<a name="conclusion"></a>Conclusión

¿Qué está cubierto en el tutorial es solo una pequeña parte de SQL U. Debido al alcance de este tutorial, no abarcan todos los elementos, como por ejemplo:

- Usar CROSS APPLY a descomprimir partes de cadenas, matrices y mapas en filas.
- Trabajar con particiones conjuntos de datos (tablas con particiones y conjuntos de archivo).
- Desarrollar operadores definidas por el usuario como extractores, outputters, procesadores, agregadores definidas por el usuario en C#.
- Usar funciones de ventanas U SQL.
- Administrar el código SQL U con vistas, funciones con valores de tabla y procedimientos almacenados.
- Ejecute código personalizado arbitrario en los nodos de procesamiento.
- Conectar con bases de datos de SQL Azure y permite la federación de consultas a través de ellos y los datos de SQL U y lago de datos de Azure.

## <a name="see-also"></a>Vea también

- [Información general de análisis de datos lago de Microsoft Azure](data-lake-analytics-overview.md)
- [Desarrollar secuencias de comandos de SQL U con datos lago Tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Usar funciones de la ventana de SQL U para trabajos de análisis de lago de datos de Azure](data-lake-analytics-use-window-functions.md)
- [Supervisar y solucionar problemas de trabajos de análisis de lago de datos de Azure con el Portal de Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>Díganos qué opina

- [Enviar una solicitud de característica](http://aka.ms/adlafeedback)
- [Obtenga ayuda en los foros](http://aka.ms/adlaforums)
- [Proporcionar comentarios sobre U SQL](http://aka.ms/usqldiscuss)
