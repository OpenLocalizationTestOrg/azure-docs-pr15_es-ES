<properties 
    pageTitle="Almacenamiento de blobs de datos de ejemplo de Azure | Microsoft Azure" 
    description="Datos de ejemplo en el almacenamiento de blobs de Windows Azure" 
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
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Almacenamiento de blobs de datos de ejemplo de Azure


Este documento trata sobre los datos de muestreo almacenados en el almacenamiento de blobs de Windows Azure, descargarlo mediante programación y, a continuación, muestreo mediante procedimientos escritos en Python.

**¿Por qué los datos de ejemplo?**
Si el conjunto de datos que se va a analizar es grande, suele ser una buena idea abajo-muestra los datos para reducir a un tamaño menor pero representante y más manejable. Esto facilita la comprensión de datos, de exploración y de ingeniería de la característica. Su rol en el proceso de análisis de Cortana es habilitar la creación de prototipos rápida de las funciones de procesamiento de datos y modelos de aprendizaje.

El **menú** debajo de vínculos a temas que describen cómo datos desde diversos entornos de almacenamiento de ejemplo. 

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Esta tarea de muestreo es un paso en el [Proceso de ciencias de datos de equipo (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="download-and-down-sample-data"></a>Descargar y datos de ejemplo de abajo
1. Descargar los datos de almacenamiento de blobs de Windows Azure con el servicio de blobs de Windows desde el siguiente código Python: 

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

2. Leer datos en una trama de datos Pandas desde el archivo descargado anteriormente.

        import pandas as pd

        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. El uso de datos de ejemplo de abajo el `numpy`de `random.choice` como sigue:

        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Ahora puede trabajar con el marco de datos anteriores con el ejemplo 1 por ciento para la generación de la característica y obtener información más detallada.

##<a name="heading"></a>Cargar datos y lea en aprendizaje de máquina de Azure

Puede usar el siguiente código de ejemplo para los datos de ejemplo de abajo y usarla directamente en Azure m:

1. Escribir la trama de datos en un archivo local

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Cargue el archivo local a un blobs de Windows Azure con el siguiente código:

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
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Leer los datos de la blobs de Windows Azure con Azure m [Importar datos](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) , tal como se muestra en la imagen siguiente:
 
![blob lector](./media/machine-learning-data-science-sample-data-blob/reader_blob.png)

 
