<properties
   pageTitle="Administrar el análisis de lago de datos de Azure con Azure SDK para Node.js | Azure"
   description="Obtenga información sobre cómo administrar cuentas de análisis de datos lago, orígenes de datos, los trabajos y los usuarios con Azure SDK para Node.js"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Administrar el análisis de lago de datos de Azure con Azure SDK para Node.js


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

El SDK de Azure para Node.js puede utilizarse para la administración de cuentas, los trabajos y catálogos de análisis de lago de datos de Azure. Para ver el tema de administración con otras herramientas, haga clic en la ficha, seleccione anterior.

Ahora es compatible con:

  *  **Versión de Node.js: 0.10.0 o superior**
  *  **Versión de la API de REST de cuenta: vista previa de 2015-10-01**
  *  **Versión de la API de REST de catálogo: vista previa de 2015-10-01**
  *  **Versión de la API de REST de trabajo: vista previa 2016-03-20**

## <a name="features"></a>Características

- Administración de cuentas: crear, obtener, lista, actualizar y eliminar.
- Administración del trabajo: enviar, obtener, lista, Cancelar.
- Administración de catálogos: obtener, lista, crear (información confidencial), actualizar (información confidencial), eliminar (información confidencial).

## <a name="how-to-install"></a>Cómo instalar

```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>Autenticar con Azure Active Directory

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>Crear al cliente de análisis de datos lago

```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Crear una cuenta de análisis de datos lago

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-jobs"></a>Obtener una lista de tareas

```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>Obtener una lista de bases de datos en el catálogo de análisis de datos lago
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Vea también

- [SDK de Microsoft Azure para Node.js](https://github.com/azure/azure-sdk-for-node)
- [SDK de Microsoft Azure para Node.js - administración de almacén de datos lago](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)
