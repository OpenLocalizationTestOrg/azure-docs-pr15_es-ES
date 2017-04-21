<properties 
   pageTitle="Introducción a análisis de lago de datos con la API de REST | Microsoft Azure" 
   description="Utilizar las API de REST de WebHDFS para realizar operaciones de análisis de datos lago" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/19/2016"
   ms.author="jgao"/>

# <a name="get-started-with-azure-data-lake-analytics-using-rest-apis"></a>Introducción a análisis de lago de datos de Azure con las API de REST

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Obtenga información sobre cómo usar las API de REST de WebHDFS y API de REST de análisis de datos lago para administrar cuentas de análisis de datos lago, trabajos y catálogo. 

## <a name="prerequisites"></a>Requisitos previos

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

- **Crear una aplicación de Azure Active Directory**. Utilizar la aplicación de Azure AD para autenticar la aplicación de análisis de datos lago con Azure AD. Existen diferentes métodos para autenticar con Azure AD, que son la **autenticación de usuario final** o **servicio de servicio**. Para obtener instrucciones y más información acerca de cómo autenticar, vea [autenticar con datos lago análisis con Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

- [doblez](http://curl.haxx.se/). En este artículo utiliza doblez para demostrar cómo realizar llamadas de la API de REST con una cuenta de análisis de datos lago.

## <a name="authenticate-with-azure-active-directory"></a>Autenticar con Azure Active Directory

Existen dos métodos para autenticar con Azure Active Directory.

### <a name="end-user-authentication-interactive"></a>Autenticación de usuario final (interactiva)

Con este método, aplicación pide al usuario que inicie sesión y todas las operaciones que se realizan en el contexto del usuario. 

Siga estos pasos para la autenticación interactiva:

1. A través de la aplicación, redirija al usuario a la siguiente URL:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<URI de REDIRECCIÓN > es necesario codificar para su uso en una dirección URL. Por lo tanto, para https://localhost, use `https%3A%2F%2Flocalhost`)

    Para el propósito de este tutorial, puede reemplazar los valores de marcador de posición de la dirección URL que arriba y péguelos en la barra de direcciones del explorador web. Se le redirigirá para autenticar con el inicio de sesión de Azure. Una vez correctamente inicie sesión, la respuesta se muestra en la barra de direcciones del explorador. La respuesta será el siguiente formato:
        
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Capturar el código de autorización de la respuesta. En este tutorial, puede copiar el código de autorización de la barra de direcciones del explorador web y pasar en la convocatoria de la publicación de al extremo token, tal como se muestra a continuación:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

    >[AZURE.NOTE] En este caso, el \<REDIRECCIÓN URI > no se necesita codificar.

3. La respuesta es un objeto JSON que contiene un token de acceso (por ejemplo, `"access_token": "<ACCESS_TOKEN>"`) y un símbolo de actualización (por ejemplo, `"refresh_token": "<REFRESH_TOKEN>"`). La aplicación utiliza el token de acceso al acceder a la tienda de lago de datos de Azure y el token de actualización para obtener otro token de acceso cuando expira un token de acceso.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  Cuando expira el token de acceso, puede solicitar un nuevo token de acceso mediante el token de actualización, tal como se muestra a continuación:

         curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
            -F grant_type=refresh_token \
            -F resource=https://management.core.windows.net/ \
            -F client_id=<CLIENT-ID> \
            -F refresh_token=<REFRESH-TOKEN>
 
Para obtener más información sobre la autenticación de usuario interactivo, vea el [código de autorización conceder flujo](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Servicio de servicio de autenticación (no interactiva)

Con este método, la aplicación proporciona sus propias credenciales para realizar las operaciones. Para ello, se debe enviar una solicitud de publicación como el que se muestra a continuación: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

El resultado de esta solicitud incluirá un token de autorización (indicado por `access-token` en el siguiente resultado) que pasará posteriormente con las llamadas de la API de REST. Guardar este token de autenticación en un archivo de texto; lo necesitará más adelante en este artículo.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Este artículo se utiliza la **no interactivo** enfoque. Para obtener más información sobre no interactivo (llamadas del servicio de servicio), vea [servicio a llamadas de servicio con las credenciales](https://msdn.microsoft.com/library/azure/dn645543.aspx).
## <a name="create-a-data-lake-analytics-account"></a>Crear una cuenta de análisis de datos lago

Debe crear un grupo de recursos de Azure y una cuenta de almacén de datos lago antes de poder crear una cuenta de análisis de datos lago.  Consulte [crear una cuenta de almacén de datos de lago](../data-lake-store/data-lake-store-get-started-rest-api.md#create-a-data-lake-store-account).

El comando Curl siguiente muestra cómo crear una cuenta:

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<NewAzureDataLakeAnalyticsAccountName>?api-version=2016-11-01 -d@"C:\tutorials\adla\CreateDataLakeAnalyticsAccountRequest.json"

Reemplazar \< `REDACTED` \> con el token de autorización \< `AzureSubscriptionID` \> con su identificador de suscripción, \< `AzureResourceGroupName` \> con un nombre de grupo de recursos de Azure existente y \< `NewAzureDataLakeAnalyticsAccountName` \> con un nuevo nombre de cuenta de análisis de datos lago. La carga de solicitud para este comando se encuentra en el archivo **CreateDatalakeAnalyticsAccountRequest.json** que se proporcionó para la `-d` parámetro anteriormente. El contenido del archivo input.json similar al siguiente:

    {  
        "location": "East US 2",  
        "name": "myadla1004",  
        "tags": {},  
        "properties": {  
            "defaultDataLakeStoreAccount": "my1004store",  
            "dataLakeStoreAccounts": [  
                {  
                    "name": "my1004store"  
                }     
            ]
        }  
    }  


## <a name="list-data-lake-analytics-accounts-in-a-subscription"></a>Cuentas de análisis de lago de datos de lista en una suscripción

El siguiente comando Curl muestra cómo enumerar las cuentas de una suscripción:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/providers/Microsoft.DataLakeAnalytics/Accounts?api-version=2016-11-01

Reemplazar \< `REDACTED` \> con el token de autorización \< `AzureSubscriptionID` \> con su identificador de suscripción. El resultado es similar a:

    {
        "value": [
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla0831.azuredatalakeanalytics.net",
                "accountId": "21e74660-0941-4880-ae72-b143c2615ea9",
                "creationTime": "2016-09-01T12:49:12.7451428Z",
                "lastModifiedTime": "2016-09-01T12:49:12.7451428Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla0831rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla0831",
            "name": "myadla0831",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            },
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla1004.azuredatalakeanalytics.net",
                "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
                "creationTime": "2016-10-04T20:46:42.287147Z",
                "lastModifiedTime": "2016-10-04T20:46:42.287147Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
            "name": "myadla1004",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            }
        ]
    }

## <a name="get-information-about-a-data-lake-analytics-account"></a>Obtener información acerca de una cuenta de análisis de datos lago

El comando Curl siguiente muestra cómo obtener la información de una cuenta:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>?api-version=2015-11-01

Reemplazar \< `REDACTED` \> con el token de autorización \< `AzureSubscriptionID` \> con su identificador de suscripción, \< `AzureResourceGroupName` \> con un nombre de grupo de recursos de Azure existente y \< `DataLakeAnalyticsAccountName` \> con el nombre de una cuenta de análisis de lago de datos existente. El resultado es similar a:

    {
        "properties": {
            "defaultDataLakeStoreAccount": "my1004store",
            "dataLakeStoreAccounts": [
            {
                "properties": {
                "suffix": "azuredatalakestore.net"
                },
                "name": "my1004store"
            }
            ],
            "provisioningState": "Creating",
            "state": null,
            "endpoint": null,
            "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
            "creationTime": null,
            "lastModifiedTime": null
        },
        "location": "East US 2",
        "tags": {},
        "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
        "name": "myadla1004",
        "type": "Microsoft.DataLakeAnalytics/accounts"
    }

## <a name="list-data-lake-stores-of-a-data-lake-analytics-account"></a>Lista de datos lago almacena de una cuenta de análisis de datos lago

El siguiente comando Curl muestra cómo almacena lago de datos de una cuenta de la lista:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>/DataLakeStoreAccounts/?api-version=2016-11-01

Reemplazar \< `REDACTED` \> con el token de autorización \< `AzureSubscriptionID` \> con su identificador de suscripción, \< `AzureResourceGroupName` \> con un nombre de grupo de recursos de Azure existente y \< `DataLakeAnalyticsAccountName` \> con el nombre de una cuenta de análisis de lago de datos existente. El resultado es similar a:

    {
        "value": [
            {
            "properties": {
                "suffix": "azuredatalakestore.net"
            },
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004/dataLakeStoreAccounts/my1004store",
            "name": "my1004store",
            "type": "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts"
            }
        ]
    }

## <a name="submit-u-sql-jobs"></a>Enviar trabajos U SQL

El siguiente comando Curl muestra cómo enviar un trabajo U SQL:

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs/<NewGUID>?api-version=2016-03-20-preview -d@"C:\tutorials\adla\SubmitADLAJob.json"

Reemplazar \< `REDACTED` \> con el token de autorización \< `DataLakeAnalyticsAccountName` \> con el nombre de una cuenta de análisis de lago de datos existente. La carga de solicitud para este comando se encuentra en el archivo **SubmitADLAJob.json** que se proporcionó para la `-d` parámetro anteriormente. El contenido del archivo input.json similar al siguiente:

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "properties": {
            "type": "USql",
            "script": "@searchlog =\n    EXTRACT UserId          int,\n            Start           DateTime,\n            Region          string,\n            Query          
        string,\n            Duration        int?,\n            Urls            string,\n            ClickedUrls     string\n    FROM \"/Samples/Data/SearchLog.tsv\"\n    US
        ING Extractors.Tsv();\n\nOUTPUT @searchlog   \n    TO \"/Output/SearchLog-from-Data-Lake.csv\"\nUSING Outputters.Csv();"
        }
    }

El resultado es similar a:

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "myadl@SPI",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "2016-10-05T13:54:59.9871859+00:00",
        "state": "Compiling",
        "result": "Succeeded",
        "stateAuditRecords": [
            {
            "newState": "New",
            "timeStamp": "2016-10-05T13:54:59.9871859+00:00",
            "details": "userName:myadl@SPI;submitMachine:N/A"
            }
        ],
        "properties": {
            "owner": "myadl@SPI",
            "resources": [],
            "runtimeVersion": "default",
            "rootProcessNodeId": "00000000-0000-0000-0000-000000000000",
            "algebraFilePath": "adl://myadls0831.azuredatalakestore.net/system/jobservice/jobs/Usql/2016/10/05/13/54/8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a/algebra.xml",
            "compileMode": "Semantic",
            "errorSource": "Unknown",
            "totalCompilationTime": "PT0S",
            "totalPausedTime": "PT0S",
            "totalQueuedTime": "PT0S",
            "totalRunningTime": "PT0S",
            "type": "USql"
        }
    }


## <a name="list-u-sql-jobs"></a>Lista U SQL trabajos

El siguiente comando Curl muestra cómo la lista de trabajos U SQL:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs?api-version=2016-11-01 

Reemplazar \< `REDACTED` \> con el token de autorización y \< `DataLakeAnalyticsAccountName` \> con el nombre de una cuenta de análisis de lago de datos existente. 


El resultado es similar a:

    {
    "value": [
        {
        "jobId": "65cf1691-9dbe-43cd-90ed-1cafbfb406fb",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someone@microsoft.com",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:46:53 GMT",
        "startTime": "Wed, 05 Oct 2016 13:47:33 GMT",
        "endTime": "Wed, 05 Oct 2016 13:48:07 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        },
        {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someoneadl@SPI",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:54:59 GMT",
        "startTime": "Wed, 05 Oct 2016 13:55:43 GMT",
        "endTime": "Wed, 05 Oct 2016 13:56:11 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        }
    ],
    "nextLink": null,
    "count": null
    }


## <a name="get-catalog-items"></a>Obtener los elementos del catálogo

El comando Curl siguiente muestra cómo obtener las bases de datos del catálogo:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/catalog/usql/databases?api-version=2016-11-01

El resultado es similar a:

    {
    "@odata.context":"https://myadla0831.azuredatalakeanalytics.net/sqlip/$metadata#databases","value":[
        {
        "computeAccountName":"myadla0831","databaseName":"mytest","version":"f6956327-90b8-4648-ad8b-de3ff09274ea"
        },{
        "computeAccountName":"myadla0831","databaseName":"master","version":"e8bca908-cc73-41a3-9564-e9bcfaa21f4e"
        }
    ]
    }

## <a name="see-also"></a>Vea también

- Para ver una consulta más compleja, vea [registros de sitio Web de analizar mediante el análisis de lago de datos de Azure](data-lake-analytics-analyze-weblogs.md).
- Para empezar a desarrollar aplicaciones SQL U, vea [secuencias de comandos SQL de U desarrollar mediante datos lago Tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obtener U SQL, consulte [Introducción a Azure datos lago análisis U-SQL idioma](data-lake-analytics-u-sql-get-started.md).
- Para las tareas de administración, vea [administrar el análisis de Azure datos lago con Azure portal](data-lake-analytics-manage-use-portal.md).
- Para obtener una visión general de análisis de lago de datos, vea [información general de análisis de lago de datos de Azure](data-lake-analytics-overview.md).
- Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestaña en la parte superior de la página.
- Para registrar información de diagnóstico, consulte [registros de diagnósticos de acceso para el análisis de lago de datos de Azure](data-lake-analytics-diagnostic-logs.md)
