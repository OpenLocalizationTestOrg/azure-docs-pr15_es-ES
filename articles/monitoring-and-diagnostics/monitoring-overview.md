<properties
    pageTitle="Información general sobre la supervisión de Microsoft Azure | Microsoft Azure"
    description="Introducción de nivel superior de supervisión y diagnóstico de Microsoft Azure incluidos alertas, webhooks, Autoescala y mucho más."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"l
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="robb"/>

# <a name="overview-of-monitoring-in-microsoft-azure"></a>Información general sobre la supervisión de Microsoft Azure

Este artículo proporciona una introducción conceptual de supervisar los recursos de Azure. Proporciona punteros a la información en determinados tipos de recursos.  Para obtener información avanzada sobre la supervisión de la aplicación de Azure no punto de vista, vea [Guía de supervisión y diagnóstico](../best-practices-monitoring.md).

Aplicaciones de nube son complejas con muchas partes móviles. Supervisión proporciona datos para asegurarse de que la aplicación sea hacia arriba y en funcionamiento en buen estado. También ayuda a evitar posibles problemas o solucionar los problemas más allá de los. Además, puede usar datos de supervisión para obtener recomendaciones profundos acerca de la aplicación. Esa información puede ayudarle a mejorar el rendimiento de la aplicación o mantenimiento o automatizar acciones que de lo contrario, se requeriría intervención manual.

El siguiente diagrama muestra una vista conceptual de supervisión de Azure, incluido el tipo de registros que puede recopilar y qué puede hacer con esos datos.   

![Modelo lógico de seguimiento y diagnóstico para recursos de cálculo no](./media/monitoring-overview/MonitoringAzureResources-non-compute_v3.png)

Figura 1: Modelo Conceptual de seguimiento y diagnóstico para recursos de cálculo no

<br/>

![Modelo lógico de seguimiento y diagnóstico para recursos de cálculo](./media/monitoring-overview/MonitoringAzureResources-compute_v3.png)

Figura 2: Modelo Conceptual de seguimiento y diagnóstico para recursos de cálculo


## <a name="monitoring-sources"></a>Orígenes de supervisión
### <a name="activity-logs"></a>Registros de actividad
Puede buscar el registro de actividad (anteriormente denominado operativas o registros de auditoría) para obtener información acerca de los recursos como se ve la infraestructura de Azure. El registro contiene información como horas cuando se crea o destruye recursos.  

### <a name="host-vm"></a>Host VM
**Calcular solo**


Algunos calcular recursos como servicios de nube, máquinas virtuales, y servicio tela tiene una máquina virtual de Host dedicada que interactúan. La máquina virtual de Host es el equivalente de VM raíz en el modelo de hipervisor Hyper-V. En este caso, puede recopilar métricas en solo la VM Host además del sistema operativo de invitado.  

Para otros servicios de Azure, no hay necesariamente una asignación de 1:1 entre los recursos y una máquina virtual de Host determinado por lo que no hay disponible métrica de máquina virtual de host.


### <a name="resource---metrics-and-diagnostics-logs"></a>Recursos - métricas y los registros de diagnóstico
Recopilar métricas varían según el tipo de recurso. Por ejemplo, máquinas virtuales de Windows proporciona estadísticas en el disco IO y el porcentaje de CPU. Pero no existen las estadísticas de una cola de Bus de servicio, que proporciona en su lugar métricas como el rendimiento de mensaje y el tamaño de cola.

Para recursos de cálculo puede obtener métricas en los módulos SO invitado y diagnósticos como diagnósticos de Azure. Diagnóstico de Azure le ayuda a recopilar y distribuir recopilar datos de diagnóstico en otras ubicaciones, incluido el almacenamiento de Azure.

Una lista de las mediciones recopilar actualmente está disponible en [admite métricas](monitoring-supported-metrics.md).

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Aplicación - registros de diagnóstico, registros de aplicaciones y mediciones
**Calcular solo**

Pueden ejecutar aplicaciones sobre el sistema operativo de invitado en el modelo de cálculo. Emiten su propio conjunto de registros y mediciones.

Tipos de métricas incluyen

- Contadores de rendimiento
- Registros de aplicación
- Registros de eventos de Windows
- Origen de eventos de .NET
- Registros de IIS
- ETW en función de manifiestos
- Volcados
- Registros de errores de cliente


## <a name="uses-for-monitoring-data"></a>Usos de los datos de supervisión

### <a name="visualize"></a>Visualizar
Visualizar datos en gráficos y diagramas supervisión le ayuda a encontrar tendencias mucho más rápido que buscar en los datos en Sí.  

Incluyen unos cuantos métodos de visualización:

- Usar el portal de Azure
- Datos de ruta de impresiones de aplicación de Azure
- Enrutar los datos a Microsoft PowerBI
- Enrutar los datos a una herramienta de visualización de terceros con cualquier transmisión live o mediante la herramienta de leer a partir de un archivo de almacenamiento de Azure

