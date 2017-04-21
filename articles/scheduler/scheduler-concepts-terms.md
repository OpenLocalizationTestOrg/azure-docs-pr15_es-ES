<properties
 pageTitle="Conceptos del programador, términos y entidades | Microsoft Azure"
 description="Conceptos de programador Azure, terminología y la jerarquía de entidad, incluidos los trabajos y colecciones de trabajo.  Muestra un ejemplo de un trabajo programado completo."
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>Conceptos del programador, terminología + jerarquía de entidad

## <a name="scheduler-entity-hierarchy"></a>Jerarquía de entidad del programador

La siguiente tabla describe los recursos principales expuestas o que utiliza la API del programador:

|Recursos | Descripción |
|---|---|
|**Colección de trabajo**|Una colección de trabajo contiene un grupo de tareas y mantiene la configuración, las cuotas y límites que están compartidos por trabajos dentro de la colección. Una colección de trabajo se crea un propietario de la suscripción y agrupa los trabajos en función de los límites de uso o de aplicación. Está restringido a una región específica. También permite la aplicación de cuotas para restringir el uso de todos los trabajos de esa colección. Las cuotas incluyen MaxJobs y MaxRecurrence.|
|**Trabajo**|Un trabajo define una única acción recurrente, simples o complejas estrategias de ejecución de. Pueden incluir acciones HTTP, cola de almacenamiento, cola de bus de servicio o solicitudes de tema de bus de servicio.|
|**Historial de trabajos**|Un historial de trabajos representa detalles para una ejecución de una tarea. Contiene éxito frente a errores, así como los detalles de la respuesta.|

## <a name="scheduler-entity-management"></a>Administración de la entidad de programador

En un nivel alto, el programador y la API de administración de servicio exponen las siguientes operaciones en los recursos:

|Capacidad|Dirección URI y descripción|
|---|---|
|**Administración de colecciones de trabajo**|OBTENER, colocar y eliminar soporte para crear y modificar colecciones de trabajo y los trabajos contenidos en ella. Una colección de trabajo es un contenedor para trabajos y se asigna a las cuotas y configuraciones compartidas. Ejemplos de cuotas, que se describe más adelante, son el número máximo de trabajos y menor intervalo de repetición. <p>Coloque y eliminar:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>Obtener:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p>
|**Administración del trabajo**|OBTENER, colocar, publicar, revisión y eliminar compatibilidad para crear y modificar trabajos. Todos los trabajos deben pertenecer a una colección de trabajo que ya existe, lo que no hay ninguna creación implícita. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p>|
|**Administración del historial de trabajo**|OBTENER soporte técnico para obtener el historial de ejecución de trabajo, como tiempo de trabajo y los resultados de la ejecución de trabajo de 60 días. Agrega soporte de parámetro de cadena de consulta para filtrado basado en estado y. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p>|

## <a name="job-types"></a>Tipos de tarea

Existen varios tipos de tareas: trabajos HTTP (incluidas las tareas HTTPS que admiten SSL), trabajos en cola almacenamiento, los trabajos de cola de bus de servicio y trabajos de tema de bus de servicio. Trabajos HTTP están ideales si tiene un punto final de una carga de trabajo o servicio existente. Puede usar trabajos en cola almacenamiento para enviar mensajes a colas de almacenamiento, para que las tareas estén ideales para cargas de trabajo que utiliza colas de almacenamiento. Asimismo, los trabajos de bus de servicio son ideales para cargas de trabajo que use temas y colas de bus de servicio.

## <a name="the-job-entity-in-detail"></a>Detalles de la entidad de "trabajo"

En un nivel básico, un trabajo programado tiene varias partes:

- La acción que se realizará cuando se activa el temporizador de trabajo  

- (Opcional) Ejecutar el trabajo  

- (Opcional) Cuándo y la frecuencia con que se repita el trabajo  

- (Opcional) Acción que se aplicará si se produce un error en la acción principal  

Internamente, un trabajo programado también contiene datos proporcionados por el sistema, como el tiempo de ejecución programado siguiente.

El código siguiente proporciona un ejemplo completo de un trabajo programado. En las siguientes secciones se proporcionan detalles.

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

Como se ve en el trabajo programado de ejemplo anterior, una definición de trabajo tiene varias partes:

- Hora de inicio ("hora de inicio")  

- Acción (""), que incluye la acción de error ("errorAction")

- Periodicidad ("periodicidad")  

- Estado ("")  

- Estado ("")  

- Vuelva a intentar directiva ("retryPolicy")  

Examinemos cada uno de ellos en detalle:

## <a name="starttime"></a>hora de inicio

