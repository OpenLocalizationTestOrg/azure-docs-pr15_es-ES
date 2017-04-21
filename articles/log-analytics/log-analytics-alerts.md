<properties 
   pageTitle="Las alertas de análisis de registro | Microsoft Azure"
   description="Las alertas de análisis de registro identificar información importante en el repositorio OMS y proactiva pueden avisarle de problemas o invocar acciones para intentar corregirlos.  En este artículo se describe cómo crear una regla de alerta y detalles las diferentes acciones que pueden realizar."
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
   ms.date="08/22/2016"
   ms.author="bwren" />

# <a name="alerts-in-log-analytics"></a>Alertas de análisis de registro

Las alertas de análisis de registro identifican información importante en el repositorio OMS.  Reglas de alerta automáticamente ejecutan búsquedas de registros según una programación y creen un registro de alerta si los resultados que coincidan con determinados criterios.  La regla, a continuación, puede ejecutar automáticamente una o varias acciones para informarle de la alerta de manera proactiva o invocar ningún otro proceso.   

![Alertas de análisis de registro](media/log-analytics-alerts/overview.png)

## <a name="creating-an-alert-rule"></a>Crear una regla de alerta
Para crear una regla de alerta, primero debe crear una búsqueda de registro de los registros que se debe invocar la alerta.  El botón de **alerta** , a continuación, estará disponible para que pueda crear y configurar la regla de alerta.

1.  Desde la página información general de OMS, haga clic en **Búsqueda de registros**.
2.  Cree una nueva consulta de búsqueda de registro o seleccione una búsqueda de registro guardado. 
3.  Haga clic en **alerta** en la parte superior de la página para abrir la pantalla **Agregar regla de alerta** .
4. Consulte las tablas siguientes para obtener más información acerca de las opciones para configurar la alerta.
5. Al proporcionar la ventana de tiempo para la regla de alerta, se mostrará el número de registros existentes que cumplen los criterios de búsqueda para dicha ventana de tiempo.  Esto puede ayudarle a determinar la frecuencia con la que le proporcionará el número de resultados que espera.
4.  Haga clic en **Guardar** para terminar la regla de alerta.  Se iniciará ejecutar inmediatamente.


![Agregar regla de alerta](media/log-analytics-alerts/add-alert-rule.png)

| (Propiedad) | Descripción |
|:--|:--|
| **Información de alertas** | |
| Nombre |  Nombre único para identificar la regla de alerta. |
| Gravedad | Gravedad de la alerta que se crea por esta regla. |
| Consulta de búsqueda | Seleccione **usar la consulta actual** a usar la consulta actual o seleccionar una búsqueda guardada existente de la lista.  La sintaxis de la consulta está disponible en el cuadro de texto donde puede modificar si es necesario.  |
| Ventana de tiempo | Especifica el intervalo de tiempo para la consulta.  La consulta devuelve sólo los registros que se crearon dentro de este intervalo de la hora actual.  Esto puede ser cualquier valor entre 5 minutos y 24 horas.  Debe ser mayor o igual que la frecuencia de alertas.  <br><br> Por ejemplo, si la ventana de tiempo se establece en 60 minutos, y la consulta se ejecuta en 1:15 PM, se devolverá solo los registros que se crea entre 12:15 PM y 1:15 PM. |
| **Programación** |     
| Umbral | Criterios para crear una alerta.  Si el número de registros devueltos por la consulta coincide con estos criterios, se crea una alerta. |
| Frecuencia de alertas | Especifica con qué frecuencia se debe ejecutar la consulta.  Puede ser cualquier valor entre 5 minutos y 24 horas.  Debe ser igual o inferior a la ventana de tiempo. |
| Suprimir alertas | Cuando se activan supresión para la regla de alerta, acciones de la regla se deshabilitan durante un período de tiempo después de crear una nueva alerta definido.  La regla aún se está ejecutando y creará registros de alerta si se cumplen los criterios.  Esto es para permitir que tiempo para corregir el problema sin ejecutar acciones duplicados. |
| **Acciones** | |
| Notificación de correo electrónico | Especifique **Sí** si desea que un correo electrónico que se envíen cuando se activa la alerta. |
| Asunto    | Asunto del correo electrónico.  No se puede modificar el cuerpo del mensaje de correo. |
| Destinatarios | Direcciones de todos los destinatarios de correo electrónico.  Si especifica más de una dirección, a continuación, separe las direcciones con un punto y coma (;). |
| Webhook | Especifique **Sí** si desea llamar a un webhook cuando se activa la alerta. |
| Dirección URL de Webhook | La dirección URL de la webhook. |
| Incluir la carga JSON personalizado | Seleccione esta opción si desea reemplazar la carga predeterminada con una carga personalizada. |
| Escriba su carga JSON personalizado | La carga personalizada para el webhook.  Consulte la sección anterior para obtener información detallada. |
| Runbook | Especifique **Sí** si desea iniciar un runbook de automatización de Azure cuando se activa la alerta. |
| Seleccione un runbook | Seleccione runbook para iniciar desde el runbooks en la cuenta de automatización configurada en su solución de automatización. |
| Ejecutar en | Seleccione **Azure** para ejecutar runbook en la nube de Azure.  Seleccione **Híbrido trabajador** ejecuten runbook en un [Híbrido Runbook trabajo](..\automation\automation-hybrid-runbook-worker.md) en su entorno local. |


