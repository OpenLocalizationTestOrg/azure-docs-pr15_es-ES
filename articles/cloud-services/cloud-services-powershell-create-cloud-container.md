<properties
   pageTitle="Crear un contenedor de servicio de nube con PowerShell | Microsoft Azure"
   description="En este artículo se explica cómo crear un contenedor de servicio de nube con PowerShell. El contenedor aloja roles de web y trabajador."
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="cawa"/>

# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Utilice un comando de Azure PowerShell para crear un contenedor de servicio de nube vacío
En este artículo se explica cómo crear rápidamente un contenedor de servicios de nube usar cmdlets de PowerShell de Azure. Siga los pasos siguientes:

1. Instale el cmdlet de PowerShell de Microsoft Azure de la página de [descargas de PowerShell de Azure](http://aka.ms/webpi-azps) .
2. Abra el símbolo del sistema de PowerShell.
3. Use el [Complemento AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) para iniciar sesión.

    > [AZURE.NOTE] Para obtener más instrucciones sobre cómo instalar el cmdlet de PowerShell de Azure y conectarse a su suscripción de Azure, consulte [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

4. Usar el cmdlet **AzureService de nuevo** para crear un contenedor de servicio de nube de Azure vacío.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

5. Siga este ejemplo para invocar el cmdlet:
```
New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
```

Para obtener más información sobre la creación del servicio de nube de Azure, ejecute:
```
Get-help New-AzureService
```

### <a name="next-steps"></a>Pasos siguientes

 * Para administrar la implementación de servicio de nube, consulte los comandos [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Quitar AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx)y [Establecer AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx) . También puede hacer referencia a [cómo configurar los servicios de nube](cloud-services-how-to-configure.md) para obtener más información.

 * Para publicar el proyecto de servicio de nube en Azure, consulte el ejemplo de código de **PublishCloudService.ps1** de [entrega continua de servicio de nube en Azure](cloud-services-dotnet-continuous-delivery.md).
