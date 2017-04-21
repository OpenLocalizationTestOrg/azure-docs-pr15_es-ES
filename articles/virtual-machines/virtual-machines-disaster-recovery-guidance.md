<properties
    pageTitle="Qué hacer en caso de que una interrupción del servicio de Azure afecta a máquinas virtuales de Windows Azure | Microsoft Azure"
    description="Obtenga información sobre qué hacer en caso de que una interrupción del servicio de Azure afecta a máquinas virtuales de Windows Azure."
    services="virtual-machines"
    documentationCenter=""
    authors="kmouss"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines"
    ms.workload="virtual-machines"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="kmouss;aglick"/>

#<a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-virtual-machines"></a>Qué hacer en caso de que una interrupción del servicio de Azure afecta a máquinas virtuales de Windows Azure

En Microsoft, nos trabajar duro para asegurarse de que nuestros servicios siempre están disponibles cuando los necesite. Fuerzas fuera de nuestro control afectan a veces nos de maneras que producen interrupciones del servicio no planeado.

Microsoft proporciona un contrato de nivel de servicio (SLA) para sus servicios como un compromiso y conectividad. El SLA de servicios de Azure individuales puede encontrarse en [Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

Azure ya tiene muchas características integradas de plataforma que admiten aplicaciones altamente disponibles. Para obtener más información acerca de estos servicios, lea la [recuperación y alta disponibilidad para las aplicaciones de Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Este artículo trata un escenario de recuperación de desastres true cuando produce de una región toda una interrupción debido a principal desastre natural o interrupción del servicio extendido. Estos son repeticiones raras, pero debe prepararse para la posibilidad de que hay una interrupción de toda una región. Si una región entera experimenta una interrupción del servicio, las copias de los datos redundantes localmente temporalmente sería disponibles. Si ha habilitado la replicación geo, tres copias adicionales de su almacenamiento Azure BLOB y las tablas se almacenan en una región distinta. En el caso de una interrupción regional completa o un desastre en el que el área principal no es recuperable, Azure reasigna todas las entradas DNS a la región geográfica replicada.

>[AZURE.NOTE]Tenga en cuenta que no tiene ningún control sobre este proceso, y sólo se produce para las interrupciones del servicio de toda la región. Por este motivo, también debe confiar en otras estrategias de copia de seguridad específicos de la aplicación para obtener el máximo nivel de disponibilidad. Para obtener más información, vea la sección de [estrategias de datos para la recuperación](../resiliency/resiliency-disaster-recovery-azure-applications.md#data-strategies-for-disaster-recovery).

Para ayudarle a controlar estas repeticiones raras, le proporcionamos las siguientes instrucciones para Azure máquinas virtuales de Windows en el caso de una interrupción del servicio de toda la región donde se implementa la aplicación de Azure máquina virtual.

##<a name="option-1-wait-for-recovery"></a>Opción 1: Esperar recuperación
En este caso, se requiere ninguna acción por su parte. Sepa que estamos trabajando diligentemente para restaurar la disponibilidad de servicio. Puede ver el estado del servicio actual en el [Panel de estado del servicio de Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Esta es la mejor opción si no ha configurado recuperación de sitio de Azure, copia de seguridad de máquina virtual, almacenamiento geo redundantes de acceso de lectura o almacenamiento geo redundantes antes de la interrupción. Si ha configurado almacenamiento geo redundante o almacenamiento geo redundantes de acceso de lectura para la cuenta de almacenamiento donde se almacenan las unidades de disco duras virtuales (VHD) de VM, puede buscar para recuperar la imagen de disco duro virtual base y pruebe proporcionando una nueva máquina virtual de él. No es una opción preferida porque no hay ninguna garantía de sincronización de datos a menos que se usan la copia de seguridad de Azure VM o recuperación de sitio de Azure. Por lo tanto, no se garantiza que esta opción para que funcione.

Los clientes que deseen tener acceso inmediato a máquinas virtuales, las dos opciones siguientes están disponibles.  

>[AZURE.NOTE]Tenga en cuenta que tanto de las siguientes opciones tienen la posibilidad de perder datos.     

##<a name="option-2-restore-a-vm-from-a-backup"></a>Opción 2: Restaurar una máquina virtual desde una copia de seguridad
Para los clientes que se han configurado una copia de seguridad de la máquina virtual, puede restaurar la máquina virtual desde su punto de copia de seguridad y recuperación.

Para restaurar una nueva máquina virtual de copia de seguridad de Azure, consulte [restaurar máquinas virtuales de Azure](../backup/backup-azure-restore-vms.md).

Para ayudarle a planear la infraestructura de copia de seguridad de máquinas virtuales de Windows Azure, vea [Planear la infraestructura de copia de seguridad de VM en Azure](../backup/backup-azure-vms-introduction.md).

##<a name="option-3-initiate-a-failover-by-using-azure-site-recovery"></a>Opción 3: Iniciar un error mediante la recuperación de sitios de Azure
Si ha configurado la recuperación de sitios de Azure para trabajar con sus máquinas virtuales de Azure afectadas, puede restaurar sus máquinas virtuales de sus réplicas. Estas réplicas pueden residen en Azure o de forma local. En este caso, puede crear una nueva máquina virtual de su réplica existente. Para restaurar sus máquinas virtuales de una réplica de recuperación de sitios de Azure, consulte [máquinas virtuales de migrar Azure IaaS entre regiones Azure con la recuperación de sitio de Azure](../site-recovery/site-recovery-migrate-azure-to-azure.md).

>[AZURE.NOTE]Aunque el sistema operativo de Azure máquina virtual y los discos de datos se replicarse en un disco duro virtual secundario, si se encuentran en una cuenta de almacenamiento geo redundantes de acceso de lectura o de almacenamiento geo redundantes, cada disco duro virtual se duplica por separado. Este nivel de replicación no garantiza la coherencia entre los VHD replicados. Si su aplicación o bases de datos que utilizan estos discos de datos tienen dependencias entre sí, no se garantiza que todos los VHD se duplican como una instantánea. También no se garantiza que la réplica de disco duro virtual de almacenamiento geo redundante o almacenamiento de acceso de lectura geo redundantes dará como resultado una instantánea coherente de aplicación para iniciar la máquina virtual.

##<a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre cómo implementar una estrategia de alta disponibilidad y recuperación, consulte [recuperación y alta disponibilidad para las aplicaciones de Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Para desarrollar una comprensión técnica detallada de capacidades de la plataforma de nube, consulte [orientación técnica de la resistencia de Azure](../resiliency/resiliency-technical-guidance.md).

Para obtener información sobre cómo realizar copias de seguridad de máquinas virtuales, vea [realizar copias de seguridad máquinas virtuales de Windows Azure](../backup/backup-azure-vms.md).

Obtenga información sobre cómo usar recuperación de Azure sitio para coordinar y automatizar la protección de su físicos (y) Windows y Linux máquinas virtuales que se ejecutan en máquinas virtuales de Hyper-V y VMWare, consulte [Recuperación de sitio de Azure](https://azure.microsoft.com/documentation/learning-paths/site-recovery/).

Si las instrucciones no están claros o si le gustaría Microsoft para realizar las operaciones en su nombre, póngase en contacto con [Soporte al cliente](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
