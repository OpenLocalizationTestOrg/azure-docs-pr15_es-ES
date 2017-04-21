<properties 
    pageTitle="Explorar datos en el equipo Virtual de SQL Server en Azure | Microsoft Azure" 
    description="Cómo explorar los datos almacenados en una máquina virtual de servidor de SQL en Azure." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/13/2016" 
    ms.author="bradsev" /> 

#<a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Explorar datos en el equipo Virtual de SQL Server en Azure


Este documento trata sobre cómo explorar los datos almacenados en una máquina virtual de servidor de SQL en Azure. Esto puede hacerse datos problemas con el uso de SQL o mediante un lenguaje de programación como Python.

Los siguientes vínculos de **menú** a temas que describen cómo usar herramientas para explorar los datos desde diversos entornos de almacenamiento. Esta tarea es un paso en el proceso de análisis de Cortana (capital).

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


> [AZURE.NOTE] Las instrucciones SQL de ejemplo en este documento se suponen que los datos están en SQL Server. Si no es así, consulte el mapa de proceso de ciencias de nube datos para obtener información sobre cómo mover los datos a SQL Server.



## <a name="sql-dataexploration"></a>Explorar los datos SQL con secuencias de comandos SQL

Estas son algunas secuencias de comandos SQL de ejemplo que se pueden usar para explorar almacena datos de SQL Server.

1. Obtener el recuento de observaciones por día

    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 

2. Obtenga los niveles en una columna de categorías

    `select  distinct <column_name> from <databasename>`

3. Obtener el número de niveles de combinación de dos columnas por categorías 

    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. Obtener la distribución de columnas numéricas

    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [AZURE.NOTE] Para obtener un ejemplo práctico, puede usar el [conjunto de datos de Nueva York Taxi](http://www.andresmh.com/nyctaxitrips/) y consulte la IPNB titulado [datos de Nueva York problemas con el Bloc de notas de IPython y SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para un tutorial to-end.

##<a name="python"></a>Explorar los datos SQL con Python

Utilizar Python para explorar los datos y generar características cuando los datos están en SQL Server es similar al procesamiento de datos de blobs de Windows Azure con Python, tal como se describe en [los datos de proceso de Azure Blob en su entorno de ciencias de datos](machine-learning-data-science-process-data-blob.md). Los datos deben cargar desde la base de datos en un pandas DataFrame y, a continuación, se pueden procesar más. Documentamos el proceso de conectarse a la base de datos y cargar los datos en el DataFrame en esta sección.

El formato de cadena de conexión siguiente puede usarse para conectarse a una base de datos de SQL Server desde Python mediante pyodbc (reemplazar nombre_de_servidor, dbname, nombre de usuario y contraseña con los valores específicos):

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La [biblioteca de Pandas](http://pandas.pydata.org/) en Python proporciona un amplio conjunto de estructuras de datos y herramientas de análisis de datos para la manipulación de datos para la programación de Python. El siguiente código lee los resultados se devuelven desde una base de datos de SQL Server en un marco de datos Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Ahora puede trabajar con la DataFrame Pandas como se explica en el tema [blobs de Azure de proceso de datos en su entorno de ciencias de datos](machine-learning-data-science-process-data-blob.md).

## <a name="cortana-analytics-process-in-action-example"></a>Proceso de análisis de Cortana en el ejemplo de acción

Para obtener un ejemplo Tutorial de llevar a cabo el proceso de análisis de Cortana con un conjunto de datos público, vea [el proceso de ciencias de datos de equipo en acción: uso de SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

 
