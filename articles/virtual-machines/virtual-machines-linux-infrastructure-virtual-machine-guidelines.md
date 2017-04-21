<properties
    pageTitle="Directrices de máquinas virtuales de Linux | Microsoft Azure"
    description="Obtenga más información sobre las directrices de diseño e implementación claves para implementar máquinas virtuales Linux en Azure"
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="virtual-machines-guidelines"></a>Directrices de máquinas virtuales

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

En este artículo se centra en la descripción de los pasos de diseño necesarios para crear y administrar máquinas virtuales (VM) dentro de su entorno de Azure.

## <a name="implementation-guidelines-for-vms"></a>Directrices de implementación para máquinas virtuales
Decisiones:

- ¿Cuántas VM necesita para varios niveles de aplicaciones y componentes de la infraestructura?
- ¿Qué recursos de CPU y memoria deben cada VM y cuáles son los requisitos de almacenamiento?

Tareas:

- Definir las cargas de trabajo para la aplicación y los recursos que requieren las máquinas virtuales.
- Alinear las necesidades de recursos para cada VM con el tipo de tamaño y el almacenamiento VM adecuado.
- Definir los grupos de recursos para los diferentes niveles y componentes de la infraestructura.
- Definir la convención de nomenclatura de máquina virtual.
- Crear sus máquinas virtuales con la CLI de Azure web portal, o con las plantillas de administrador de recursos.

## <a name="virtual-machines"></a>Máquinas virtuales de Windows

Uno de los componentes principales en su entorno de Azure es probable que VM. Esto es donde se ejecutan sus aplicaciones, bases de datos, servicios de autenticación, etcetera.

Es importante conocer los [distintos tamaños VM](virtual-machines-linux-sizes.md) a correctamente el tamaño de su entorno desde una perspectiva de costo y el rendimiento. Si sus máquinas virtuales no tiene suficiente memoria o núcleos de CPU, rendimiento de la aplicación se ve afectado independientemente de cómo está diseñado y desarrollado. Revisar las cargas de trabajo sugeridos para cada serie VM como punto de partida cuando decida qué VM para cada componente en la infraestructura de tamaño. Puede [cambiar el tamaño de una máquina virtual](virtual-machines-linux-change-vm-size.md) después de la implementación.

Almacenamiento desempeña un papel clave en el rendimiento de la máquina virtual. Puede utilizar almacenamiento estándar, que usa los discos de rotación normal, o almacenamiento Premium para altas cargas de trabajo de i/OS y rendimiento de recursos asignadas, que usan discos SSD. Como con el tamaño de la memoria virtual, hay coste consideraciones para seleccionar el medio de almacenamiento. Puede leer el [artículo de instrucciones de infraestructura de almacenamiento](virtual-machines-linux-infrastructure-storage-solutions-guidelines.md) para comprender cómo diseñar almacenamiento adecuado para un rendimiento óptimo de sus máquinas virtuales.


## <a name="resource-groups"></a>Grupos de recursos
Componentes como máquinas virtuales lógicamente se agrupan para facilitar la administración y el mantenimiento con [Grupos de recursos de Azure](../azure-resource-manager/resource-group-overview.md). Mediante el uso de grupos de recursos, puede crear, administrar y supervisar todos los recursos que forman una aplicación. También puede implementar [controles de acceso basado en roles](../active-directory/role-based-access-control-what-is.md) para conceder acceso a otras personas dentro de su equipo a sólo los recursos que requieren. Dedique tiempo a planear los grupos de recursos y las asignaciones de roles. Existen diferentes métodos para realmente diseñar e implementar grupos de recursos, así que asegúrese de que lea el [artículo de instrucciones de grupos de recursos](virtual-machines-linux-infrastructure-resource-groups-guidelines.md) para comprender cuál es la mejor para crear sus máquinas virtuales.


## <a name="templates"></a>Plantillas 
Puede crear plantillas, definidas por descriptiva archivos JSON, para crear sus máquinas virtuales. Plantillas normalmente también generación el almacenamiento necesario, redes, interfaces de red, direcciones IP, etc. junto con las máquinas virtuales a sí mismos. Puede usar plantillas para crear entornos reproducibles coherentes para el desarrollo y fines de pruebas replicar fácilmente los entornos de producción y viceversa. Puede obtener más información sobre cómo [crear y usar plantillas](../azure-resource-manager/resource-group-overview.md#template-deployment) para comprender cómo puede usarlos para crear e implementar sus máquinas virtuales.


## <a name="next-steps"></a>Pasos siguientes
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 