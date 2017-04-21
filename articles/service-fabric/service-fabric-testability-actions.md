<properties
   pageTitle="Acción de prueba | Microsoft Azure"
   description="En este artículo se trata las acciones de prueba que se encuentra en tela de servicio de Microsoft Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="timlt"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/03/2016"
   ms.author="motanv;heeldin"/>

# <a name="testability-actions"></a>Acciones de prueba
Para simular una infraestructura poco confiable, tela de servicio de Azure proporciona, el programador, con formas para simular diversos errores reales y transiciones de estado. Se exponen como acciones de prueba. Las acciones son las API de bajo nivel de compatibilidad que producen una inserción de errores específicos, la transición de estado o la validación. Mediante la combinación de estas acciones, puede escribir escenarios de prueba completa de los servicios.

Servicio tela proporciona que algunos escenarios comunes de prueba se componen de estas acciones. Le recomendamos encarecidamente que utilizan estos escenarios integrados, que se seleccionan cuidadosamente para probar comunes transiciones de estado y las cajas de error. Sin embargo, acciones pueden utilizarse para crear escenarios de prueba personalizado al que desea agregar cobertura para escenarios que no están cubiertos por los escenarios integrados todavía o que están personalizadas adaptadas a su aplicación.

C# implementaciones de las acciones se encuentran en el conjunto de System.Fabric.dll. El módulo de PowerShell de tela de sistema se encuentra en el conjunto de Microsoft.ServiceFabric.Powershell.dll. Como parte de la instalación de runtime, está instalado el módulo de ServiceFabric PowerShell para permitir la facilidad de uso.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Ordenado frente a acciones de errores incorrectamente
Acciones de prueba se clasifican en dos sectores principales:

* Errores incorrectamente: estos errores simular errores como reinicio del equipo y proceso falla. En estos casos de errores, el contexto de ejecución del proceso se detiene inesperadamente. Esto significa que no puede ejecutar ninguna limpieza del estado antes de que la aplicación se inicie de nuevo.

* Errores correctas: estos errores simulan correctas acciones como réplica mueve y colocaciones desencadenados por Equilibrio de carga. En estos casos, el servicio recibe una notificación de cierre y puede limpiar el estado antes de salir.

Para la validación de mejor calidad, realizar la carga de trabajo de servicio y de negocio y provocar errores correctas e incorrectamente distintos. Errores incorrectamente ejercicio escenarios donde termina inesperadamente el proceso de servicio en el medio de algún flujo de trabajo. Esto comprueba la ruta de recuperación una vez que se restaura la réplica de servicio por servicio tela. Esto le ayudará a comprobar coherencia de los datos y si se mantiene correctamente el estado del servicio después de errores. El otro conjunto de prueba de errores (el correcto) que el servicio reacciona correctamente a réplicas servicio tela movidas. Esta prueba tratamiento de cancelación en el método RunAsync. El servicio debe buscar el token de cancelación está establecida, guardar correctamente su estado y salir del método RunAsync.

## <a name="testability-actions-list"></a>Lista de acciones de prueba

