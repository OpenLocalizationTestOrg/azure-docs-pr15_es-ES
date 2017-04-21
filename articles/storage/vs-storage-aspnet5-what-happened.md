<properties
    pageTitle="¿Qué ha ocurrido con mi proyecto 5 ASP.NET (Visual Studio conectado servicios) | Almacenamiento de Azure"
    description="Describe lo que sucede después de conectarse a una cuenta de almacenamiento de Azure en un proyecto de Visual Studio, 5 ASP.NET con Visual Studio conectado servicios"
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

# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>¿Qué ha ocurrido con mi proyecto 5 ASP.NET (Visual Studio Azure almacenamiento conectado services)?

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

Además, se ha agregado el paquete de NuGet **Microsoft.Framework.Configuration.Json** .

## <a name="connection-string-for-azure-storage-added"></a>Cadena de conexión para el almacenamiento de Azure agregado
En el archivo config.json del proyecto, se crea un elemento con la cadena de conexión y la clave de la cuenta de almacenamiento seleccionado.

Para obtener más información, vea [5 ASP.NET](http://www.asp.net/vnext).
