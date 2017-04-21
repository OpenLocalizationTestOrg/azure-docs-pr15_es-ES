<properties 
   pageTitle="Registros de eventos de Windows en el análisis de registro | Microsoft Azure"
   description="Registros de eventos de Windows son uno de los orígenes de datos más comunes usados por el análisis de registro.  En este artículo se describe cómo configurar la colección de registros de eventos de Windows y detalles de los registros que se crean en el repositorio OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="windows-event-log-data-sources-in-log-analytics"></a>Orígenes de datos de registro de eventos de Windows en el análisis de registro

Registros de eventos de Windows son uno de los más comunes [orígenes de datos](log-analytics-data-sources.md) usados para agentes Windows ya que es el método usado por la mayoría de las aplicaciones para registrar información y errores.  Puede recopilar eventos de registros estándar como sistema y aplicación además de especificar los registros personalizados creados por las aplicaciones que necesita supervisar.

![Eventos de Windows](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Registros de eventos de Windows de configuración

Configurar los registros de eventos de Windows en el [menú datos de configuración de análisis de registro](log-analytics-data-sources.md#configuring-data-sources).

Análisis de registro solo recopilar eventos de los registros de eventos de Windows que se especifican en la configuración.  Puede agregar un nuevo registro, escriba el nombre del registro y haciendo clic en **+**.  Para cada registro, se recopilará sólo eventos con la gravedad seleccionado.  Compruebe la gravedad del registro concreto que se desean recopilar.  No puede proporcionar los criterios para filtrar eventos adicionales.

![Configurar eventos de Windows](media/log-analytics-data-sources-windows-events/configure.png)


## <a name="data-collection"></a>Recopilación de datos

Análisis de registro recopilará cada evento que coincida con una gravedad seleccionada de un registro de eventos supervisado mientras se crea el evento.  El agente grabará su posición en cada registro de eventos que recopila de.  Si el agente se desconecta durante un período de tiempo, a continuación, el análisis de registro recopilar eventos desde donde se quedó, incluso si los eventos que se crearon mientras el agente estaba sin conexión.


## <a name="windows-event-records-properties"></a>Propiedades de registros de eventos de Windows

Registros de eventos de Windows tienen un tipo de **evento** y las propiedades de la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| Equipo            | Nombre del equipo que se ha recopilado el evento. |
| EventCategory       | Categoría del evento. |
| EventData           | Todos los datos del evento en formato. |
| IdEvento             | Número del evento. |
| EventLevel          | Gravedad del evento en formato numérico. |
| EventLevelName      | Gravedad del evento en forma de texto. |
| Registro de eventos            | Nombre del registro de eventos que se ha recopilado el evento. |
| ParameterXml        | Valores de parámetro de evento en formato XML. |
| ManagementGroupName | Nombre del grupo de administración de agentes SCOM.  Para otros agentes, esto es AOI-<workspace ID> |
| RenderedDescription | Descripción del evento con valores de parámetro |
| Origen              | Origen del evento. |
| SourceSystem  | Tipo de agente que se ha recopilado el evento. <br> Conectar OpsManager: agente de Windows, ambos directa o SCOM <br> Linux: todos los agentes de Linux  <br> AzureStorage: diagnóstico de Azure |
| TimeGenerated       | Fecha y hora que se creó el evento de Windows. |
| Nombre de usuario            | Nombre de usuario de la cuenta que inició el evento. |



## <a name="log-searches-with-windows-events"></a>Búsquedas de registro con los eventos de Windows

La tabla siguiente proporciona diferentes ejemplos de búsquedas de registro que recuperar registros de eventos de Windows.

| Consulta | Descripción |
|:--|:--|
| Tipo = evento | Todos los eventos de Windows. |
| Tipo = evento EventLevelName = error | Todos los eventos de Windows con gravedad del error. |
| Tipo = evento & #124; Medir count() origen | Eventos de recuento de Windows por origen. |
| Tipo = evento EventLevelName = error & #124; Medir count() origen | Eventos de error de recuento de Windows origen. |

## <a name="next-steps"></a>Pasos siguientes

- Configurar el análisis de registro para recopilar otros [orígenes de datos](log-analytics-data-sources.md) para el análisis.
- Obtenga información acerca de las [búsquedas de registro](log-analytics-log-searches.md) analizar los datos recopilados desde orígenes de datos y soluciones.  
- Usar [Campos personalizados](log-analytics-custom-fields.md) para analizar los registros de eventos en campos individuales.
- Configurar la [colección de contadores de rendimiento](log-analytics-data-sources-performance-counters.md) de los agentes de Windows.