| Acción | Descripción | API administrada | Cmdlet de PowerShell | Errores GRACEFUL/incorrectamente |
|---------|-------------|-------------|-------------------|------------------------------|
|CleanTestState| Quita el estado de prueba de clúster en caso de un apagado incorrecto del controlador de prueba. | CleanTestStateAsync | Quitar ServiceFabricTestState | No aplicable |
| InvokeDataLoss | Induce pérdida de datos en una partición de servicio. | InvokeDataLossAsync | ServiceFabricPartitionDataLoss invocar | Correcto |
| InvokeQuorumLoss | Coloca una partición de servicio con estado determinado en pérdida de quórum. | InvokeQuorumLossAsync | ServiceFabricQuorumLoss invocar | Correcto |
| Mover principal | Mueve la réplica principal especificada de un servicio de estado para el nodo de clúster especificado. | MovePrimaryAsync | Mover ServiceFabricPrimaryReplica | Correcto |
| Mover secundario | Mueve la réplica secundaria actual de un servicio de estado a un nodo de clúster diferente. | MoveSecondaryAsync | Mover ServiceFabricSecondaryReplica | Correcto |
| RemoveReplica | Quitar una réplica de un clúster simula un error de réplica. Esto se cerrará la réplica y pasará a la función 'Ninguna', quitar todo el estado del clúster. | RemoveReplicaAsync | Quitar ServiceFabricReplica | Correcto |
| RestartDeployedCodePackage | Simula un error de proceso del paquete de código reiniciar implementado en un nodo en un clúster de un paquete de código. Esto anula el proceso de paquete de código, que se reinicia todas las réplicas del servicio de usuario hospedadas en ese proceso. | RestartDeployedCodePackageAsync | Reiniciar ServiceFabricDeployedCodePackage | Incorrectamente |
| RestartNode | Simula un error de nodo de clúster de servicio tela reiniciar un nodo. | RestartNodeAsync | Reiniciar ServiceFabricNode | Incorrectamente |
| RestartPartition | Reiniciar algunas o todas las réplicas de una partición simula un escenario de en negro en negro o clúster de centro de datos. | RestartPartitionAsync | Reiniciar ServiceFabricPartition | Correcto |
| RestartReplica | Simula un error de réplica reiniciar una réplica almacenada en un clúster, cierre la réplica y vuelva a abrirlo. | RestartReplicaAsync | Reiniciar ServiceFabricReplica | Correcto |
| StartNode | Inicia un nodo en un clúster que ya se ha detenido. | StartNodeAsync | ServiceFabricNode inicio | No aplicable |
| StopNode | Simula un error de nodo detención de un nodo en un clúster. El nodo permanecerá hacia abajo hasta que se llama StartNode. | StopNodeAsync | Detener ServiceFabricNode | Incorrectamente |
| ValidateApplication | Valide la disponibilidad y la salud de todos los servicios de servicio tela dentro de una aplicación, normalmente después de provocar algunos errores en el sistema. | ValidateApplicationAsync | ServiceFabricApplication de prueba | No aplicable |
| ValidateService | Valide la disponibilidad y la salud de un servicio de tela de servicio, normalmente después de provocar algunos errores en el sistema. | ValidateServiceAsync | ServiceFabricService de prueba | No aplicable |

## <a name="running-a-testability-action-using-powershell"></a>Ejecutar una acción de prueba con PowerShell

En este tutorial se muestra cómo realizar una acción de prueba con PowerShell. Obtendrá información sobre cómo ejecutar una acción de prueba en un clúster (cuadro) local o Azure. Microsoft.Fabric.Powershell.dll: módulo de PowerShell de tela de servicio: se instala automáticamente cuando se instala el servicio de Microsoft tela MSI. El módulo se carga automáticamente al abrir un símbolo del sistema de PowerShell.

Tutoriales segmentos:

