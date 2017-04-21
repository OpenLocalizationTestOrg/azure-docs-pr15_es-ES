<properties
    pageTitle="Disponibilidad de establece directrices | Microsoft Azure"
    description="Obtenga información sobre las directrices de diseño e implementación claves para implementar conjuntos de disponibilidad de los servicios de infraestructura de Azure."
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

# <a name="availability-sets-guidelines"></a>Directrices de conjuntos de disponibilidad

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

En este artículo se centra en la descripción de los pasos de diseño necesarios para los conjuntos de disponibilidad garantizar su permanece aplicaciones accesibles durante planificados o eventos.

## <a name="implementation-guidelines-for-availability-sets"></a>Directrices de implementación de conjuntos de disponibilidad

Decisiones:

- ¿Cuántos conjuntos de disponibilidad necesita para los distintos niveles y funciones en la infraestructura de la aplicación?

Tareas:

- Defina el número de máquinas virtuales de cada nivel de la aplicación que requiere.
- Determinar si necesita ajustar el número de errores o actualizar los dominios que se usará para la aplicación.
- Definir los conjuntos de disponibilidad requeridos utilizando la convención de nomenclatura y máquinas virtuales residen en ellos. Una máquina virtual solo puede encontrarse en la disponibilidad de un conjunto. 

## <a name="availability-sets"></a>Conjuntos de disponibilidad

En Azure, máquinas virtuales (VM) se pueden colocar en a un grupo lógico llamado un conjunto de disponibilidad. Al crear máquinas virtuales dentro de un conjunto de disponibilidad, la plataforma de Windows Azure distribuye la colocación de esas máquinas virtuales en la infraestructura subyacente. Debe haber un evento de mantenimiento planeado para la plataforma Windows Azure o un hardware subyacente y errores de infraestructura, el uso de conjuntos de disponibilidad asegura que al menos una VM sigue en ejecución.

Como práctica recomendada, no incluirán las aplicaciones en una única VM. Un conjunto de disponibilidad que contiene una única VM no obtiene ninguna protección de eventos planificados o dentro de la plataforma Windows Azure. El [SLA de Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines) requiere dos o más VM dentro de una disponibilidad configurado para permitir la distribución de máquinas virtuales en toda la infraestructura subyacente.

La infraestructura subyacente en Azure se divide en actualizar los dominios y errores. Estos dominios están definidos por qué hosts compartir un ciclo de actualización comunes o compartir la infraestructura similares física como power y redes. Azure distribuye automáticamente sus máquinas virtuales dentro de una disponibilidad configurar dominios para mantener la disponibilidad y tolerancia a errores. Dependiendo del tamaño de la aplicación y el número de máquinas virtuales dentro de un conjunto de disponibilidad, puede ajustar el número de dominios que desee usar. Puede obtener más información sobre la [administración de disponibilidad y uso de dominios de actualización y errores](virtual-machines-windows-manage-availability.md).

Al diseñar la infraestructura de la aplicación, también debe planear los niveles de aplicación que usa. Conjuntos de máquinas virtuales de grupo que tienen el mismo propósito de disponibilidad, como una disponibilidad para sus máquinas virtuales front-end que ejecuta IIS. Crear una disponibilidad independiente para sus máquinas virtuales de back-end con SQL Server. El objetivo es asegurarse de que cada componente de la aplicación está protegido por un conjunto de disponibilidad y al menos una vez instancia siempre sigue en ejecución.

Equilibradores de carga se pueden utilizar delante de cada nivel de aplicación para trabajar junto con un conjunto de disponibilidad y asegúrese de que siempre se pueda enrutar el tráfico a una instancia en ejecución. Sin un equilibrador de carga, sus máquinas virtuales pueden continuar ejecución en eventos de mantenimiento planeado y no planeado, pero los usuarios finales no pueda resolverla si la máquina virtual principal no está disponible.


## <a name="next-steps"></a>Pasos siguientes
[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 