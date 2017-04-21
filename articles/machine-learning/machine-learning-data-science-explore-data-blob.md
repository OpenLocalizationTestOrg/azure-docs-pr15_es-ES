<properties 
    pageTitle="Explorar datos en el almacenamiento de blobs de Windows Azure con Pandas | Microsoft Azure" 
    description="Cómo explorar los datos que se almacenan en el contenedor de blobs de Windows Azure con Pandas." 
    services="machine-learning,storage" 
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

#<a name="explore-data-in-azure-blob-storage-with-pandas"></a>Explorar datos en el almacenamiento de blobs de Windows Azure con Pandas

Este documento trata sobre cómo explorar los datos que se almacenan en el contenedor de blobs de Windows Azure con paquete de Python [Pandas](http://pandas.pydata.org/) .

Los siguientes vínculos de **menú** a temas que describen cómo usar herramientas para explorar los datos desde diversos entornos de almacenamiento. Esta tarea es un paso del [Proceso de ciencias de datos]().

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que tiene:

* Crear una cuenta de almacenamiento de Azure. Si necesita instrucciones, vea [crear una cuenta de almacenamiento de Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Los datos se almacenan en una cuenta de almacenamiento de blobs de Windows Azure. Si necesita instrucciones, vea [mover datos hacia y desde el almacenamiento de Azure](../storage/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Cargar los datos en un DataFrame Pandas
Para explorar y manipular un conjunto de datos, se debe primero descargar desde el origen de blobs de Windows a un archivo local, que puede cargar en una DataFrame Pandas. Estos son los pasos a seguir para realizar este procedimiento:

1. Descargar los datos de Azure blob con el siguiente ejemplo de código de Python usando el servicio de blobs de Windows. Reemplazar la variable en el código siguiente con los valores específicos: 

        from azure.storage.blob import BlobService
        import tables
        
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))


2. Leer los datos en una trama de datos Pandas del archivo descargado.

        #LOCALFILE is the file path 
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Ahora ya está listo para explorar los datos y generar características en este conjunto de datos.

##<a name="blob-dataexploration"></a>Ejemplos de exploración de datos mediante Pandas

Estos son algunos ejemplos de formas de explorar datos con Pandas:

1. Inspeccionar el **número de filas y columnas** 

        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. **Inspeccionar** el primer o último algunas **filas** en el conjunto de datos siguiente:

        dataframe_blobdata.head(10)
        
        dataframe_blobdata.tail(10)

3. Comprobar el **tipo de datos** de que cada columna se importó como con el siguiente código de ejemplo
    
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Active las **estadísticas básicas** para las columnas del conjunto de datos de manera
 
        dataframe_blobdata.describe()
    
5. Ver el número de entradas de cada valor de la columna siguiente

        dataframe_blobdata['<column_name>'].value_counts()

6. **Contar valores que faltan** en comparación con el número real de entradas de cada columna con el siguiente código de ejemplo

        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
     
7.  Si tiene **que faltan valores** de una columna específica en los datos, puede colocarlas como sigue:

        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape

    Otra manera de reemplazar valores que faltan es con la función de modo:
    
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        

8. Crear un gráfico de **histograma** con número de variables de clases para trazar la distribución de una variable 
    
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
        
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    
9. Mire **correlación** entre variables usando un scatterplot o usar la función de correlación integrados

        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
        
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()
