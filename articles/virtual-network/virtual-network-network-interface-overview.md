<properties 
   pageTitle="Interfaces de red | Microsoft Azure"
   description="Obtenga información sobre las interfaces de red de Azure en el Administrador de recursos de Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="jdial" />

# <a name="network-interfaces"></a>Interfaces de red

Una interfaz de red (NIC) es la interconexión entre una máquina Virtual (VM) y la red de software subyacente. En este artículo se explica qué interfaz de red y cómo se utiliza en el modelo de implementación de administrador de recursos de Azure.

Microsoft recomienda implementar nuevos recursos utilizando el modelo de implementación de administrador de recursos, pero también puede implementar máquinas virtuales con la conectividad de red en el modelo de implementación [clásico](virtual-network-ip-addresses-overview-classic.md) . Si ya está familiarizado con el modelo clásico, hay diferencias importantes en las redes de VM en el modelo de implementación de administrador de recursos. Más información acerca de las diferencias, lea el artículo de la [Máquina Virtual redes: clásico](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) .

En Azure, una interfaz de red:

1. Es un recurso que se puede crear, eliminar, y tiene su propia configuración.
2. Debe estar conectado a una subred de una red Virtual de Azure (VNet) cuando se crea. Si no está familiarizado con VNets, más información acerca de ellos, lea el artículo [Introducción a la red Virtual](virtual-networks-overview.md) . La NIC debe estar conectada a un VNet que existe en la misma [ubicación](https://azure.microsoft.com/regions) de Azure y [suscripción](../azure-glossary-cloud-terminology.md#subscription) como NIC. Después de crea una NIC, puede cambiar la subred a que está conectado, pero no puede cambiar la VNet está conectado a.
3. Tiene un nombre asignado que no se puede cambiar una vez creada la NIC. El nombre debe ser único dentro de un [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups)de Azure, pero no tiene que ser único dentro de la suscripción, se crea en la ubicación de Azure o la VNet está conectado a. Normalmente se crean varias NIC dentro de una suscripción de Azure. Se recomienda que diseñar una convención de nomenclatura que facilita la administración de varias NIC más fácil que usar nombres predeterminados. Vea el artículo [recomendado convenciones de nomenclatura para los recursos de Azure](../guidance/guidance-naming-conventions.md) para obtener sugerencias.
4. Puede estar asociado a una máquina virtual, pero solo puede estar asociado a una única VM que existe en la misma ubicación que la NIC.
5. Tiene una dirección de MAC, se conserva con la NIC para mientras esté conectado a una máquina virtual. La dirección MAC se conserva si se reinicia la máquina virtual (desde dentro del sistema operativo) o detenido (desasignar) y a usar el Portal de Azure, Azure PowerShell o la interfaz de línea de comandos de Azure. Si ha desconectado de una máquina virtual y conectado a una máquina virtual diferente, la NIC recibe una dirección MAC distinta. Si se elimina la NIC, la dirección MAC se asigna a otras NIC.
6. Debe tener una principal **privada** estática o dinámica dirección IP *IPv4* asignados.
8. Puede tener un recurso de dirección IP público asociados a él.
9. Es compatible con acelera redes con virtualización de i/OS de raíz única (SR IOV) para tamaños específicos de VM ejecuta versiones específicas del sistema operativo Microsoft Windows Server. Para obtener más información acerca de esta característica de vista previa, lea el artículo [acelerado de la red para una máquina virtual](virtual-network-accelerated-networking-powershell.md) .
10. Puede recibir tráfico no destinado a las direcciones IP privadas asignadas si se habilita el reenvío de IP para la NIC. Si una máquina virtual está ejecutando software de firewall por ejemplo, enruta paquetes no destinados a sus propias direcciones IP. La máquina virtual debe ejecutar software capaz de enrutar o reenviar el tráfico, pero para ello, el reenvío de IP debe habilitarse para una NIC.
11. A menudo se crea en el mismo grupo de recursos como la máquina virtual está conectado a la misma VNet que está conectado, aunque no es necesario ser.

## <a name="vms-with-multiple-network-interfaces"></a>Máquinas virtuales con varias interfaces de red

Varias NIC pueden estar asociadas a una máquina virtual, pero al hacerlo, tenga en cuenta lo siguiente:  

- El tamaño de la máquina virtual debe ser compatible con varias NIC. Para obtener más información acerca de los tamaños VM admiten varias NIC, lea los artículos de [tamaños de máquina virtual de Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) o [tamaños de máquina virtual Linux](../virtual-machines/virtual-machines-linux-sizes.md) .   
- La máquina virtual debe crearse con al menos dos NIC. Si se ha creado la máquina virtual con solo una NIC, incluso si el tamaño de la máquina virtual es compatible con más de uno, no puede adjuntar NIC adicionales a la máquina virtual después de crearlo. Como la máquina virtual se creó con al menos dos NIC, puede adjuntar NIC adicionales a la máquina virtual después de crearlo, siempre y cuando el tamaño de la máquina virtual es compatible con más de dos NIC.  
- Puede desasociar NIC secundarias (no se puede separar la NIC principal) de una máquina virtual si la máquina virtual tiene al menos tres NIC adjuntas. No se puede separar NIC si dispone de la máquina virtual de dos o menos NIC adjunta.  
- El orden de las NIC desde dentro de la máquina virtual será aleatorio y también podría cambiar en todas las actualizaciones de infraestructura de Azure. Sin embargo, las direcciones IP y MAC ethernet correspondiente direcciones, permanecerán igual. Por ejemplo, suponga que el sistema operativo identifica NIC1 Azure como Eth1. Eth1 tiene la dirección IP 10.1.0.100 y la dirección de MAC 00-0D-3A-B0-39-0D. Después de una infraestructura de Azure actualizar y reinicie, el sistema operativo ahora puede identificar NIC1 Azure como ethl2, pero las direcciones IP y MAC será el mismo que se encontraban cuando el sistema operativo identificado NIC1 Azure como Eth1. Cuando es iniciado por el cliente, el orden NIC permanecerán igual dentro del sistema operativo.  
- Si la máquina virtual es un miembro de un [conjunto de disponibilidad](../azure-glossary-cloud-terminology.md#availability-set), todas las máquinas virtuales dentro del conjunto de disponibilidad deben tener una sola NIC o varias NIC. Si las máquinas virtuales tienen varias NIC, el número tienen no deben ser los mismos, siempre y cuando cada VM tiene al menos dos NIC.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo crear una máquina virtual con una sola NIC, lea el artículo [crear una máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
- Obtenga información sobre cómo crear una máquina virtual con varias NIC, lea el artículo de [implementar una máquina virtual con varias NIC](virtual-network-deploy-multinic-arm-ps.md) .
- Obtenga información sobre cómo crear una NIC con varias configuraciones de IP, lea el artículo de [direcciones IP múltiples para máquinas virtuales de Windows Azure](virtual-network-multiple-ip-addresses-powershell.md) .
