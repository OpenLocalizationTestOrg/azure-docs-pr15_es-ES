<properties 
    pageTitle="Ejemplo de datos de SQL Server en Azure | Microsoft Azure" 
    description="Datos de ejemplo de SQL Server en Azure" 
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
    ms.date="09/19/2016" 
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Datos de ejemplo de SQL Server en Azure


Este documento muestra cómo muestrear los datos almacenados en SQL Server en Azure con SQL o el lenguaje de programación Python. También se muestra cómo mover los datos muestras en Azure aprendizaje guardar a un archivo, cargar a un blobs de Windows Azure y leerlo a continuación en Azure máquina aprendizaje Studio.

El muestreo Python utiliza la biblioteca ODBC de [pyodbc](https://code.google.com/p/pyodbc/) para conectarse al servidor de SQL Azure y la biblioteca de [Pandas](http://pandas.pydata.org/) a la toma de muestras.

>[AZURE.NOTE] El código SQL de ejemplo en este documento se supone que los datos están en un servidor SQL Azure. Si no es así, consulte [mover los datos a SQL Server en Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) tema para obtener instrucciones sobre cómo mover los datos a SQL Server en Azure.

**¿Por qué los datos de ejemplo?**
Si el conjunto de datos que se va a analizar es grande, suele ser una buena idea abajo-muestra los datos para reducir a un tamaño menor pero representante y más manejable. Esto facilita la comprensión de datos, de exploración y de ingeniería de la característica. Su rol en el [Grupo datos ciencias proceso (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) es habilitar la creación de prototipos rápida de las funciones de procesamiento de datos y modelos de aprendizaje.

El **menú** debajo de vínculos a temas que describen cómo datos desde diversos entornos de almacenamiento de ejemplo. 

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Esta tarea de muestreo es un paso en el [Proceso de ciencias de datos de equipo (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

##<a name="SQL"></a>Uso de SQL

Esta sección describe varios métodos utilizando SQL para realizar un muestreo aleatorio simple con los datos en la base de datos. Elija un método basado en el tamaño de los datos y su distribución.

Los dos elementos siguientes muestran cómo utilizar newid en SQL Server para realizar el muestreo. El método que elija depende de lo aleatorios que desee en el ejemplo se (pk_id en el código de ejemplo siguiente se supone que una clave principal generado automáticamente).

1. Muestra aleatoria menos estricto

        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())

2. Muestra más aleatoria 

        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

TABLESAMPLE puede ser utilizado para el muestreo así como muestra a continuación. Esto puede ser mejor si el tamaño de los datos es grande (suponiendo que no está asociados datos de diferentes páginas) y de la consulta para completar en un tiempo razonable.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

>[AZURE.NOTE] Puede explorar y generar características de datos muestras almacenando en una nueva tabla


###<a name="sql-aml"></a>Conectar con el aprendizaje Azure

Puede usar directamente encima de las consultas de ejemplo en el aprendizaje del equipo de Azure [Importar datos] [ import-data] módulo a los datos sobre la marcha de ejemplo de abajo y ponerlo en un ensayo de aprendizaje del equipo de Azure. A continuación se muestra una captura de pantalla de uso del módulo de lector para leer los datos muestras:
   
![lector sql][1]

##<a name="python"></a>Usando el lenguaje de programación de Python 

Esta sección muestra cómo utilizar la [biblioteca de pyodbc](https://code.google.com/p/pyodbc/) para establecer un ODBC conectarse a una base de datos SQL server en Python. La cadena de conexión de base de datos es como sigue: (reemplace nombre_de_servidor, dbname, nombre de usuario y contraseña con su configuración):

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La biblioteca de [Pandas](http://pandas.pydata.org/) en Python proporciona un amplio conjunto de estructuras de datos y herramientas de análisis de datos para la manipulación de datos para la programación de Python. El código siguiente lee una muestra de 0,1% de los datos de una tabla de base de datos de SQL Azure en los datos de Pandas:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Ahora puede trabajar con los datos de muestras en el marco de datos Pandas. 

###<a name="python-aml"></a>Conectar con el aprendizaje Azure

Puede usar el siguiente código de ejemplo para guardar los datos de muestra de abajo a un archivo y cargue a un blobs de Windows Azure. Los datos en el blob pueden directamente leerse en un ensayo de aprendizaje del equipo de Azure con los [Datos de importación] [ import-data] módulo. Los pasos son los siguientes: 

1. Escribir la trama de datos de pandas en un archivo local

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Cargar archivo local a blobs de Windows Azure

        from azure.storage import BlobService
        import tables

        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
        
        try:
       
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
        
        except:         
            print ("Something went wrong with uploading blob:"+BLOBNAME)

3. Leer datos de blobs de Windows Azure con el aprendizaje Azure [Importar datos] [ import-data] módulo tal como se muestra en la siguiente captura de pantalla:
 
![blob lector][2]

## <a name="the-team-data-science-process-in-action-example"></a>El proceso de ciencias grupo datos de ejemplo de acción

Para obtener un ejemplo de tutorial de llevar a cabo el proceso de ciencias grupo datos de uso de un conjunto de datos público, vea [proceso de ciencias de datos de equipo en acción: uso de SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png

 [import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
