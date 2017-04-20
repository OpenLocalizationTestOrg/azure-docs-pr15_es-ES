<properties
    pageTitle="Suscripción de Microsoft Azure y límites de servicio, cuotas y restricciones"
    description="Proporciona una lista de suscripción de Azure comunes y límites de servicio, cuotas y restricciones. Esto incluye información acerca de cómo aumentar los límites junto con valores máximos."
    services=""
    documentationCenter=""
    authors="rothja"
    manager="jeffreyg"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="btardif"/>

# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Suscripción de Azure y los límites del servicio, cuotas y restricciones

## <a name="overview"></a>Información general

Este documento especifican los límites de Microsoft Azure más comunes. Esto no cubren todos los servicios de Azure. Con el tiempo, estos límites se expande y actualizados para cubrir más de la plataforma.

Visite [Introducción a Azure precios](https://azure.microsoft.com/pricing/) para obtener más información sobre precios de Azure. Allí, puede calcular los costos con la [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/) o visite la página de detalles de precios de un servicio (por ejemplo, [Máquinas virtuales de Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)).

> [AZURE.NOTE] Si desea elevar el límite por encima del **Límite predeterminado**, puede [Abrir una solicitud de soporte técnico en línea para clientes sin cargo](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). No se puede aumentar los límites por encima del valor **Máximo** en las tablas siguientes. Si no hay ninguna columna **Límite máximo** , el recurso especificado no tiene límites ajustables.

## <a name="limits-and-the-azure-resource-manager"></a>El Administrador de recursos de Azure y límites

Ahora es posible combinar varios recursos Azure en un único grupo de recursos de Azure. Al usar grupos de recursos, los límites de una vez eran globales administrarse a nivel regional con el Administrador de recursos de Azure. Para obtener más información acerca de los grupos de recursos de Azure, consulte [información general del Administrador de recursos de Azure](azure-resource-manager/resource-group-overview.md).

En los siguientes límites, se ha agregado una nueva tabla para reflejar las diferencias de los límites al usar el Administrador de recursos de Azure. Por ejemplo, es una tabla de **Límites de suscripción** y **Límites de suscripción - Administrador de recursos de Azure** . Cuando un límite se aplica a ambos casos, solo se muestra en la primera tabla. A menos que se indique lo contrario, los límites son globales en todas las regiones.

> [AZURE.NOTE] Es importante hacer hincapié en que las cuotas de recursos de grupos de recursos de Azure están accesibles por la suscripción por región y no por suscripción, como las cuotas de administración de servicio. Vamos a usar las cuotas de núcleo como ejemplo. Si necesita solicitar un aumento de cuota con soporte para núcleos, debe decidir cuántos núcleos que desea usar en las regiones y, a continuación, realizar una solicitud de cuotas de grupo de recursos de Azure core específica para los importes y las regiones que desee. Por lo tanto, si necesita usar 30 núcleos en Europa occidental para ejecutar la aplicación; específicamente, debe solicitar 30 núcleos en Europa occidental. Pero no tendrá una cuota core aumentar en cualquier otra región: Europa occidental solo tendrán la cuota de 30 principales.
<!-- -->
Como resultado, puede resultarle útil para tener en cuenta decidir qué las cuotas de grupo de recursos de Azure necesitan para su carga de trabajo de cualquier región de una y solicitar dicha cantidad en cada región en la que está pensando en implementación. Vea [solución de problemas de implementación](./resource-manager-common-deployment-errors.md) para obtener más ayuda a descubrir las cuotas actuales para regiones específicas.

## <a name="service-specific-limits"></a>Límites específicos del servicio

- [Active Directory](#active-directory-limits)
- [Administración de la API](#api-management-limits)
- [Aplicación de servicio](#app-service-limits)
- [Puerta de enlace de aplicación](#application-gateway-limits)
- [Información de la aplicación](#application-insights-limits)
- [Automatización](#automation-limits)
- [Caché de Azure Redis](#azure-redis-cache-limits)
- [RemoteApp de Azure](#azure-remoteapp-limits)
- [Copia de seguridad](#backup-limits)
- [Proceso por lotes](#batch-limits)
- [Servicios de BizTalk](#biztalk-services-limits)
- [CDN](#cdn-limits)
- [Servicios de nube](#cloud-services-limits)
- [Generador de datos](#data-factory-limits)
- [Análisis de datos lago](#data-lake-analytics-limits)
- [DNS](#dns-limits)
- [DocumentDB](#documentdb-limits)
- [Evento Hubs](#event-hubs-limits)
- [Concentrador IoT](#iot-hub-limits)
- [Depósito clave](#key-vault-limits)
- [Media Services](#media-services-limits)
- [Compromiso de móvil](#mobile-engagement-limits)
- [Servicios de móvil](#mobile-services-limits)
- [Supervisión](#monitoring-limits)
- [Autenticación multifactor](#multi-factor-authentication)
- [Redes](#networking-limits)
- [Servicio de notificación de concentrador](#notification-hub-service-limits)
- [Perspectivas operativas](#operational-insights-limits)
- [Grupo de recursos](#resource-group-limits)
- [Programador](#scheduler-limits)
- [Búsqueda](#search-limits)
- [Bus de servicio](#service-bus-limits)
- [Recuperación de sitio](#site-recovery-limits)
- [Base de datos SQL](#sql-database-limits)
- [Almacenamiento de información](#storage-limits)
- [Sistema de StorSimple](#storsimple-system-limits)
- [Análisis de secuencia](#stream-analytics-limits)
- [Suscripción](#subscription-limits)
- [Administrador de tráfico](#traffic-manager-limits)
- [Máquinas virtuales de Windows](#virtual-machines-limits)
- [Conjuntos de escala de máquina virtual](#virtual-machine-scale-sets-limits)


### <a name="subscription-limits"></a>Límites de suscripción
#### <a name="subscription-limits"></a>Límites de suscripción
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Límites de suscripción - Administrador de recursos de Azure

Los siguientes límites se aplican al usar el Administrador de recursos de Azure y Azure grupos de recursos. A continuación, no se muestran los límites que no se han cambiado con el Administrador de recursos de Azure. Consulte la tabla anterior para estos límites.

Para obtener información sobre cómo controlar los límites de solicitudes de administrador de recursos, vea [las solicitudes de límite del Administrador de recursos](resource-manager-request-limits.md).

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### <a name="resource-group-limits"></a>Límites de grupo de recursos

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Límites de máquinas virtuales
#### <a name="virtual-machine-limits"></a>Límites de máquina virtual
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### <a name="virtual-machines-limits---azure-resource-manager"></a>Límites de máquinas virtuales - Administrador de recursos de Azure

Los siguientes límites se aplican al usar el Administrador de recursos de Azure y Azure grupos de recursos. A continuación, no se muestran los límites que no se han cambiado con el Administrador de recursos de Azure. Consulte la tabla anterior para estos límites.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Límites de conjuntos de escala de máquina virtual

[AZURE.INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="networking-limits"></a>Límites de las redes

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Límites de las redes
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Límites de puerta de enlace de aplicación

[AZURE.INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="traffic-manager-limits"></a>Límites de tráfico administrador

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Límites de DNS

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>Límites de almacenamiento

Para obtener más información sobre los límites de cuenta de almacenamiento, vea [destinos de rendimiento y escalabilidad de almacenamiento de Azure](../articles/storage/storage-scalability-targets.md).

#### <a name="storage-service-limits"></a>Límites de servicio de almacenamiento

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### <a name="virtual-machine-disk-limits"></a>Límites de disco de máquina virtual

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Para obtener más información, vea [tamaños de máquina Virtual](../articles/virtual-machines/virtual-machines-linux-sizes.md) .

**Cuentas de almacenamiento estándar**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**Cuentas de almacenamiento Premium**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### <a name="storage-resource-provider-limits"></a>Límites de almacenamiento del proveedor de recursos

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### <a name="cloud-services-limits"></a>Límites de los servicios de nube

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### <a name="app-service-limits"></a>Límites de servicio de aplicaciones
Los siguientes límites de servicio de aplicaciones incluyen límites para aplicaciones Web, aplicaciones móviles, API de aplicaciones y aplicaciones de lógica.

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Límites del programador

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Límites de proceso por lotes

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###<a name="biztalk-services-limits"></a>Límites de los servicios de BizTalk
La siguiente tabla muestra los límites de los servicios de Biztalk de Azure.

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### <a name="documentdb-limits"></a>Límites de DocumentDB

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

Cuotas de muestra con un asterisco (*), [puede ajustar póngase en contacto con soporte técnico de Azure](./documentdb/documentdb-increase-limits.md).

### <a name="mobile-engagement-limits"></a>Límites de compromiso de móvil

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### <a name="search-limits"></a>Límites de búsqueda

Niveles de precios determinan la capacidad y los límites de su servicio de búsqueda. Los niveles incluyen:

- En el servicio de múltiples arrendatario *libre* , que se comparte con otros suscriptores Azure, está pensados para proyectos de desarrollo pequeños y evaluación.
- *Básico* proporciona recursos informáticos dedicados para cargas de trabajo de producción en una escala menor, con hasta tres réplicas para cargas de trabajo de consulta altamente disponibles.
- *Estándar (S1, S2, S3, S3 alta densidad)* es de cargas de trabajo de producción mayores. Para que pueda elegir una configuración de recursos para escenarios específicos, existen varios niveles dentro del nivel estándar.

**Límites de suscripción**

[AZURE.INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Límites de servicio de búsqueda**

[AZURE.INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Para obtener información más detallada sobre otros límites, incluido el tamaño del documento, consultas por segundo, teclas, las convocatorias y respuestas, vea [límites de servicio de búsqueda de Azure](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Límites de Media Services

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Límites CDN

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Límites de servicios móviles

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitoring-limits"></a>Límites de supervisión

[AZURE.INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Límites de servicio de notificación de concentrador

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Límites de Hubs de evento

[AZURE.INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Límites de Bus de servicio

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Límites de concentrador IoT

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="data-factory-limits"></a>Límites del generador de datos

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Límites de lago análisis de datos
[AZURE.INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="stream-analytics-limits"></a>Límites de análisis de secuencia
[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Límites de Active Directory

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### <a name="azure-remoteapp-limits"></a>Límites de RemoteApp de Azure

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### <a name="storsimple-system-limits"></a>Límites del sistema de StorSimple

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### <a name="operational-insights-limits"></a>Límites de perspectivas operativos

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Límites de copia de seguridad

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Límites de recuperación de sitios

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Límites de perspectivas de aplicación

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Límites de administración de la API

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Límites de caché Redis Azure

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Límites de depósito de clave

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Autenticación multifactor
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Límites de automatización
[AZURE.INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>Límites de la base de datos SQL

Para los límites de la base de datos de SQL, vea [Límites de recursos de base de datos de SQL](sql-database/sql-database-resource-limits.md).

## <a name="see-also"></a>Vea también

[Descripción de aumentan y los límites de Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Máquina virtual y los tamaños de servicio de nube de Azure](virtual-machines/virtual-machines-linux-sizes.md)

[Tamaños de los servicios en la nube](cloud-services/cloud-services-sizes-specs.md)
