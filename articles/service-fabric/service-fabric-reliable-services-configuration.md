<properties
   pageTitle="Información general sobre la configuración de Azure servicios tela fiable | Microsoft Azure"
   description="Obtenga información acerca de cómo configurar los servicios confiables con estado en tela de servicio de Azure."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="sumukhs"/>

# <a name="configure-stateful-reliable-services"></a>Configurar servicios confiables con estado

Hay dos conjuntos de valores de configuración de servicios confiables. Un conjunto es global para todos los servicios confiables en el clúster, mientras que el otro conjunto es específico de un determinado servicio confiable.

## <a name="global-configuration"></a>Configuración global

La configuración del servicio confiable global especificada en el manifiesto de clúster para el clúster en la sección KtlLogger. Permite la configuración de la ubicación compartida y tamaño más los límites de memoria global usados por el registrador. El manifiesto de clúster es un único archivo XML que contiene las configuraciones que se aplican a todos los nodos y servicios en el clúster. El archivo se suele llamar ClusterManifest.xml. Puede ver el clúster manifiestos para el clúster con el comando de powershell Get-ServiceFabricClusterManifest.

### <a name="configuration-names"></a>Nombres de configuración

|Nombre|Unidad|Valor predeterminado|Notas|
|----|----|-------------|-------|
|WriteBufferMemoryPoolMinimumInKB|Kilobytes|8388608|Número mínimo de KB asignar en modo de núcleo para el bloque de memoria del búfer de escritura de registrador. En este grupo de memoria se usa para almacenar en caché la información de estado antes de escribir en el disco.|
|WriteBufferMemoryPoolMaximumInKB|Kilobytes|Sin límite|Puede aumentar el tamaño máximo en el que el registrador de escribir el búfer de memoria.|
|SharedLogId|GUID|""|Especifica un único GUID para identificar el archivo de registro predeterminado compartido usado por todos los servicios fiables en todos los nodos del clúster que no se especifica la SharedLogId en su configuración específica del servicio. Si se especifica SharedLogId, SharedLogPath también debe especificarse.|
|SharedLogPath|Nombre de ruta de acceso completa|""|Especifica la ruta de acceso completa donde se usa el archivo de registro compartido por todos los servicios confiables en todos los nodos del clúster que no se especifica la SharedLogPath en su configuración específica del servicio. Sin embargo, si se especifica SharedLogPath, a continuación, SharedLogId también debe especificarse.|
|SharedLogSizeInMB|Megabytes|8192|Especifica el número de MB de espacio en disco para asignar de forma estática para el registro compartido. El valor debe ser mayor o 2048.|

### <a name="sample-cluster-manifest-section"></a>Sección manifiesto clúster de ejemplo
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Notas
El registrador tiene un grupo global de memoria asignada de memoria del núcleo no paginada disponible para todos los servicios fiables en un nodo de almacenamiento en caché de datos de estado antes de que se escriben en el registro dedicado asociada con la réplica de servicio confiable. El tamaño del grupo se controla mediante la configuración de WriteBufferMemoryPoolMinimumInKB y WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB especifica el tamaño inicial de este grupo de memoria y el tamaño inferior a la que puede reducir el bloque de memoria. WriteBufferMemoryPoolMaximumInKB es el tamaño más alto que puede alcanzar el bloque de memoria. Cada réplica de servicio confiable que se abre puede aumentar el tamaño del bloque de memoria en una cantidad determinada de sistema hasta WriteBufferMemoryPoolMaximumInKB. Si hay más demanda de memoria del bloque de memoria que está disponible, las solicitudes de memoria se retrasará hasta que la memoria disponible. Por lo tanto, si el bloque de memoria del búfer de escritura es demasiado pequeño para una determinada configuración puede sufrir rendimiento.

La configuración de SharedLogId y SharedLogPath siempre se utiliza conjuntamente para definir el GUID y la ubicación para el registro compartido predeterminado para todos los nodos en el clúster. El registro compartido predeterminado se usa para todos los servicios fiables que no se especifica la configuración en la settings.xml para el servicio específico. Mejor rendimiento compartido archivos de registro se colocarán en discos que se utilizan únicamente para el archivo de registro compartido para reducir el conflicto.

SharedLogSizeInMB especifica la cantidad de espacio en disco para asignar previamente para el registro compartido de forma predeterminada en todos los nodos.  SharedLogId y SharedLogPath no es necesario especificarlo en orden de SharedLogSizeInMB que se especifique.


## <a name="service-specific-configuration"></a>Configuración específica del servicio
Puede modificar las configuraciones predeterminadas de estado confiable los servicios mediante el paquete de configuración (Config) o la aplicación de servicio (código).

