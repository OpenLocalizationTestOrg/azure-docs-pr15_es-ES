<properties 
   pageTitle="Orígenes de datos en el análisis de registro | Microsoft Azure"
   description="Orígenes de datos definen los datos que recopila de análisis de registro de agentes y otro conectado orígenes.  En este artículo se describe el concepto de cómo registro análisis utiliza orígenes de datos, se explican los detalles de cómo configurarlos y proporciona un resumen de los distintos orígenes de datos disponibles."
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

# <a name="data-sources-in-log-analytics"></a>Orígenes de datos en el análisis de registro

Análisis de registro recopila los datos de los orígenes de conectado en el área de trabajo OMS y lo almacena en el repositorio OMS.  Los datos que se recopilan de cada definidos por los orígenes de datos que se configure.  Datos en el repositorio OMS se almacenan como un conjunto de registros.  Cada origen de datos crea los registros de un tipo determinado con cada tipo de su propio conjunto de propiedades.

![Iniciar la recopilación de datos de análisis](./media/log-analytics-data-sources/overview.png)

Orígenes de datos son diferentes de soluciones de OMS que también recopilar datos de orígenes conectados y crear registros en el repositorio OMS.  Se pueden agregar al área de trabajo desde la Galería de soluciones y normalmente proporcionará herramientas adicionales de análisis en el portal OMS soluciones.  

## <a name="summary-of-data-sources"></a>Resumen de orígenes de datos

Los orígenes de datos que están disponibles actualmente en el análisis de registro se muestran en la tabla siguiente.  Cada una tiene un vínculo a un artículo independiente con detalles para ese origen de datos.

| Origen de datos | Tipo de evento | Descripción |
|:--|:--|:--|
| [Registros personalizados](log-analytics-data-sources-custom-logs.md) | \<Nombre de registro\>_CL | Archivos de texto en Windows o Linux agentes que contiene la información de registro. |
| [Registros de eventos de Windows](log-analytics-data-sources-windows-events.md) | Evento | Eventos recopilados desde el registro de eventos en equipos con Windows. |
| [Contadores de rendimiento de Windows](log-analytics-data-sources-performance-counters.md) | Rendimiento | Contadores de rendimiento recopilan de equipos de Windows. |
| [Contadores de rendimiento de Linux](log-analytics-data-sources-performance-counters.md) | Rendimiento | Contadores de rendimiento recopilan equipos Linux. |
| [Registros de IIS](log-analytics-data-sources-iis-logs.md) | W3CIISLog | Internet Information Services se registra en formato de W3C. |
| [Registro del sistema](log-analytics-data-sources-syslog.md) | Registro del sistema | Eventos de registro del sistema en equipos con Windows o Linux. |

## <a name="configuring-data-sources"></a>Configuración de orígenes de datos

Configurar orígenes de datos en el menú de **datos** de **configuración**de análisis de registro.  Cualquier configuración se entrega a todos los orígenes conectados en el área de trabajo OMS.  Actualmente no puede excluir a los agentes de esta configuración.

![Configurar eventos de Windows](./media/log-analytics-data-sources/configure-events.png)

2. En la consola OMS seleccione el icono de **configuración** .
3. Seleccionar **datos**.
4. Haga clic en el origen de datos para configurar.
5. Siga el vínculo a la documentación de cada origen de datos de la tabla anterior para obtener más información sobre su configuración.

## <a name="data-collection"></a>Recopilación de datos

Las configuraciones de origen de datos se entregarán a agentes que están conectados directamente a OMS en unos minutos.  Los datos especificados es recopilados desde el agente y se entregarán directamente al análisis de registro a intervalos específicos para cada origen de datos.  Consulte la documentación de cada origen de datos para estas características.

Para agentes de System Center Operations Manager (SCOM) en un grupo de administración conectada, configuraciones de origen de datos se convertirá a paquetes de administración y entregadas al grupo de administración cada 5 minutos de forma predeterminada.  El agente de descarga del paquete de administración como cualquier otro y recopila los datos especificados. Dependiendo del origen de datos los datos serán envía a un servidor de administración que envía los datos para el análisis de registro o el agente enviará los datos para el análisis de registro sin pasar por el servidor de administración. Consulte los [Detalles del conjunto de datos para características OMS y soluciones](log-analytics-add-solutions.md#data-collection-details-for-oms-features-and-solutions) para obtener información detallada.  Puede leer acerca de los detalles de la conexión SCOM y OMS y modificar la frecuencia de que la configuración se ha entregado al [Configurar la integración con System Center Operations Manager](log-analytics-om-agents.md).

## <a name="log-analytics-records"></a>Análisis de registros

Todos los datos recopilados por el análisis de registro se almacena en el repositorio OMS como registros.  Registros recopilados por los distintos orígenes de datos tendrán su propio conjunto de propiedades e identificados por su propiedad **tipo** .  Consulte la documentación para cada origen de datos y soluciones para obtener información detallada sobre cada tipo de registro.


## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [las soluciones](log-analytics-add-solutions.md) que agregar funcionalidad al análisis de registro y también recopilar datos en el repositorio OMS.
- Obtenga información acerca de las [búsquedas de registro](log-analytics-log-searches.md) analizar los datos recopilados desde orígenes de datos y soluciones.  
- Configurar [alertas](log-analytics-alerts.md) para notificarle proactiva de datos críticos recopilados soluciones y orígenes de datos.