## <a name="manage-alert-rules"></a>Administrar reglas de alertas
Puede obtener una lista de todas las reglas de alerta en el menú de **alertas** en la **configuración**de análisis de registro.  

![Administrar alertas](./media/log-analytics-alerts/configure.png)

1. En la consola OMS seleccione el icono de **configuración** .
2. Seleccione **alertas**.

Puede realizar varias acciones de esta vista.

- Desactivar una regla seleccionando **desactivar** junto a ella.
- Editar una regla de alerta haciendo clic en el icono de lápiz junto a ella.
- Quitar una regla de alerta, haga clic en el icono **X** junto a ella. 


## <a name="setting-time-windows"></a>Configuración de las ventanas de tiempo 

### <a name="event-alerts"></a>Alertas de eventos

Eventos incluyen orígenes de datos como registros de eventos de Windows, registro del sistema, y registros personalizados.  Desea crear una alerta cuando se crea un evento de error concreto, o cuando se crean varios eventos de errores dentro de una ventana de tiempo determinado.

Para advertir en un único evento, establezca el número de resultados en mayor que 0 y la frecuencia y la ventana de tiempo a 5 minutos.  Que se ejecute la consulta cada 5 minutos y compruebe la aparición de un evento creado desde la última vez que se ejecuta la consulta.  Una mayor frecuencia puede retrasar el tiempo entre el evento que se recopila y la alerta se ha creado.

Algunas aplicaciones pueden registrar un error ocasional que necesariamente no debería generar una alerta.  Por ejemplo, la aplicación puede reintentar el proceso que creó el evento de error y, a continuación, realice correctamente la próxima vez.  En este caso, no desea crear la alerta a menos que se crean varios eventos dentro de una ventana de tiempo determinado.  

En algunos casos, es aconsejable crear una alerta en ausencia de un evento.  Por ejemplo, un proceso puede iniciar eventos regulares para indicar que funciona correctamente.  Si no inicia sesión uno de estos eventos dentro de una ventana de tiempo determinado, debe crear una alerta.  En este caso se establezca el umbral en *menos de 1*.

### <a name="performance-alerts"></a>Alertas de rendimiento

[Datos de rendimiento](log-analytics-data-sources-performance-counters.md) se almacenan como registros en el repositorio OMS similar a los eventos.  El valor de cada registro es el promedio medido a lo largo de los últimos 30 minutos.  Si desea avisar cuando un contador de rendimiento supera un determinado umbral, umbral debe incluirse en la consulta.

Por ejemplo, si desea avisarle cuando se ejecuta el procesador 90% durante 30 minutos, debería usar una consulta como *tipo = Perf ObjectName = procesador CounterName = "% de tiempo de procesador" CounterValue > 90* y el umbral de la regla de alerta *mayor que 0*.  

 Dado que se agregan los [registros de rendimiento](log-analytics-data-sources-performance-counters.md) cada 30 minutos independientemente de la frecuencia que recopilar cada contador, una ventana de tiempo menor que 30 minutos no puede devolver registros.  Configuración de la ventana de tiempo en 30 minutos se asegurará de que obtendrá un único registro para cada origen conectada que representa el promedio en el tiempo.

