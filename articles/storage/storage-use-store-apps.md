<properties
    pageTitle="Usar el almacenamiento de Azure en las aplicaciones de la tienda Windows | Microsoft Azure"
    description="Obtenga información sobre cómo crear una aplicación de la tienda de Windows que usa almacenamiento de blobs de Windows Azure, cola, tabla o archivo."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>
    
# <a name="how-to-use-azure-storage-in-windows-store-apps"></a>Cómo usar el almacenamiento de Azure en las aplicaciones de la tienda Windows

## <a name="overview"></a>Información general

Esta guía muestra cómo empezar a trabajar con el desarrollo de una aplicación de la tienda de Windows que utiliza el almacenamiento de Azure.

## <a name="download-required-tools"></a>Descargar herramientas necesarias

- [Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx) facilita la generar, depurar, adaptar, paquete y distribuir aplicaciones de la tienda Windows. Se requiere Visual Studio 2012 o posterior.
- La [Biblioteca de cliente de almacenamiento de Azure](https://www.nuget.org/packages/WindowsAzure.Storage) proporciona una biblioteca de clases de tiempo de ejecución de Windows para trabajar con el almacenamiento de Azure.
- [Herramientas de WCF datos Services para Windows Store aplicaciones](http://www.microsoft.com/download/details.aspx?id=30714) extiende la experiencia de agregar referencia de servicio con el soporte técnico de OData de cliente para las aplicaciones de la tienda Windows de Visual Studio.

## <a name="develop-apps"></a>Desarrolle aplicaciones

### <a name="getting-ready"></a>Preparación

Crear un nuevo proyecto de aplicación de la tienda Windows en Visual Studio 2012 o posterior:

![almacén de aplicaciones de almacenamiento y proyecto][store-apps-storage-vs-project]

A continuación, agregue una referencia a la biblioteca de cliente de almacenamiento de Azure haciendo clic en **referencias**, haciendo clic en **Agregar referencia**y, a continuación, buscando almacenamiento cliente biblioteca de tiempo de ejecución Windows que ha descargado:

![almacén de aplicaciones de almacenamiento-elija-Biblioteca][store-apps-storage-choose-library]

### <a name="using-the-library-with-the-blob-and-queue-services"></a>Uso de la biblioteca con los servicios de cola y Blob

En este momento, la aplicación esté lista para llamar a los servicios de blobs de Windows Azure y cola. Agregue las siguientes instrucciones **using** para que se pueden hacer referencia directamente a los tipos de almacenamiento de Azure:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

A continuación, agregue un botón a la página. Agregue el código siguiente al evento **Click** y modificar su método de controlador de eventos con la [palabra clave](http://msdn.microsoft.com/library/vstudio/hh156513.aspx):

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

Este código supone que tiene las variables de cadena dos, *nombre de la cuenta* y *accountKey*. Representan el nombre de su cuenta de almacenamiento y la clave de cuenta que está asociada a esa cuenta.

Crear y ejecutar la aplicación. Haga clic en el botón se comprobar si existe un contenedor denominado *container1* en su cuenta y crearlo si no.

### <a name="using-the-library-with-the-table-service"></a>Uso de la biblioteca con el servicio de tabla

Tipos que se usan para comunicarse con el servicio de la tabla de Azure dependen de WCF Data Services para la biblioteca de la aplicación de la tienda Windows. A continuación, agregue una referencia a las bibliotecas WCF necesarias mediante la consola del Administrador de paquetes:

![almacén de aplicaciones de almacenamiento paquete Administrador][store-apps-storage-package-manager]

Use el comando siguiente para el Administrador de paquetes de punto en la ubicación en su equipo:

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Este comando agregará automáticamente todas las referencias necesarias al proyecto. Si no desea usar la consola del Administrador de paquetes, puede agregar la carpeta NuGet de servicios de datos de WCF en su equipo local a la lista de orígenes de paquetes y, a continuación, agregue la referencia a través de la interfaz de usuario, como se describe en [Administrar paquetes de NuGet utilizando el cuadro de diálogo](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

Cuando se hace referencia el paquete NuGet de servicios de datos de WCF, cambie el código en el evento de **haga clic en** el botón:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

Este código comprueba si una tabla denominada *tabla1* existe en su cuenta y, a continuación, lo crea si no.

También puede agregar una referencia a Microsoft.WindowsAzure.Storage.Table.dll, que está disponible en el mismo paquete que ha descargado. Esta biblioteca contiene funciones adicionales, como serialización basada en reflexión y consultas genéricas. Tenga en cuenta que esta biblioteca no admite JavaScript.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png
