<properties
   pageTitle="Servicios compatibles con el Administrador de recursos | Microsoft Azure"
   description="Describe los proveedores de recursos que son compatibles con el Administrador de recursos, sus esquemas y versiones API disponibles y las regiones que pueden hospedar los recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="magoedte;tomfitz"/>

# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Proveedores de administrador de recursos, regiones, versiones API y esquemas

Administrador de recursos de Azure proporciona una nueva forma para implementar y administrar los servicios que hacen que las aplicaciones. Administrador de recursos de soporte técnico de la mayoría, pero no para todos los servicios y algunos servicios de soporte técnico Administrador de recursos parcialmente. Este tema proporciona una lista de proveedores de recursos admitidos para el Administrador de recursos de Azure.

Al implementar los recursos, debe saber qué regiones los recursos de soporte técnico y qué versiones de API están disponibles para los recursos. La sección [regiones compatibles](#supported-regions) muestra cómo averiguar qué áreas de trabajo de su suscripción y recursos. La sección [API compatibles con versiones](#supported-api-versions) muestra cómo determinar qué versiones de API que puede usar.

Para ver qué servicios se admiten en el portal de Azure y portal clásica, vea [el gráfico de disponibilidad de portal de Azure](https://azure.microsoft.com/features/azure-portal/availability/). Para ver mover recursos de soporte de los servicios, vea [mover recursos al nuevo grupo de recursos o suscripción](resource-group-move-resources.md).

Las tablas siguientes enumeran qué implementación de soporte técnico de servicios de Microsoft y administración mediante el Administrador de recursos y cuáles no. El vínculo de la columna **Tutorial plantillas** envía una consulta en el repositorio de plantillas de Azure tutorial rápido para el proveedor de recurso especificado. Plantillas de tutorial rápido se agregan y se actualiza con frecuencia. La presencia de un vínculo para un servicio particular necesariamente que la consulta devuelve plantillas del repositorio. También hay muchos proveedores de recursos de terceros que el Administrador de recursos de soporte técnico. Aprenda a ver todos los proveedores de recursos en la sección [tipos y proveedores de recursos](#resource-providers-and-types) .


## <a name="compute"></a>Calcular

| Servicio | Administrador de recursos habilitado | API DE REST | Esquema | Tutorial rápido plantillas |
| ------- | ------------------------ |-------- | ------ | ------ |
| Proceso por lotes   | Sí     | [Lote REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) | [2015-12-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-12-01/Microsoft.Batch.json)       |  |
|Contenedor | Sí | [Contenedor de servicio REST](https://msdn.microsoft.com/library/azure/mt711470.aspx) | [2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.ContainerService.json) | [Microsoft.ContainerService](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerService%22&type=Code) |
| Servicios de ciclo de vida de Dynamics | Sí  |      |        |  |
| Conjuntos de escala | Sí | [Escala establezca resto](https://msdn.microsoft.com/library/azure/mt705635.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [virtualMachineScaleSets](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=virtualMachineScaleSets&type=Code) | 
| Tela de servicio | Sí  | [Servicio tela Rest](https://msdn.microsoft.com/library/azure/dn707692.aspx)  |      | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Máquinas virtuales de Windows | Sí | [RESTO DE VM](https://msdn.microsoft.com/library/azure/mt163647.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [virtualMachines](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%2Fvirtualmachines%22&type=Code) |
| Máquinas virtuales (clásico) | Limitado | - | - | - |
| Aplicación remota | No      | -  | - | - |
| Servicios de nube (clásico) | Limitado (vea a continuación) | - | - | - |

Máquinas virtuales (clásico) se refiere a los recursos que se implementaron mediante el modelo de implementación clásica, en lugar de a través del modelo de implementación de administrador de recursos. En general, estos recursos no admiten operaciones de administrador de recursos, pero hay algunas operaciones que se han habilitado. Para obtener más información acerca de estos modelos de implementación, vea [Descripción de administrador de recursos e implementación clásico](resource-manager-deployment-model.md). 

Servicios de nube (clásicos) se pueden utilizar con otros recursos clásicas; Sin embargo, los recursos clásicos no aprovechar todas las características de administrador de recursos y no son una buena opción para las soluciones futuras. Considere la posibilidad de cambiar su infraestructura de la aplicación para usar los recursos de los espacios de nombres Microsoft.Compute, Microsoft.Storage y Microsoft.Network.


## <a name="networking"></a>Redes

| Servicio | Administrador de recursos habilitado | API DE REST | Esquema | Tutorial rápido plantillas |
| ------- | -------  | -------- | ------ | ------ |
| Puerta de enlace de aplicación | Sí | [Aplicación de puerta de enlace resto](https://msdn.microsoft.com/library/azure/mt684939.aspx)   |        | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS     | Sí | [RESTO DE DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx)         | [2016-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute | Sí  | [ExpressRoute REST](https://msdn.microsoft.com/library/azure/mt586720.aspx)  |       | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| Equilibrador de carga | Sí  | [RESTO del equilibrador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Administrador de tráfico | Sí | [RESTO de tráfico de administrador](https://msdn.microsoft.com/library/azure/mt163667.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json)  | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Redes virtuales | Sí| [Red virtual REST](https://msdn.microsoft.com/en-us/library/azure/mt163650.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Puerta de enlace VPN | Sí | [La puerta de enlace resto de red](https://msdn.microsoft.com/library/azure/mt163859.aspx) |  | [virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[conexiones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |


## <a name="storage"></a>Almacenamiento de información

| Servicio | Administrador de recursos habilitado | API DE REST | Esquema | Tutorial rápido plantillas |
| ------- | ------- | -------- | ------ | ------- | ------ |
| Almacenamiento de información | Sí  | [Almacenamiento REST](https://msdn.microsoft.com/library/azure/mt163683.aspx) | [Cuenta de almacenamiento](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| StorSimple | Sí  |         |        |  |

## <a name="databases"></a>Bases de datos

| Servicio | Administrador de recursos habilitado | API DE REST | Esquema | Tutorial rápido plantillas |
| ------- | ------- | -------- | ------ | ------- | ------ |
| DocumentDB | Sí  | [DocumentDB REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json)  | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Redis caché | Sí |   | [2016-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Base de datos SQL | Sí | [RESTO de base de datos SQL](https://msdn.microsoft.com/library/azure/mt163571.aspx) | [vista previa de 2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| Almacén de datos SQL | Sí |   |      |


## <a name="web--mobile"></a>Web & móvil

| Servicio | Administrador de recursos habilitado | API DE REST | Esquema | Tutorial rápido plantillas |
| ------- | ------- | -------- | ------ | ------ |
| Aplicaciones de la API | Sí |   | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Aplicaciones de la API](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Administración de la API | Sí  | [Administración de la API REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) | [2016-07-07](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-07-07/Microsoft.ApiManagement.json)       | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| Contenido moderador | Sí |   |   |   |
| Aplicación de función | Sí |   |   | [functionApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22functionApp%22&type=Code) |
| Aplicaciones de lógica | Sí   | [API de REST de servicio de flujo de trabajo](https://msdn.microsoft.com/library/azure/mt643787.aspx) | [2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.Logic.json) | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Aplicaciones móviles | Sí |  | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json)  | [mobileApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22mobileApp%22&type=Code)   |
| Compromisos de móvil | Sí  |  [Compromiso móvil REST](https://msdn.microsoft.com/library/azure/mt683754.aspx)        |        | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Búsqueda | Sí  | [Búsqueda REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) |  | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Aplicaciones Web | Sí |          | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## <a name="analytics"></a>Análisis

| Servicio | Administrador de recursos habilitado | API DE REST | Esquema | Tutorial rápido plantillas |
| ------- | -------  | -------- | ------ | ------ |
| Catálogo de datos | Sí  | [RESTO del catálogo de datos](https://msdn.microsoft.com/library/azure/mt267595.aspx)  | [2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |  |
| Generador de datos | Sí | [RESTO del generador de datos](https://msdn.microsoft.com/library/azure/dn906738.aspx) |    | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Análisis de datos lago | Sí |   |   [vista previa de 2015-10-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) | [Microsoft.DataLakeAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeAnalytics%22&type=Code) |
| Almacén de datos de lago | Sí  | [RESTO de almacén de datos lago](https://msdn.microsoft.com/library/azure/mt693424.aspx) | [vista previa de 2015-10-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) | [Microsoft.DataLakeStore](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeStore%22&type=Code) |
| HDInsights | Sí | [HDInsights REST](https://msdn.microsoft.com/library/azure/mt622197.aspx) |        | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Aprendizaje de equipo | Sí | [Aprendizaje REST](https://msdn.microsoft.com/library/azure/mt767538.aspx)        | [vista previa 2016-05-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-01-preview/Microsoft.MachineLearning.json) |
| Análisis de secuencia | Sí  | [Análisis de vapor REST](https://msdn.microsoft.com/library/azure/dn835031.aspx)   |        |  |
| Power BI | Sí | [Power BI incrustados REST](https://msdn.microsoft.com/library/azure/mt712303.aspx) | [29-01-2016](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-01-29/Microsoft.PowerBI.json) |  |

## <a name="intelligence"></a>Inteligencia

| Servicio | Administrador de recursos habilitado | API DE REST | Esquema | Tutorial rápido plantillas |
| ------- | ------- | -------- | ------ | ------ |
| Servicios cognitivos | Sí |  | [vista previa 2016-02-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-01-preview/Microsoft.CognitiveServices.json) |   |

## <a name="internet-of-things"></a>Internet de acciones

| Servicio | Administrador de recursos habilitado | API DE REST | Esquema | Tutorial rápido plantillas |
| ------- | ------- | -------- | ------ | ------ |
| Hub de evento | Sí  | [Evento concentrador REST](https://msdn.microsoft.com/library/azure/dn790674.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.EventHub.json) | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code)  |
| IoTHubs | Sí | [Concentrador IoT REST](https://msdn.microsoft.com/library/azure/mt589014.aspx) | [2016-02-03](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-03/Microsoft.Devices.json) | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Notificación Hubs | Sí | [Hub de notificación REST](https://msdn.microsoft.com/library/azure/dn495827.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## <a name="media--cdn"></a>Medios y CDN

| Servicio | Administrador de recursos habilitado | API DE REST | Esquema | Tutorial rápido plantillas |
| ------- | ------- | -------- | ------ | ------ |
| CDN | Sí | [RESTO DE CDN](https://msdn.microsoft.com/library/azure/mt634456.aspx)  | [2016-04-02](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-02/Microsoft.Cdn.json) | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Servicio de medios | Sí | [Media Services REST](https://msdn.microsoft.com/library/azure/hh973617.aspx)  | [2015-10-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01/Microsoft.Media.json) |


## <a name="hybrid-integration"></a>Integración de híbrido

| Servicio | Administrador de recursos habilitado | API DE REST | Esquema | Tutorial rápido plantillas |
| ------- | ------- | -------- | ------ | ------ |
| Servicios de BizTalk | Sí |          | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |  |
| Servicio de recuperación | Sí | [Recuperación del resto del sitio](https://msdn.microsoft.com/library/azure/mt750497.aspx) | [2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.RecoveryServices.json) | [Microsoft.RecoveryServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.RecoveryServices%22&type=Code) |
| Bus de servicio | Sí | [Bus de servicio REST](https://msdn.microsoft.com/library/azure/mt639375.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.ServiceBus.json)       | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## <a name="identity--access-management"></a>Identidad y administración de acceso 

Azure Active Directory funciona con el Administrador de recursos para habilitar el control de acceso basado en roles para su suscripción. Para obtener información sobre cómo usar el control de acceso basado en roles y Active Directory, consulte [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Servicios de desarrollador 

| Servicio | Administrador de recursos habilitado | API DE REST | Esquema | Tutorial rápido plantillas |
| ------- | ------- | -------- | ------ | ------ |
| Información de la aplicación | Sí  | [Perspectivas REST](https://msdn.microsoft.com/library/azure/dn931943.aspx)  | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.Insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Mapas de Bing | Sí  |          |        |  |
| DevTest prácticas | Sí |   | [2016-05-15](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-15/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code)  |
| Cuenta Visual Studio | Sí   |          | [26 de 02 de 2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |  |

## <a name="management-and-security"></a>Administración y seguridad

| Servicio | Administrador de recursos habilitado | API DE REST | Esquema | Tutorial rápido plantillas |
| ------- | ------- | -------- | ------ | ------ |
| Automatización | Sí | [Automatización REST](https://msdn.microsoft.com/library/azure/mt662285.aspx) | [2015-10-31](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json)       | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Depósito clave | Sí | [Depósito clave REST](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [Depósito clave](resource-manager-template-keyvault.md)<br />[Secreto depósito clave](resource-manager-template-keyvault-secret.md)   | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Perspectivas operativas | Sí |          |        |  |
| Programador | Sí  | [Programador de REST](https://msdn.microsoft.com/library/azure/mt629143.aspx)     | [2016-03-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Seguridad (vista preliminar) | Sí | [Seguridad REST](https://msdn.microsoft.com/library/azure/mt704034.aspx)  |  | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code)  |

## <a name="resource-manager"></a>Administrador de recursos

| Característica | Administrador de recursos habilitado | API DE REST | Esquema | Tutorial rápido plantillas |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Autorización | Sí | [Bloqueos de administración](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Control de acceso basado en roles](https://msdn.microsoft.com/library/azure/dn906885.aspx)  | [Bloqueo de recurso](resource-manager-template-lock.md)<br />[Asignaciones de roles](resource-manager-template-role.md)  | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Recursos | Sí | [Recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Vínculos a recursos](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## <a name="resource-providers-and-types"></a>Tipos y proveedores de recursos

Al implementar recursos, debe con frecuencia recuperar información sobre los tipos y proveedores de recursos. Puede recuperar esta información a través de la API de REST, Azure PowerShell o CLI de Azure.

Para trabajar con un proveedor de recursos, ese proveedor de recursos debe estar registrado con su cuenta. De forma predeterminada, muchos de los proveedores de recursos se registran automáticamente; Sin embargo, tendrá que registrar manualmente algunos proveedores de recursos. Los ejemplos siguientes muestran cómo obtener el estado de registro de un proveedor de recursos y registrar el proveedor de recursos, si es necesario.

### <a name="portal"></a>Portal

Puede ver fácilmente una lista de proveedores de recursos compatibles con los siguientes pasos:

1. Seleccione **Mis permisos**.

    ![permisos de mi](./media/resource-manager-supported-services/my-permissions.png)

2. Seleccione el **estado de proveedor de recursos**

    ![estado de proveedor de recursos](./media/resource-manager-supported-services/resource-provider-status.png)

3. Vea la lista completa de proveedores de recursos para la suscripción.

    ![lista de proveedores de recursos](./media/resource-manager-supported-services/list-providers.png)

### <a name="rest-api"></a>API DE REST

Para obtener todos los recursos disponibles proveedores, incluyendo sus tipos, ubicaciones, versiones API y su estado de registro, use la operación de [la lista de todos los proveedores de recursos](https://msdn.microsoft.com/library/azure/dn790524.aspx) . Si necesita registrar un proveedor de recursos, vea [registrar una suscripción con un proveedor de recursos](https://msdn.microsoft.com/library/azure/dn790548.aspx).

### <a name="powershell"></a>PowerShell

En el ejemplo siguiente se muestra cómo obtener todos los proveedores de recursos disponibles.

    Get-AzureRmResourceProvider -ListAvailable
    

En el ejemplo siguiente se muestra cómo obtener los tipos de recursos para un proveedor de recursos determinado.

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
    
El resultado es similar a:

    ResourceTypeName                Locations                                         
    ----------------                ---------                                         
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} 
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} .
    ...
    
Para registrar un proveedor de recursos, proporcionar el espacio de nombres:

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### <a name="azure-cli"></a>CLI de Azure

En el ejemplo siguiente se muestra cómo obtener todos los proveedores de recursos disponibles.

    azure provider list
    
El resultado es similar a:

    info:    Executing command provider list
    + Getting registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

Puede guardar la información de un proveedor de recursos determinado en un archivo con el siguiente comando.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Para registrar un proveedor de recursos, proporcionar el espacio de nombres:

    azure provider register -n Microsoft.ServiceBus

## <a name="supported-regions"></a>Regiones admitidas

Al implementar recursos, normalmente necesitará especificar una región para los recursos. Administrador de recursos es compatible en todas las regiones, pero los recursos que implementa podrían no ser compatible en todas las regiones. Además, puede haber limitaciones en la suscripción que impiden usando algunas regiones que son compatibles con el recurso. Estas limitaciones pueden deberse a problemas de tu país de impuestos o el resultado de una directiva realizada por el Administrador de suscripción para usar solo determinadas regiones. 

Para obtener una lista completa de todas las regiones admitidas para todos los servicios de Azure, vea [servicios por región](https://azure.microsoft.com/regions/#services). Sin embargo, esta lista puede incluir regiones que no se admite la suscripción. Puede determinar las regiones para un tipo de recurso determinado que es compatible con la suscripción a través del portal, API de REST, PowerShell o CLI de Azure.

### <a name="portal"></a>Portal

Puede ver las regiones admitidas para un tipo de recurso a través de los siguientes pasos:

1. Seleccione **más servicios** > **Explorador de recursos**.

    ![Explorador de recursos](./media/resource-manager-supported-services/select-resource-explorer.png)

2. Abra el nodo de **proveedores** .

    ![Seleccionar proveedores](./media/resource-manager-supported-services/select-providers.png)

3. Seleccione un proveedor de recursos y ver las regiones admitidas y las versiones API.

    ![proveedor de vista](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>API DE REST

Para descubrir qué regiones están disponibles para un tipo de recurso determinado en su suscripción, use la operación de [la lista de todos los proveedores de recursos](https://msdn.microsoft.com/library/azure/dn790524.aspx) . 

### <a name="powershell"></a>PowerShell

En el ejemplo siguiente se muestra cómo obtener las regiones admitidas para los sitios web.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
El resultado es similar a:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

### <a name="azure-cli"></a>CLI de Azure

En el ejemplo siguiente se devuelve todas las ubicaciones compatibles para cada tipo de recurso.

    azure location list

También puede filtrar los resultados de la ubicación con una herramienta JSON como [jq](https://stedolan.github.io/jq/).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Que devuelve:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## <a name="supported-api-versions"></a>Versiones de API compatibles

Cuando se implementa una plantilla, debe especificar una versión de la API de usar para crear cada recurso. La versión de la API corresponde a una versión de las operaciones de la API de REST que se publican en el proveedor de recursos. Como un proveedor de recursos permite nuevas características, libera una nueva versión de la API de REST. Por lo tanto, la versión de la API que especifique en la plantilla afecta a las propiedades que se pueden especificar en la plantilla. En general, que desee para seleccionar la versión más reciente de la API al crear plantillas. Las plantillas existentes, puede decidir si desea seguir utilizando una versión anterior de la API o actualizar la plantilla de la versión más reciente aprovechar las nuevas características.

### <a name="portal"></a>Portal

Determinar las versiones de API compatibles de la misma manera determinado admite regiones (mostradas anteriormente).

### <a name="rest-api"></a>API DE REST

Para descubrir qué versiones API están disponibles para los tipos de recursos, utilice la operación de [la lista de todos los proveedores de recursos](https://msdn.microsoft.com/library/azure/dn790524.aspx) . 

### <a name="powershell"></a>PowerShell

En el ejemplo siguiente se muestra cómo obtener las versiones de API disponibles para un tipo de recurso determinado.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
El resultado es similar a:
    
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

### <a name="azure-cli"></a>CLI de Azure

Puede guardar la información (incluidas las versiones de API disponibles) para un proveedor de recursos a un archivo con el siguiente comando.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Puede abrir el archivo y busque el elemento **apiVersions**

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre cómo crear plantillas de administrador de recursos, consulte [plantillas de administrador de recursos de Azure de creación](resource-group-authoring-templates.md).
- Para obtener información sobre cómo implementar los recursos, vea [implementar una aplicación con el Administrador de recursos de Azure plantilla](resource-group-template-deploy.md).
