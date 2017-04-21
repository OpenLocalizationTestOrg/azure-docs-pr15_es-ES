<properties
   pageTitle="Varias VIP de un servicio de nube"
   description="Información general sobre multiVIP y cómo configurar a varias VIP en un servicio de nube"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="configure-multiple-vips-for-a-cloud-service"></a>Configurar a varios VIP de un servicio de nube

Para obtener acceso a servicios en la nube Azure sobre Internet público usando una dirección IP proporcionada por Azure. Esta dirección IP pública se conoce como una dirección VIP (IP virtual) porque está vinculado al equilibrador de carga de Azure, y no la máquina Virtual (VM) instancias del servicio de nube. Puede acceder a cualquier instancia de máquina virtual dentro de un servicio de nube mediante el uso de una sola VIP.

Sin embargo, hay situaciones en las que puede tener más de un punto VIP como una entrada al mismo servicio de nube. Por ejemplo, el servicio de nube puede hospedar varios sitios Web que requiere conectividad SSL utiliza el puerto predeterminado 443, como cada sitio está alojado para un cliente diferente o de inquilinos. En este escenario, debe tener una dirección IP de opuestas de público diferente para cada sitio Web. El diagrama siguiente muestra un típico varios inquilino de hospedaje con una necesidad de varios certificados SSL en el mismo puerto público.

![Escenario de múltiples VIP SSL](./media/load-balancer-multivip/Figure1.png)

En el ejemplo anterior, todas las direcciones VIP utilizan el mismo puerto público (443) y el tráfico se redirige a uno o máquinas virtuales en un único puerto privado para la dirección IP interna del servicio de nube hospedaje todos los sitios Web de equilibrio de carga más.

>[AZURE.NOTE] Otra situación que requieren el uso de las direcciones VIP varios aloja varios agentes de escucha de grupo de disponibilidad de AlwaysOn SQL en el mismo conjunto de máquinas virtuales.

VIP es dinámico de forma predeterminada, lo que significa que la dirección IP asignada al servicio de nube puede cambiar con el tiempo. Para evitar que ocurra, puede reservar a VIP de su servicio. Para obtener más información sobre VIP reservada, vea [Reservada IP pública](../virtual-network/virtual-networks-reserved-public-ip.md).

>[AZURE.NOTE] Vea [dirección IP precios](https://azure.microsoft.com/pricing/details/ip-addresses/) para obtener información sobre precios en VIP y direcciones IP reservada.

Puede usar PowerShell para comprobar a las direcciones VIP usadas por los servicios de nube, así como agregar y quitar a VIP, asociar a una dirección VIP a un extremo y configurar Equilibrio de carga en una sola dirección VIP específica.

## <a name="limitations"></a>Limitaciones

En este momento, la funcionalidad de múltiples VIP está limitada a las situaciones siguientes:

- **Solo IaaS**. Sólo puede habilitar a múltiples VIP para los servicios de nube que contienen máquinas virtuales. No puede usar a Multi VIP en escenarios de PaaS con instancias de la función.
- **Solo PowerShell**. Solo puede administrar a múltiples VIP con PowerShell.

Estas limitaciones son temporales y se pueden cambiar en cualquier momento. Asegúrese de visitar la página para comprobar los cambios en el futuro.


## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Cómo agregar a una VIP a un servicio de nube

Para agregar a una dirección VIP a su servicio, ejecute el siguiente comando PowerShell:

    Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

Este comando muestra un resultado similar al siguiente ejemplo:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Cómo quitar a una VIP de un servicio de nube

Para quitar a la dirección VIP agregada a su servicio en el ejemplo anterior, ejecute el siguiente comando PowerShell:

    Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

>[AZURE.IMPORTANT] Sólo puede quitar a una VIP si no tiene ningún extremos asociados a él.

## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Cómo recuperar información de la dirección VIP de un servicio de nube

Para recuperar a las direcciones VIP asociadas con un servicio de nube, ejecute el siguiente script de PowerShell:

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

La secuencia de comandos muestra un resultado similar al siguiente ejemplo:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

En este ejemplo, el servicio de nube tiene 3 VIP:

- **Vip1** es la dirección VIP de forma predeterminada, sabrá que porque el valor de IsDnsProgrammedName se establece en true.
- **Vip2** y **Vip3** no se usan como no tiene ninguna dirección IP. Solo se usa si se asocia un punto final a la dirección VIP.

>[AZURE.NOTE] La suscripción sólo se cobrará por VIP adicional una vez que están asociados con un extremo. Para obtener más información sobre los precios, consulte [precios de dirección IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Cómo asociar a una dirección VIP a un punto final

Para asociar a una dirección VIP en un servicio de nube para un extremo, ejecute el siguiente comando PowerShell:

    Get-AzureVM -ServiceName myService -Name myVM1 `
  	| Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 `
  	| Update-AzureVM

El comando crea un extremo vinculado a la dirección VIP denominada *Vip2* en los puertos *80*y vínculos a la máquina virtual denominado *myVM1* en un servicio de nube denominado *myService* mediante *TCP* en el puerto *8080*.

Para comprobar la configuración, ejecute el siguiente comando PowerShell:

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

El resultado es similar al siguiente ejemplo:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Cómo habilitar el equilibrio de carga en una sola dirección VIP específica

Puede asociar a una sola VIP con varios equipos virtuales con fines de equilibrio de carga. Por ejemplo, tiene un servicio de nube denominada *myService*y dos máquinas virtuales de Windows denominado *myVM1* y *myVM2*. Y el servicio de nube tiene varias VIP, uno de ellos denominado *Vip2*. Si desea asegurarse de que todo el tráfico al puerto *81* en *Vip2* está equilibrado entre *myVM1* y *myVM2* en puerto *8181*, ejecute el siguiente script de PowerShell:

    Get-AzureVM -ServiceName myService -Name myVM1 `
  	| Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
  	| Update-AzureVM

    Get-AzureVM -ServiceName myService -Name myVM2 `
  	| Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
  	| Update-AzureVM

También puede actualizar el equilibrador de carga para utilizar a una VIP diferente. Por ejemplo, si ejecuta el siguiente comando PowerShell, cambiará el conjunto de utilizar a una VIP denominada Vip1 de equilibrio de carga:

    Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1

## <a name="next-steps"></a>Pasos siguientes

[Análisis de registro de equilibrio de carga de Azure](load-balancer-monitor-log.md)

[Información general del equilibrador de carga hacia de Internet](load-balancer-internet-overview.md)

[Introducción en Internet opuestas equilibrador de carga](load-balancer-get-started-internet-arm-ps.md)

[Información general de una red virtual](../virtual-network/virtual-networks-overview.md)

[API REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx)