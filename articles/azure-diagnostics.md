<properties
    pageTitle="Información general sobre diagnóstico de Azure | Microsoft Azure"
    description="Usar diagnóstico de Azure para depurar, medir el rendimiento, supervisión, análisis del tráfico en servicios en la nube, máquinas virtuales y tela de servicio"
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/02/2016"
    ms.author="robb"/>


# <a name="what-is-microsoft-azure-diagnostics"></a>¿Qué es Microsoft Azure diagnósticos


Diagnóstico de Azure es la capacidad de Azure que permite la recopilación de datos de diagnósticos en una aplicación implementada. Puede usar la extensión de diagnósticos de un número de orígenes diferentes. En estos momentos son Web de servicio de nube de Azure y Roles de trabajo, máquinas virtuales de Azure ejecuta Microsoft Windows y servicio tela. Otros servicios de Azure tienen su propio diagnóstico independiente.

## <a name="data-you-can-collect"></a>Puede recopilar de datos

Diagnóstico de Azure puede recopilar los siguientes tipos de datos:

Origen de datos|Descripción
---|---
Contadores de rendimiento | Sistema operativo y contadores personalizados
Registros de aplicación     | Seguimiento de mensajes escritos por la aplicación
Registros de eventos de Windows   | Información enviada al sistema de registro de eventos de Windows
Origen de eventos de .NET    | Código de escritura de eventos con la clase de [origen de eventos](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) de .NET
Registros de IIS             | Información sobre los sitios web de IIS
ETW en función de manifiestos   | Eventos generados por todos los procesos de eventos de seguimiento para Windows
Volcados          | Información sobre el estado del proceso en caso de bloqueo de la aplicación
Registros de errores personalizado    | Registros creados por la aplicación o servicio
Registros de infraestructura de diagnóstico de Azure|Información acerca de diagnósticos de sí mismo

La extensión de diagnóstico de Azure puede transferir estos datos a una cuenta de almacenamiento de Azure o enviarlo a servicios como [Perspectivas de aplicación](./application-insights/app-insights-cloudservices.md). Puede usar los datos de depuración y solución de problemas, medir el rendimiento, uso de recursos, análisis del tráfico y planear la capacidad de supervisión y auditoría.


## <a name="versioning"></a>Control de versiones
Ver [historial de versiones de diagnóstico de Azure](azure-diagnostics-versioning-history.md).

## <a name="next-steps"></a>Pasos siguientes
Elija qué servicio está intentando recopilar diagnósticos en y use los artículos siguientes para empezar. Use los vínculos de diagnóstico de Azure general como referencia para tareas específicas.

## <a name="web-apps"></a>Aplicaciones Web
Tenga en cuenta que aplicaciones Web no utilice Diagnósticos de Azure. Encontrar la información equivalente en [Aplicaciones Web](./app-service-web/web-sites-enable-diagnostic-log.md)

## <a name="cloud-services-using-azure-diagnostics"></a>Servicios de nube mediante Diagnósticos de Azure
- Si utiliza Visual Studio, consulte [Usar Visual Studio para una aplicación de servicios de nube de seguimiento](./vs-azure-tools-debug-cloud-services-virtual-machines.md) para empezar. En caso contrario, vea
- [Cómo supervisar el uso de diagnósticos de Azure servicios en la nube](./cloud-services/cloud-services-how-to-monitor.md)
- [Configurar el diagnóstico de Azure en una aplicación de servicios de nube](./cloud-services/cloud-services-dotnet-diagnostics.md)

Para temas más avanzados, vea

- [Uso de diagnósticos de Azure con recomendaciones de aplicación para los servicios de nube](./application-insights/app-insights-cloudservices.md)
- [Seguimiento del flujo de una aplicación de servicios de nube con diagnósticos de Azure](./cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
- [Usar PowerShell para configurar diagnósticos de los servicios en la nube](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)


## <a name="virtual-machines-using-azure-diagnostics"></a>Máquinas virtuales con diagnósticos de Azure
- Si utiliza Visual Studio, vea [Usar Visual Studio para seguimiento máquinas virtuales de Azure](./vs-azure-tools-debug-cloud-services-virtual-machines.md) para empezar. En caso contrario, vea
- [Configurar el diagnóstico de Azure en una máquina Virtual de Azure](./virtual-machines-dotnet-diagnostics.md)

Para temas más avanzados, vea

- [Usar PowerShell para configurar diagnósticos en Azure máquinas virtuales de Windows](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)
- [Crear una máquina Virtual de Windows con la supervisión y el diagnóstico usando la plantilla de administrador de recursos de Azure](./virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)

## <a name="service-fabric-using-azure-diagnostics"></a>Uso de diagnósticos de Azure de tela de servicio
Introducción al [Monitor de una aplicación de servicio tela](./service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Muchos otros artículos de diagnósticos de servicio tela están disponibles en el árbol de navegación de la izquierda cuando se encuentre en este artículo.

## <a name="general-azure-diagnostics-articles"></a>Artículos de diagnósticos de Azure general
- [Configuración de esquema de diagnósticos de azure](https://msdn.microsoft.com/library/azure/mt634524.aspx) : Obtenga información sobre cómo cambiar el archivo de esquema para recopilar y enviar los datos de diagnóstico. Tenga en cuenta que también puede usar Visual Studio para cambiar el archivo de esquema.
- [¿Cómo los diagnósticos de Azure datos se almacenan en el almacenamiento de Azure](./cloud-services/cloud-services-dotnet-diagnostics-storage.md) - conocer los nombres de las tablas y BLOB donde se escriben los datos de diagnóstico.
- Aprenda a [Usar contadores de rendimiento de diagnósticos de Azure](./cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md).
- Aprenda a [información de diagnóstico de Azure ruta de impresiones de aplicación](./azure-diagnostics-configure-applicationinsights.md)
- Si tiene problemas con los diagnósticos de inicio o buscar los datos en tablas de almacenamiento de Azure, vea [Solución de problemas de diagnósticos de Azure](./azure-diagnostics-troubleshooting.md)
