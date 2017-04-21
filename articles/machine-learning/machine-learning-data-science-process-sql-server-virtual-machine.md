<properties 
    pageTitle="Procesar datos de SQL Azure | Microsoft Azure" 
    description="Proceso de datos de SQL Azure" 
    services="machine-learning" 
    documentationCenter="" 
    authors="garyericson" 
    manager="jhubbard" 
    editor="" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/16/2016" 
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Datos de proceso en SQL Server una máquina Virtual en Azure

Este documento trata sobre cómo explorar datos y generar características para los datos almacenados en una máquina virtual de servidor de SQL en Azure. Esto puede hacerse datos problemas con el uso de SQL o mediante un lenguaje de programación como Python.


> [AZURE.NOTE] Las instrucciones SQL de ejemplo en este documento se suponen que los datos están en SQL Server. Si no es así, consulte el mapa de proceso de ciencias de nube datos para obtener información sobre cómo mover los datos a SQL Server.

##<a name="SQL"></a>Uso de SQL

Se describen las siguientes tareas wrangling de datos en esta sección con SQL:

1. [Exploración de datos](#sql-dataexploration)
2. [Generación de característica](#sql-featuregen)

###<a name="sql-dataexploration"></a>Exploración de datos
Estas son algunas secuencias de comandos SQL de ejemplo que se pueden usar para explorar almacena datos de SQL Server.


> [AZURE.NOTE] Para obtener un ejemplo práctico, puede usar el [conjunto de datos de Nueva York Taxi](http://www.andresmh.com/nyctaxitrips/) y consulte la IPNB titulado [datos de Nueva York problemas con el Bloc de notas de IPython y SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para un tutorial to-end.

1. Obtener el recuento de observaciones por día

    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 

2. Obtenga los niveles en una columna de categorías

    `select  distinct <column_name> from <databasename>`

3. Obtener el número de niveles de combinación de dos columnas por categorías 

    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. Obtener la distribución de columnas numéricas

    `select <column_name>, count(*) from <tablename> group by <column_name>`


###<a name="sql-featuregen"></a>Generación de característica

En esta sección, se describen formas de generar características mediante SQL:  

1. [Contar según la característica de generación](#sql-countfeature)
2. [Generación de característica binning](#sql-binningfeature)
3. [Implementación de las características de una sola columna](#sql-featurerollout)


> [AZURE.NOTE] Una vez generar características adicionales, puede agregar como columnas a la tabla existente o crear una tabla con las características adicionales y la clave principal, que se puede combinar con la tabla original. 

###<a name="sql-countfeature"></a>Contar según la característica de generación

Este documento muestra dos maneras de generar características de recuento. El primer método utiliza suma condicional y el segundo método usa la cláusula 'where'. A continuación, estos se unen con la tabla original (con columnas de clave principal) para que las características de recuento junto con los datos originales.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

###<a name="sql-binningfeature"></a>Generación de característica binning

En el ejemplo siguiente se muestra cómo generar características binned binning (con 5 clases) una columna numérica que puede usarse en su lugar como una función:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


###<a name="sql-featurerollout"></a>Implementación de las características de una sola columna

En esta sección, mostramos cómo implementar una sola columna de una tabla para generar características adicionales. En el ejemplo se supone que hay una columna de latitud o de la tabla desde la que está intentando generar características.

Aquí es un manual breve sobre los datos de ubicación de latitud y longitud (con los recursos de stackoverflow [para medir la precisión de latitud y longitud?](http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Esto es útil para comprender antes de featurizing el campo ubicación:

- El inicio de sesión nos indica si estamos Norte o sur, Oriente u Oeste en el globo.
- Un cero cientos dígitos nos indican que estamos utilizando longitud, latitud no!
- Decenas dígitos le da una posición a aproximadamente 1.000 kilómetros. Nos proporciona información útil sobre qué continente u Océano estamos en.
- El dígito de unidades (un grado decimal) le da una posición 111 kilómetros (60 Milla náutica, a 69 millas). Puede Díganos aproximadamente qué estado grande o estamos en el país.
- El primer decimal vale 11.1 km: distinguir la posición de una población grande de una ciudad grande adyacente.
- El segundo decimal vale 1.1 km: puede separar uno village del siguiente.
- El tercer decimal es conveniente hasta 110 m puede identificar un campo agrícola grande o campus instituciones.
- El cuarto decimal es conveniente m hasta 11 puede identificar un lote de tierra. Es comparable a la precisión típica de una unidad GPS corregida y sin interferencias.
- La posición decimal quinta es patrimonio m hasta 1.1 distinguir árboles entre sí. Precisión de este nivel de unidades GPS comerciales sólo puede conseguirse con corrección diferencial.
- La sexta posición decimal es importante hasta 0.11 m, que puede utilizarla para diseñar las estructuras de detalle para el diseño de paisajes, crear carreteras. Debe ser buena más de para realizar el seguimiento de los movimientos de glaciares y ríos. Para ello puede tomar medidas concienzudas con GPS, como GPS differentially corregido.

La información de ubicación puede featurized manera, separar región, la ubicación y la información de la ciudad. Tenga en cuenta que también puede llamar un punto final de resto como API de Bing Maps disponibles al [Buscar una ubicación de punto de](https://msdn.microsoft.com/library/ff701710.aspx) obtener la información de la región o distrito.

    select 
        <location_columnname>
        ,round(<location_columnname>,0) as l1       
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

Las características según ubicación anteriores más sirve para generar características de recuento adicionales como se describió anteriormente. 


> [AZURE.TIP] Puede insertar los registros con el idioma de elección mediante programación. Debe insertar los datos en fragmentos para mejorar la eficacia de escritura (para obtener un ejemplo de cómo hacerlo con pyodbc, vea [un ejemplo para obtener acceso a SQL Server con python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Otra alternativa es insertar datos en la base de datos con la [herramienta BCP](https://msdn.microsoft.com/library/ms162802.aspx).

###<a name="sql-aml"></a>Conectar con el aprendizaje Azure

La característica recién generada puede agregada como una columna a una tabla existente o almacenada en una tabla nueva y unida con la tabla original para el aprendizaje. Características pueden generar o acceder a ellas si ya ha creado, mediante la [Importación de datos] [ import-data] módulo en Azure máquina aprendizaje tal como se muestra a continuación:

![lectores de azureml][1] 

##<a name="python"></a>Usar un lenguaje de programación como Python

Utilizar Python para explorar los datos y generar características cuando los datos están en SQL Server es similar al procesamiento de datos de blobs de Windows Azure con Python tal como se describe en [los datos de proceso de Azure Blob en entorno ciencias datos](machine-learning-data-science-process-data-blob.md). Los datos deben cargar desde la base de datos en un marco de datos pandas y, a continuación, se pueden procesar más. Documentamos el proceso de conectarse a la base de datos y cargar los datos en el marco de datos en esta sección.

El formato de cadena de conexión siguiente puede usarse para conectarse a una base de datos de SQL Server desde Python mediante pyodbc (reemplazar nombre_de_servidor, dbname, nombre de usuario y contraseña con los valores específicos):

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La [biblioteca de Pandas](http://pandas.pydata.org/) en Python proporciona un amplio conjunto de estructuras de datos y herramientas de análisis de datos para la manipulación de datos para la programación de Python. El código siguiente lee los resultados se devuelven desde una base de datos de SQL Server en un marco de datos Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Ahora puede trabajar con el marco de datos Pandas como se explica en el artículo de [datos de proceso de Azure Blob en entorno ciencias datos](machine-learning-data-science-process-data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Ciencia de datos de Azure en el ejemplo de acción

Para obtener un ejemplo Tutorial de llevar a cabo el proceso de ciencias de datos de Azure con un conjunto de datos público, vea [Proceso de ciencias de datos de Azure en acción](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-process-sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 
