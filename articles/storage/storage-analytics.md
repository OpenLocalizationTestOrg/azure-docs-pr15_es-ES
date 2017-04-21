<properties
    pageTitle="Usar el análisis de almacenamiento para recopilar datos de registros y mediciones | Microsoft Azure"
    description="Análisis de almacenamiento permite realizar un seguimiento de los datos de métricas para todos los servicios de almacenamiento y recopilar registros para el almacenamiento de blobs de Windows, la cola y la tabla."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="storage-analytics"></a>Análisis de almacenamiento

## <a name="overview"></a>Información general

Análisis de almacenamiento de Azure realiza el registro y proporciona los datos de métricas para una cuenta de almacenamiento. Puede usar estos datos para las solicitudes de seguimiento, analizar tendencias de uso y diagnóstico de problemas con su cuenta de almacenamiento.

Para usar el análisis de almacenamiento, debe habilitarlo individualmente para cada servicio que desea supervisar. Se puede habilitar desde el [Portal de Azure](https://portal.azure.com). Para obtener más información, consulte [Monitor de una cuenta de almacenamiento en el Portal de Azure](storage-monitor-storage-account.md). También puede habilitar el análisis de almacenamiento mediante programación a través de la API de REST o la biblioteca de cliente. Utilizar las operaciones de [Obtener propiedades de servicio de blobs](https://msdn.microsoft.com/library/hh452239.aspx), [Obtener propiedades de servicio de cola](https://msdn.microsoft.com/library/hh452243.aspx), [Obtener propiedades de servicio de tabla](https://msdn.microsoft.com/library/hh452238.aspx)y [Obtener propiedades de servicio de archivo](https://msdn.microsoft.com/library/mt427369.aspx) para habilitar el análisis de almacenamiento para cada servicio.

Los datos agregados se almacenan en un blob conocido (para el registro) y en tablas conocidas (para métricas), que pueden tener acceso mediante el servicio de blobs de Windows y la API del servicio de tabla.

Análisis de almacenamiento tiene un límite de 20 TB de la cantidad de datos almacenados que es independientes del límite de su cuenta de almacenamiento total. Para obtener más información sobre facturación y directivas de retención de datos, vea [análisis de almacenamiento y facturación](https://msdn.microsoft.com/library/hh360997.aspx). Para obtener más información sobre los límites de cuenta de almacenamiento, vea [destinos de rendimiento y escalabilidad de almacenamiento de Azure](storage-scalability-targets.md).

Para obtener una guía detallada sobre el uso de análisis de almacenamiento y otras herramientas para identificar, diagnosticar y solucionar problemas relacionados con el almacenamiento de Azure, consulte [Monitor, diagnosticar y solucionar problemas de Microsoft Azure almacenamiento](storage-monitoring-diagnosing-troubleshooting.md).


## <a name="about-storage-analytics-logging"></a>Acerca del registro de análisis de almacenamiento

Análisis de almacenamiento registra información detallada sobre solicitudes correctas e incorrectas a un servicio de almacenamiento. Esta información puede utilizarse para supervisar solicitudes individuales y diagnóstico de problemas con un servicio de almacenamiento. Las solicitudes se registran en función del mejor esfuerzo.

Sólo si hay actividad de servicio de almacenamiento, se crean las entradas del registro. Por ejemplo, si una cuenta de almacenamiento tiene actividad en su servicio de blobs pero no en sus servicios de cola o tabla, se creará solo los registros relacionados con el servicio de blobs de Windows.

Registro de análisis de almacenamiento no está disponible para el servicio de archivos de Azure.

### <a name="logging-authenticated-requests"></a>Solicitudes de registro que se han autenticado

Se registran los siguientes tipos de solicitudes autenticadas:

- Solicitudes correctas.

- No se pudo solicitudes, incluidos el tiempo de espera, límite, red, autorización y otros errores.

- Solicitudes con una firma de acceso de compartido (SA), incluidas las solicitudes errores y correctas.

- Solicitudes de datos de análisis.

Solicitudes de análisis de almacenamiento, como registro creación o eliminación, no se ha iniciado sesión. Una lista completa de los datos registrados documentada en los temas de [operaciones de registro de análisis de almacenamiento y mensajes de estado](https://msdn.microsoft.com/library/hh343260.aspx) y el [Formato de registro de análisis de almacenamiento](https://msdn.microsoft.com/library/hh343259.aspx) .

### <a name="logging-anonymous-requests"></a>Solicitudes anónimas de registro
Se registran los siguientes tipos de solicitudes anónimas:

- Solicitudes correctas.

- Errores del servidor.

- Errores de tiempo de espera de cliente y el servidor.

- Error solicitudes GET con código de error 304 (no modificado).

Todas las demás error anónimas solicitudes no ha iniciado. Una lista completa de los datos registrados documentada en los temas de [operaciones de registro de análisis de almacenamiento y mensajes de estado](https://msdn.microsoft.com/library/hh343260.aspx) y el [Formato de registro de análisis de almacenamiento](https://msdn.microsoft.com/library/hh343259.aspx) .

### <a name="how-logs-are-stored"></a>¿Cómo se almacenan los registros
Todos los registros se almacenan en BLOB bloque en un contenedor denominado $logs, que se crea automáticamente cuando se habilita el análisis de almacenamiento para una cuenta de almacenamiento. El contenedor de $logs se encuentra en el espacio de nombres de blobs de la cuenta de almacenamiento, por ejemplo: `http://<accountname>.blob.core.windows.net/$logs`. Este contenedor no se puede eliminar una vez que se ha habilitado el análisis de almacenamiento, aunque se pueden eliminar su contenido.

>[Azure.NOTE] El contenedor $logs no se muestra cuando se realiza un contenedor en el listado de la operación, como el método [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) . Se debe tener acceso al directamente. Por ejemplo, puede usar el método [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) para tener acceso a los BLOB en la `$logs` contenedor.
Como las solicitudes se registran, análisis de almacenamiento cargará resultados intermedios como bloques. Periódicamente, el análisis de almacenamiento se confirmar estos bloques y hacer que estén disponibles como un blob.

Registros duplicados pueden existir para registros creados en la misma hora. Puede determinar si un registro es un duplicado comprobando el número de **ID** y **operación** .

### <a name="log-naming-conventions"></a>Convenciones de nomenclatura de registro
Cada registro se escribirá en el siguiente formato.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

La tabla siguiente describe cada atributo en el nombre del registro.

| Atributo         | Descripción                                                                                                                                                                                   |
|----------------   |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------   |
| < nombre del servicio >    | El nombre del servicio de almacenamiento. Por ejemplo: blobs, tabla o cola.                                                                                                                          |
| AAAA              | El año de cuatro dígitos para el registro. Por ejemplo: 2011.                                                                                                                                           |
| MM                | Mes de dos dígitos para el registro. Por ejemplo: 07.                                                                                                                                             |
| DD                | Mes de dos dígitos para el registro. Por ejemplo: 07.                                                                                                                                             |
| hh                | Hora de dos dígitos que indica la hora inicia para los registros, en formato de 24 horas UTC. Por ejemplo: 18.                                                                                     |
| mm                | El número de dos dígitos que indica el minuto inicio para los registros. Este valor no es compatible en la versión actual de análisis de almacenamiento y su valor siempre será 00.     |
| <counter>         | Contador base cero con seis dígitos que indica el número de blobs de registro generado para el servicio de almacenamiento en un período de horas. Este contador se inicia en 000000. Por ejemplo: 000001.     |

El siguiente es un nombre de registro de ejemplo completos que combina los ejemplos anteriores.

    blob/2011/07/31/1800/000001.log

A continuación se muestra un ejemplo de URI que se puede usar para tener acceso al registro anterior.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Cuando haya iniciado una solicitud de almacenamiento, el nombre del registro resultante está relacionado con la hora al completar la operación. Por ejemplo, si se ha completado una solicitud de GetBlob a 6:30 P.M. en 31/7/2011, el registro se escribirá con el prefijo siguiente:`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadatos del registro
Todos los blobs de registro se almacenan con los metadatos que se pueden usar para identificar qué datos de registro que contiene el blob. La tabla siguiente describe cada atributo de metadatos.

| Atributo     | Descripción                                                                                                                                                                                                                                                   |
|------------   |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| LogType       | Describe si el registro contiene información relacionada para leer, escribir o eliminar operaciones. Este valor puede incluir un tipo o una combinación de los tres, separados por comas. Ejemplo 1: escribir; Ejemplo 2: leer, escribir; Ejemplo 3: leer, escribir, eliminar.    |
| Hora de inicio     | La hora de una entrada en el registro, en el formulario de aaaa-MM-ddTHH. Por ejemplo: 2011-07-31T18:21:46Z.                                                                                                                                             |
| Hora de finalización       | La última hora de una entrada en el registro, en el formulario de aaaa-MM-ddTHH. Por ejemplo: 2011-07-31T18:22:09Z.                                                                                                                                               |
| LogVersion    | Versión del formato de registro. Actualmente, el único valor admitido es 1.0.                                                                                                                                                                                     |

La siguiente lista muestra los metadatos de ejemplo completos con los ejemplos anteriores.

- LogType = escritura

- Hora de inicio = 2011-07-31T18:21:46Z

- Hora de finalización = 2011-07-31T18:22:09Z

- LogVersion = 1.0

### <a name="accessing-logging-data"></a>Acceso a los datos del registro

Todos los datos de la `$logs` contenedor puede tener acceso mediante las API del servicio de blobs, incluidas las API de .NET proporcionadas por el Azure biblioteca administrada. El Administrador de la cuenta de almacenamiento puede leer y eliminar registros, pero no puede crear o actualizarlos. Los metadatos del registro y el nombre del registro se pueden usar cuando la consulta de un registro. Es posible que los registros de una hora determinada aparezca en el orden correcto, pero los metadatos siempre especifican el intervalo de tiempo de las entradas del registro en un registro. Por lo tanto, puede usar una combinación de nombres de registro y metadatos al buscar un registro concreto.

## <a name="about-storage-analytics-metrics"></a>Acerca de métricas de análisis de almacenamiento

Análisis de almacenamiento puede almacenar métricas que incluyen datos de estadísticas y la capacidad de transacción agregado sobre solicitudes a un servicio de almacenamiento. Se informan de transacciones en el nivel de operación de API, así como en el nivel de servicio de almacenamiento y capacidad está en el nivel de servicio de almacenamiento. Datos de métricas de pueden usarse para analizar el uso del servicio de almacenamiento, diagnosticar problemas con las solicitudes realizadas en el servicio de almacenamiento y para mejorar el rendimiento de las aplicaciones que utiliza un servicio.

Para usar el análisis de almacenamiento, debe habilitarlo individualmente para cada servicio que desea supervisar. Se puede habilitar desde el [Portal de Azure](https://portal.azure.com). Para obtener más información, consulte [Monitor de una cuenta de almacenamiento en el Portal de Azure](storage-monitor-storage-account.md). También puede habilitar el análisis de almacenamiento mediante programación a través de la API de REST o la biblioteca de cliente. Utilizar las operaciones de **Obtener propiedades del servicio** para habilitar el análisis de almacenamiento para cada servicio.

### <a name="transaction-metrics"></a>Métrica de transacción

Un conjunto sólido de datos está registrado en intervalos por horas o minutos para cada servicio de almacenamiento y solicita la operación de API, incluidos entrada/salida, disponibilidad, errores y clasifica porcentajes de solicitud. Puede ver una lista completa de los detalles de la transacción en el tema de [Esquema de la tabla de métrica de almacenamiento análisis](https://msdn.microsoft.com/library/hh343264.aspx) .

Datos de la transacción se registran en dos niveles: el nivel de servicio y el nivel de funcionamiento de la API. En el nivel de servicio, las estadísticas de resumen todos solicitan operaciones API se escriben en una entidad de tabla cada hora incluso si ninguna solicitud se ha realizado en el servicio. En el nivel de operación API, solo se escriben estadísticas a una entidad si la operación se ha solicitado dentro de esa hora.

Por ejemplo, si realiza una operación de **GetBlob** en su servicio de blobs, métrica de análisis de almacenamiento registrar la solicitud e incluir en los datos agregados para el servicio de blobs así como la operación **GetBlob** . Sin embargo, si no se solicita ninguna operación **GetBlob** durante la hora, una entidad no se escribirá en el `$MetricsTransactionsBlob` para esa operación.

Métrica de transacción se registra para las solicitudes de usuario y solicitudes de análisis de almacenamiento propiamente dicho. Por ejemplo, se registran las solicitudes de análisis de almacenamiento escribir registros y entidades de tabla. Para obtener más información acerca de cómo se facturación estas solicitudes, vea [análisis de almacenamiento y facturación](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Métrica de capacidad

>[AZURE.NOTE] Actualmente, métricas de capacidad solo están disponibles para el servicio de blobs de Windows. Métricas de capacidad para los servicios de la tabla y cola estará disponibles en futuras versiones de análisis de almacenamiento.

Datos de capacidad está registrados diaria de servicio de una cuenta de almacenamiento blobs de Windows y, a continuación, se escriben dos entidades de tabla. Una entidad proporciona estadísticas de los datos de usuario y la otra proporciona estadísticas sobre la `$logs` contenedor blob usado por el análisis de almacenamiento. La `$MetricsCapacityBlob` tabla incluye las siguientes estadísticas:

- **Capacidad**: la cantidad de almacenamiento utilizado por el servicio de blobs de la cuenta de almacenamiento, en bytes.

- **ContainerCount**: el número de contenedores de blob en servicio de la cuenta de almacenamiento blobs.

- **ObjectCount**: el número de blobs de bloque o página confirmadas y en el servicio de la cuenta de almacenamiento blobs.

Para obtener más información acerca de las métricas de capacidad, consulte el [Esquema de la tabla de métrica de almacenamiento análisis](https://msdn.microsoft.com/library/hh343264.aspx).

### <a name="how-metrics-are-stored"></a>¿Cómo se almacenan métricas

Todos los datos de métricas para cada uno de los servicios de almacenamiento de información se almacena en tres tablas reservados para ese servicio: una tabla para obtener información de la transacción, una tabla para obtener información de la transacción minuto y otra tabla para obtener información de la capacidad. Información de la transacción de transacción y minuto consta de los datos de convocatoria y respuesta y, a continuación, información de capacidad consta de los datos de uso de almacenamiento. Métrica de hora, minutos métricas y capacidad para BLOBs servicio de una cuenta de almacenamiento pueden tener acceso en tablas denominadas como se describe en la tabla siguiente.

| Nivel de métricas                         | Nombres de tabla                                                                                                                   | Versiones compatibles                                                                                                                        |
|------------------------------------   |-----------------------------------------------------------------------------------------------------------------------------  |---------------------------------------------------------------------------------------------------------------------------------------------- |
| Métricas por hora, ubicación principal      |  $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue                                                  | Versiones anteriores de 2013-08-15 solo. Aunque todavía se admiten los siguientes nombres, se recomienda que cambie al uso de las tablas siguientes.  |
| Métricas por hora, ubicación principal      | $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue               | Todas las versiones, incluida 2013-08-15.                                                                                                               |
| Minutos métricas, ubicación principal      | $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue         | Todas las versiones, incluida 2013-08-15.                                                                                                               |
| Métricas por hora, ubicación secundaria    | $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue         | Todas las versiones, incluida 2013-08-15. Debe habilitarse la replicación geo redundantes de acceso de lectura.                                                    |
| Minutos métricas, ubicación secundaria    | $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue   | Todas las versiones, incluida 2013-08-15. Debe habilitarse la replicación geo redundantes de acceso de lectura.                                                    |
| Capacidad (solo en el servicio Blob)          | $MetricsCapacityBlob                                                                                                          | Todas las versiones, incluida 2013-08-15.                                                                                                               |


Estas tablas se crean automáticamente cuando se habilita el análisis de almacenamiento para una cuenta de almacenamiento. Acceden a través del espacio de nombres de la cuenta de almacenamiento, por ejemplo:`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Acceso a los datos de métricas

Todos los datos en las tablas de métricas se puede acceder mediante el uso de la API del servicio de tabla, incluidas las API de .NET proporcionadas por el Azure biblioteca administrada. El Administrador de la cuenta de almacenamiento puede leer y eliminar entidades de tabla, pero no puede crear o actualizarlos.

## <a name="billing-for-storage-analytics"></a>Facturación para el análisis de almacenamiento

Está habilitado el análisis de almacenamiento por un propietario de la cuenta de almacenamiento; no está habilitado de forma predeterminada. Todos los datos de métricas se escriben por los servicios de una cuenta de almacenamiento. Como resultado, cada operación de escritura realizada por el análisis de almacenamiento es facturable. Además, la cantidad de almacenamiento que utiliza datos de métricas también es facturable.

Las siguientes acciones realizadas por el análisis de almacenamiento son facturables:

- Solicitudes para crear BLOB para iniciar sesión. 

- Solicitudes para crear entidades de tabla para métricas.

Si ha configurado una directiva de retención de datos, no se cargan para las transacciones de eliminar cuando el análisis de almacenamiento elimina datos de métricas y registro antiguos. Sin embargo, las transacciones de eliminar un cliente de son facturables. Para obtener más información acerca de las directivas de retención, vea [establecer una directiva de retención de datos de análisis de almacenamiento](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Comprender las solicitudes facturables

Cada solicitud realizada al servicio de almacenamiento de la cuenta es facturable o no facturable. Análisis de almacenamiento registra cada solicitud individual a un servicio, incluyendo un mensaje de estado que indica cómo se administra la solicitud. Asimismo, el análisis de almacenamiento almacena métricas para un servicio y las operaciones de la API de ese servicio, incluidos los porcentajes y el recuento de algunos mensajes de estado. Juntas, estas características pueden ayudarle a analizar las convocatorias facturables, realizar mejoras en la aplicación y diagnosticar problemas de solicitudes de los servicios. Para obtener más información sobre facturación, consulte [Descripción Azure almacenamiento facturación - ancho de banda, transacciones y capacidad](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Cuando se buscan los datos de análisis de almacenamiento, puede usar las tablas en el tema [operaciones de registro de análisis de almacenamiento y mensajes de estado](https://msdn.microsoft.com/library/azure/hh343260.aspx) para determinar qué solicitudes facturables. A continuación, puede comparar los registros y los datos de métricas a los mensajes de estado para ver si le han cobrado una solicitud en particular. También puede usar las tablas en el tema anterior a investigar la disponibilidad de un servicio de almacenamiento o la operación de API individual.

## <a name="next-steps"></a>Pasos siguientes

### <a name="setting-up-storage-analytics"></a>Configurar el análisis de almacenamiento
- [Monitor de una cuenta de almacenamiento en el Portal de Azure](storage-monitor-storage-account.md)
- [Habilitar y configurar el análisis de almacenamiento](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>Registro de análisis de almacenamiento  
- [Acerca del registro de análisis de almacenamiento](https://msdn.microsoft.com/library/hh343262.aspx)
- [Formato de registro de análisis de almacenamiento](https://msdn.microsoft.com/library/hh343259.aspx)
- [Análisis de almacenamiento iniciado operaciones y mensajes de estado](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>Métrica de análisis de almacenamiento
- [Acerca de la métrica de análisis de almacenamiento](https://msdn.microsoft.com/library/hh343258.aspx)
- [Esquema de la tabla de métrica de almacenamiento Analytics](https://msdn.microsoft.com/library/hh343264.aspx)
- [Análisis de almacenamiento iniciado operaciones y mensajes de estado](https://msdn.microsoft.com/library/hh343260.aspx)  
