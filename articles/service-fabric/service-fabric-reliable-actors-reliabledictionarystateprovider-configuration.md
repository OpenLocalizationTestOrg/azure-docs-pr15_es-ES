<properties
   pageTitle="Información general sobre la configuración de Azure servicio tela confiable actores ReliableDictionaryActorStateProvider | Microsoft Azure"
   description="Obtenga información acerca de cómo configurar los actores con estado de Azure tela de servicio de tipo ReliableDictionaryActorStateProvider."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/18/2016"
   ms.author="sumukhs"/>

# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Configurar agentes fiables--ReliableDictionaryActorStateProvider
Puede modificar la configuración predeterminada de ReliableDictionaryActorStateProvider cambiando el archivo settings.xml generado en la raíz del paquete de Visual Studio en la carpeta de configuración para el actor especificado.

El tiempo de ejecución de Azure servicio tela busca los nombres de sección predefinidos en el archivo settings.xml y consume los valores de configuración al crear los componentes de tiempo de ejecución subyacente.

>[AZURE.NOTE] Hacer **no** elimine o modifique los nombres de sección de las configuraciones siguientes en el archivo settings.xml generado en la solución de Visual Studio.

También son configuraciones globales que afectan a la configuración de ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Configuración global

La configuración global especificada en el manifiesto de clúster para el clúster en la sección KtlLogger. Permite la configuración de la ubicación compartida y tamaño más los límites de memoria global usados por el registrador. Tenga en cuenta que los cambios en el manifiesto clúster afectan a todos los servicios que use ReliableDictionaryActorStateProvider y confiable con estado.

El manifiesto de clúster es un único archivo XML que contiene las configuraciones que se aplican a todos los nodos y servicios en el clúster. El archivo se suele llamar ClusterManifest.xml. Puede ver el clúster manifiestos para el clúster con el comando de powershell Get-ServiceFabricClusterManifest.

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

## <a name="replicator-security-configuration"></a>Configuración de seguridad de Replicator
Las configuraciones de seguridad de Replicator sirven para proteger el canal de comunicación que se utiliza durante la replicación. Esto significa que los servicios no pueden ver de los demás tráfico de replicación, cómo garantizar que los datos que está altamente disponibles también están seguros.
De forma predeterminada, una sección de configuración de seguridad vacía impide la seguridad de la replicación.

### <a name="section-name"></a>Nombre de sección
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuración de Replicator
Configuraciones de Replicator se usan para configurar el duplicador que es el responsable de hacer el estado del proveedor de estado de Actor confiables replicar y conservar el estado localmente.
La configuración predeterminada se genera la plantilla de Visual Studio y debe ser suficiente. Esta sección se habla sobre configuraciones adicionales que están disponibles para ajustar el replicador.

### <a name="section-name"></a>Nombre de sección
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nombres de configuración