- [Ejecutar una acción en un clúster de un solo cuadro](#run-an-action-against-a-one-box-cluster)
- [Ejecutar una acción en un clúster de Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Ejecutar una acción en un clúster de un solo cuadro

Para ejecutar una acción de prueba en un clúster local, conéctese al clúster y abra el símbolo del sistema de PowerShell en modo de administrador. Examinemos la acción **ServiceFabricNode reiniciar** .

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

A continuación se ejecuta la acción **ServiceFabricNode reiniciar** en un nodo denominado "nodo 1". El modo de finalización especifica que no debe comprobar si el nodo de reiniciar acción realmente se realizó correctamente. Especificar el modo de finalización como "Verify" hará que compruebe si la acción de reinicio realmente correctamente. En lugar de especificar directamente el nodo por su nombre, puede especificarlo mediante una clave de partición y el tipo de réplica, como sigue:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

Se recomienda **Reiniciar ServiceFabricNode** reiniciar un nodo tela de servicio en un clúster. Esto detendrá el proceso de Fabric.exe, que se reinicia todas las réplicas sistema servicio y usuario servicio hospedadas en ese nodo. Usar esta API para probar el servicio le ayuda a descubrir errores a lo largo de las rutas de recuperación de migración tras error. Le ayuda a simular errores en los nodos en el clúster.

La siguiente captura de pantalla muestra el comando de la capacidad de **Reiniciar ServiceFabricNode** en acción.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

El resultado de la primera **Get-ServiceFabricNode** (un cmdlet desde el módulo de PowerShell de servicio tela) muestra que el clúster local tiene cinco nodos: Node.1 a Node.5. Después de ejecuta la acción de capacidad (cmdlet) **Reiniciar ServiceFabricNode** en el nodo, denominado Node.4, vemos que se ha restablecido el tiempo de actividad del nodo.

### <a name="run-an-action-against-an-azure-cluster"></a>Ejecutar una acción en un clúster de Azure

Ejecutar una acción de capacidad (mediante PowerShell) en un clúster de Azure es similar a ejecutar la acción en un clúster local. La única diferencia es que, antes de ejecutar la acción, en lugar de conectarse al clúster local, debe conectarse primero al clúster de Azure.

## <a name="running-a-testability-action-using-c35"></a>Ejecutar una acción de prueba con C & #35;

Para ejecutar una acción de prueba mediante C#, primero debe conectarse al clúster mediante FabricClient. A continuación, obtenga los parámetros necesarios para ejecutar la acción. Parámetros diferentes pueden usarse para ejecutar la misma acción.
¿Buscando la acción RestartServiceFabricNode, una forma para ejecutarlo es con la información de nodo (nombre de nodo e Id. de instancia de nodo) en el clúster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Explicación de parámetro:

- **CompleteMode** especifica que el modo no debe comprobar si la acción de reinicio realmente realizó correctamente. Especificar el modo de finalización como "Verify" hará que compruebe si la acción de reinicio realmente correctamente.  
- **OperationTimeout** establece la cantidad de tiempo finalizar antes de que se produce una excepción TimeoutException la operación.
- **CancellationToken** permite una llamada pendiente Cancelar.

En lugar de especificar directamente el nodo por su nombre, puede especificar a través de una clave de partición y el tipo de réplica.

Para obtener más información, consulte [PartitionSelector y ReplicaSelector](#partition_replica_selector).


```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector y ReplicaSelector

### <a name="partitionselector"></a>PartitionSelector
PartitionSelector es una aplicación auxiliar expuesta en la capacidad de prueba y se utiliza para seleccionar una partición específica en la que desea realizar cualquiera de las acciones de prueba. Puede usar para seleccionar una partición específica si el identificador de partición se conoce con antelación. O bien, puede proporcionar la clave de partición y la operación resolverá el identificador de partición internamente. También tiene la opción de selección de una partición aleatoria.

Para usar esta aplicación auxiliar, cree el objeto PartitionSelector y seleccione la partición mediante uno de los métodos Select *. A continuación, pase el objeto PartitionSelector a la API que sea necesario. Si no está seleccionada, el valor predeterminado es una partición aleatoria.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector es una aplicación auxiliar expuesta en la capacidad de prueba y se usa para ayudar a seleccionar una réplica en la que desea realizar cualquiera de las acciones de prueba. Puede usar para seleccionar una réplica específica si el identificador de réplica se conoce con antelación. Además, tiene la opción de selección de una réplica principal o secundario aleatorio. ReplicaSelector se deriva de PartitionSelector, por lo que debe seleccionar la réplica y la partición en la que desea realizar la operación de prueba.

Para usar esta aplicación auxiliar, cree un objeto de ReplicaSelector y establezca la forma que desea seleccionar la réplica y la partición. A continuación, puede pasar a la API de que sea necesario. Si no está seleccionada, el valor predeterminado es una réplica aleatorio y partición aleatorio.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Pasos siguientes

- [Escenarios de prueba](service-fabric-testability-scenarios.md)
- Cómo probar el servicio
   - [Simular errores durante las cargas de trabajo de servicio](service-fabric-testability-workload-tests.md)
   - [Errores de comunicación de servicio de servicio](service-fabric-testability-scenarios-service-communication.md)
