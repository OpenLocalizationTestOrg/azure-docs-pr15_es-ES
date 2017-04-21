<properties 
    pageTitle="Habilitar métrica de almacenamiento en el portal de Azure | Microsoft Azure" 
    description="Cómo habilitar métrica de almacenamiento para los servicios de blobs, cola, tabla y archivo" 
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

# <a name="enabling-storage-metrics-and-viewing-metrics-data"></a>Habilitar métrica de almacenamiento y ver datos de métricas

[AZURE.INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Información general

De forma predeterminada, métrica de almacenamiento no está habilitado para los servicios de almacenamiento. Puede habilitar la supervisión con en el [Portal de Azure clásico](https://manage.windowsazure.com), Windows PowerShell, o mediante programación a través de un API de almacenamiento.

Al habilitar métrica de almacenamiento, debe elegir un período de retención para los datos: este período determina de cuánto almacenamiento servicio conserva las métricas y cargos por el espacio necesario para almacenarlos. Normalmente, debe usar un período de retención más corto para minutos métricas que cada horas métricas debido a la significativo espacio adicional necesario para métricas minutos. Debe elegir un período de retención que tenga tiempo suficiente para analizar los datos y descargar cualquier métricas que desea mantener para el análisis sin conexión o informes. Recuerde que se le también facturará para descargar los datos de métricas desde su cuenta de almacenamiento.

## <a name="how-to-enable-storage-metrics-using-the-azure-classic-portal"></a>Cómo habilitar métrica de almacenamiento con el Portal de clásico de Azure

En el [Portal de Azure clásica](https://manage.windowsazure.com), utilice la página Configurar una cuenta de almacenamiento que control métrica de almacenamiento. Para la supervisión, puede establecer un nivel y un período de retención en días para cada uno de blobs, tablas y colas. En cada caso, el nivel es uno de estos procedimientos:

- Desactivado: No hay métricas se recopilan.

- Mínima, Métrica de almacenamiento recopila un conjunto básico de métricas como entrada/salida, disponibilidad, latencia y porcentajes de éxito, que se agregan para los servicios de blobs, tabla y cola.

- Detallado: Métrica de almacenamiento recopila un conjunto completo de métricas que incluyen la misma métrica para cada operación de API, además de las mediciones de nivel de servicio de almacenamiento. Métricas detallados habilitar un análisis detallado de los problemas que se producen durante las operaciones de la aplicación.

Tenga en cuenta que el Portal de clásico de Azure no actualmente permiten configurar métricas minutos en su cuenta de almacenamiento; debe habilitar minutos métricas con PowerShell o mediante programación.


## <a name="how-to-enable-storage-metrics-using-powershell"></a>Cómo habilitar métrica de almacenamiento con PowerShell

Puede usar PowerShell en el equipo local para configurar métrica de almacenamiento en su cuenta de almacenamiento mediante el cmdlet de PowerShell de Azure Get-AzureStorageServiceMetricsProperty para recuperar la configuración actual y el cmdlet Set-AzureStorageServiceMetricsProperty para cambiar la configuración actual.

Los cmdlets que controlan métrica de almacenamiento Utilice los parámetros siguientes:

- Valores posibles de MetricsType son horas y minutos.

- Valores posibles de ServiceType son Blob, colas y tabla.

- Los valores posibles de MetricsLevel son ninguna (equivalente a desactivar en el Portal de clásico de Azure), servicio (equivalente a mínimas en el Portal de Azure clásico) y ServiceAndApi (equivalente a detallado en el Portal de Azure clásico).

Por ejemplo, el siguiente comando cambia minuto métrica para el servicio de blobs de Windows en su cuenta de almacenamiento predeterminada con el periodo de retención establecido en cinco días:

`Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`

El comando siguiente recupera la actual por hora métricas nivel y retención días para el servicio de blobs de Windows en su cuenta de almacenamiento predeterminada:

`Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob`

Para obtener información sobre cómo configurar los cmdlets de PowerShell de Azure para trabajar con la suscripción de Azure y cómo seleccionar la cuenta de almacenamiento predeterminada para usar, consulte: [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Cómo habilitar métrica de almacenamiento mediante programación

El siguiente fragmento de C# muestra cómo habilitar el registro para el servicio de Blob con la biblioteca de cliente de almacenamiento para .NET y métricas:

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Enable Storage Analytics logging and set retention policy to 10 days. 
    ServiceProperties properties = new ServiceProperties();
    properties.Logging.LoggingOperations = LoggingOperations.All;
    properties.Logging.RetentionDays = 10;
    properties.Logging.Version = "1.0";

    // Configure service properties for metrics. Both metrics and logging must be set at the same time.
    properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.HourMetrics.RetentionDays = 10;
    properties.HourMetrics.Version = "1.0";

    properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.MinuteMetrics.RetentionDays = 10;
    properties.MinuteMetrics.Version = "1.0";

    // Set the default service version to be used for anonymous requests.
    properties.DefaultServiceVersion = "2015-04-05";

    // Set the service properties.
    blobClient.SetServiceProperties(properties);
    
## <a name="viewing-storage-metrics"></a>Métrica de almacenamiento de visualización

Cuando haya configurado métrica de almacenamiento para supervisar su cuenta de almacenamiento, registra la métrica de un conjunto de tablas conocidos en su cuenta de almacenamiento. Puede usar la página de Monitor para su cuenta de almacenamiento en el Portal de Azure clásica para ver las estadísticas de cada horas cuando estén disponibles en un gráfico. En esta página en el Portal de Azure clásico, puede:

- Seleccione las métricas que desea trazar en el gráfico (la opción de métricas disponibles dependen si decide detallado o mínimas supervisión para el servicio en la página Configurar).


- Seleccione el intervalo de tiempo para las medidas que se muestra en el gráfico.


- Seleccione Usar una escala absoluta o relativa para trazar las mediciones.


- Configurar alertas de correo electrónico para que le notifique cuando una métrica específica alcance un valor determinado.


Si desea descargar la métrica de almacenamiento a largo plazo o para analizarlos localmente, debe usar una herramienta o escribir código para leer las tablas. Debe descargar el minutos métricas para el análisis. Las tablas no aparece si la lista de todas las tablas en la cuenta de almacenamiento, pero puede tener acceso a ellos directamente por su nombre. Muchas herramientas de exploración de almacenamiento de terceros conscientes de estas tablas y le permite verlos directamente (consulte el blog de publicar [Microsoft Azure almacenamiento exploradores](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) para ver una lista de herramientas disponibles).

### <a name="hourly-metrics"></a>Métricas por hora
- $MetricsHourPrimaryTransactionsBlob
- $MetricsHourPrimaryTransactionsTable
- $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Métricas minutos
- $MetricsMinutePrimaryTransactionsBlob
- $MetricsMinutePrimaryTransactionsTable
- $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Capacidad
- $MetricsCapacityBlob

Puede encontrar información detallada sobre los esquemas para estas tablas en el [Esquema de la tabla de métrica de almacenamiento análisis](https://msdn.microsoft.com/library/azure/hh343264.aspx). Las filas del ejemplo mostrar solo un subconjunto de las columnas disponibles, pero ilustran algunas características importantes de la forma que métrica de almacenamiento guarda estas métricas:

| PartitionKey  |       RowKey       |                    Marca de tiempo | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Disponibilidad | AverageE2ELatency | AverageServerLatency | PercentSuccess |
|---------------|:------------------:|-----------------------------:|---------------|-----------------------|--------------|-------------|--------------|-------------------|----------------------|----------------|
| 20140522T1100 |      usuario; Todos los      | 2014-05-22T11:01:16.7650250Z | 7             | 7                     | 4003         | 46801       | 100          | 104.4286          | 6.857143             | 100            |
| 20140522T1100 | usuario; QueryEntities | 2014-05-22T11:01:16.7640250Z | 5             | 5                     | 2694         | 45951       | 100          | 143.8             | 7.8                  | 100            |
| 20140522T1100 |  usuario; QueryEntity  | 2014-05-22T11:01:16.7650250Z | 1             | 1                     | 538          | 633         | 100          | 3                 | 3                    | 100            |
| 20140522T1100 | usuario; UpdateEntity  | 2014-05-22T11:01:16.7650250Z | 1             | 1                     | 771          | 217         | 100          | 9                 | 6                    | 100               |

En este ejemplo métricas minuto de datos, la clave de partición utiliza la hora con la resolución de minuto. La clave de la fila identifica el tipo de información que se almacena en la fila y esto se compone de dos partes de información, el tipo de acceso y el tipo de solicitud:

- El tipo de acceso es usuario o sistema, donde el usuario hace referencia a todas las solicitudes de usuario para el servicio de almacenamiento y sistema hace referencia a las solicitudes de análisis de almacenamiento.

- El tipo de solicitud es todo en cuyo caso es una línea de resumen, o que identifica la API específica como QueryEntity o UpdateEntity.


Los datos de ejemplo anteriores muestran todos los registros de un sola minuto (comenzando en 11:00 A.M.), por lo que el número de solicitudes de QueryEntities más el número de QueryEntity solicita más el número de UpdateEntity solicita agregar hasta siete, que es el total que se muestra en la fila del usuario: todos. Asimismo, puede obtener la latencia promedio de llevar a cabo 104.4286 en la fila del usuario: todos los calculando ((143.8 * 5) + 3 + 9) / 7.

Debe tener en cuenta la configuración de alertas en el Portal de clásico de Azure en la página de Monitor para que métrica de almacenamiento puede notificar automáticamente a los cambios importantes en el comportamiento de los servicios de almacenamiento. Si utiliza una herramienta de explorador de almacenamiento para descargar datos métricas en un formato delimitado, puede usar Microsoft Excel para analizar los datos. Consulte el blog [Microsoft Azure almacenamiento exploradores](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) para ver una lista de herramientas del explorador de almacenamiento disponible.



## <a name="accessing-metrics-data-programmatically"></a>Acceso a los datos de métricas mediante programación

El siguiente listado muestra C# código de ejemplo que tiene acceso a las mediciones minutos para un rango de minutos y muestra los resultados en una ventana de consola. Utiliza la versión 4 que incluya la clase CloudAnalyticsClient que simplifica el acceso a las tablas de métrica de almacenamiento de la biblioteca de almacenamiento de Azure.

    private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
    {
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    
    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
    Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
    var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
    var t = analyticsClient.GetMinuteMetricsTable(service);
    var opContext = new OperationContext();
    var query =
    from entity in metricsQuery
    // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
    // because they are calculated fields in the MetricsEntity class.
    // The PartitionKey identifies the DataTime of the metrics.
    where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0 
    select entity;
    
    // Filter on "user" transactions after fetching the metrics from Table Storage.
    // (StartsWith is not supported using LINQ with Azure table storage)
    var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
    var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
    Console.WriteLine(resultString);
    }
    }
    
    private static string MetricsString(MetricsEntity entity, OperationContext opContext)
    {
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
    string.Format("Time: {0}, ", entity.Time) +
    string.Format("AccessType: {0}, ", entity.AccessType) +
    string.Format("TransactionType: {0}, ", entity.TransactionType) +
    string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
    
    }




## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>¿Qué cargos se produce cuando se habilita la métrica de almacenamiento?

Escribir las solicitudes para crear entidades de tabla para métricas se cargarán a los coeficientes aplicables a todas las operaciones de almacenamiento de Azure.

Las solicitudes de leer y eliminar un cliente a datos de métricas también son facturables en tasas estándar. Si ha configurado una directiva de retención de datos, no se cargan cuando el almacenamiento de Azure elimina datos de métricas antiguos. Sin embargo, si se eliminan los datos de análisis, la cuenta se cargará para las operaciones de eliminación.

La capacidad de las tablas de métricas también es facturable: puede usar los siguientes para calcular la cantidad de capacidad se utiliza para almacenar los datos de métricas:

- Si cada hora un servicio usa cada API en cada servicio, aproximadamente 148KB de datos se almacena cada hora en las tablas de transacciones métricas si ha habilitado el servicio y resumen de nivel de API.

- Si cada hora un servicio usa cada API en cada servicio, aproximadamente 12KB de datos se almacena cada hora en las tablas de transacciones métricas si ha habilitado el nivel de servicio solo resumen.

- La tabla de capacidad para blobs tiene dos filas agregadas cada día (siempre que ha elegido el usuario de registros): Esto implica que todos los días el tamaño de esta tabla se incrementa en aproximadamente 300 bytes.

## <a name="next-steps"></a>Pasos siguientes:
[Habilitar análisis de almacenamiento de registro y acceso a los datos del registro](https://msdn.microsoft.com/library/dn782840.aspx)
 