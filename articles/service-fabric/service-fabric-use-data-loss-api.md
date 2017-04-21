<properties
   pageTitle="Cómo invocar la pérdida de datos en servicios de tela | Microsoft Azure"
   description="Describe cómo utilizar la pérdida de datos api"
   services="service-fabric"
   documentationCenter=".net"
   authors="LMWF"
   manager="rsinha"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="lemai"/>
   
# <a name="how-to-invoke-data-loss-on-services"></a>Cómo invocar la pérdida de datos de servicios

>[AZURE.WARNING] Este documento describen cómo provocar la pérdida de datos en los servicios y se debe utilizar con cuidado.

## <a name="introduction"></a>Introducción
Puede invocar la pérdida de datos en una partición de su servicio de tela por llamada StartPartitionDataLossAsync().  Esta api utiliza la inserción de errores y el servicio de análisis para realizar el trabajo para hacer que las condiciones de pérdida de datos.

## <a name="using-the-fault-injection-and-analysis-service"></a>Uso de la inserción de errores y el servicio de análisis

La inserción de errores y el servicio de análisis actualmente es compatible con las siguientes API en la tabla siguiente.  El cmdlet de PowerShell correspondiente se muestra en el lado derecho del gráfico.  Consulte la documentación de msdn en cada API para obtener más información sobre cada una de ellas.

|           API DE C#                    |         Cmdlet de PowerShell                      |
|-------------------------------------|-----------------------------------------------:|
|[StartPartitionDataLossAsync] [dl]   |[Inicio ServiceFabricPartitionDataLoss] [psdl]   |
|[StartPartitionQuorumLossAsync] [ql] |[Inicio ServiceFabricPartitionQuorumLoss] [psql] |
|[StartPartitionRestartAsync] [rp]    |[Inicio ServiceFabricPartitionRestart] [psrp]    |

## <a name="conceptual-overview-of-running-a-command"></a>Información general y conceptual de la ejecución de un comando

La inserción de errores y el servicio de análisis utiliza un modelo asincrónico dónde comenzar el comando con una API, conocida como la API de "Inicio" en este documento y luego comprueba el progreso de este comando mediante una API "GetProgress" hasta que alcance un estado terminal o hasta que cancele.
Para iniciar un comando, llame a la API de "Inicio" para la API correspondiente.  Esta API devuelve cuando la inserción de errores y el servicio de análisis ha aceptado la solicitud.  Sin embargo, no indica cuánto se ejecuta un comando, o incluso si ha iniciado.  Para comprobar el progreso de un comando, llame a la API que corresponde a la API de "Inicio" anteriormente denominada "GetProgress".  "GetProgress" API devolverá un objeto que indica el estado actual del comando dentro de su propiedad de estado.  Se ejecuta un comando de forma indefinida hasta que:

1.  Se complete correctamente.  Si se llama "GetProgress" en ella en este caso, se completará el progreso estado del objeto.
2.  Encuentra un error.  Si se llama "GetProgress" en ella en este caso, se generará un error estado del objeto de progreso
3.  Cancela a través de la [CancelTestCommandAsync]  [ cancel] API o [Detener ServiceFabricTestCommand]  [ cancelps] cmdlet de PowerShell.  Si se llama "GetProgress" en ella en este caso, el progreso estado del objeto será cancelada o ForceCancelled, dependiendo de argumento de dicha API.  Consulte la documentación de [CancelTestCommandAsync]  [ cancel] para obtener más detalles.


## <a name="details-of-running-a-command"></a>Detalles de la ejecución de un comando

Para iniciar un comando, llame a la API de inicio con los argumentos esperados.  Todas las API de inicio tiene un argumento de Guid denominado operationId.  Se debe realizar un seguimiento del argumento operationId, ya que se utiliza para realizar un seguimiento del progreso de este comando.  Esto se debe pasar a la API "GetProgress" para realizar un seguimiento del progreso del comando.  La operationId debe ser único.

Después de llamar correctamente la API de inicio, debe llamar la API GetProgress en un bucle hasta que se complete la devuelto el progreso de la propiedad del objeto estado.  Todos los [de FabricTransientException]  [ fte] y reintentos de OperationCanceledException.
Cuando el comando alcanza un estado terminal (completada, con errores o cancelada), la devuelto el progreso de la propiedad del objeto resultado tendrá información adicional.  Si el estado se ha completado, Result.SelectedPartition.PartitionId contendrá el identificador de partición que se ha seleccionado.  Result.Exception es nulo.  Si el estado tiene errores, Result.Exception tendrá la razón por la inserción de errores y servicio de análisis de un error en el comando.  Result.SelectedPartition.PartitionId tendrá el identificador de partición que se ha seleccionado.  En algunos casos, el comando no puede ha realizado lo suficiente para elegir una partición.  En ese caso, el valor de PartitionId será 0.  Si se ha cancelado el estado, Result.Exception será null.  Al igual que el caso con errores, Result.SelectedPartition.PartitionId tendrá el identificador de partición que se ha elegido, pero si el comando no se han realizado lo suficiente para hacerlo, será 0.  Consulte también en el siguiente ejemplo.

El código de ejemplo siguiente muestra cómo iniciar y comprobar el progreso de un comando de provocar la pérdida de datos en una partición específica.

```csharp
    static async Task PerformDataLossSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/MyService");

        // The id of the target partition inside the target service
        Guid targetPartitionId = new Guid("00000000-0000-0000-0000-000002233445");

        PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(targetServiceName, targetPartitionId);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.        

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                // In a terminal state .Result.SelectedPartition.PartitionId will have the chosen partition
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);
                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}'", operationId, progress.Result.Exception);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(5.0d)).ConfigureAwait(false);
        }
    }
```

En el ejemplo siguiente se muestra cómo usar el PartitionSelector para elegir una partición aleatoria de un servicio especificado:

```csharp
    static async Task PerformDataLossUseSelectorSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/SampleService ");

        // Use a PartitionSelector that will have the Fault Injection and Analysis Service choose a random partition of “targetServiceName”
        PartitionSelector partitionSelector = PartitionSelector.RandomOf(targetServiceName);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }
            catch (Exception e)
            {
                Console.WriteLine("Unexpected exception '{0}'", e);
                throw;
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                Console.WriteLine("Printing progress.Result:");
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);

                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}', SelectedPartition {2}", operationId, progress.Result.Exception, progress.Result.SelectedPartition);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }
    }
```

## <a name="history-and-truncation"></a>Historial y truncamiento

Después de un comando alcanzó un estado terminal, sus metadatos permanecerá en la inserción de errores y el servicio de análisis de una hora determinada, antes de que se quitarán para ahorrar espacio.  Si se llama "GetProgress" utilizando el operationId de un comando después de que se ha quitado, devolverá un FabricException con un código de error de KeyNotFound.

[dl]: https://msdn.microsoft.com/library/azure/mt693569.aspx
[ql]: https://msdn.microsoft.com/library/azure/mt693558.aspx
[rp]: https://msdn.microsoft.com/library/azure/mt645056.aspx
[psdl]: https://msdn.microsoft.com/library/mt697573.aspx
[psql]: https://msdn.microsoft.com/library/mt697557.aspx
[psrp]: https://msdn.microsoft.com/library/mt697560.aspx
[cancel]: https://msdn.microsoft.com/library/azure/mt668910.aspx
[cancelps]: https://msdn.microsoft.com/library/mt697566.aspx
[fte]: https://msdn.microsoft.com/library/azure/system.fabric.fabrictransientexception.aspx
