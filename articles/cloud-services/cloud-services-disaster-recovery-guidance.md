<properties
    pageTitle="Qué hacer en caso de un Azure servicio interrupción que afecta a los servicios de nube de Azure | Microsoft Azure"
    description="Obtenga información sobre qué hacer en caso de una interrupción de servicio Azure que afecta a los servicios de nube de Azure."
    services="cloud-services"
    documentationCenter=""
    authors="kmouss"
    manager="drewm"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="cloud-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="kmouss;aglick"/>

#<a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Qué hacer en caso de un Azure servicio interrupción que afecta a los servicios de nube de Azure

En Microsoft, nos trabajar duro para asegurarse de que nuestros servicios siempre están disponibles cuando los necesite. Fuerzas fuera de nuestro control afectan a veces nos de maneras que producen interrupciones del servicio no planeado.

Microsoft proporciona un contrato de nivel de servicio (SLA) para sus servicios como un compromiso y conectividad. El SLA de servicios de Azure individuales puede encontrarse en [Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

Azure ya tiene muchas características integradas de plataforma que admiten aplicaciones altamente disponibles. Para obtener más información acerca de estos servicios, lea la [recuperación y alta disponibilidad para las aplicaciones de Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Este artículo trata un escenario de recuperación de desastres true cuando produce de una región toda una interrupción debido a principal desastre natural o interrupción del servicio extendido. Estos son repeticiones raras, pero debe prepararse para la posibilidad de que hay una interrupción de toda una región. Si una región entera experimenta una interrupción del servicio, las copias de los datos redundantes localmente temporalmente sería disponibles. Si ha habilitado la replicación geo, tres copias adicionales de su almacenamiento Azure BLOB y las tablas se almacenan en una región distinta. En el caso de una interrupción regional completa o un desastre en el que el área principal no es recuperable, Azure reasigna todas las entradas DNS a la región geográfica replicada.

>[AZURE.NOTE]Tenga en cuenta que no tiene ningún control sobre este proceso, y sólo se produce para las interrupciones del servicio de todo el centro de datos. Por este motivo, también debe confiar en otras estrategias de copia de seguridad específicos de la aplicación para obtener el máximo nivel de disponibilidad. Para obtener más información, vea la sección acerca de [estrategias de datos de recuperación](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md#DSDR). Si desea poder afectan a su propia migración tras error, desea tener en cuenta el uso de [almacenamiento de acceso de lectura geo redundantes (RA GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage), que crea una copia de sólo lectura de los datos en otra región.

Para ayudarle a controlar estas repeticiones raras, le proporcionamos las siguientes instrucciones para Azure máquinas virtuales de Windows (VM) en el caso de una interrupción del servicio de toda la región donde se implementa la aplicación de la máquina virtual de Azure.

##<a name="option-1-wait-for-recovery"></a>Opción 1: Esperar recuperación
En este caso, se requiere ninguna acción por su parte. Saber que equipos de Azure están trabajando diligentemente para restaurar la disponibilidad de servicio. Puede ver el estado del servicio actual en el [Panel de estado del servicio de Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Esta es la mejor opción si un cliente no ha configurado la recuperación de sitios de Azure o que tenga una implementación secundaria en una región diferente.

Los clientes que deseen tener acceso inmediato a sus servicios de nube implementada, están disponibles las siguientes opciones.

>[AZURE.NOTE]Tenga en cuenta que estas opciones tienen la posibilidad de perder datos.     

##<a name="option-2-re-deploy-your-cloud-service-configuration-to-a-new-region"></a>Opción 2: Volver a implementar la configuración del servicio de nube a una nueva área

Si tiene el código original, simplemente puede implementar la aplicación, configuración asociada y los recursos asociados a un servicio de nube de nuevo en una región nueva.  

Para obtener más detalles acerca de cómo crear e implementar una aplicación de servicio de nube, consulte [cómo crear e implementar un servicio de nube](./cloud-services-how-to-create-deploy-portal.md).

Dependiendo de los orígenes de datos de la aplicación, tendrá que comprobar los procedimientos de recuperación para el origen de datos de aplicación.
  * Para los orígenes de datos de almacenamiento de Azure, vea [replicación de almacenamiento de Azure](../storage/storage-redundancy.md#read-access-geo-redundant-storage) para comprobar en las opciones que están disponibles basándose en el modelo de replicación elegido para la aplicación.
  * Para los orígenes de base de datos de SQL, lea [información general: empresa continuidad y base de datos de recuperación de base de datos SQL de nube](../sql-database/sql-database-business-continuity.md) para comprobar en las opciones que están disponibles basan en el modelo de replicación elegido para la aplicación.

##<a name="option-3-use-a-backup-deployment-through-azure-traffic-manager"></a>Opción 3: Usar una implementación de copia de seguridad a través del Administrador de tráfico de Azure
Esta opción, se supone que ya ha diseñado la solución de aplicación con la recuperación regional en cuenta. Puede usar esta opción si ya tiene una implementación de la aplicación de servicios de nube secundaria que se está ejecutando en una región diferente y conectado a través de un canal de administrador de tráfico. En este caso, comprobar el estado de la implementación secundario. Si es correcto, puede redirigir el tráfico a ella mediante el administrador del tráfico de Azure. Con esta estrategia, puede sacar partido de las configuraciones de orden de migración tras error y el método de enrutamiento de tráfico en el administrador del tráfico de Azure. Para obtener más información, consulte [cómo configurar opciones del Administrador de tráfico](../traffic-manager/traffic-manager-overview.md#how-to-configure-traffic-manager-settings).

![Equilibrar servicios de nube de Azure en regiones con el administrador del tráfico de Azure](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

##<a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo implementar una estrategia de alta disponibilidad y recuperación, consulte [recuperación y alta disponibilidad para las aplicaciones de Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Para desarrollar una comprensión técnica detallada de capacidades de la plataforma de nube, consulte [orientación técnica de la resistencia de Azure](../resiliency/resiliency-technical-guidance.md).

Si las instrucciones no están claros, o si desea que Microsoft para realizar las operaciones en su nombre póngase en contacto con [Soporte al cliente](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
