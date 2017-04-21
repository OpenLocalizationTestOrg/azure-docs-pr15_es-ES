<properties
 pageTitle="Referencia de Cmdlets de PowerShell de programador"
 description="Referencia de Cmdlets de PowerShell de programador"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-powershell-cmdlets-reference"></a>Referencia de Cmdlets de PowerShell de programador

En la tabla siguiente describe y vínculos a la página de referencia de cada uno de los cmdlets principales en el programador de Azure.

Para instalar Azure PowerShell y asociar con su suscripción de Azure, consulte [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md). 

Para obtener más información acerca de los [cmdlets del Administrador de recursos de Azure](https://msdn.microsoft.com/library/mt125356\(v=azure.200\).aspx), vea [Usar PowerShell de Azure con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md).

|Cmdlet|Descripción de cmdlet|
|---|---|
[Deshabilitar AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490133\(v=azure.200\).aspx) |Desactiva una colección de trabajo. 
[Habilitar AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490135\(v=azure.200\).aspx) |Habilita la colección de trabajo.
[Get-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490125\(v=azure.200\).aspx) |Obtiene los trabajos del programador.
[Get-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490132\(v=azure.200\).aspx) |Obtiene el trabajo colecciones.
[Get-AzureRmSchedulerJobHistory](https://msdn.microsoft.com/library/mt490126\(v=azure.200\).aspx) |Obtiene el historial de trabajos.
[Nueva AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490136\(v=azure.200\).aspx) |Crea un trabajo HTTP.
[Nueva AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490141\(v=azure.200\).aspx) |Crea una colección de trabajo.
[Nueva AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490134\(v=azure.200\).aspx) |Crea una tarea de cola de bus de servicio.
[Nueva AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490142\(v=azure.200\).aspx) |Crea un trabajo de tema de bus de servicio.
[Nueva AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490127\(v=azure.200\).aspx) |Crea una tarea de cola de almacenamiento. 
[Quitar AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490140\(v=azure.200\).aspx) |Quita una tarea del programador.  
[Quitar AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490131\(v=azure.200\).aspx) |Quita una colección de trabajo. 
[Establecer AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490130\(v=azure.200\).aspx) |Modifica un trabajo HTTP programador.
[Establecer AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490129\(v=azure.200\).aspx) |Modifica un conjunto de trabajo. 
[Establecer AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490143\(v=azure.200\).aspx) |Modifica una tarea de cola de bus de servicio.  
[Establecer AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490137\(v=azure.200\).aspx) |Modifica un trabajo de tema de bus de servicio. 
[Establecer AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490128\(v=azure.200\).aspx) |Modifica una tarea de cola de almacenamiento.   

Para obtener más información, puede ejecutar cualquiera de los siguientes cmdlets: 

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Vea también


 [¿Qué es programador?](scheduler-intro.md)

 [Azure programador conceptos, terminología y la jerarquía de entidad](scheduler-concepts-terms.md)

 [Empezar a usar a planificador en el portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en el programador de Azure](scheduler-plans-billing.md)

 [Referencia de API de REST de programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Azure programador de alta disponibilidad y confiabilidad](scheduler-high-availability-reliability.md)

 [Límites de programador Azure, valores predeterminados y códigos de error](scheduler-limits-defaults-errors.md)

 [Autenticación de salida de Azure programador](scheduler-outbound-authentication.md)
