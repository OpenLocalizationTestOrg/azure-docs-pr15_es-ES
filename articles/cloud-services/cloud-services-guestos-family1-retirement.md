<properties
   pageTitle="Familia de SO invitado 1 jubilación Observe | Microsoft Azure"
   description="Proporciona información sobre cómo determinar si se ven afectadas y cuando ocurrió la jubilación Azure invitado OS familia 1"
   services="cloud-services"
   documentationCenter="na"
   authors="raiye"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/24/2016"
   ms.author="raiye"/>



# <a name="guest-os-family-1-retirement-notice"></a>Aviso de retirada de invitado OS familia 1

Retirada de OS familia 1 se presentó por primera vez en el 1 de junio de 2013.

**2 de septiembre de 2014** El sistema operativo de invitado Azure (SO invitado) familia 1.x, que se basa en el sistema operativo Windows Server 2008, se retiró oficialmente. Todos los intentos de implementar nuevos servicios o actualizar con 1 de la familia de servicios existentes se provocarán un mensaje de error que le informa de que se ha retirado el 1 de familia de sistema operativo de invitado.

**3 de noviembre de 2014** Finalización del soporte técnico extendido para invitado OS familia 1 y se ha retirado completamente. Todos los servicios continúa en la familia 1 se verán afectados. Nos podemos detener estos servicios en cualquier momento. No hay ninguna garantía que sus servicios seguirán ejecutándose a menos que manualmente actualizarlos usted mismo.

Si tiene preguntas adicionales, visite los [Foros de servicios de nube](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) o [póngase en contacto con soporte técnico de Azure](https://azure.microsoft.com/support/options/).




## <a name="are-you-affected"></a>¿Están afectados?

Los servicios de nube se ven afectados si se aplica uno de estos procedimientos:

1. Tiene un valor de "osFamily ="1"especificado explícitamente en el archivo ServiceConfiguration.cscfg de su servicio de nube.
2. No tiene un valor para osFamily especificado explícitamente en el archivo ServiceConfiguration.cscfg de su servicio de nube. En la actualidad, el sistema utiliza el valor predeterminado de "1" en este caso.
3. El portal clásico Azure muestra el valor de familia de sistema operativo invitado como "Windows Server 2008".

Para buscar cuál de los servicios de nube ejecutan qué familia OS, puede ejecutar la siguiente secuencia de comandos en Azure PowerShell, aunque primero debe [Configurar Azure PowerShell](../powershell-install-configure.md) . Para obtener más información sobre la secuencia de comandos, consulte [Azure invitado OS familia 1 final del ciclo de vida: junio de 2014](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx). 

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Los servicios de nube se verán afectados por jubilación OS familia 1 si la columna osFamily en los resultados de la secuencia de comandos está vacía o contiene un "1".

## <a name="recommendations-if-you-are-affected"></a>Recomendaciones si se afectado

Se recomienda que migrar los roles de servicio de nube a una de las familias de sistema operativo de invitado compatibles:

**SO invitado 4.x familia** -Windows Server 2012 R2 *(recomendado)*

1. Asegúrese de que la aplicación está utilizando SDK 2.1 o posterior con .NET framework 4.0, 4.5 o 4.5.1.
2. Establecer el atributo osFamily en "4" en el archivo ServiceConfiguration.cscfg y volver a implementar su servicio de nube.


**SO invitado 3.x familia** -Windows Server 2012

1. Asegúrese de que la aplicación está utilizando SDK 1,8 o posterior con .NET framework 4.0 o 4.5.
2. Establecer el atributo osFamily a "3" en el archivo ServiceConfiguration.cscfg y volver a implementar su servicio de nube.


**SO invitado 2.x familia** -Windows Server 2008 R2

1. Asegúrese de que la aplicación está utilizando SDK 1.3 y superior con .NET framework 3.5 o 4.0.
2. Establecer el atributo osFamily a "2" en el archivo ServiceConfiguration.cscfg y volver a implementar su servicio de nube.


## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Finalización del soporte técnico extendido para invitado OS familia 1 3 de noviembre de 2014
Servicios de nube de familia SO invitado 1 ya no son compatibles. Migrar el desactivar familia 1 tan pronto como sea posible para evitar la interrupción del servicio.  

## <a name="next-steps"></a>Pasos siguientes
Revise la última [libera SO invitado](cloud-services-guestos-update-matrix.md).