+ **Configuración** - configuración mediante el paquete de configuración se logra cambiando el archivo Settings.xml generado en la raíz del paquete de Microsoft Visual Studio en la carpeta de configuración para cada servicio en la aplicación.
+ **Código** - configuración a través del código se logra mediante la creación de un ReliableStateManager con un objeto ReliableStateManagerConfiguration con el conjunto de opciones que desee.

De forma predeterminada, el tiempo de ejecución de Azure servicio tela busca los nombres de sección predefinidos en el archivo Settings.xml y consume los valores de configuración al crear los componentes de tiempo de ejecución subyacente.

>[AZURE.NOTE] No **eliminar el archivo de los nombres de sección de las configuraciones siguientes en la Settings.xml** generado en la solución de Visual Studio a menos que se va a configurar el servicio a través de código.
Cambiar los nombres de paquete o sección config requieren un cambio de código al configurar la ReliableStateManager.


### <a name="replicator-security-configuration"></a>Configuración de seguridad de Replicator
Las configuraciones de seguridad de Replicator sirven para proteger el canal de comunicación que se utiliza durante la replicación. Esto significa que servicios no podrá ver de los demás tráfico de replicación, garantizar que los datos que está altamente disponibles también están seguros. De forma predeterminada, una sección de configuración de seguridad vacía impide la seguridad de la replicación.

### <a name="default-section-name"></a>Nombre de la sección de forma predeterminada
ReplicatorSecurityConfig

>[AZURE.NOTE] Para cambiar el nombre de sección, reemplace el parámetro replicatorSecuritySectionName al constructor ReliableStateManagerConfiguration cuando cree el ReliableStateManager para este servicio.


### <a name="replicator-configuration"></a>Configuración de Replicator
Configuraciones de Replicator configurar el duplicador que es responsable de estado del servicio confiable con estado confiables por replicar y conservar el estado localmente.
La configuración predeterminada se genera la plantilla de Visual Studio y debe ser suficiente. Esta sección se habla sobre configuraciones adicionales que están disponibles para ajustar el replicador.

### <a name="default-section-name"></a>Nombre de la sección de forma predeterminada
ReplicatorConfig

>[AZURE.NOTE] Para cambiar el nombre de sección, reemplace el parámetro replicatorSettingsSectionName al constructor ReliableStateManagerConfiguration cuando cree el ReliableStateManager para este servicio.


### <a name="configuration-names"></a>Nombres de configuración
|Nombre|Unidad|Valor predeterminado|Notas|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Segundos|0,015|Período de tiempo para que el replicador en el secundario espera después de recibir una operación antes de enviar hacer copia de confirmación en el principal. Cualquier otras confirmaciones que se envíen para las operaciones que se procesan dentro de este intervalo se envían como una respuesta.|
|ReplicatorEndpoint|N/A.|Ningún valor predeterminado: parámetro obligatorio|Establecen la dirección IP y el puerto que usará el replicador principal y secundario para comunicarse con otros replicadores en la réplica. Esto debe hacer referencia a un extremo de recursos TCP en el manifiesto del servicio. Consulte los [recursos de manifiestos de servicio](service-fabric-service-manifest-resources.md) para obtener más información sobre cómo definir los recursos de extremo en un manifiesto de servicio. |
|MaxPrimaryReplicationQueueSize|Número de operaciones|8192|Número máximo de operaciones en la cola principal. Una operación se libera después de que el replicador principal recibe una confirmación de todos los replicadores secundarios. Este valor debe ser mayor que 64 y una potencia de 2.|
|MaxSecondaryReplicationQueueSize|Número de operaciones|16384|Número máximo de operaciones en la cola secundaria. Una operación se libera después de hacer que su estado es muy disponibles a través de persistencia. Este valor debe ser mayor que 64 y una potencia de 2.|
|CheckpointThresholdInMB|MB|50|Cantidad de espacio de archivo de registro después de que el estado está marcado.|
|MaxRecordSizeInKB|KB|1024|Tamaño de registro mayor que el replicador puede escribir en el registro. Este valor debe ser un múltiplo de 4 y mayor que 16.|
|MinLogSizeInMB|MB|0 (sistema determinado)|Tamaño mínimo de registro de transacciones. El registro no se permitirá truncar a un tamaño inferior a esta configuración. 0 indica que el replicador determinará el tamaño mínimo del registro. Aumentar este valor aumenta la posibilidad de hacer copias parciales e incrementales desde posibilidades de registros relevantes se trunquen se reduce.|
|TruncationThresholdFactor|Factor|2|Determina qué tamaño del registro, se activará truncamiento. Umbral de truncamiento depende de MinLogSizeInMB multiplicado por TruncationThresholdFactor. TruncationThresholdFactor debe ser mayor que 1. MinLogSizeInMB * TruncationThresholdFactor debe ser menor que MaxStreamSizeInMB.|
|ThrottlingThresholdFactor|Factor|4|Determina qué tamaño del registro, iniciará la réplica está limitando. Límite umbral (en MB) depende de Max ((MinLogSizeInMB *ThrottlingThresholdFactor),(CheckpointThresholdInMB* ThrottlingThresholdFactor)). Límite umbral (en MB) debe ser mayor que el umbral de truncamiento (en MB). Umbral de truncamiento (en MB) debe ser menor que MaxStreamSizeInMB.|
|MaxAccumulatedBackupLogSizeInMB|MB|800|Máximo acumulado tamaño (en MB) de registros de copia de seguridad de una cadena de registro de copia de seguridad dado. Un solicitudes de copia de seguridad incremental se producirá un error si la copia de seguridad incremental generará un registro de copia de seguridad que haría que los registros de copia de seguridad acumulados desde la copia de seguridad completa relevante sea mayor de este tamaño. En estos casos, el usuario es necesaria para realizar una copia de seguridad completa.|
|SharedLogId|GUID|""|Especifica un único GUID para identificar el archivo de registro compartido que se utiliza con esta réplica. Normalmente, los servicios no deben utilizar esta configuración. Sin embargo, si se especifica SharedLogId, a continuación, SharedLogPath también debe especificarse.|
|SharedLogPath|Nombre de ruta de acceso completa|""|Especifica la ruta de acceso completa donde se creará el archivo de registro compartido para esta réplica. Normalmente, los servicios no deben utilizar esta configuración. Sin embargo, si se especifica SharedLogPath, a continuación, SharedLogId también debe especificarse.|
|SlowApiMonitoringDuration|Segundos|300|Establece el intervalo de supervisión de llamadas a la API administradas. Ejemplo: función de copia de seguridad de devolución de llamada proporcionada por usuario. Una vez haya pasado el intervalo, se enviará un informe de estado de advertencia al administrador de estado.|

