<properties
   pageTitle="Use contadores de rendimiento de diagnóstico de Azure | Microsoft Azure"
   description="Use contadores de rendimiento en servicios de nube de Azure o máquina virtual para buscar botella y ajustar el rendimiento."
   services="cloud-services"
   documentationCenter=".net"
   authors="rboucher"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/29/2016"
   ms.author="robb" />

# <a name="create-and-use-performance-counters-in-an-azure-application"></a>Crear y utilizar contadores de rendimiento en una aplicación de Azure

Este artículo describe las ventajas de y cómo poner contadores de rendimiento en la aplicación de Azure. Puede usarlos para recopilar datos y encuentre botella para ajustar el rendimiento del sistema y de aplicación.

Contadores de rendimiento disponibles para Windows Server, IIS y ASP.NET también pueden recopilar y que utiliza para determinar el estado de las funciones web Azure, funciones de trabajo y máquinas virtuales de Windows. También puede crear y utilizar contadores personalizados.  

Puede examinar los datos de contador de rendimiento
1. Directamente en el host de la aplicación con la herramienta Monitor de rendimiento acceder a ellas mediante Escritorio remoto
2. Con System Center Operations Manager usando el módulo de administración de Azure
3. Con otras herramientas de supervisión que tienen acceso a los datos de diagnóstico transfieren al almacenamiento de Azure. Para obtener más información, vea [almacenar y ver datos de diagnóstico en el almacenamiento de Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) .  

