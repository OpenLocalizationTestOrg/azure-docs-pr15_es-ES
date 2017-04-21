<properties
   pageTitle="Información general sobre la configuración de Azure servicio tela fiable actores KVSActorStateProvider | Microsoft Azure"
   description="Obtenga información acerca de cómo configurar los actores con estado de Azure tela de servicio de tipo KVSActorStateProvider."
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
   ms.date="09/20/2016"
   ms.author="sumukhs"/>

# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Configurar agentes confiables--KVSActorStateProvider
Puede modificar la configuración predeterminada de KVSActorStateProvider cambiando el archivo settings.xml generado en la raíz del paquete de Microsoft Visual Studio en la carpeta de configuración para el actor especificado.

El tiempo de ejecución de Azure servicio tela busca los nombres de sección predefinidos en el archivo settings.xml y consume los valores de configuración al crear los componentes de tiempo de ejecución subyacente.

>[AZURE.NOTE] Hacer **no** elimine o modifique los nombres de sección de las configuraciones siguientes en el archivo settings.xml generado en la solución de Visual Studio.

## <a name="replicator-security-configuration"></a>Configuración de seguridad de Replicator
Las configuraciones de seguridad de Replicator sirven para proteger el canal de comunicación que se utiliza durante la replicación. Esto significa que los servicios no pueden ver de los demás tráfico de replicación, garantizar que los datos que está altamente disponibles también están seguros.
De forma predeterminada, una sección de configuración de seguridad vacía impide la seguridad de la replicación.

### <a name="section-name"></a>Nombre de sección
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuración de Replicator
Configuraciones de Replicator configurar el duplicador que es el responsable de hacer que el estado del proveedor de estado de Actor altamente confiable.
La configuración predeterminada se genera la plantilla de Visual Studio y debe ser suficiente. Esta sección se habla sobre configuraciones adicionales que están disponibles para ajustar el replicador.

### <a name="section-name"></a>Nombre de sección
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nombres de configuración

|Nombre|Unidad|Valor predeterminado|Notas|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Segundos|0,015|Período de tiempo para que el replicador en el secundario espera después de recibir una operación antes de enviar hacer copia de confirmación en el principal. Cualquier otras confirmaciones que se envíen para las operaciones que se procesan dentro de este intervalo se envían como una respuesta.|
|ReplicatorEndpoint|N/A.|Ningún valor predeterminado: parámetro obligatorio|Establecen la dirección IP y el puerto que usará el replicador principal y secundario para comunicarse con otros replicadores en la réplica. Esto debe hacer referencia a un extremo de recursos TCP en el manifiesto del servicio. Consulte los [recursos de manifiestos de servicio](service-fabric-service-manifest-resources.md) para obtener más información sobre cómo definir los recursos de extremo en el manifiesto del servicio. |
|RetryInterval|Segundos|5|Período de tiempo después de que el replicador volver a transmite un mensaje si no recibe una confirmación de una operación.|
|MaxReplicationMessageSize|Bytes|50 MB|Tamaño máximo de los datos de replicación que se pueden transmitir en un único mensaje.|
|MaxPrimaryReplicationQueueSize|Número de operaciones|1024|Número máximo de operaciones en la cola principal. Una operación se libera después de que el replicador principal recibe una confirmación de todos los replicadores secundarios. Este valor debe ser mayor que 64 y una potencia de 2.|
|MaxSecondaryReplicationQueueSize|Número de operaciones|2048|Número máximo de operaciones en la cola secundaria. Una operación se libera después de hacer que su estado es muy disponibles a través de persistencia. Este valor debe ser mayor que 64 y una potencia de 2.|

## <a name="store-configuration"></a>Configuración del almacén
Configuraciones Store se usan para configurar el almacén local que se utiliza para conservar el estado que se va a replicar.
La configuración predeterminada se genera la plantilla de Visual Studio y debe ser suficiente. En esta sección se habla sobre configuraciones adicionales que están disponibles para ajustar el almacén local.

### <a name="section-name"></a>Nombre de sección
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Nombres de configuración

|Nombre|Unidad|Valor predeterminado|Notas|
|----|----|-------------|-------|
|MaxAsyncCommitDelayInMilliseconds|Milisegundos.|200|Establece el máximo por lotes intervalo para confirmaciones almacén local permanente.|
|MaxVerPages|Número de páginas|16384|El número máximo de páginas de la versión de la variable local almacena la base de datos. Determina el número máximo de transacciones pendientes.|

## <a name="sample-configuration-file"></a>Archivo de configuración de ejemplo

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
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
