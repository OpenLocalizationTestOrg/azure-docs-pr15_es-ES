<properties
    pageTitle="Recopilar datos de almacenamiento de Azure en la información general de análisis de registro | Microsoft Azure"
    description="Recursos de Azure pueden escribir registros y mediciones a una cuenta de almacenamiento de Azure, a menudo mediante los diagnósticos de Azure. Análisis de registro puede indizar estos datos y facilitar su búsqueda."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="collecting-azure-storage-data-in-log-analytics-overview"></a>Recopilar datos de almacenamiento de Azure en la información general de análisis de registro

Cuántos recursos Azure son capaces de escribir registros y mediciones a una cuenta de almacenamiento de Azure. Análisis de registro puede consumir estos datos y que sea más fácil supervisar los recursos de Azure.

Para escribir en el almacenamiento de Azure un recurso puede usar diagnóstico de Azure o tiene su propia forma para escribir datos. Pueden escribir estos datos en varios formatos a una de las siguientes ubicaciones:

+ Tablas de Azure
+ Blobs de Windows Azure
+ EventHub

Análisis de registro es compatible con servicios de Azure que escriben datos con los [registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Además, el análisis de registro es compatible con otros servicios que dan como resultado registros y mediciones en diferentes formatos y ubicaciones.  

>[AZURE.NOTE] Le cobrarán tasas de datos de Azure normal para almacenamiento y las transacciones cuando envíe diagnósticos a una cuenta de almacenamiento y al análisis de registro lee los datos de su cuenta de almacenamiento.

![Diagrama de almacenamiento de Azure](media/log-analytics-azure-storage/azure-storage-diagram.png)

## <a name="supported-azure-resources"></a>Recursos de Azure admitidos

Análisis de registro puede recopilar datos de los siguientes recursos de Azure:

| Tipo de recurso | Registros (categorías de diagnósticos) | Solución de análisis de registro |
| --------------------------------------- | -------------------------------- | --------------- |
| Información de la aplicación | Disponibilidad <br> Eventos personalizados <br> Excepciones <br> Solicitudes <br> | Aplicación perspectivas (vista preliminar) |
| Administración de la API | | *Ninguno* (Vista previa) |
| Automatización <br> Microsoft.Automation/AutomationAccounts | JobLogs <br> JobStreams          | AzureAutomation (vista previa) |
| Depósito clave <br> Microsoft.KeyVault/Vaults               | AuditEvent                       | KeyVault (vista previa) |
| Puerta de enlace de aplicación <br> Microsoft.Network/ApplicationGateways   | ApplicationGatewayAccessLog <br> ApplicationGatewayPerformanceLog | AzureNetworking (vista previa) |
| Grupo de seguridad de red <br> Microsoft.Network/NetworkSecurityGroups | NetworkSecurityGroupEvent <br> NetworkSecurityGroupRuleCounter | AzureNetworking (vista previa) |
| Tela de servicio                          | ETWEvent <br> Evento operativa <br> Evento Actor confiable <br> Evento de servicio confiable| ServiceFabric (vista previa) |
| Máquinas virtuales de Windows | Registro del sistema de Linux <br> Eventos de Windows <br> Registro de IIS <br> ETWEvent de Windows | *Ninguno* |
| Funciones Web <br> Funciones de trabajo | Registro del sistema de Linux <br> Eventos de Windows <br> Registro de IIS <br> ETWEvent de Windows | *Ninguno* |

>[AZURE.NOTE] Para la supervisión de Azure máquinas (Linux y Windows), se recomienda instalar la [extensión de máquina virtual de análisis de registro](log-analytics-azure-vm-extension.md). El agente le proporciona más profundos perspectivas en sus máquinas virtuales que si utiliza los diagnósticos escritos en el almacenamiento.

Para ayudar a prioridades registros adicionales para OMS analizar por votar en nuestra [página de comentarios](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy).


- Ver [registros de diagnóstico de Azure analizar mediante el análisis de registro](log-analytics-azure-storage-json.md) para obtener más información sobre cómo el análisis de registro puede leer los registros de los servicios de Azure que admiten [Azure registros de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md):
  - Depósito de clave de Azure
  - Automatización de Azure
  - Puerta de enlace de aplicación
  - Grupos de seguridad de red
- Vea [almacenamiento de blobs de uso para IIS y almacenamiento de la tabla de eventos](log-analytics-azure-storage-iis-table.md) para obtener más información sobre cómo el análisis de registro puede leer los registros para ese diagnósticos de escritura a almacenamiento de tablas o registros de IIS escritos en almacenamiento, de blobs de Azure servicios incluidos:
  - Tela de servicio
  - Funciones Web
  - Funciones de trabajo
  - Máquinas virtuales de Windows


Perspectivas aplicación es en vista previa privado y se usa exportar continuo al almacenamiento de blobs. Para unirse a la vista previa privada, póngase en contacto con el equipo de Microsoft Account o consulte los detalles en el [sitio de comentarios](https://feedback.azure.com/forums/267889-log-analytics/suggestions/6519248-integration-with-app-insights).

## <a name="next-steps"></a>Pasos siguientes

- [Registros de diagnóstico de Azure analizar mediante el análisis de registro](log-analytics-azure-storage-json.md) para leer los registros de Azure servicios que diagnósticos de escritura al almacenamiento de blobs en formato JSON.
- Ese diagnósticos de escritura a almacenamiento de tablas o registros de IIS escritos en almacenamiento de blobs de servicios de [almacenamiento de blobs de uso para IIS y almacenamiento de la tabla de eventos](log-analytics-azure-storage-iis-table.md) para leer los registros de Azure.
- [Habilitar soluciones](log-analytics-add-solutions.md) para proporcionar una perspectiva de los datos.
- [Usar consultas de búsqueda](log-analytics-log-searches.md) para analizar los datos.