## <a name="alert-actions"></a>Acciones de alerta

Además de crear un registro de alerta, puede configurar la regla de alerta para que ejecute automáticamente una o varias acciones.  Acciones de manera proactiva pueden le notifique de la alerta o invocar algún proceso que intenta solucionar el problema que se ha detectado.  Las secciones siguientes describen las acciones que están disponibles actualmente.

### <a name="email-actions"></a>Acciones de correo electrónico
Acciones de correo electrónico envíe un correo electrónico con los detalles de la alerta a uno o más destinatarios.  Puede especificar al asunto del mensaje de correo, pero su contenido es un formato estándar creado mediante el análisis de registro.  Incluye información de resumen, como el nombre de la alerta, además de detalles de un máximo de diez registros devueltos por la búsqueda de registros.  También incluye un vínculo a una búsqueda de registro de análisis de registro que se devolverá todo el conjunto de registros de esa consulta.   El remitente del mensaje de correo es *equipo de conjunto de aplicaciones de administración de operaciones de Microsoft &lt; noreply@oms.microsoft.com *. 


### <a name="webhook-actions"></a>Acciones de Webhook

Acciones de Webhook permiten invocar un proceso externo a través de una solicitud HTTP POST.  El servicio que se llama debe webhooks de soporte técnico y determinar cómo utilizará cualquier carga que recibe.  También podría llamar a una API de REST que específicamente no admite webhooks mientras la solicitud está en un formato de la API comprende.  Ejemplos de uso de un webhook en respuesta a una alerta usa un servicio como [margen de demora](http://slack.com) para enviar un mensaje con los detalles de la alerta o crear un incidente en un servicio como [PagerDuty](http://pagerduty.com/).  

Para obtener un tutorial completo de creación de una regla de alerta con un webhook para llamar a un servicio de ejemplo está disponible en [Webhooks en alertas de análisis de registro](log-analytics-alerts-webhooks.md).

Webhooks incluyen una dirección URL y una carga con formato JSON que son los datos que se envían al servicio externo.  De forma predeterminada, la carga incluirá los valores de la tabla siguiente.  Puede reemplazar este carga personalizado de su elección.  En ese caso puede usar las variables de la tabla para cada uno de los parámetros para incluir su valor en la carga personalizada.


| Parámetro | Variable | Descripción |
|:--|:--|:--|
| AlertRuleName | #alertrulename | Nombre de la regla de alerta. |
| AlertThresholdOperator | #thresholdoperator | Operador de umbral de la regla de alerta.  *Mayor* o *menor que*. |
| AlertThresholdValue | #thresholdvalue | Valor de umbral de la regla de alerta. |
| LinkToSearchResults | #linktosearchresults | Vínculo a búsqueda de registros de análisis de registro que devuelve los registros de la consulta que creó la alerta. |
| ResultCount  | #searchresultcount | Número de registros de los resultados de búsqueda. |
| SearchIntervalEndtimeUtc  | #searchintervalendtimeutc | Hora de finalización de la consulta en formato UTC. |
| SearchIntervalInSeconds | #searchinterval | Ventana de tiempo para la regla de alerta. |
| SearchIntervalStartTimeUtc  | #searchintervalstarttimeutc | Hora de inicio de la consulta en formato UTC. |
| SearchQuery | #SearchQuery | Consulta de búsqueda de registro usado por la regla de alerta. |
| SearchResults | Consulte más adelante | Registros devueltos por la consulta en formato JSON.  Se limita a los primeros 5.000 registros. |
| WorkspaceID | #workspaceid | Identificador de su área de trabajo OMS. |


Por ejemplo, puede especificar la siguiente carga personalizada que incluya un solo parámetro denominado *texto*.  El servicio que llama a esta webhook ¿se esperaba este parámetro.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Esta carga de ejemplo se resuelva algo parecido al siguiente cuando se envía a la webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Para incluir los resultados de búsqueda en una carga personalizada, agregue la siguiente línea como una propiedad de nivel superior de la carga de json.  

    "IncludeSearchResults":true

Por ejemplo, para crear una carga personalizada que incluya el nombre de alerta y los resultados de búsqueda, puede utilizar el siguiente. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Puede recorrer un ejemplo completo de creación de una regla de alerta con un webhook para iniciar un servicio externo en [webhook ejemplo de alerta de análisis de registro](log-analytics-alerts-webhooks.md).

### <a name="runbook-actions"></a>Acciones de runbook

Acciones de runbook iniciar un runbook automatización de Azure.  Para usar este tipo de acción, debe tener la [solución de automatización](log-analytics-add-solutions.md) instalado y configurado en el área de trabajo OMS.  Si no tiene instalada cuando se crea una nueva regla de alerta, se muestra un vínculo a su instalación.  Puede seleccionar desde la runbooks en la cuenta de automatización que configuró en la solución de automatización.

Acciones de runbook iniciar runbook mediante un [webhook](../automation/automation-webhooks.md).  Cuando se crea la regla de alerta, creará automáticamente una nueva webhook para runbook con el nombre **OMS alerta corrección** seguido de un GUID.  

No puede rellenar los parámetros del runbook directamente, pero el [parámetro $WebhookData](../automation/automation-webhooks.md) incluirá los detalles de la alerta, incluidos los resultados de la búsqueda de registros que se creó.  Runbook deberá definir **$WebhookData** como un parámetro para que pueda tener acceso a las propiedades de la alerta.  Los datos de alerta están disponibles en formato de json en una sola propiedad denominada **SearchResults** en la propiedad **RequestBody** de **$WebhookData**.  Esto tendrá con las propiedades de la tabla siguiente.


| Nodo | Descripción |
|:--|:--|
| Id.         | Ruta de acceso y GUID de la búsqueda. |
| __metadata | Información sobre la alerta incluidos el número de registros y el estado de los resultados de búsqueda. |
|  valor     |  Entrada independiente para cada registro en los resultados de búsqueda.  Los detalles de la entrada se coinciden con las propiedades y los valores del registro.   |

Por ejemplo, runbook siguiente desea extraer los registros devueltos por la búsqueda de registros y asignar propiedades diferentes en función del tipo de cada registro.  Tenga en cuenta que inicia runbook convirtiendo **RequestBody** desde json para que pueden trabajar con como un objeto en PowerShell.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value
    
    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer
        
        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }
        
        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="alert-records"></a>Registros de alerta

