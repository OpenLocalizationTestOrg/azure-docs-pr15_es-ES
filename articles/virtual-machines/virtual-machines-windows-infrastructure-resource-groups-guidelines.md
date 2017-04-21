<properties
    pageTitle="Directrices de grupos de recursos | Microsoft Azure"
    description="Obtenga información sobre las directrices de diseño e implementación claves para implementar los grupos de recursos en servicios de infraestructura de Azure."
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="azure-resource-group-guidelines"></a>Directrices de grupo de recursos de Azure

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

En este artículo se centra en comprender cómo crear su entorno de forma lógica y todos los componentes de grupos de recursos del grupo.


## <a name="implementation-guidelines-for-resource-groups"></a>Directrices de implementación para grupos de recursos

Decisiones:

- ¿Va a crear grupos de recursos los componentes principales de la infraestructura o implementación de la aplicación completa?
- ¿Necesita restringir el acceso a grupos de recursos con controles de acceso basado en roles?

Tareas:

- Definir qué infraestructura componentes principales y dedicada de grupos de recursos que necesita.
- Revise cómo implementar plantillas de administrador de recursos para implementaciones coherentes y reproducibles.
- Definir qué funciones de acceso de usuario que necesita para controlar el acceso a los grupos de recursos.
- Crear el conjunto de grupos de recursos utilizando la convención de nomenclatura. Puede usar PowerShell Azure o el portal.


## <a name="resource-groups"></a>Grupos de recursos

En Azure, lógicamente agrupar recursos relacionados como cuentas de almacenamiento, redes virtuales y máquinas virtuales (VM) para implementar, administrar y mantenerlas como una entidad única. Este enfoque facilita la implementación de aplicaciones y mantener todos los recursos relacionados juntos desde una perspectiva de administración, o para conceder a otros usuarios acceso a ese grupo de recursos. Para obtener una descripción más completa de los grupos de recursos, lea la [información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md).

Una característica clave a grupos de recursos es la capacidad para crear su entorno de uso de plantillas. Una plantilla es simplemente un archivo JSON que declara el almacenamiento, redes y recursos de cálculo. También puede definir configuraciones a aplicar ni scripts personalizados relacionados. Mediante estas plantillas, crear implementaciones coherentes y reproducibles para las aplicaciones. Este enfoque facilita la creación de un entorno de desarrollo y, a continuación, utilizar la misma plantilla para crear una implementación de producción, o viceversa. Para comprender mejor uso de plantillas, lea [el tutorial de plantilla](../resource-manager-template-walkthrough.md) que le guiará por cada paso de la construcción de una plantilla.

Existen dos enfoques que puede seguir al diseñar su entorno con grupos de recursos:

- Grupos de recursos para cada aplicación de implementación que combina la cuentas de almacenamiento, redes virtuales y subredes, VM, cargar equilibradores, etcetera.
- Grupos de recursos centralizada que contienen las cuentas principales de almacenamiento y subredes o redes virtuales. Las aplicaciones son, a continuación, en sus propios grupos de recursos que contienen solo VM, equilibradores de carga, interfaces de red, etcetera.

Mientras se escala, creación de grupos de recursos centralizado para su red y subredes virtual facilita la compilación local entre conexiones de red para opciones de conectividad híbrido. Es el enfoque alternativo para que cada aplicación tiene su propia red virtual que requiere la configuración y el mantenimiento.  [Controles de acceso basado en roles](../active-directory/role-based-access-control-what-is.md) proporciona una manera de granular para controlar el acceso a grupos de recursos. Para las aplicaciones de producción, puede controlar los usuarios que pueden tener acceso a los recursos o, para los recursos de infraestructura de core puede limitar solo los ingenieros de infraestructura para trabajar con ellos. Los propietarios de aplicaciones sólo tienen acceso a los componentes de la aplicación en su grupo de recursos y no el núcleo Azure infraestructura de su entorno. Al diseñar su entorno, considere la posibilidad de los usuarios que necesitan tener acceso a los recursos y diseñar los grupos de recursos en consecuencia. 


## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 