### <a name="archive"></a>Archivar
Supervisar datos normalmente se escriben en el almacenamiento de Azure y se conservan hasta que se elimine.

Algunas maneras de utilizar estos datos:

- Una vez escritos, puede tener otras herramientas dentro o fuera de Azure leerlo y procesa.
- Descargar los datos localmente para un archivo local o cambiar la directiva de retención en la nube para mantener los datos de largos períodos de tiempo.  
- Deje los datos en Azure almacenamiento indefinidamente, aunque tiene que pagar para el almacenamiento de Azure en función de la cantidad de datos que tenga.
-

### <a name="query"></a>Consulta
Puede usar la API de REST de Monitor de Azure cruz comandos de la interfaz de línea de comandos (CLI) de plataforma, cmdlets de PowerShell o el SDK de .NET para tener acceso a los datos en el sistema o el almacenamiento de Azure

Algunos ejemplos:

-  Obtener datos de una aplicación de supervisión personalizada que ha escrito
-  Creación de consultas personalizadas y enviar datos a una aplicación de terceros.

### <a name="route"></a>Ruta
Puede transmitir datos de supervisión en otras ubicaciones en tiempo real.

Algunos ejemplos:

- Enviar a aplicación perspectivas para poder usar las herramientas de visualización allí.
- Enviar a Hubs de evento para que pueda enrutar herramientas de terceros para realizar análisis en tiempo real.

### <a name="automate"></a>Automatizar
Puede usar los datos de supervisión para desencadenar eventos o procesos de par entero algunos ejemplos:

- Utilizar datos a instancias de cálculo Autoescala hacia arriba o hacia abajo en función de la carga de la aplicación.
- Enviar correos electrónicos cuando una métrica cruza un umbral predeterminado.
- Llamar a una dirección URL de web (webhook) para ejecutar una acción en un sistema de Azure
- Iniciar un runbook en Azure automatización para realizar una variedad de tareas

## <a name="methods-of-use"></a>Métodos de uso
En general, puede manipular el seguimiento de los datos, el enrutamiento y la recuperación mediante uno de los métodos siguientes. No todos los métodos están disponibles para todos los tipos de datos o acciones.

- [Portal de Azure](https://portal.azure.com)
- [PowerShell](insights-powershell-samples.md)  
- [Interfaz de línea de comandos de varias plataformas (CLI)](insights-cli-samples.md)
- [API DE REST](https://msdn.microsoft.com/library/dn931943.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/dn802153.aspx)

## <a name="azures-monitoring-offerings"></a>Ofertas de supervisión de Azure
Azure tiene ofertas disponibles para supervisar los servicios de infraestructura de vacío de telemetría de aplicación. La mejor estrategia de supervisión combina el uso de los tres para obtener información completa y detallada sobre el estado de los servicios.

- [Monitor de azure](http://aka.ms/azmondocs) : ofertas de visualización, consulta, enrutamiento, alertas, Autoescala y automatización de los datos de la infraestructura de Azure (registro de actividad) y cada recurso Azure individual (registros de diagnóstico). En este artículo forma parte de la documentación del Monitor de Azure. El nombre del Monitor de Azure se publicó el 25 de septiembre de Ignite 2016.  El nombre anterior era "Perspectivas Azure".  
- [Perspectivas de aplicación](https://azure.microsoft.com/documentation/services/application-insights/) : proporciona detección enriquecida y diagnósticos de problemas en el nivel de aplicación del servicio, bien integrado encima de datos de Azure supervisión. Es la plataforma de diagnósticos predeterminada para la aplicación de servicio Web Apps.  Puede redirigir datos de otros servicios.  
- Parte de [Análisis de registro](https://azure.microsoft.com/documentation/services/log-analytics/) del [Conjunto de aplicaciones de administración de las operaciones](https://www.microsoft.com/cloud-platform/operations-management-suite) – proporciona una solución de administración de TI holística para infraestructura de basada en nube de terceros (como AWS) además de los recursos de Azure y local.  Datos de Azure Monitor pueden enrutar directamente al análisis de registro para que pueda ver registros y métricas para todo su entorno en un único lugar.     


## <a name="next-steps"></a>Pasos siguientes
Más información acerca de

- [Monitor de Azure en un vídeo desde Ignite 2016](https://myignite.microsoft.com/videos/4977)
- [Introducción a Monitor de Azure](monitoring-get-started.md)
- [Diagnósticos de azure](../azure-diagnostics.md) si está intentando diagnosticar problemas en la aplicación de servicio de nube, Máquina Virtual o servicio tela.
- [Perspectivas de aplicación](https://azure.microsoft.com/documentation/services/application-insights/) si está intentando diagnósticos problemas en la aplicación Web de servicio de aplicación.
- [Solución de problemas de almacenamiento de Azure](../storage/storage-e2e-troubleshooting.md) cuando se usa el almacenamiento de Blobs, tablas o colas
- [Análisis de registro](https://azure.microsoft.com/documentation/services/log-analytics/) y la [serie de administración de operaciones](https://www.microsoft.com/cloud-platform/operations-management-suite)