Para obtener más información acerca de cómo supervisar el rendimiento de la aplicación en el [portal de clásica Azure](http://manage.azure.com/), vea [cómo supervisar servicios en la nube](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

Para obtener información detallada adicional sobre la creación de un registro y seguimiento de estrategia y usar diagnósticos y otras técnicas para solucionar problemas y optimizar aplicaciones de Azure, consulte [Solución de problemas de prácticas recomendadas para desarrollar aplicaciones de Azure](https://msdn.microsoft.com/library/azure/hh771389.aspx).


## <a name="enable-performance-counter-monitoring"></a>Habilitar la supervisión de rendimiento contador

Rendimiento no están habilitados de forma predeterminada. La aplicación o una tarea de inicio debe modificar la configuración predeterminada de agente de diagnóstico para incluir los contadores de rendimiento específicos que desea supervisar para cada instancia de rol.

### <a name="performance-counters-available-for-microsoft-azure"></a>Contadores de rendimiento disponibles para Microsoft Azure

Azure proporciona un subconjunto de los contadores de rendimiento disponibles para Windows Server, IIS y la pila ASP.NET. La siguiente tabla enumera el proveedor de interés especial para las aplicaciones de Azure.

|Categoría de contador: Objeto (instancia)|Nombre de contador      |Referencia|
|---|---|---|
|Excepciones de .NET CLR (_Global_)|# Excepciones iniciadas por segundo   |Contadores de rendimiento de excepción|
|Memoria .NET CLR (_Global_)    |% De tiempo del catálogo global            |Contadores de rendimiento de memoria|
|ASP.NET                      |Reinicio de la aplicación    |Contadores de rendimiento de ASP.NET|
|ASP.NET                      |Tiempo de ejecución de solicitud  |Contadores de rendimiento de ASP.NET|
|ASP.NET                      |Solicitudes desconectadas   |Contadores de rendimiento de ASP.NET|
|ASP.NET                      |Reinicio del proceso de trabajo |Contadores de rendimiento de ASP.NET|
|Aplicaciones de ASP.NET (__Total__)|Total de solicitudes        |Contadores de rendimiento de ASP.NET|
|Aplicaciones de ASP.NET (__Total__)|Solicitudes/seg.          |Contadores de rendimiento de ASP.NET|
|V4.0.30319 ASP.NET           |Tiempo de ejecución de solicitud  |Contadores de rendimiento de ASP.NET|
|V4.0.30319 ASP.NET           |Tiempo de espera de la solicitud       |Contadores de rendimiento de ASP.NET|
|V4.0.30319 ASP.NET           |Solicitudes actual        |Contadores de rendimiento de ASP.NET|
|V4.0.30319 ASP.NET           |Solicitudes en cola         |Contadores de rendimiento de ASP.NET|
|V4.0.30319 ASP.NET           |Solicitudes rechazadas       |Contadores de rendimiento de ASP.NET|
|Memoria                       |MB disponibles        |Contadores de rendimiento de memoria|
|Memoria                       |Bytes comprometidos         |Contadores de rendimiento de memoria|
|Procesador (_Total)            |% De tiempo de procesador        |Contadores de rendimiento de ASP.NET|
|TCPv4                        |Errores de conexión     |Objeto TCP|
|TCPv4                        |Conexiones establecidas |Objeto TCP|
|TCPv4                        |Restablecimiento de conexiones       |Objeto TCP|
|TCPv4                        |Segmentos enviados/seg.       |Objeto TCP|
|Interface(*) de red         |/ Seg.      |Objeto de la interfaz de red|
|Interface(*) de red         |/ Seg.          |Objeto de la interfaz de red|
|Interfaz de red (_2 adaptador de red de Bus de máquina Virtual de Microsoft)|/ Seg.|Objeto de la interfaz de red|
|Interfaz de red (_2 adaptador de red de Bus de máquina Virtual de Microsoft)|/ Seg.|Objeto de la interfaz de red|
|Interfaz de red (_2 adaptador de red de Bus de máquina Virtual de Microsoft)|Total de bytes/seg.|Objeto de la interfaz de red|

## <a name="create-and-add-custom-performance-counters-to-your-application"></a>Crear y agregar contadores personalizados a la aplicación

Azure tiene compatibilidad para crear y modificar contadores personalizados para las funciones de web y las funciones de trabajo. Los contadores pueden utilizarse para realizar un seguimiento y supervisar el comportamiento específico de la aplicación. Puede crear y eliminar categorías de contador de rendimiento personalizado y especificadores de una tarea de inicio, el rol web o el rol de trabajo con permisos elevados.

>[AZURE.NOTE] Código que realiza cambios a registros de rendimiento personalizado debe tener elevados permisos para ejecutar. Si el código está en una función web o trabajo, la función debe incluir la etiqueta <Runtime executionContext="elevated" /> en el archivo ServiceDefinition.csdef del rol de inicializarse correctamente.

Puede enviar los datos de contador de rendimiento personalizado a almacenamiento de Azure con el agente de diagnóstico.

Los datos de contador de rendimiento estándar generados por los procesos de Azure. Datos de contador de rendimiento personalizado deben crearse por la aplicación web rol de función o de trabajo. Para obtener información sobre los tipos de datos que se pueden almacenar en contadores personalizados, vea [Tipos de contador de rendimiento](https://msdn.microsoft.com/library/z573042h.aspx) . Vea [PerformanceCounters muestra](http://code.msdn.microsoft.com/azure/) un ejemplo que crea y conjuntos de datos de contador de rendimiento personalizada en una función web.

## <a name="store-and-view-performance-counter-data"></a>Almacenar y ver datos de contador de rendimiento

Azure almacena datos de contador de rendimiento con otra información de diagnóstico. Estos datos están disponibles para la supervisión remota mientras se ejecuta la instancia de rol con acceso al escritorio remoto para ver herramientas como Monitor de rendimiento. Para conservar los datos fuera de la instancia de rol, el agente de diagnósticos debe transferir los datos al almacenamiento de Azure. Puede configurar el límite de tamaño de los datos de contador de rendimiento en caché en el agente de diagnósticos o que esté configurado para formar parte de un límite compartido para todos los datos de diagnósticos. Para obtener más información sobre cómo configurar el tamaño del búfer, consulte [OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) y [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx). Para obtener información general de configurar el agente de diagnóstico para transferir datos a una cuenta de almacenamiento, consulte [almacenar y ver datos de diagnóstico en el almacenamiento de Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) .

Cada instancia de contador de rendimiento configurado se graba a una velocidad de muestreo especificada y los datos muestras se transfieren a la cuenta de almacenamiento por una solicitud de transferencia programada o una solicitud de transferencia a petición. Transferencias automáticas pueden programarse como una vez por minuto. Datos de contador de rendimiento transferidos por el agente de diagnósticos se almacenan en una tabla, WADPerformanceCountersTable, en la cuenta de almacenamiento. Esta tabla se puede acceder a ellas y consultar con métodos de almacenamiento de Azure estándar API. Vea [Microsoft Azure PerformanceCounters muestra](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9) un ejemplo de consulta y mostrar los datos de contador de rendimiento de la tabla WADPerformanceCountersTable.

>[AZURE.NOTE] Según la frecuencia de transferencia de agente de diagnósticos y latencia de cola, los datos más recientes de contador de rendimiento en la cuenta de almacenamiento pueden ser actualizada de unos minutos.

## <a name="enable-performance-counters-using-diagnostics-configuration-file"></a>Habilitar contadores de rendimiento mediante el archivo de configuración de diagnósticos

Use el procedimiento siguiente para habilitar contadores de rendimiento en la aplicación de Azure.

## <a name="prerequisites"></a>Requisitos previos

Esta sección se supone que ha importado al monitor de diagnósticos a la aplicación y agregado el archivo de configuración de diagnósticos la solución de Visual Studio (diagnostics.wadcfg en 2,4 SDK y a continuación o diagnostics.wadcfgx de SDK 2.5 y posteriores). Consulte los pasos 1 y 2 de [Diagnósticos de habilitación de servicios de nube de Azure y máquinas virtuales](./cloud-services-dotnet-diagnostics.md)) para obtener más información.

## <a name="step-1-collect-and-store-data-from-performance-counters"></a>Paso 1: Recopilar y almacenar datos de contadores de rendimiento

Después de haber agregado el archivo de diagnóstico para la solución de Visual Studio puede configurar la recopilación y el almacenamiento de datos de contador de rendimiento en una aplicación de Azure. Esto se hace agregando contadores de rendimiento para el archivo de diagnóstico. Datos de diagnóstico, incluidos los contadores de rendimiento, primero se recopilan en la instancia. Los datos se conservan en la tabla WADPerformanceCountersTable en el servicio de la tabla de Azure, por lo que también debe especificar la cuenta de almacenamiento en la aplicación. Si está probando su aplicación localmente en el emulador calcular, también puede almacenar datos de diagnósticos localmente en el emulador de almacenamiento. Antes de guardar datos de diagnóstico debe ir al [portal clásica Azure](http://manage.windowsazure.com/) y crear una cuenta de almacenamiento. Un procedimiento recomendado es encontrar su cuenta de almacenamiento en la misma ubicación geográfica como la aplicación de Azure con el fin de evitar el pago de los costos de ancho de banda externo y para reducir la latencia.

### <a name="add-performance-counters-to-the-diagnostics-file"></a>Agregar contadores de rendimiento para el archivo de diagnóstico

Hay muchos contadores que puede utilizar. En el ejemplo siguiente se muestra varios contadores de rendimiento que se recomiendan para la web y la supervisión de rol de trabajo.

Abra el archivo de diagnósticos (diagnostics.wadcfg en 2,4 SDK y a continuación o diagnostics.wadcfgx en SDK 2,5 y superior) y agregue lo siguiente al elemento DiagnosticMonitorConfiguration:

```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
       <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
    </PerformanceCounters>
```

El atributo bufferQuotaInMB, que especifica la cantidad máxima de almacenamiento del sistema de archivos que está disponible para el tipo de conjunto de datos (registros de Azure, registros de IIS, etcetera). El valor predeterminado es 0. Cuando se alcanza la cuota, se eliminan los datos más antiguos al agregar nuevos datos. La suma de todas las propiedades de bufferQuotaInMB debe ser mayor que el valor del atributo OverallQuotaInMB. Para obtener información más detallada de determinar cuánto almacenamiento será necesario para la recopilación de datos de diagnóstico, vea la sección TORUNDA de configuración de [Solución de problemas de los procedimientos recomendados para desarrollar aplicaciones de Azure](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx).

El atributo scheduledTransferPeriod, que especifica el intervalo entre programados transferencias de datos, se redondea hacia arriba hasta el minuto más cercano. En los ejemplos siguientes se establece en PT30M (30 minutos). Establecer el período de transferencia en un valor pequeño, como 1 minuto impacto negativo en el rendimiento de su aplicación en producción, pero puede ser útil para ver diagnósticos trabajando rápidamente cuando se está probando. El período de transferencia programada debe ser lo suficientemente pequeño para asegurarse de que no se sobrescriben los datos de diagnóstico en la instancia, pero lo suficientemente grande como para que no afectará al rendimiento de la aplicación.

El atributo counterSpecifier especifica el contador de rendimiento para recopilar. El atributo sampleRate especifica la velocidad a la que el contador de rendimiento debería tomarse, en este caso 30 segundos.

Una vez que haya agregado los contadores de rendimiento que se desean recopilar, guardar los cambios en el archivo de diagnóstico. A continuación, debe especificar la cuenta de almacenamiento que se conservarán los datos de diagnóstico para.

### <a name="specify-the-storage-account"></a>Especificar la cuenta de almacenamiento

Para conservar la información de diagnóstico a su cuenta de almacenamiento de Azure, debe especificar una cadena de conexión en el archivo de configuración (ServiceConfiguration.cscfg) de servicio.

Para Azure SDK 2,5 la cuenta de almacenamiento se pueden especificar en el archivo diagnostics.wadcfgx.

>[AZURE.NOTE] Estas instrucciones solo se aplican a Azure SDK 2,4 y a continuación. Para Azure SDK 2,5 la cuenta de almacenamiento se pueden especificar en el archivo diagnostics.wadcfgx.

Para establecer las cadenas de conexión:

1. Abra el archivo ServiceConfiguration.Cloud.cscfg utilizando el editor de texto y establezca la cadena de conexión para el almacenamiento. Los valores de *nombre de la cuenta* y *AccountKey* se encuentran en el portal de clásico Azure en el panel de la cuenta de almacenamiento, en administrar claves.

    ```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. Guarde el archivo ServiceConfiguration.Cloud.cscfg.

3. Abra el archivo ServiceConfiguration.Local.cscfg y compruebe que UseDevelopmentStorage está establecida en true.

    ```
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
Ahora que se establecen las cadenas de conexión, la aplicación conservará los datos de diagnóstico a su cuenta de almacenamiento cuando se implementa la aplicación.
4. Guarde y genere el proyecto y luego implementar la aplicación.

## <a name="step-2-optional-create-custom-performance-counters"></a>Paso 2: Contadores de rendimiento personalizados crear (opcional)

Además de los contadores de rendimiento predefinidos, puede agregar sus propios contadores personalizados para supervisar funciones web o de trabajo. Contadores personalizados pueden utilizarse para realizar un seguimiento y supervisar el comportamiento específico de la aplicación y pueden crearse o eliminado en una tarea de inicio, el rol web o el rol de trabajo con permisos elevados.

El agente de diagnóstico de Azure actualiza la configuración de contador de rendimiento del archivo .wadcfg un minuto después de iniciar.  Si crea contadores personalizados en el método OnStart y tomar más de un minuto para ejecutar las tareas de inicio, los contadores de rendimiento personalizados no habrá creados cuando el agente de diagnósticos de Azure intenta cargarlos.  En este escenario se muestra que los diagnósticos de Azure correctamente captura todos los datos de diagnósticos excepto los contadores personalizados.  Para resolver este problema, cree los contadores de rendimiento en una tarea de inicio o mover parte de la tarea de inicio funcionan para el método OnStart después de crear los contadores de rendimiento.

Realice los pasos siguientes para crear un rendimiento personalizado simple contador denominado "\MyCustomCounterCategory\MyButton1Counter":

1. Abra el archivo de definición de servicio (CSDEF) para la aplicación.
2. Agregar el elemento de tiempo de ejecución a la WebRole o el elemento de WorkerRole para permitir la ejecución con privilegios elevados:

    ```
    <runtime executioncontext="elevated"/>
    ```
3. Guarde el archivo.
4. Abra el archivo de diagnósticos (diagnostics.wadcfg en 2,4 SDK y a continuación o diagnostics.wadcfgx en SDK 2,5 y superior) y agregue lo siguiente a la DiagnosticMonitorConfiguration 

    ```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
     <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. Guarde el archivo.
6. Crear la categoría de contador de rendimiento personalizado en el método OnStart de su rol, antes de llamar a base. OnStart. En el ejemplo de C# siguiente se crea una categoría personalizada, si aún no existe:

    ```
    public override bool OnStart()
    {
    if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
    {
       CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

       // add a counter tracking user button1 clicks
       CounterCreationData operationTotal1 = new CounterCreationData();
       operationTotal1.CounterName = "MyButton1Counter";
       operationTotal1.CounterHelp = "My Custom Counter for Button1";
       operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
       counterCollection.Add(operationTotal1);

       PerformanceCounterCategory.Create(
         "MyCustomCounterCategory",
         "My Custom Counter Category",
         PerformanceCounterCategoryType.SingleInstance, counterCollection);

       Trace.WriteLine("Custom counter category created.");
    }
    else{
       Trace.WriteLine("Custom counter category already exists.");
    }

    return base.OnStart();
    }
    ```
7. Actualizar los contadores dentro de la aplicación. En el ejemplo siguiente se actualiza un contador de rendimiento personalizado en eventos Button1_Click:

    ```
    protected void Button1_Click(object sender, EventArgs e)
        {
         PerformanceCounter button1Counter = new PerformanceCounter(
           "MyCustomCounterCategory",
           "MyButton1Counter",
           string.Empty,
           false);
         button1Counter.Increment();
        this.Button1.Text = "Button 1 count: " +
           button1Counter.RawValue.ToString();
        }
    ```
8. Guarde el archivo.  

Datos de contador de rendimiento personalizado ahora se cobrará por el monitor de diagnóstico de Azure.

## <a name="step-3-query-performance-counter-data"></a>Paso 3: Consultar los datos de contador de rendimiento

Una vez que se implementa la aplicación y ejecute el monitor de diagnósticos comenzará recopilar contadores de rendimiento y conservar los datos con el almacenamiento de Azure. Use herramientas como el Explorador de servidor en Visual Studio, [El Explorador de almacenamiento de Azure](http://azurestorageexplorer.codeplex.com/)o [Administrador de diagnósticos de Azure](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) por Cerebrata para ver los datos de contadores de rendimiento de la tabla WADPerformanceCountersTable. Puede consultar el servicio de tabla mediante [C#](../storage/storage-dotnet-how-to-use-tables.d), [Java](../storage/storage-java-how-to-use-table-storage.md), [Node.js](../storage/storage-nodejs-how-to-use-table-storage.md), [Python](../storage/storage-python-how-to-use-table-storage.md), [Ruby](../storage/storage-ruby-how-to-use-table-storage.md)o [PHP](../storage/storage-php-how-to-use-table-storage.md)también mediante programación.

En el ejemplo de C# siguiente se muestra una consulta simple en la tabla WADPerformanceCountersTable y guarda los datos de diagnóstico en un archivo CSV. Una vez que se guardan los contadores de rendimiento en un archivo CSV puede usar las funciones gráficos en Microsoft Excel o alguna otra herramienta para visualizar los datos. Asegúrese de agregar una referencia a Microsoft.WindowsAzure.Storage.dll, que se incluye en el SDK de Azure para .NET octubre de 2012 y versiones posteriores. El ensamblado está instalado en el directorio de programa Files%\Microsoft SDKs\Microsoft Azure.NET SDK\version-num\ref\ %.

```
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ...

    // Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
    // you store your connection string using the Microsoft Azure service configuration
    // system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
    // to retrieve your storage connection string.  If you're not using Cloud Services, it's
    // recommended that you store the connection string in your web.config or app.config file.
    // Use the ConfigurationManager type to retrieve your storage connection string.

    string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

    // Get a reference to the storage account using the connection string.  You can also use the development
    // storage account (Storage Emulator) for local debugging.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
    CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

    // Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
    TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
       .Where(
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
          TableOperators.And,
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
          TableOperators.And,
          TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
          )
       )
    );

    // Execute the table query.
    IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

    // Process the query results and build a CSV file.
    StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

    foreach (PerformanceCountersEntity entity in result)
    {
       sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
          + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
    }

    StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
    sw.Write(sb.ToString());
    sw.Close();
```

Mapa de entidades a C# objetos con una clase personalizada que se deriva de **TableEntity**. El código siguiente define una clase de entidad que representa un contador de rendimiento de la tabla **WADPerformanceCountersTable** .


    public class PerformanceCountersEntity : TableEntity
    {
       public long EventTickCount { get; set; }
       public string DeploymentId { get; set; }
       public string Role { get; set; }
       public string RoleInstance { get; set; }
       public string CounterName { get; set; }
       public double CounterValue { get; set; }
    }


## <a name="next-steps"></a>Pasos siguientes
[Vista artículos adicionales en los diagnósticos de Azure] (.. / diagnostics.md de azure)
