<properties
   pageTitle="Reservado IP | Microsoft Azure"
   description="Comprender las direcciones IP reservada y cómo se administran"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/10/2016"
   ms.author="jdial" />

# <a name="reserved-ip-overview"></a>Información general IP reservada
Direcciones IP de Azure se dividen en dos categorías: reservados y dinámicas. Direcciones IP públicas administradas por Azure son dinámicas de forma predeterminada. Esto significa que la dirección IP utilizado para un servicio de nube dado (VIP) o tener acceso a un rol o VM instancia directamente (ILPIP), puede cambiar de vez en cuando, cuando los recursos están cerrados o desasignar.

Para evitar que cambien las direcciones IP, puede reservar una dirección IP. Direcciones IP reservada puede usarse solo como VIP, garantiza que la dirección IP del servicio de nube serán los mismos incluso a medida que se cierre o se cancela la asignación de recursos. Además, puede convertir existente direcciones IP dinámica usado como VIP a una dirección IP reservada.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Obtenga información sobre cómo reservar una dirección IP pública estática utiliza el [modelo de implementación de administrador de recursos](virtual-network-ip-addresses-overview-arm.md).

Asegúrese de que comprender cómo funcionan [las direcciones IP](virtual-network-ip-addresses-overview-classic.md) en Azure.

## <a name="when-do-i-need-a-reserved-ip"></a>¿Cuándo debo una IP reservada?
- **Para asegurarse de que la dirección IP está reservada en su suscripción**. Si desea reservar una dirección IP que no se anulan de la suscripción en cualquier caso, debe usar un IP reservada público.  
- **Desea que su dirección IP para mantener su servicio de nube incluso a través de manera o ha dejado de estado (VM)**. Si desea que su servicio para tener acceso mediante una dirección IP que no cambiará incluso cuando máquinas virtuales en el servicio de nube son detener o manera.
- **Para asegurarse de que el tráfico saliente de Azure utiliza una dirección IP predecible**. Puede que el firewall local configurado para permitir únicamente el tráfico de direcciones IP específicas. Al reservar una dirección IP, sabrá la dirección IP de origen y no tenga que actualizar las reglas de firewall debido a un cambio de IP.

## <a name="faq"></a>Preguntas más frecuentes
1. ¿Puedo usar una dirección IP reservada para todos los servicios de Azure?  
  - Direcciones IP reservada sólo puede utilizarse para máquinas virtuales y roles de instancia de servicio de nube expuestas a través de una dirección VIP.
1. ¿Cuántos direcciones IP reservadas puedo tener?  
  - En este momento, todas las suscripciones de Azure están autorizadas para usar direcciones IP reservadas de 20. Sin embargo, puede solicitar adicional direcciones IP reservada. Consulte la página de [suscripción y los límites de servicio](../azure-subscription-service-limits.md) para obtener más información.
