<properties
    pageTitle="Documentación de gobierno Azure | Microsoft Azure"
    description="Proporciona una comparación de características e instrucciones sobre cómo desarrollar aplicaciones para la administración pública de Azure"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="scooxl"
    manager="zakramer"
    editor=""/>
<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/25/2016"
    ms.author="scooxl"/>
#  <a name="azure-government-management-and-security"></a>Seguridad y administración de la administración pública de azure

## <a name="automation"></a>Automatización

Automatización está generalmente disponible en la administración pública de Azure.

### <a name="variations"></a>Variaciones

Las siguientes características de automatización no ya están disponibles en la administración pública de Azure.

+ Creación de una credencial de principio de servicio para la autenticación

Para obtener más información, consulte la [documentación público de automatización](../automation/automation-intro.md).


##  <a name="key-vault"></a>Depósito clave
Para obtener detalles sobre este servicio y cómo usarla, vea el <a href="https://azure.microsoft.com/documentation/services/key-vault">documentación pública de Azure clave depósito.</a>
### <a name="data-considerations"></a>Consideraciones de datos
La siguiente información identifica el límite de la administración pública de Azure para Azure depósito de clave:

| Datos regulados/controlados permitidos | Datos regulados/controlados no permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos los datos cifrados con una clave de Azure clave depósito pueden contener datos regulados/controlados. | No se permiten que los metadatos de Azure depósito de clave contienen datos de exportación controla. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener su cámara clave.  No escriba datos regulados/controlados en los siguientes campos: nombres de grupo de recursos o depósito de clave, nombre de suscripción |

Depósito de clave está generalmente disponible en la administración pública de Azure. Como en público, no hay ninguna extensión, por lo que solo está disponible a través de PowerShell y CLI depósito de clave.
## <a name="log-analytics"></a>Análisis de registro
Análisis de registro está generalmente disponible en la administración pública de Azure. 

### <a name="variations"></a>Variaciones

Las características de análisis de registro y soluciones siguientes no están disponibles actualmente en la administración pública de Azure. Esta lista se actualiza cuando el estado de las características y cambios de soluciones.

+ Las soluciones que están en vista previa de Azure público, incluidos:
  - Solución de supervisión de red
  - Supervisión de dependencia de aplicaciones
  - Solución de Office 365
  - Solución de análisis de actualización de Windows 10
  - Información de la aplicación
  - Solución de análisis de redes de Azure
  - Análisis de automatización de Azure
  - Análisis de depósito clave
+ Soluciones y características que requieren las actualizaciones de software en local, incluidos
  - Integración con el sistema Centro Operations Manager 2016 (son compatibles con versiones anteriores de Operations Manager)
  - Grupos de equipos desde el Administrador de configuración de System Center
  - Solución de concentrador de superficie
+ Características que están en vista previa de Azure público, incluidos
  - Exportación de datos a PowerBI
+ Métrica de Azure y diagnóstico de Azure
+ Aplicaciones móviles de OMS

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
+ Para conectar el servidor de administración de System Center Operations Manager para el análisis de registro, debe descargar e importar actualizado de administración de paquetes.
  1. Descargue y guarde la [actualización de paquetes de administración](http://go.microsoft.com/fwlink/?LinkId=828749)
  2. Descomprima el archivo que ha descargado
  3. Importar los paquetes de administración en Operations Manager. Para obtener información sobre cómo importar un módulo de administración de un disco, vea el tema de [cómo importar un paquete de administración de Operations Manager](http://technet.microsoft.com/library/hh212691.aspx) en el sitio Web de Microsoft TechNet.
  4. Para conectar Operations Manager para el análisis de registro, siga los pasos de [Conectar Operations Manager para el análisis de registro](../log-analytics/log-analytics-om-agents.md) 



### <a name="frequently-asked-questions"></a>Preguntas más frecuentes

+ ¿Puedo migrar datos de análisis de registro en la administración pública de Azure a Azure público?
  - No. No es posible mover los datos o el área de trabajo de administración pública de Azure a Azure público.
+ ¿Puedo cambiar entre áreas de trabajo públicas de Azure y administración pública de Azure desde el portal de análisis de registro de OMS?
  - No. Los portales de público Azure y administración pública de Azure son independientes y no compartan información. 

Para obtener más información, consulte la [documentación público de análisis de registro](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener información adicional y actualizaciones, suscribirse a la <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog de Microsoft Azure gobierno.</a>
 
