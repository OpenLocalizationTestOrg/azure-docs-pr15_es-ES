<properties
    pageTitle="¿Qué ha ocurrido con mi proyecto de servicio de nube? | Microsoft Azure | Servicios visuales Studio conectado"
    description="Describe lo que sucede en un proyecto de servicios de nube después de conectarse a una cuenta de almacenamiento de Azure mediante Visual Studio conectado servicios"
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

# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>¿Qué ha ocurrido con mi proyecto de servicios de nube (Visual Studio Azure almacenamiento conectado servicio)?

## <a name="references-added"></a>Referencias agregadas

El paquete de Azure almacenamiento NuGet se ha agregado a su proyecto de Visual Studio.  
Este paquete agrega las referencias de .NET siguientes:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Cadena de conexión para el almacenamiento de Azure agregado
Elementos creados con la cadena de conexión y la clave de la cuenta de almacenamiento seleccionado. Las modificaciones se han realizado en los archivos siguientes:

- **ServiceDefinition.csdef**
- **ServiceConfiguration.Cloud.cscfg**
- **ServiceConfiguration.Local.cscfg**
