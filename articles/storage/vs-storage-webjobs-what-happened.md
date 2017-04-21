<properties
    pageTitle="¿Qué ha ocurrido con mi proyecto WebJob (Visual Studio Azure almacenamiento conectado servicio)? | Microsoft Azure"
    description="¿Qué ha ocurrido en un proyecto de Azure WebJob después de conectarse a una cuenta de almacenamiento mediante Visual Studio conectado servicios se describen"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>¿Qué ha ocurrido con mi proyecto WebJob (Visual Studio Azure almacenamiento conectado servicio)?

## <a name="references-added"></a>Referencias agregadas

El paquete de Azure almacenamiento NuGet se agregó a o se actualice en el proyecto de Visual Studio.  
Este paquete agrega las referencias de .NET siguientes:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Cadena de conexión para el almacenamiento de Azure agregado
En el archivo App.config del proyecto, las entradas de **AzureWebJobsStorage** y **AzureWebJobsDashboard** se actualizaron con la cadena de conexión de la cuenta de almacenamiento seleccionado y clave.

Para obtener más información, consulte [los recursos de documentación WebJobs de Azure](http://go.microsoft.com/fwlink/?linkid=390226).
