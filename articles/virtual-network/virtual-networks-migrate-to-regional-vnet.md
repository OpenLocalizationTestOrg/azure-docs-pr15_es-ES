<properties 
   pageTitle="Cómo migrar de grupos de afinidad a una red Virtual Regional (VNet)"
   description="Obtenga información sobre cómo migrar de grupos de afinidad a vnets regionales"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-migrate-from-affinity-groups-to-a-regional-virtual-network-vnet"></a>Cómo migrar de grupos de afinidad a una red Virtual Regional (VNet)

Puede usar un grupo de afinidad para asegurarse de que los recursos creados dentro del mismo grupo afinidad física se hospedan en servidores que están muy juntos, lo que estos recursos para comunicarse con más rapidez. En el pasado, grupos de afinidad eran un requisito para crear redes virtuales (VNets). En ese momento, el servicio de administrador de red que administran VNets solo podría trabajar dentro de un conjunto de servidores físicos o unidad de escala. Mejoras en la arquitectura han aumentado el ámbito de administración de la red a una región.

Como resultado de estas mejoras arquitectónicas, afinidad grupos ya no se recomienda, o necesarios para redes virtuales. El uso de grupos de afinidad para VNets ha quedado reemplazado por regiones. VNets que están asociados con las regiones se denominan VNets regional.

Además, le recomendamos que no use grupos de afinidad en general. Aparte de los requisitos de VNet grupos de afinidad también estaban importantes utilizar para asegurarse de recursos, como el almacenamiento y el cálculo se colocan cerca de la otra. Sin embargo, con la arquitectura de red Azure actual, estos requisitos de colocación ya no son necesarios. Consulte [grupos de afinidad y máquinas virtuales](#Affinity-groups-and-VMs) para los casos específicos restantes algunos donde desea usar un grupo de afinidad.

## <a name="creating-and-migrating-to-regional-vnets"></a>Crear y migrar a VNets regionales

En el futuro, al crear nuevos VNets, use la *región*. Verá esto como una opción en el Portal de administración. Tenga en cuenta que en el archivo de configuración de red muestra como *ubicación*.

>[AZURE.IMPORTANT] Aunque es técnicamente posible crear una red virtual que está asociada con un grupo de afinidad, no hay ninguna razón para hacerlo. Nuevas características, como los grupos de seguridad de red, solo están disponibles cuando se usa un VNet regional y no están disponibles para redes virtuales que están asociadas con grupos de afinidad.

### <a name="about-vnets-currently-associated-with-affinity-groups"></a>Acerca de VNets actualmente asociados con grupos de afinidad

VNets que están asociados con grupos de afinidad están habilitados para la migración a VNets regional. Para migrar a un VNet regional, siga estos pasos:

1. Exportar el archivo de configuración de red. Puede usar PowerShell o el Portal de administración. Para obtener instrucciones con el Portal de administración, consulte [configurar su VNet con un archivo de configuración de red](virtual-networks-using-network-configuration-file.md).

1. Editar el archivo de configuración de red, reemplazar los valores antiguos con los nuevos valores. 

    > [AZURE.NOTE] La **ubicación** es la región que haya especificado para el grupo de afinidad que esté asociado a su VNet. Por ejemplo, si su VNet está asociado con un grupo de afinidad que se encuentra en oeste de Estados Unidos, al migrar, debe indicar su ubicación oeste de Estados Unidos. 
    
    Editar las siguientes líneas en el archivo de configuración de red, reemplazando los valores con su propio: 

    **Valor antiguo:** \<VirtualNetworkSitename = AffinityGroup "VNetUSWest" = "VNetDemoAG"\> 

    **Nuevo valor:** \<VirtualNetworkSitename = ubicación "VNetUSWest" = "US Oeste"\>

1. Guarde los cambios e [Importar](virtual-networks-using-network-configuration-file.md) la configuración de red en Azure.

>[AZURE.NOTE] Esta migración no hace que el tiempo de inactividad en los servicios.

## <a name="affinity-groups-and-vms"></a>Grupos de afinidad y máquinas virtuales

Como se mencionó anteriormente, generalmente ya no se recomienda la afinidad grupos para máquinas virtuales. Debe usar un grupo de afinidad solo cuando un conjunto de máquinas virtuales debe tener la menor latencia de red entre las máquinas virtuales absolutas. Colocando máquinas virtuales en un grupo de afinidad, las máquinas virtuales se se coloca en la misma unidad de escala o clúster de cálculo.

Es importante que tenga en cuenta que uso de un grupo de afinidad puede tener dos, posiblemente negativo, consecuencias:

- El conjunto de tamaños VM se limitará al conjunto de tamaños VM ofrecidas por la unidad de escala de cálculo.

- Hay una mayor probabilidad de no poder asignar una nueva máquina virtual. Esto sucede cuando la unidad de escala específicas para el grupo afinidad está fuera de la capacidad.

### <a name="what-to-do-if-you-have-a-vm-in-an-affinity-group"></a>Qué hacer si tiene una máquina virtual en un grupo de afinidad

Máquinas virtuales que se encuentran actualmente en un grupo de afinidad no es necesario que lo quiten del grupo afinidad.

Una vez que se implementa una máquina virtual, se implementa en una sola unidad. Grupos de afinidad puede restringir el conjunto de tamaños VM disponibles para una nueva implementación de máquina virtual, pero cualquier máquina virtual existente que se ha implementado ya está restringido al conjunto de tamaños VM disponibles en la unidad de escala en el que se implementa la máquina virtual. Por este motivo, quitando el grupo afinidad en una máquina virtual tendrá ningún efecto.
 
