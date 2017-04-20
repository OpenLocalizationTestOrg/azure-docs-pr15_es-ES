<properties
    pageTitle="Documentación de gobierno Azure | Microsoft Azure"
    description="Esto proporciona una comparación de características e instrucciones sobre cómo desarrollar aplicaciones para la administración pública de Azure."
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/25/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-monitoring-and-management"></a>Administración y supervisión de administración pública de Azure

En este artículo se describe la supervisión y administración de servicios variaciones y consideraciones para el entorno de administración pública de Azure.

## <a name="automation"></a>Automatización

Automatización está generalmente disponible en la administración pública de Azure.

### <a name="variations"></a>Variaciones

Las siguientes características de automatización no ya están disponibles en la administración pública de Azure.

+ Creación de una credencial de principio de servicio para la autenticación

Para obtener más información, consulte la [documentación público de automatización](../automation/automation-intro.md).

## <a name="log-analytics"></a>Análisis de registro

Análisis de registro está generalmente disponible en la administración pública de Azure.

### <a name="variations"></a>Variaciones

Las características de análisis de registro y soluciones siguientes no están disponibles actualmente en la administración pública de Azure.

+ Las soluciones que están en la vista previa en Microsoft Azure, incluidos:
  - Solución de supervisión de red
  - Solución de supervisión de las dependencias de aplicaciones
  - Solución de Office 365
  - Solución de análisis de actualización de Windows 10
  - Solución de perspectivas de aplicación
  - Solución de análisis de redes de Azure
  - Solución de análisis de automatización de Azure
  - Solución de análisis de la cámara de clave
+ Soluciones y características que requieren las actualizaciones de software en local, incluidos:
  - Integración con el sistema Centro Operations Manager 2016 (son compatibles con versiones anteriores de Operations Manager)
  - Grupos de equipos desde el Administrador de configuración de System Center
  - Solución de concentrador de superficie
+ Características que están en vista previa de Azure público, incluidos:
  - Exportación de datos para Power BI
+ Métrica de Azure y diagnóstico de Azure
+ Aplicaciones móviles de operaciones Management Suite

Las direcciones URL para el análisis de registro son diferentes en Azure gobierno:

| Azure público | Administración pública de Azure | Notas |
|--------------|------------------|-------|
| MMS.Microsoft.com | OMS.Microsoft.us | Portal de análisis de registro |
| *workspaceId*. ods.opinsights.azure.com | *workspaceId*. ods.opinsights.azure.us | [Selector de datos API](../log-analytics/log-analytics-data-collector-api.md) 
| \*. ods.opinsights.azure.com | \*. ods.opinsights.azure.us | Comunicación agente - [Configuración del firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. oms.opinsights.azure.com | \*. oms.opinsights.azure.us | Comunicación agente - [Configuración del firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. blob.core.windows.net | \*. blob.core.usgovcloudapi.net | Comunicación agente - [Configuración del firewall](../log-analytics/log-analytics-proxy-firewall.md) |


Las siguientes características de análisis de registro tienen un comportamiento diferente en Azure gobierno:

+ El agente de Windows debe descargarse desde el [portal de análisis de registro](https://oms.microsoft.us) para la administración pública de Azure.
+ Para conectar el servidor de administración de System Center Operations Manager para el análisis de registro, debe descargar e importar módulos de administración actualizada.
  1. Descargue y guarde la [actualización de paquetes de administración](http://go.microsoft.com/fwlink/?LinkId=828749).
  2. Descomprima el archivo que ha descargado.
  3. Importar los paquetes de administración en Operations Manager. Para obtener información sobre cómo importar un módulo de administración de un disco, vea [cómo importar un paquete de administración de Operations Manager](http://technet.microsoft.com/library/hh212691.aspx) en el sitio Web de Microsoft TechNet.
  4. Para conectar Operations Manager para el análisis de registro, siga los pasos de [Conectar Operations Manager para el análisis de registro](../log-analytics/log-analytics-om-agents.md).


## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

+ ¿Puedo migrar datos de análisis de registro en Microsoft Azure al gobierno de Azure?
  - No. No es posible mover los datos o el área de trabajo de Microsoft Azure al gobierno de Azure.
+ ¿Puedo cambiar entre Microsoft Azure y administración pública de Azure áreas de trabajo desde el portal de análisis de registro de operaciones Management Suite?
  - No. Los portales de Microsoft Azure y administración pública de Azure son independientes y no compartan información.

Para obtener más información, consulte la [documentación público de análisis de registro](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener información adicional y actualizaciones, suscribirse a la <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog de Microsoft Azure gobierno.</a>