|Nombre|Unidad|Valor predeterminado|Notas|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Segundos|0,015|Período de tiempo para que el replicador en el secundario espera después de recibir una operación antes de enviar hacer copia de confirmación en el principal. Cualquier otras confirmaciones que se envíen para las operaciones que se procesan dentro de este intervalo se envían como una respuesta.||
|ReplicatorEndpoint|N/A.|Ningún valor predeterminado: parámetro obligatorio|Establecen la dirección IP y el puerto que usará el replicador principal y secundario para comunicarse con otros replicadores en la réplica. Esto debe hacer referencia a un extremo de recursos TCP en el manifiesto del servicio. Consulte los [recursos de manifiestos de servicio](service-fabric-service-manifest-resources.md) para obtener más información sobre cómo definir los recursos de extremo en manifiesto del servicio. |
|MaxReplicationMessageSize|Bytes|50 MB|Tamaño máximo de los datos de replicación que se pueden transmitir en un único mensaje.|
|MaxPrimaryReplicationQueueSize|Número de operaciones|8192|Número máximo de operaciones en la cola principal. Una operación se libera después de que el replicador principal recibe una confirmación de todos los replicadores secundarios. Este valor debe ser mayor que 64 y una potencia de 2.|
|MaxSecondaryReplicationQueueSize|Número de operaciones|16384|Número máximo de operaciones en la cola secundaria. Una operación se libera después de hacer que su estado es muy disponibles a través de persistencia. Este valor debe ser mayor que 64 y una potencia de 2.|
|CheckpointThresholdInMB|MB|200|Cantidad de espacio de archivo de registro después de que el estado está marcado.|
|MaxRecordSizeInKB|KB|1024|Tamaño de registro mayor que el replicador puede escribir en el registro. Este valor debe ser un múltiplo de 4 y mayor que 16.|
|OptimizeLogForLowerDiskUsage|Valor booleano|Verdadero|Cuando es true, el registro está configurado para que el archivo de registro dedicada de la réplica se crea mediante un archivo disperso de NTFS. Esto reduce el uso de espacio de disco real para el archivo. Cuando es false, se crea el archivo con asignaciones fijas, que proporciona que el mejor rendimiento de escritura.|
|SharedLogId|GUID|""|Especifica un único guid para identificar el archivo de registro compartido que se utiliza con esta réplica. Normalmente, los servicios no deben utilizar esta configuración. Sin embargo, si se especifica SharedLogId, a continuación, SharedLogPath también debe especificarse.|
|SharedLogPath|Nombre de ruta de acceso completa|""|Especifica la ruta de acceso completa donde se creará el archivo de registro compartido para esta réplica. Normalmente, los servicios no deben utilizar esta configuración. Sin embargo, si se especifica SharedLogPath, a continuación, SharedLogId también debe especificarse.|


## <a name="sample-configuration-file"></a>Archivo de configuración de ejemplo

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
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

## <a name="remarks"></a>Notas
La latencia de replicación de controles de parámetro BatchAcknowledgementInterval. Un valor de "0" resultado de la menor latencia posible a costa de rendimiento (como deben enviar más mensajes de confirmación y procesados, que contiene las confirmaciones menos).
Cuanto mayor sea el valor de BatchAcknowledgementInterval, cuanto mayor sea la replicación el rendimiento general latencia operación superior. Esto se traduce directamente a la latencia de transacciones ejecutadas.

El parámetro CheckpointThresholdInMB controla la cantidad de espacio en disco que puede usar el replicador para almacenar la información de estado dedicado archivo de la réplica de registro. Aumentar este valor a un valor mayor que el valor predeterminado podría provocar tiempos de nueva configuración cuando se agrega una nueva réplica al conjunto. Esto es debido a la transferencia de estado parcial que se lleve a cabo debido a la disponibilidad de historial más de las operaciones en el registro. Esto puede incrementar el tiempo de recuperación de una réplica después de un error.

Si establece OptimizeForLowerDiskUsage en true, estará provista en exceso espacio de archivo de registro para que active réplicas pueden almacenar más información de estado en sus archivos de registro mientras réplicas inactivas usará menos espacio en disco. Esto permite alojar réplicas más de un nodo. Si establece OptimizeForLowerDiskUsage en false, la información de estado se escribe en los archivos de registro más rápidamente.

La configuración de MaxRecordSizeInKB define el tamaño máximo de un registro que se puede escribir por el replicador en el archivo de registro. En la mayoría de los casos, el tamaño del registro de 1024 KB predeterminado es óptimo. Sin embargo, si el servicio está causando elementos mayores de datos forme parte de la información de estado, este valor que deba aumentar. Hay poco beneficio en realizar MaxRecordSizeInKB menor que 1024, como registros más pequeños usan solo el espacio necesario para el registro más pequeño. Esperamos que este valor deberá ser cambiado solo en algunos casos.

La configuración de SharedLogId y SharedLogPath siempre se utiliza conjuntamente para hacer que un servicio utiliza un registro de compartido independiente del registro compartido predeterminado para el nodo. Para obtener mayor eficacia tantos servicios como sea posible deben especificar el mismo registro compartido. Compartido archivos de registro se colocarán en discos que se utilizan únicamente para el archivo de registro compartido para reducir el conflicto de movimiento cabezas. Esperamos que estos valores necesarias para cambiar solo en algunos casos.
