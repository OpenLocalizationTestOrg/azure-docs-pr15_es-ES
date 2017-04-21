<properties
    pageTitle="Crear funciones de datos de almacenamiento de blobs de Windows Azure utilizando Panda | Microsoft Azure"
    description="Cómo crear características para los datos que se almacenan en el contenedor de blobs de Windows Azure con el paquete de Panda Python."
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
    ms.date="09/19/2016"
    ms.author="bradsev;garye" />

#<a name="create-features-for-azure-blob-storage-data-using-panda"></a>Crear funciones de datos de almacenamiento de blobs de Windows Azure utilizando Panda

Este documento muestra cómo crear características para los datos que se almacenan en el contenedor de blobs de Windows Azure mediante el paquete de Python [Pandas](http://pandas.pydata.org/) . Después de esquematizar cómo cargar los datos en un marco de datos Panda, se muestra cómo generar características por categorías usando secuencias de comandos de Python con valores de indicador y binning características.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]Vínculos de este **menú** a temas que describen cómo crear características para los datos en distintos entornos. Esta tarea es un paso en el [Proceso de ciencias de datos de equipo (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="prerequisites"></a>Requisitos previos

En este artículo se supone que ha creado una cuenta de almacenamiento de blobs de Windows Azure y ha almacenado los datos allí. Si necesita instrucciones para configurar una cuenta, consulte [crear una cuenta de almacenamiento de Azure](../storage/storage-create-storage-account.md#create-a-storage-account)


## <a name="load-the-data-into-a-pandas-data-frame"></a>Cargar los datos en un marco de datos Pandas
Para explorar y manipular un conjunto de datos, se debe descargar desde el origen de blobs de Windows a un archivo local que puede cargar en una trama de datos de Pandas. Estos son los pasos a seguir para realizar este procedimiento:

1. Descargar los datos de Azure blob con el siguiente código Python usando el servicio de blobs de Windows. Reemplazar la variable en el código siguiente con los valores específicos:

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

##<a name="blob-featuregen"></a>Generación de característica

Las dos secciones siguientes muestran cómo generar por categorías funciones con valores de indicador y binning mediante secuencias de comandos de Python.

###<a name="blob-countfeature"></a>Valor de indicador basado característica generación

Se pueden crear categorías características como sigue:

1. Inspeccionar la distribución de la columna categorías:

        dataframe_blobdata['<categorical_column>'].value_counts()

2. Generar los valores de indicador para cada uno de los valores de columna

        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. Unirse a la columna de indicador con el marco de datos original

            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. Quitar la propia variable original:

        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

###<a name="blob-binningfeature"></a>Generación de característica binning

Para generar características binned, se realice lo siguiente:

1. Agregar una secuencia de columnas en una columna numérica de clase

        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)

2. Convertir binning en una secuencia de variables de tipo boolean

        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')

3. Por último, unirse a las variables ficticias volver a la trama de datos original

        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)

##<a name="sql-featuregen"></a>Escritura de datos en blobs de Windows Azure y consumo en aprendizaje de máquina de Azure

Una vez que haya explorado los datos y creado las características necesarias, puede cargar los datos (muestreo o featurized) a un Azure blob y consumir en el aprendizaje Azure con los pasos siguientes: tenga en cuenta que se pueden crear características adicionales de Azure máquina aprendizaje Studio también.
1. Escribir la trama de datos en el archivo local

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Cargar los datos para blobs de Windows Azure como sigue:

        from azure.storage.blob import BlobService
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

3. Ahora los datos se pueden leer desde el blob uso del módulo de aprendizaje del equipo de Azure [Importar datos](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) , como se muestra en la pantalla siguiente:

![blob lector](./media/machine-learning-data-science-process-data-blob/reader_blob.png)
