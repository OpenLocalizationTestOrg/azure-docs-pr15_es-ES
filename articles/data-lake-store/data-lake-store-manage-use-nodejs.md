<properties 
   pageTitle="Introducción a Azure datos lago almacena con Azure SDK para Node.js | Microsoft Azure"
   description="Obtenga información sobre cómo usar Node.js para trabajar con cuentas de la tienda de lago de datos y el sistema de archivos." 
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
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-sdk-for-nodejs"></a>Introducción a Azure datos lago tienda con Azure SDK para Node.js

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API DE REST](data-lake-store-get-started-rest-api.md)
- [CLI de Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)


Aprenda a usar el SDK de Azure para Node.js para crear una cuenta de la tienda de lago de datos de Azure y realizar operaciones básicas, como crear carpetas, cargar y descargar archivos de datos, elimine la cuenta, etcetera. Para obtener más información acerca de la tienda de lago de datos, vea [Información general de la tienda de lago datos](data-lake-store-overview.md). Actualmente, admite el SDK

  *  **Versión de Node.js: 0.10.0 o superior**
  *  **Versión de la API de REST de cuenta: vista previa de 2015-10-01**
  *  **Versión de la API de REST de sistema de archivos: vista previa de 2015-10-01**

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este artículo, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

- **Crear una aplicación de Azure Active Directory**. Utilizar la aplicación de Azure AD para autenticar la aplicación de almacenamiento de lago de datos con Azure AD. Existen diferentes métodos para autenticar con Azure AD, que son la **autenticación de usuario final** o **servicio de servicio**. Para obtener instrucciones y más información acerca de cómo autenticar, vea [autenticar con el almacén de datos de lago con Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="how-to-install"></a>Cómo instalar

```bash
npm install azure-arm-datalake-store
```

## <a name="authenticate-using-azure-active-directory"></a>Autenticar con Azure Active Directory

Los fragmentos de código siguiente muestran dos formas independientes de autenticación de almacén de datos de lago mediante Azure AD. Para obtener información detallada sobre los distintos métodos para usar para la autenticación con el almacén de datos de lago, vea [autenticar con el almacén de datos de lago con Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

El fragmento de código siguiente requiere también entradas como nombre de dominio de Azure AD, ID de cliente para una aplicación de Azure AD, etcetera. Todos estos detalles se pueden recuperar desde una Azure AD aplicación que deba creada, los detalles de la que también se incluyen en el vínculo anterior.

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-store-clients"></a>Crear a los clientes de almacén de datos lago

```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## <a name="create-a-data-lake-store-account"></a>Crear una cuenta de almacén de datos lago

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
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

## <a name="create-a-file-with-content"></a>Crear un archivo con el contenido
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.fileSystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## <a name="get-a-list-of-files-and-folders"></a>Obtener una lista de archivos y carpetas

```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.fileSystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Vea también

- [SDK de Microsoft Azure para Node.js](https://github.com/azure/azure-sdk-for-node)
- [SDK de Microsoft Azure para Node.js - administración de lago análisis de datos](https://www.npmjs.com/package/azure-arm-datalake-analytics)