### <a name="sample-configuration-via-code"></a>Ejemplo de configuración a través del código
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Archivo de configuración de ejemplo
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


### <a name="remarks"></a>Notas
Latencia de replicación de controles de BatchAcknowledgementInterval. Un valor de "0" resultado de la menor latencia posible a costa de rendimiento (como deben enviar más mensajes de confirmación y procesados, que contiene las confirmaciones menos).
Cuanto mayor sea el valor de BatchAcknowledgementInterval, cuanto mayor sea la replicación el rendimiento general latencia operación superior. Esto se traduce directamente a la latencia de transacciones ejecutadas.

El valor de CheckpointThresholdInMB controla la cantidad de espacio en disco que puede usar el replicador para almacenar la información de estado dedicado archivo de la réplica de registro. Aumentar este valor a un valor mayor que el valor predeterminado podría provocar tiempos de nueva configuración cuando se agrega una nueva réplica al conjunto. Esto es debido a la transferencia de estado parcial que se lleve a cabo debido a la disponibilidad de historial más de las operaciones en el registro. Esto puede incrementar el tiempo de recuperación de una réplica después de un error.

La configuración de MaxRecordSizeInKB define el tamaño máximo de un registro que se puede escribir por el replicador en el archivo de registro. En la mayoría de los casos, el tamaño del registro de 1024 KB predeterminado es óptimo. Sin embargo, si el servicio está causando elementos mayores de datos forme parte de la información de estado, este valor que deba aumentar. Hay poco beneficio en realizar MaxRecordSizeInKB menor que 1024, como registros más pequeños usan solo el espacio necesario para el registro más pequeño. Esperamos que este valor deberá ser cambiado en sólo algunos casos.

La configuración de SharedLogId y SharedLogPath siempre se utiliza conjuntamente para hacer que un servicio utiliza un registro de compartido independiente del registro compartido predeterminado para el nodo. Para obtener mayor eficacia tantos servicios como sea posible deben especificar el mismo registro compartido. Compartido archivos de registro se colocarán en discos que se utilizan únicamente para el archivo de registro compartido para reducir el conflicto de movimiento cabezas. Esperamos que este valor deberá ser cambiado en sólo algunos casos.

## <a name="next-steps"></a>Pasos siguientes
 - [Depurar la aplicación de servicio tela en Visual Studio](service-fabric-debugging-your-application.md)
 - [Referencia del programador para servicios confiables](https://msdn.microsoft.com/library/azure/dn706529.aspx)
