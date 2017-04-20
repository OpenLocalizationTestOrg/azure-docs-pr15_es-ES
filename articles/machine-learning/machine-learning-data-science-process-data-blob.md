<properties 
    pageTitle="Procesar datos de Azure blob con análisis avanzado | Microsoft Azure" 
    description="Datos de proceso de almacenamiento Azure Blob." 
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

#<a name="heading"></a>Procesar datos de Azure blob con análisis avanzado

Este documento trata de explorar datos y características de generación de datos almacenados en almacenamiento Azure Blob. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Cargar los datos en una trama de datos de Pandas
Para explorar y manipular un conjunto de datos, se debe descargar desde el origen de blob en un archivo local que se puede cargar en una trama de datos de Pandas. Estos son los pasos a seguir para realizar este procedimiento:

1. Descarga los datos de Azure blob con el siguiente código Python mediante servicio de blob. Reemplace la variable en el código siguiente con los valores específicos: 

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

Ahora está listo para explorar los datos y generar características de este conjunto de datos.


##<a name="blob-dataexploration"></a>Exploración de datos

A continuación presentamos algunos ejemplos de formas de explorar datos mediante Pandas:

1. Inspeccionar el número de filas y columnas 

        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. Inspeccionar el primeras o el últimas pocas filas en el conjunto de datos como sigue:

        dataframe_blobdata.head(10)
        
        dataframe_blobdata.tail(10)

3. Comprobar el tipo de datos que cada columna se importó como mediante el siguiente código de ejemplo
    
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Comprobar las estadísticas básicas para las columnas del conjunto de datos como sigue
 
        dataframe_blobdata.describe()
    
5. Fíjese en el número de entradas para cada valor de columna como sigue

        dataframe_blobdata['<column_name>'].value_counts()

6. Contar valores que faltan en comparación con el número real de entradas de cada columna mediante el siguiente código de ejemplo

        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
     
7.  Si tiene valores que faltan para una columna concreta de los datos, puede colocarlas como sigue:

        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape

    Es otra forma de reemplazar los valores que faltan con la función de modo:
    
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        

8. Crear un gráfico de histograma con un número variable de bins para trazar la distribución de una variable 
    
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
        
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    
9. Mire las correlaciones entre variables utilizando un diagrama de dispersión o utilizando la función incorporada de correlación

        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
        
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    
    
##<a name="blob-featuregen"></a>Generación de la característica
    
Podemos generar características utilizando Python como sigue:

###<a name="blob-countfeature"></a>Valor del indicador según la característica de generación

Se pueden crear funciones por categorías como sigue:

1. Examine la distribución de la columna de categorías:
    
        dataframe_blobdata['<categorical_column>'].value_counts()

2. Generar valores de indicador para cada uno de los valores de columna

        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. Unirse a la columna de indicador con la trama de datos original 
 
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. Eliminar la propia variable original:

        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    
###<a name="blob-binningfeature"></a>Generación de función binning

Para generar características desechados, proceder como sigue:

1. Agregar una secuencia de columnas en una columna numérica de bin
 
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
        
2. Convertir binning en una secuencia de variables booleanas

        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    
3. Por último, unir las variables ficticias a la trama de datos original

        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool) 


##<a name="sql-featuregen"></a>Escritura de datos en Azure blob y consumir en el aprendizaje de la máquina de Azure

Una vez que haya explorado los datos y crea las características necesarias, puede cargar los datos (muestreo o featurized) a un Azure blob y utilizar en aprendizaje de máquinas de Azure mediante los pasos siguientes: tenga en cuenta que se pueden crear funciones adicionales en el estudio de aprendizaje de máquina de Azure también. 
1. Escribir la trama de datos en el archivo local

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Cargar los datos en Azure blob como sigue:

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

3. Ahora los datos se pueden leer desde el blob mediante el aprendizaje automático de Azure [Importar datos] [ import-data] módulo como se muestra en la pantalla siguiente:
 
![blob de lector][1]

[1]: ./media/machine-learning-data-science-process-data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 