Registros de alerta crea reglas de alerta de análisis de registro tienen un **tipo** de **alerta** y un **SourceSystem** de **OMS**.  Tienen las propiedades de la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| Tipo          | *Alertas* |
| SourceSystem  | *OMS* |
| AlertSeverity | Nivel de gravedad de la alerta. |
| NombreAlerta     | Nombre de la alerta. |
| Consulta         | Texto de la consulta que se ha ejecutado.  |
| QueryExecutionEndTime   | Final del intervalo de tiempo para la consulta. |
| QueryExecutionStartTime | Inicio del intervalo de tiempo para la consulta.  |
| TimeGenerated | Fecha y hora de que creación de la alerta. |

Hay otros tipos de registros de alerta creados por la [solución de administración de alertas](log-analytics-solution-alert-management.md) y [exporta Power BI](log-analytics-powerbi.md).  Todos tienen un **tipo** de **alerta** , pero se distinguen por sus **SourceSystem**.




## <a name="next-steps"></a>Pasos siguientes

- Instale la [solución de administración de alertas](log-analytics-solution-alert-management.md) para analizar las alertas que creó en el análisis de registro junto con alertas recopiladas de System Center Operations Manager (SCOM).
- Obtenga más información acerca de [las búsquedas de registro](log-analytics-log-searches.md) que puede generar alertas.
- Completar un tutorial para [configurar un webook](log-analytics-alerts-webhooks.md) con una regla de alerta.  
- Obtenga información sobre cómo escribir [runbooks en Azure automatización](https://azure.microsoft.com/documentation/services/automation) para corregir problemas identificados en alertas.