"Hora de inicio" es la hora de inicio y permite que el llamador especificar una zona horaria en la conexión en [formato ISO 8601](http://en.wikipedia.org/wiki/ISO_8601).

## <a name="action-and-erroraction"></a>acción y errorAction

"Acción" es la acción que se invoca en cada aparición y describe un tipo de llamada de servicio. La acción es lo que se ejecutará en la programación proporcionada. Programador es compatible con HTTP, cola de almacenamiento, tema de bus de servicio y las acciones de cola de bus de servicio.

En el ejemplo anterior a la acción es una acción de HTTP. A continuación se muestra un ejemplo de una acción de cola de almacenamiento:

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

A continuación se muestra un ejemplo de una acción de tema de bus de servicio.

  "acción": {"tipo": "serviceBusTopic", "serviceBusTopicMessage": {"topicPath": "t1"  
      "espacio de nombres": "mySBNamespace", "tipo de transporte": "netMessaging", o puede ser netMessaging o AMQP "autenticación": {"sasKeyName": "QPolicy", "tipo": "sharedAccessKey"}, "mensaje": "Algún mensaje", "brokeredMessageProperties": {}, "customMessageProperties": {"nombre de la aplicación": "FromScheduler"}},}

A continuación se muestra un ejemplo de una acción de cola de bus de servicio:


  "acción": {"serviceBusQueueMessage": {"nombre": "T1"  
      "espacio de nombres": "mySBNamespace", "tipo de transporte": "netMessaging", o puede ser netMessaging o AMQP "autenticación": {  
        "sasKeyName": "QPolicy", "tipo": "sharedAccessKey"}, "mensaje": "Algún mensaje"  
      "brokeredMessageProperties": {}, "customMessageProperties": {"nombre de la aplicación": "FromScheduler"}}, "tipo": "serviceBusQueue"}

"errorAction" es el controlador de errores, la acción que se invoca cuando se produce un error en la acción principal. Puede usar esta variable para llamar a un extremo de control de errores o enviar una notificación al usuario. Esto puede utilizarse para llegar a un extremo secundario en el caso de que el sitio primario no está disponible (por ejemplo, en el caso de un desastre en el sitio del extremo) o puede usarse para notificar a un extremo de control de errores. Al igual que la acción primaria, la acción de error puede ser simple o compuesta lógica basada en otras acciones. Para obtener información sobre cómo crear un símbolo de SA, consulte [crear y usar una firma de acceso compartido](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="recurrence"></a>periodicidad

Periodicidad tiene varias partes:

- Frecuencia: Uno de minuto, hora, día, semana, mes, año  

- Intervalo: Intervalo con la frecuencia especificada para la repetición  

- Programación recomendada: especifique los minutos, horas, días de la semana, meses y monthdays de periodicidad  

- : Recuento de apariciones  

- Hora de finalización: ningún trabajo se ejecutará después de la hora de finalización  

Un trabajo es periódica si tiene un objeto periódico especificado en su definición de JSON. Si se especifican recuento y hora de finalización, se aplica la regla de finalización que suceda primero.

## <a name="state"></a>estado

El estado de la tarea es uno de los cuatro valores: habilitado, deshabilitado, completado o errores. Puede colocar o trabajos de revisión con el fin de actualizarlas en el estado habilitado o deshabilitado. Si un trabajo se ha completado o errores, que es un estado final que no se pueden actualizar (aunque puede eliminarse el trabajo). Un ejemplo de la propiedad de estado es la siguiente:


        "state": "disabled", // enabled, disabled, completed, or faulted
Tareas completadas y con errores se eliminan después de 60 días.

## <a name="status"></a>estado

Una vez que haya empezado una tarea del programador, se devuelve información sobre el estado actual de la tarea. Este objeto no es configurable por el usuario, se establece mediante el sistema. Sin embargo, se incluye en el objeto de trabajo (en lugar de un recurso vinculado independiente) para que uno puede obtener fácilmente el estado de una tarea.

Estado del trabajo incluye el momento de la ejecución del anterior (si existe), la hora de la siguiente ejecución programada (para trabajos en curso) y el recuento de la ejecución del trabajo.

## <a name="retrypolicy"></a>retryPolicy

Si se produce un error en una tarea del programador, es posible especificar una directiva de reintento para determinar cuándo y cómo se vuelve a intentar la acción. Esto es determinado por el objeto **retryType** : se establece en **Ninguno** si no hay ninguna directiva de reintento, como se indicó anteriormente. Establecer a **fijo** si hay una directiva de reintento.

Para establecer una directiva de reintento, se pueden especificar dos opciones adicionales: un intervalo de reintento (**retryInterval**) y el número de reintentos (**retryCount**).

El intervalo de reintento especificado con el objeto **retryInterval** , es el intervalo entre reintentos. El valor predeterminado es 30 segundos, su valor configurable mínimo es 15 segundos y su valor máximo es de 18 meses. Trabajos en colecciones de trabajo libre tienen un valor mínimo configurable de 1 hora.  Se ha definido en el formato ISO 8601. Del mismo modo, se especifica el valor del número de reintentos con el objeto **retryCount** ; es el número de veces que se intenta un reintento. El valor predeterminado es 4 y su valor máximo es 20\. ambos **retryInterval** y **retryCount** son opcionales. Se obtienen su predeterminada valores si **retryType** se establece como **fijo** y no se especifica explícitamente.

## <a name="see-also"></a>Vea también

 [¿Qué es programador?](scheduler-intro.md)

 [Empezar a usar a planificador en el portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en el programador de Azure](scheduler-plans-billing.md)

 [Cómo crear programaciones complejas y periodicidad avanzada con el programador de Azure](scheduler-advanced-complexity.md)

 [Referencia de API de REST de programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de programador Azure](scheduler-powershell-reference.md)

 [Azure programador de alta disponibilidad y confiabilidad](scheduler-high-availability-reliability.md)

 [Límites de programador Azure, valores predeterminados y códigos de error](scheduler-limits-defaults-errors.md)

 [Autenticación de salida de Azure programador](scheduler-outbound-authentication.md)
