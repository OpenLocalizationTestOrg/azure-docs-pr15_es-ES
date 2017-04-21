<properties 
   pageTitle="Ver registros de diagnóstico para el almacén de lago de datos de Azure | Microsoft Azure" 
   description="Comprender cómo configurar y tener acceso a los registros de diagnóstico para el almacén de lago de datos de Azure " 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Obtener acceso a los registros de diagnóstico para el almacén de lago de datos de Azure

Obtenga información sobre cómo habilitar el registro de diagnóstico para su cuenta de almacén de datos lago y cómo ver los registros que se recopilan para su cuenta.

Las organizaciones pueden habilitar el registro de diagnóstico para su cuenta de almacén de lago de datos de Azure recopilar registros de auditoría de acceso de datos que proporciona información como la lista de usuarios acceso a los datos, con qué frecuencia se tiene acceso a los datos, la cantidad de datos se almacena en la cuenta, etcetera.

## <a name="prerequisites"></a>Requisitos previos

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

- **Cuenta de la tienda de lago de datos de azure**. Siga las instrucciones en [Introducción a Azure datos lago tienda con el Portal de Azure](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>Habilitar el registro de diagnóstico para su cuenta de almacén de datos lago

1. Inicie sesión el nuevo [Portal de Azure](https://portal.azure.com).

2. Abrir la cuenta de almacén de datos lago desde el módulo de almacén de datos lago cuenta, haga clic en **configuración**y, a continuación, haga clic en **Configuración de diagnóstico**.

3. En el módulo de **diagnóstico** , realice los cambios siguientes para configurar el registro de diagnóstico.

    ![Habilitar el registro de diagnóstico] (./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Habilitar los registros de diagnóstico")

    * Establecer **estado de** **en** para habilitar el registro de diagnóstico.
    * Puede elegir al proceso/almacén de los datos de dos formas diferentes.
        * Seleccione la opción para **Exportar a concentrador de evento** para transmitir datos de registro a un concentrador de evento de Azure. Es muy probable que se use esta opción si tiene una canalización de procesamiento descendente para analizar los registros entrantes en tiempo real. Si selecciona esta opción, debe proporcionar los detalles para el concentrador de eventos de Azure que desea usar.
        * Seleccione la opción para **Exportar a la cuenta de almacenamiento** para almacenar los registros a una cuenta de almacenamiento de Azure. Use esta opción si desea archivar los datos que se procesan por lotes en una fecha posterior. Si selecciona esta opción debe proporcionar una cuenta de almacenamiento de Azure para guardar los registros a.
    * Especifique si desea obtener los registros de auditoría, los registros de solicitudes o ambos.
    * Especifique el número de días que se conservarán los datos.
    * Haga clic en **Guardar**.

Una vez que se ha habilitado la configuración de diagnóstico, puede ver los registros en la ficha **Registros de diagnóstico** .

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>Ver registros de diagnóstico para su cuenta de almacén de datos lago

Hay dos formas de ver los datos del registro para su cuenta de almacén de datos lago.

* Desde la vista de configuración de cuenta de almacén de datos lago
* Desde la cuenta de almacenamiento de Azure donde se almacenan los datos

### <a name="using-the-data-lake-store-settings-view"></a>Usar la vista de la configuración del almacenamiento de datos lago

1. En el módulo de **configuración** de cuenta de almacén de lago de datos, haga clic en **Registros de diagnóstico**.

    ![Registro de diagnóstico] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Ver registros de diagnóstico") 

2. En el módulo de **Registros de diagnóstico** , debería ver los registros clasificados por **Registros de auditoría** y **Solicitar**.
    * Registros de solicitudes de capturan cada solicitud de API realizada en la cuenta de almacén de datos lago.
    * Registros de auditoría son similares a registros de solicitud pero proporciona un desglose de las operaciones que se realicen en la cuenta de almacén de datos lago mucho más detallado. Por ejemplo, una llamada a la API de carga única en registros de solicitud puede ocasionar varias operaciones "Anexar" en los registros de auditoría.

3. Haga clic en el vínculo **Descargar** frente a cada entrada de registro para descargar los registros.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Desde la cuenta de almacenamiento de Azure que contiene los datos del registro

1. Abra el módulo de cuenta de Azure almacenamiento asociado con el almacén de datos de lago para iniciar sesión y, a continuación, haga clic en BLOB. El módulo de **servicio de blobs de Windows** muestra dos contenedores.

    ![Registro de diagnóstico] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Ver registros de diagnóstico")

    * El contenedor **registros de información de auditoría** contiene los registros de auditoría.
    * Las **solicitudes de registros de información** de contenedor contiene los registros de la solicitud.

2. Dentro de estos contenedores, los registros se almacenan en la siguiente estructura.

    ![Registro de diagnóstico] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Ver registros de diagnóstico")

    Por ejemplo, podría ser la ruta de acceso completa a un registro de auditoría`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    Similar, podría ser la ruta de acceso completa a un registro de solicitud`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Comprender la estructura de los datos del registro

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
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
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
| operationName   | Cadena | Nombre de la operación que ha iniciado sesión. Por ejemplo, getfilestatus.              |
| resultType      | Cadena | El estado de la operación, por ejemplo, 200.                                 |
| callerIpAddress | Cadena | La dirección IP del cliente que realiza la solicitud                                |
| correlationId   | Cadena | El identificador de registro que puede utiliza para agrupar un conjunto de entradas de registro relacionadas |
| identidad        | Objeto | La identidad que generó el registro                                            |
| propiedades      | JSON   | Consulte a continuación para obtener más información                                                          |

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
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
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
| operationName   | Cadena | Nombre de la operación que ha iniciado sesión. Por ejemplo, getfilestatus.              |
| resultType      | Cadena | El estado de la operación, por ejemplo, 200.                                 |
| correlationId   | Cadena | El identificador de registro que puede utiliza para agrupar un conjunto de entradas de registro relacionadas |
| identidad        | Objeto | La identidad que generó el registro                                            |
| propiedades      | JSON   | Consulte a continuación para obtener más información                                                          |

#### <a name="audit-log-properties-schema"></a>Esquema de propiedades de registro de auditoría

| Nombre       | Tipo   | Descripción                              |
|------------|--------|------------------------------------------|
| StreamName | Cadena | La ruta de la operación se realizó en  |


## <a name="samples-to-process-the-log-data"></a>Ejemplos de procesar los datos del registro

Almacén de lago de datos de Azure proporciona una muestra procesar y analizar los datos del registro. Puede encontrar los datos de ejemplo en [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 


## <a name="see-also"></a>Vea también

- [Introducción a Azure lago de almacén de datos](data-lake-store-overview.md)
- [Proteger los datos en el almacén de datos de lago](data-lake-store-secure-data.md)

