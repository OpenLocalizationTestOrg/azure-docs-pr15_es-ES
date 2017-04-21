<properties
   pageTitle="Provocar un verdadero caos en tela servicio clústeres | Microsoft Azure"
   description="Mediante inyección de errores y las API de servicio de análisis de clúster para administrar caos en el clúster."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="rsinha"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="motanv"/>

# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Producir caos controlado en clústeres tela de servicio
Sistemas distribuidos a gran escala como infraestructuras cloud inherente poco confiables. Azure tela de servicio permite a los desarrolladores escribir servicios confiables sobre una infraestructura confiable. Para escribir servicios sólidos, los desarrolladores necesitan puedan provocar errores de dicha infraestructura poco confiable para probar la estabilidad de sus servicios.

La inserción de errores y el servicio de análisis de clúster (también conocido como el servicio de análisis de errores) permite a los desarrolladores a producir acciones de errores para probar los servicios. Sin embargo, destinadas errores simuladas ayudarán hasta ahora. Para seguir las pruebas, puede usar caos.

Caos simula continuos, intercaladas errores (correctas e incorrectamente) en todo el clúster de largos períodos de tiempo. Después de configurar caos con la velocidad y el tipo de errores, puede iniciar o detener a través de las API de C# o PowerShell se generan errores en el clúster y el servicio.

Mientras se está ejecutando caos, genera eventos diferentes que capturan el estado de la ejecución en ese momento. Por ejemplo, una ExecutingFaultsEvent contiene todos los errores que se están ejecutando en esa iteración. Un ValidationFailedEvent contiene los detalles de un error que se ha encontrado durante la validación de clúster. Puede invocar la API GetChaosReportAsync para obtener el informe de ejecuciones caos.

## <a name="faults-induced-in-chaos"></a>Errores inducidas en caos
Caos genera errores en todo el clúster de servicio tela y comprime errores que se ven en meses o años en unas pocas horas. La combinación de errores intercaladas con la tasa de errores alta busca casos de esquina que se pierden en caso contrario. Este ejercicio caos lleva a una mejora significativa en la calidad del código del servicio.

Caos induce a errores de las siguientes categorías:

 - Reinicie el nodo
 - Reinicie un paquete de código implementado
 - Quitar una réplica
 - Reiniciar una réplica
 - Mover una réplica principal (configurable)
 - Mover una réplica secundaria (configurable)

Caos se ejecuta en varias iteraciones. Cada iteración consta de errores y validación de clúster durante el período especificado. Puede configurar el tiempo empleado para el clúster a regular y de validación correctamente. Si se encuentra un error de validación de clúster, caos genera y continúa una ValidationFailedEvent con la marca de tiempo UTC y los detalles del error.

Por ejemplo, considere la posibilidad de una instancia de caos se establece para que se ejecute durante una hora con un máximo de tres errores simultáneas. Caos induce tres errores y, a continuación, valide el estado de clúster. Recorre anterior pasa paso hasta que esté expresamente detenido a través de la API StopChaosAsync o una hora. Si el clúster se convierte en mal estado en cualquier iteración (es decir, no estabilizar dentro de un tiempo configurado), caos genera una ValidationFailedEvent. Este evento indica que algo ha sido incorrecta y que tenga una investigación.

En su forma actual, caos induce solo los errores de seguros. Esto significa que, en ausencia de errores externos, una pérdida de quórum o datos nunca se produce.

## <a name="important-configuration-options"></a>Opciones de configuración importantes
 - **TimeToRun**: Total de tiempo que se ejecuta de caos antes de que finalice con éxito. Puede dejar de caos antes de que se ejecute durante el período de TimeToRun a través de la API StopChaos.
 - **MaxClusterStabilizationTimeout**: la cantidad máxima de tiempo de espera para que el clúster se convierten en buen estado antes de comprobar de nuevo en él. Esta espera es reducir la carga en el clúster mientras está recuperando. Las comprobaciones realizadas son:
    - Si el estado de clúster es aceptable
    - Si el estado del servicio es correcto
    - Si la réplica de destino establece tamaño se logra para la partición de servicio
    - Que no existen réplicas InBuild
 - **MaxConcurrentFaults**: el número máximo de errores de simultáneas induce en cada iteración. Es el caos más agresivo, el número mayor. El resultado failovers más complejos y combinaciones de transición. Caos garantiza que, en ausencia de errores externos, no hay ninguna pérdida de quórum o la pérdida de datos, independientemente de un valor alto cómo tiene esta configuración.
 - **EnableMoveReplicaFaults**: habilita o deshabilita los errores que provocan las réplicas primaria o secundarias mover. Estos errores están deshabilitados de forma predeterminada.
 - **WaitTimeBetweenIterations**: la cantidad de tiempo de espera entre iteraciones, es decir, después de un redondeo de errores y validación correspondiente.
 - **WaitTimeBetweenFaults**: la cantidad de tiempo de espera entre dos errores consecutivos en una iteración.

## <a name="how-to-run-chaos"></a>Cómo ejecutar caos
**C#:**

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```
**PowerShell:**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```