1. ¿Hay un cargo de direcciones IP reservada?
  - Consulte [Detalles de precios de la dirección de IP reservada](http://go.microsoft.com/fwlink/?LinkID=398482) para la información de precios.
1. ¿Cómo reservar una dirección IP?
  - Puede usar PowerShell o la [API de REST de administración de Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx) para reservar una dirección IP en una región determinada. Esta dirección IP reservada está asociada a su suscripción. No puede reservar una dirección IP a través del Portal de administración.
1. ¿Puedo usar esto con afinidad grupo según VNets?
  - Direcciones IP reservada solo son compatibles con VNets regional. No se admite para VNets que están asociados con grupos de afinidad. Para obtener más información acerca de cómo asociar un VNet a una región o un grupo de afinidad, vea [acerca de Regional VNets y grupos de afinidad](virtual-networks-migrate-to-regional-vnet.md).

## <a name="how-to-manage-reserved-vips"></a>Cómo administrar a VIP reservado

Antes de poder usar direcciones IP reservada, debe agregarlo a la suscripción. Para crear una dirección IP reservada del grupo de direcciones IP públicas disponibles en la ubicación *Central de Estados Unidos* , ejecute el siguiente comando PowerShell:

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

Sin embargo, tenga en cuenta que no puede especificar qué IP se está reservado. Para ver qué direcciones IP están reservadas en su suscripción, ejecute el siguiente comando PowerShell y observe los valores para *ReservedIPName* y *dirección*:

    Get-AzureReservedIP

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

Una vez que se reserva una dirección IP, permanece asociado a la suscripción hasta que se elimine. Para eliminar la IP reservada mostrada arriba, ejecute el siguiente comando PowerShell:

    Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## <a name="how-to-reserve-the-ip-address-of-an-existing-cloud-service"></a>Cómo reservar la dirección IP de un servicio de nube existente

Puede reservar la dirección IP de un servicio de nube existente agregando el parámetro *- ServiceName* . Para reservar la dirección IP de un servicio de nube *TestService* en la ubicación *Central de Estados Unidos* , ejecute el siguiente comando PowerShell:

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService


## <a name="how-to-associate-a-reserved-ip-to-a-new-cloud-service"></a>Cómo asociar una dirección IP reservada a un nuevo servicio de nube
La siguiente secuencia de comandos crea un nuevo IP reservada y, a continuación, asocia a un nuevo servicio de nube denominado *TestService*.

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

>[AZURE.NOTE] Cuando se crea una dirección IP reservada para usar con un servicio de nube, todavía tendrá que hacer referencia a la máquina virtual mediante *VIP:&lt;número de puerto >* para las comunicaciones entrantes. Reservar una dirección IP no significa que se puede conectar a la máquina virtual directamente. La dirección IP reservada se asigna al servicio de nube que se ha implementado la máquina virtual a. Si desea conectarse a una máquina virtual por IP directamente, debe configurar una dirección IP pública de nivel de instancia. Una dirección IP pública de nivel de instancia es un tipo de IP pública (llamado un ILPIP) que se asigna directamente a su máquina virtual. No se puede reservar. Para obtener más información, vea [IP pública de nivel de instancia (ILPIP)](virtual-networks-instance-level-public-ip.md) .

## <a name="how-to-remove-a-reserved-ip-from-a-running-deployment"></a>Cómo quitar una dirección IP reservada de una implementación de ejecución
Para quitar la IP reservada agregada al nuevo servicio creado en la secuencia de comandos anterior, ejecute el siguiente comando PowerShell:

    Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

>[AZURE.NOTE] Quitar una dirección IP reservada de una implementación de ejecución, no se elimina la reserva de la suscripción. Simplemente libera la dirección IP que se utilizarán por otro recurso de la suscripción.

## <a name="how-to-associate-a-reserved-ip-to-a-running-deployment"></a>Cómo asociar una dirección IP reservada para una implementación de ejecución
La siguiente secuencia de comandos crea un nuevo servicio de nube denominado *TestService2* con una nueva máquina virtual denominada *TestVM2*y asocia el IP reservada existente denominado *MyReservedIP* al servicio de nube.

    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| New-AzureVM -ServiceName TestService2 -Location "Central US"
    Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## <a name="how-to-associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Cómo asociar una dirección IP reservada a un servicio de nube mediante un archivo de configuración de servicio
También puede asociar una dirección IP reservada a un servicio de nube mediante un archivo de configuración (CSCFG) de servicio. El xml de ejemplo siguiente muestra cómo configurar un servicio de nube para utilizar a una VIP reservada denominada *MyReservedIP*:

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Pasos siguientes

- Comprender cómo funciona la [asignación de direcciones IP](virtual-network-ip-addresses-overview-classic.md) en el modelo de implementación clásico.

- Obtenga información sobre las [direcciones IP reservadas](virtual-networks-reserved-private-ip.md).

- Obtenga información sobre [las direcciones de la instancia nivel público IP (ILPIP)](virtual-networks-instance-level-public-ip.md).
