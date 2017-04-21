<properties
    pageTitle="Solución de análisis de redes Azure en el análisis de registro | Microsoft Azure"
    description="Puede usar la solución de análisis de redes de Azure en el análisis de registro para revisar los registros del grupo de seguridad de red de Azure y los registros de la puerta de enlace de aplicaciones de Azure."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="richrund"/>

# <a name="azure-networking-analytics-preview-solution-in-log-analytics"></a>Solución de redes análisis (vista preliminar) Azure en el análisis de registro

>[AZURE.NOTE] Esta es una [solución de vista previa](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).

Puede usar la solución de análisis de redes de Azure en el análisis de registro para revisar los registros de puerta de enlace de aplicaciones de Azure y Azure red grupo de seguridad.

Puede habilitar el registro para los registros de la puerta de enlace de aplicaciones de Azure y grupos de seguridad de la red de Azure. Estos registros se escriben en donde pueden, a continuación, indizar por registro análisis para realizar búsquedas y análisis de almacenamiento de blobs.

Los siguientes registros son compatibles con puertas de enlace de aplicación:

+ ApplicationGatewayAccessLog
+ ApplicationGatewayPerformanceLog

Se admiten los siguientes registros para los grupos de seguridad de red:

+ NetworkSecurityGroupEvent
+ NetworkSecurityGroupRuleCounter

## <a name="install-and-configure-the-solution"></a>Instalar y configurar la solución

Utilice las instrucciones siguientes para instalar y configurar la solución de análisis de redes de Azure:

1.  Habilitar el registro de diagnóstico para los recursos que desea supervisar:
  + [Puerta de enlace de aplicación](../application-gateway/application-gateway-diagnostics.md)
  + [Grupo de seguridad de red](../virtual-network/virtual-network-nsg-manage-log.md)
2.  Configurar el análisis de registro para leer los registros de almacenamiento de blobs mediante el proceso descrito en [archivos JSON en el almacenamiento de blobs](../log-analytics/log-analytics-azure-storage-json.md).
3.  Habilitar la solución de análisis de redes de Azure mediante el proceso descrito en [soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md).  

Si no habilita el registro de diagnóstico para un tipo de recurso determinado, los módulos de panel para ese recurso estará en blanco.

## <a name="review-azure-networking-analytics-data-collection-details"></a>Revise los detalles de conjunto de datos de análisis de redes de Azure

La solución de análisis de redes de Azure recopila registros de diagnósticos de almacenamiento de blobs de Windows Azure para puertas de enlace de aplicación de Azure y grupos de seguridad de red.
No se necesita para la recopilación de datos agente.

La siguiente tabla muestra los métodos de conjunto de datos y otros detalles acerca de cómo se recopilan los datos para el análisis de redes de Azure.

| Plataforma | Agente directo | Agente de Systems Center Operations Manager (SCOM) | Almacenamiento de Azure | ¿SCOM obligatorio? | Datos de agente SCOM enviadas por grupo de administración | Frecuencia de la colección |
|---|---|---|---|---|---|---|
|Azure|![No](./media/log-analytics-azure-networking/oms-bullet-red.png)|![No](./media/log-analytics-azure-networking/oms-bullet-red.png)|![Sí](./media/log-analytics-azure-networking/oms-bullet-green.png)|            ![No](./media/log-analytics-azure-networking/oms-bullet-red.png)|![No](./media/log-analytics-azure-networking/oms-bullet-red.png)| 10 minutos|

## <a name="use-azure-networking-analytics"></a>Usar el análisis de redes Azure

Después de instalar la solución, puede ver el resumen del cliente y errores del servidor para las puertas de enlace de aplicación supervisados utilizando el **Análisis de redes de Azure** en mosaico en la página de **información general** de análisis de registro.

![imagen del icono de análisis de redes de Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking-tile.png)

Después de hacer clic en el icono de **información general** , puede ver resúmenes de los registros y explora detalles para las siguientes categorías:

+ Registros de acceso de puerta de enlace de aplicación
  - Errores de cliente y de servidor para los registros de acceso de puerta de enlace de aplicación
  - Solicitudes por hora para cada puerta de enlace de aplicaciones
  - Error de solicitudes por hora para cada puerta de enlace de aplicaciones
  - Errores de agente de usuario para las puertas de enlace de aplicación
+ Rendimiento de puerta de enlace de aplicación
  - Estado de host de puerta de enlace de aplicación
  - Solicitudes de error de percentil 95 y máximo de puerta de enlace de aplicación
+ Grupo de seguridad de red bloqueados flujos
  - Reglas del grupo de seguridad de red con flujos bloqueados
  - Direcciones de MAC con flujos bloqueados
+ Grupo de seguridad de red permitida flujos
  - Reglas del grupo de seguridad de red con flujos permitidos
  - Direcciones de MAC con flujos permitidos


![imagen del panel de análisis de redes de Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking01.png)

![imagen del panel de análisis de redes de Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking02.png)

![imagen del panel de análisis de redes de Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking03.png)

![imagen del panel de análisis de redes de Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking04.png)

### <a name="to-view-details-for-any-log-summary"></a>Ver detalles de resumen del registro

1. En la página **Introducción** , haga clic en el mosaico de **Análisis de redes de Azure** .
2. En el panel de **Análisis de redes de Azure** , revise la información de resumen en uno de los módulos y, a continuación, haga clic en para ver información detallada sobre él en la página de búsqueda de registro.

    En cualquiera de las páginas de búsqueda de registro, puede ver los resultados de tiempo, resultados detallados y el historial de búsqueda de registro. También puede filtrar por facetas y limitar los resultados.

## <a name="next-steps"></a>Pasos siguientes

- Usar [búsquedas de registro de análisis de registro](log-analytics-log-searches.md) para ver datos detallados de análisis de redes de Azure.
