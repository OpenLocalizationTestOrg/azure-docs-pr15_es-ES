<properties 
    pageTitle="Mover los datos de tabla Web | Generador de datos de Azure" 
    description="Obtenga información sobre cómo mover datos locales una tabla en una página Web con el generador de datos de Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Mover los datos de un origen de tabla de Web con el generador de datos de Azure
En este artículo se describe cómo puede usar la actividad de copia de un generador de datos de Azure para copiar los datos de una tabla en una página Web a otro almacén de datos. En este artículo se basa en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , que ofrece una descripción general de movimiento de datos con la actividad de copia y combinaciones de almacén de datos compatibles.

Generador de datos admite actualmente solo los mover datos de una tabla Web a otro almacena datos, pero no mover los datos de otros datos almacena a un destino de la tabla de Web.

> [AZURE.NOTE] Este conector Web admite actualmente solo contenido de la tabla extracción desde una página HTML.

## <a name="sample-copy-data-from-web-table-to-azure-blob"></a>Ejemplo: Copiar datos de tabla Web a blobs de Windows Azure

El siguiente ejemplo muestra:

1.  Servicio de tipo [Web](#web-linked-service-properties)vinculado.
2.  Servicio de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)vinculado.
3.  Un [conjunto de datos](data-factory-create-datasets.md) a la entrada de tipo [WebTable](#WebTable-dataset-properties).
4.  Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  [Canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [WebSource](#websource-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia datos de una tabla Web a un blobs de Windows Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos. 

En el ejemplo siguiente se muestra cómo copiar datos de una tabla Web a un blobs de Windows Azure. Sin embargo, los datos se pueden copiar directamente a cualquiera de los receptores indicados en el artículo de [Actividades de movimiento de datos](data-factory-data-movement-activities.md) mediante la actividad de copia en el generador de datos de Azure. 

**Servicio de listas vinculadas Web** Este ejemplo usa el servicio Web vinculado con la autenticación anónima. Consulte la sección [Web vinculado servicio](#web-linked-service-properties) para distintos tipos de autenticación que se puede usar. 

    {
        "name": "WebLinkedService",
        "properties":
        {
            "type": "Web",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "url" : "https://en.wikipedia.org/wiki/"
            }
        }
    }


**Servicio de almacenamiento vinculado de Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Conjunto de datos de entrada de WebTable** Establecer **externos** en **true** , servicio del generador de datos le informa de que el conjunto de datos, el generador de datos externo y no se produce en una actividad en el generador de datos.

> [AZURE.NOTE] Vea la sección [obtener el índice de una tabla en una página HTML](#get-index-of-a-table-in-an-html-page) para conocer los pasos para obtener el índice de una tabla en una página HTML.  

    
    {
        "name": "WebTableInput",
        "properties": {
            "type": "WebTable",
            "linkedServiceName": "WebLinkedService",
            "typeProperties": {
                "index": 1,
                "path": "AFI's_100_Years...100_Movies"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval":  1
            }
        }
    }



**Conjunto de datos de salida de blobs de Windows Azure**

Se escriben datos en un nuevo blob cada hora (frecuencia: horas, intervalo: 1). 

    {
        "name": "AzureBlobOutput",
        "properties":
        {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties":
            {
                "folderPath": "adfgetstarted/Movies"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }




**Canalización con actividad de copia**

La canalización contiene una actividad de copia está configurado para usar la entrada anterior y conjuntos de datos de salida y se ha programado para que se ejecute cada hora. En la canalización de definición de JSON, el tipo de **origen** se establece en **WebSource** y tipo de **receptor** se establece en **BlobSink**. 

Ver [Propiedades de tipo de WebSource](#websource-copy-activity-type-properties) para la lista de propiedades compatibles con el WebSource. 
    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [
              {
                "name": "WebTableInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "WebSource"
              },
              "sink": {
                "type": "BlobSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
          ]
       }
    }


## <a name="web-linked-service-properties"></a>Propiedades del servicio vinculadas Web

La tabla siguiente proporciona la descripción para elementos de JSON específicos vinculado servicio Web.

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| tipo | La propiedad tipo debe estar establecida en: **Web** | Sí | 
| Dirección URL | Dirección URL para el origen de la Web | Sí |
| authenticationType | Anónima o básica. | Sí |
| nombre de usuario | Nombre de usuario para la autenticación básica. | Sí (para la autenticación básica)
| contraseña | Contraseña para la autenticación básica. | Sí (para la autenticación básica)

### <a name="using-anonymous-authentication"></a>Uso de autenticación anónima

    {
        "name": "web",
        "properties":
        {
            "type": "Web",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "url" : "https://en.wikipedia.org/wiki/"
            }
        }
    }


### <a name="using-basic-authentication"></a>Mediante la autenticación básica
    
    {
        "name": "web",
        "properties":
        {
            "type": "Web",
            "typeProperties":
            {
                "authenticationType": "basic",
                "url" : "http://myit.mycompany.com/",
                "userName": "Administrator",
                "password": "password"
            }
        }
    }


## <a name="webtable-dataset-properties"></a>Propiedades del conjunto de datos WebTable

Para obtener una lista completa de secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [crear conjuntos de datos](data-factory-create-datasets.md) . Secciones como estructura, la disponibilidad y la directiva de un conjunto de datos JSON son similares para todos los tipos de conjunto de datos (SQL Azure, blobs de Windows Azure, tablas de Azure, etcetera.).

La sección **typeProperties** es diferente para cada tipo de conjunto de datos y proporciona información sobre la ubicación de los datos en el almacén de datos. La sección de typeProperties para el conjunto de datos de tipo **WebTable** tiene las siguientes propiedades

(Propiedad) | Descripción | Obligatorio
:-------- | :----------- | :--------
tipo    | tipo del conjunto de datos. debe estar establecida en **WebTable** | Sí
ruta de acceso | Una dirección URL relativa al recurso que contiene la tabla. | No. Cuando no se especifica la ruta de acceso, se utiliza solo la dirección URL especificada en la definición de servicios vinculados. 
índice | El índice de la tabla en el recurso. Vea la sección [obtener el índice de una tabla en una página HTML](#get-index-of-a-table-in-an-html-page) para conocer los pasos para obtener el índice de una tabla en una página HTML. | Sí


**Ejemplo:**

    {
        "name": "WebTableInput",
        "properties": {
            "type": "WebTable",
            "linkedServiceName": "WebLinkedService",
            "typeProperties": {
                "index": 1,
                "path": "AFI's_100_Years...100_Movies"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval":  1
            }
        }
    }

## <a name="websource---copy-activity-type-properties"></a>WebSource - propiedades de tipo de actividad de copia

Para obtener una lista completa de secciones y propiedades disponibles para definir actividades, vea el artículo de la [Creación de canalizaciones](data-factory-create-pipelines.md) . Propiedades como nombre, descripción, entrada y salida tablas y directiva están disponibles para todos los tipos de actividades. 

Propiedades disponibles en la sección typeProperties de la actividad por otro lado varían en función de cada tipo de actividad. Actividad de copia, que varían en función de los tipos de orígenes y receptores.

Actualmente, cuando el origen de la actividad de copia es de tipo **WebSource**, no se admiten propiedades adicionales. 

## <a name="get-index-of-a-table-in-an-html-page"></a>Obtener el índice de una tabla en una página HTML

1. Inicie **Excel 2016** y cambie a la pestaña **datos** .  
2. Haga clic en **Nueva consulta** en la barra de herramientas, elija **Desde otros orígenes** y haga clic en **Desde la Web**.
    
    ![Menú de Power Query](./media/data-factory-web-table-connector/PowerQuery-Menu.png) 
3. En el cuadro de diálogo **Desde la Web** , introduzca el **URL** que usaría en servicios vinculados JSON (por ejemplo: https://en.wikipedia.org/wiki/) junto con la ruta de acceso que especificaría para el conjunto de datos (por ejemplo: AFI 27s_100_Years de %... 100_Movies) y haga clic en **Aceptar**. 

    ![En el cuadro de diálogo de Web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png) 

    Dirección URL utilizada en este ejemplo: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies 
4.  Si ve el cuadro de diálogo **contenido Web de Access** , seleccione el derecho de **dirección URL**, **autenticación**y haga clic en **Conectar**. 

    ![Cuadro de diálogo contenido Web de Access](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5.  Haga clic en un elemento de **tabla** en la vista de árbol para ver el contenido de la tabla y, a continuación, haga clic en el botón **Editar** en la parte inferior.  

    ![Cuadro de diálogo navegador](./media/data-factory-web-table-connector/Navigator-DialogBox.png) 

5. En la ventana **Editor de consultas** , haga clic en botón **Editor avanzado** en la barra de herramientas.

    ![Botón Editor avanzado](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)

6. En el cuadro de diálogo Editor avanzado, el número junto a "Origen" es el índice.

    ![Editor - avanzado de índice](./media/data-factory-web-table-connector/AdvancedEditor-Index.png) 


Si está utilizando Excel 2013, utilice [Microsoft Power Query para Excel](https://www.microsoft.com/download/details.aspx?id=39379) para obtener el índice. Consulte el artículo de [conectarse a una página web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) para obtener información detallada. Los pasos son similares si usa [Microsoft Power BI para escritorio](https://powerbi.microsoft.com/desktop/). 

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener información acerca de los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure y distintas formas de optimizar.
