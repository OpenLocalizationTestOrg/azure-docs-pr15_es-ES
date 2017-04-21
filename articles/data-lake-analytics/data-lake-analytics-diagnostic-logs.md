<properties 
   pageTitle="Ver registros de diagnóstico para el análisis de lago de datos de Azure | Microsoft Azure" 
   description="Comprender cómo configurar y tener acceso a los registros de diagnóstico para el análisis de lago de datos de Azure " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="Blackmist" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="08/11/2016"
   ms.author="larryfr"/>

# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Obtener acceso a registros de diagnóstico para el análisis de lago de datos de Azure

Obtenga información sobre cómo habilitar el registro de diagnóstico para su cuenta de análisis de datos lago y cómo ver los registros que se recopilan para su cuenta.

Organizaciones pueden habilitar el registro de diagnóstico para su cuenta de Azure datos lago análisis recopilar registros de auditoría de acceso de datos. Estos registros proporcionan información como:

* Una lista de usuarios que tiene acceso a los datos.
* ¿Con qué frecuencia se tiene acceso a los datos.
* La cantidad de datos se almacena en la cuenta.

## <a name="prerequisites"></a>Requisitos previos

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).
- **Habilitar la suscripción de Azure** de vista previa de datos lago análisis público. Vea [las instrucciones](data-lake-analytics-get-started-portal.md#signup).
- **Cuenta de azure datos lago análisis**. Siga las instrucciones en [Introducción a Azure datos lago análisis con el portal de Azure](data-lake-analytics-get-started-portal.md).

## <a name="enable-logging"></a>Habilitar el registro

1. Inicie sesión el nuevo [portal de Azure](https://portal.azure.com).

2. Abrir la cuenta de análisis de datos lago desde el módulo de análisis de datos lago cuenta, haga clic en **configuración**y, a continuación, haga clic en **Configuración de diagnóstico**.

3. En el módulo de **diagnóstico** , realice los cambios siguientes para configurar el registro de diagnóstico.

    ![Habilitar el registro de diagnóstico] (./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Habilitar los registros de diagnóstico")

    * Establecer **estado de** **en** para habilitar el registro de diagnóstico.
    * Puede elegir al proceso/almacén de los datos de dos formas diferentes.
        * Seleccione **Exportar a concentrador de evento** para transmitir datos de registro a un concentrador de evento de Azure. Use esta opción si tiene una canalización de procesamiento descendente para analizar los registros entrantes en tiempo real. Si selecciona esta opción, debe proporcionar los detalles para el concentrador de eventos de Azure que desea usar.
        * Seleccione **Exportar a la cuenta de almacenamiento** para almacenar los registros a una cuenta de almacenamiento de Azure. Use esta opción si desea archivar los datos. Si selecciona esta opción, debe proporcionar una cuenta de almacenamiento de Azure para guardar los registros a.
    * Especifique si desea obtener los registros de auditoría, los registros de solicitudes o ambos.
    * Especifique el número de días que se conservarán los datos.
    * Haga clic en **Guardar**.

Una vez que se ha habilitado la configuración de diagnóstico, puede ver los registros en la ficha **Registros de diagnóstico** .

## <a name="view-logs"></a>Ver registros

Hay dos formas de ver los datos del registro para su cuenta de análisis de datos lago.

* Desde la configuración de la cuenta de análisis de datos lago
* Desde la cuenta de almacenamiento de Azure donde se almacenan los datos

### <a name="using-the-data-lake-analytics-settings-view"></a>Usar la vista de configuración de análisis de datos lago

1. En el módulo de **configuración** de análisis de datos lago cuenta, haga clic en **Registros de diagnóstico**.

    ![Registro de diagnóstico] (./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs.png "Ver registros de diagnóstico") 

2. En el módulo de **Registros de diagnóstico** , debería ver los registros clasificados por **Registros de auditoría** y **Solicitar**.
    * Registros de solicitudes de capturan cada solicitud de API realizada en la cuenta de análisis de datos lago.
    * Registros de auditoría son similares a registros de solicitud pero proporciona un desglose de las operaciones que se realicen en la cuenta de análisis de datos lago mucho más detallado. Por ejemplo, una llamada a la API de carga única en registros de solicitud puede ocasionar varias operaciones "Anexar" en los registros de auditoría.

3. Haga clic en el vínculo **Descargar** para descargar los registros de una entrada de registro.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Desde la cuenta de almacenamiento de Azure que contiene los datos del registro

1. Abra el módulo de cuenta de Azure almacenamiento asociado con el análisis de datos lago para iniciar sesión y, a continuación, haga clic en BLOB. El módulo de **servicio de blobs de Windows** muestra dos contenedores.

    ![Registro de diagnóstico] (./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs-storage-account.png "Ver registros de diagnóstico")

    * El contenedor **registros de información de auditoría** contiene los registros de auditoría.
    * Las **solicitudes de registros de información** de contenedor contiene los registros de la solicitud.

2. Dentro de estos contenedores, los registros se almacenan en la siguiente estructura.

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json
    
    > [AZURE.NOTE] La `##` entradas de la ruta de acceso contienen el año, mes, día y hora en que se creó el registro. Análisis de datos lago se crea un archivo cada hora, así que `m=` siempre contiene un valor de `00`.

    Por ejemplo, podría ser la ruta de acceso completa a un registro de auditoría:
    
        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Del mismo modo, la ruta de acceso completa a un registro de solicitud podría ser:
    
        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Estructura de registro

Los registros de auditoría y solicitud están en formato JSON. En esta sección, se mire la estructura de JSON para la solicitud y registros de auditoría.

### <a name="request-logs"></a>Solicitud de registros

Aquí es un ejemplo de entrada en el registro de solicitud con formato JSON. Cada blob tiene un objeto de raíz denominado **registros** que contiene una matriz de objetos de registro.

    {
    "records": 
      [     
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Esquema de solicitud de registro

| Nombre            | Tipo   | Descripción                                                                    |
|-----------------|--------|--------------------------------------------------------------------------------|
| hora            | Cadena | La marca de tiempo (en UTC) del registro                                              |
| resourceId      | Cadena | Coloque el identificador del recurso que se realizó la operación en                            |
| categoría        | Cadena | La categoría de registro. Por ejemplo, **las solicitudes**.                                   |
| operationName   | Cadena | Nombre de la operación que ha iniciado sesión. Por ejemplo, GetAggregatedJobHistory.              |
| resultType      | Cadena | El estado de la operación, por ejemplo, 200.                                 |
| callerIpAddress | Cadena | La dirección IP del cliente que realiza la solicitud                                |
| correlationId   | Cadena | El identificador del registro. Este valor puede usarse para agrupar un conjunto de entradas de registro relacionados |
| identidad        | Objeto | La identidad que generó el registro                                            |
| propiedades      | JSON   | Vea la sección siguiente (esquema de propiedades del registro de solicitud) para obtener más información |

#### <a name="request-log-properties-schema"></a>Esquema de propiedades de registro de solicitud

| Nombre                 | Tipo   | Descripción                                               |
|----------------------|--------|-----------------------------------------------------------|
| HttpMethod           | Cadena | Método HTTP utilizado para la operación. Por ejemplo, obtener. |
| Ruta de acceso                 | Cadena | La ruta de la operación se realizó en                   |
| RequestContentLength | int    | La longitud del contenido de la solicitud HTTP                    |
| ClientRequestId      | Cadena | El identificador que identifica esta solicitud              |
| Hora de inicio            | Cadena | La hora en que el servidor recibió la solicitud         |
| Hora de finalización              | Cadena | La hora a la que el servidor envía una respuesta              |

### <a name="audit-logs"></a>Registros de auditoría

Aquí es un ejemplo de entrada en el registro de auditoría con formato JSON. Cada blob tiene un objeto de raíz denominado **registros** que contiene una matriz de objetos de registro

    {
    "records": 
      [     
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job", 
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Esquema de registro de auditoría

| Nombre            | Tipo   | Descripción                                                                    |
|-----------------|--------|--------------------------------------------------------------------------------|
| hora            | Cadena | La marca de tiempo (en UTC) del registro                                              |
| resourceId      | Cadena | Coloque el identificador del recurso que se realizó la operación en                            |
| categoría        | Cadena | La categoría de registro. Por ejemplo, **auditoría**.                                      |
| operationName   | Cadena | Nombre de la operación que ha iniciado sesión. Por ejemplo, JobSubmitted.              |
| resultType | Cadena | Un subestado para el estado del trabajo (operationName). |
| resultSignature | Cadena | Obtener más información sobre el estado del trabajo (operationName). |
| identidad      | Cadena | El usuario que solicita la operación. Por ejemplo, susan@contoso.com.                                 |
| propiedades      | JSON   | Vea la sección siguiente (esquema de propiedades del registro de auditoría) para obtener más información |

> [AZURE.NOTE] __resultType__ __resultSignature__ proporcionan información sobre el resultado de una operación y solo contengan un valor si se ha completado una operación. Por ejemplo, que contienen un valor cuando __operationName__ contiene un valor de __JobStarted__ o __JobEnded__.

#### <a name="audit-log-properties-schema"></a>Esquema de propiedades de registro de auditoría

| Nombre       | Tipo   | Descripción                              |
|------------|--------|------------------------------------------|
| Id. | Cadena | El identificador asignado a la tarea  |
| NombreDeTrabajo | Cadena | El nombre que se proporcionó para el trabajo |
| JobRunTime | Cadena | El tiempo de ejecución utilizado para procesar el trabajo |
| SubmitTime | Cadena | El tiempo (en UTC) que se envió el trabajo |
| Hora de inicio | Cadena | El tiempo que inicia el trabajo ejecutando después de la presentación (en UTC). |
| Hora de finalización | Cadena | La hora de finalización de la tarea. |
| Paralelismo | Cadena | El número de unidades de análisis de datos lago solicitada para este trabajo durante la presentación. |

> [AZURE.NOTE] __SubmitTime__, __hora de inicio__, __hora de finalización__ y __paralelismo__ proporcionan información sobre una operación y solo contengan un valor si una operación ha comenzado o finalizado. Por ejemplo, __SubmitTime__ contiene un valor después __operationName__ indica __JobSubmitted__.

## <a name="process-the-log-data"></a>Proceso de los datos del registro

Análisis de lago datos Azure proporciona una muestra procesar y analizar los datos del registro. Puede encontrar los datos de ejemplo en [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 


## <a name="next-steps"></a>Pasos siguientes

- [Información general de análisis de datos de Azure lago](data-lake-analytics-overview.md)


