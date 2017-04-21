<properties
    pageTitle="Automatizar la administración de la aplicación de servicio tela con PowerShell | Microsoft Azure"
    description="Implementar, actualizar, probar y quitar aplicaciones de servicio tela con PowerShell."
    services="service-fabric"
    documentationCenter=".net"
    authors="rwike77"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-fabric"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="ryanwi"/>

# <a name="automate-the-application-lifecycle-using-powershell"></a>Automatizar el ciclo de vida de la aplicación con PowerShell

Se pueden automatizar muchos de los aspectos del [ciclo de vida de aplicación de servicio tela](service-fabric-application-lifecycle.md) de.  Este artículo le muestra cómo usar PowerShell para automatizar tareas comunes para implementar, actualizar, eliminar y probar las aplicaciones de Azure servicio tela.  Administra y APIs HTTP para la administración de la aplicación también están disponibles. Vea [ciclo de vida de aplicación](service-fabric-application-lifecycle.md) para obtener más información.  

## <a name="prerequisites"></a>Requisitos previos
Antes de pasar a las tareas en el artículo, asegúrese de:

+ Familiarizarse con los conceptos de servicio tela descritos en [información general técnica de tela de servicio](service-fabric-technical-overview.md).
+ [Instalar las herramientas, SDK y en tiempo de ejecución](service-fabric-get-started.md), que también instala el módulo de PowerShell **ServiceFabric** .
+ [Ejecución de scripts de PowerShell habilitar](service-fabric-get-started.md#enable-powershell-script-execution).
+ Iniciar un clúster local.  Iniciar una nueva ventana de PowerShell como administrador y, a continuación, ejecute la secuencia de comandos de configuración de clúster desde la carpeta SDK:`& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
+ Antes de ejecutar los comandos de PowerShell en este artículo, primero conectarse al clúster tela de servicio local mediante [**ServiceFabricCluster conectar**](https://msdn.microsoft.com/library/azure/mt125938.aspx):`Connect-ServiceFabricCluster localhost:19000`
+ Las siguientes tareas requieren un paquete de aplicación v1 a implementar y un paquete de aplicación v2 para la actualización. Descargar la [aplicación de ejemplo **WordCount** ](http://aka.ms/servicefabricsamples) (que se encuentra en los ejemplos de introducción). Compilar y empaquetar la aplicación en Visual Studio (ratón en **WordCount** en Explorador de soluciones y seleccione el **paquete**). Copie en el paquete de v1 `C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug` a `C:\Temp\WordCount`. Copiar `C:\Temp\WordCount` a `C:\Temp\WordCountV2`, crear el paquete de aplicación v2 para la actualización. Abrir `C:\Temp\WordCountV2\ApplicationManifest.xml` en un editor de texto. En el elemento **ApplicationManifest** , cambie el atributo de **ApplicationTypeVersion** de "1.0.0" a "2.0.0" para actualizar el número de versión de la aplicación. Guarde el archivo ApplicationManifest.xml modificado.

## <a name="task-deploy-a-service-fabric-application"></a>Tarea: Implementar una aplicación de servicio tela

Una vez que haya creado y empaquetado de la aplicación (o descargado el paquete de aplicación), puede implementar la aplicación en un clúster de tela de servicio local. Implementación implica cargar el paquete de aplicación, registrar el tipo de aplicación y la creación de instancia de la aplicación. Utilice las instrucciones de esta sección para implementar una nueva aplicación a un clúster.

### <a name="step-1-upload-the-application-package"></a>Paso 1: Cargar el paquete de aplicación
Cargar el paquete de aplicación en el almacén de imágenes coloca en una ubicación accesible a los componentes de servicio tela internos.  El paquete de aplicación contiene la configuración de manifiesto de aplicación, los manifiestos de servicio y código, es necesaria y los paquetes de datos para crear la aplicación y las instancias de servicio.  El comando [**Copiar ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) carga el paquete. Por ejemplo:

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### <a name="step-2-register-the-application-type"></a>Paso 2: Registrar el tipo de aplicación
Registrar el paquete de aplicación hace que el tipo de aplicación y versión declarados manifiesto de aplicación disponible para su uso. El sistema lee el paquete cargado en el paso 1, compruebe el paquete (equivalente a ejecutar [**Prueba ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx) localmente), el contenido del paquete de proceso y copie el paquete transformado en una ubicación del sistema interno.  Ejecute el cmdlet [**ServiceFabricApplicationType de registro**](https://msdn.microsoft.com/library/azure/mt125958.aspx) :

```powershell
Register-ServiceFabricApplicationType WordCount
```
Para ver todos los tipos de aplicación registrados en el clúster, ejecute el cmdlet [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125871.aspx) :

```powershell
Get-ServiceFabricApplicationType
```

### <a name="step-3-create-the-application-instance"></a>Paso 3: Crear la instancia de aplicación
Una aplicación se puede crear instancias con cualquier versión de tipo de aplicación que se ha registrado correctamente con el comando [**ServiceFabricApplication de nuevo**](https://msdn.microsoft.com/library/azure/mt125913.aspx) . El nombre de cada aplicación se declara en tiempo de implementación y deben comenzar con la **tela:** esquema y ser única para cada instancia de la aplicación. El nombre del tipo de aplicación y la versión del tipo de aplicación declarados en el archivo **ApplicationManifest.xml** del paquete de aplicación. Si se han definido todos los servicios de forma predeterminada en la información de la aplicación de tipo de aplicación de destino, los que se crean en este momento.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

El comando [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) enumera todas las instancias de aplicación que se han creado correctamente, junto con su estado general. El comando [**Get-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx) enumera todas las instancias de servicio que se crearon correctamente dentro de una instancia de la aplicación. Se muestran los servicios predeterminados (si los hay).

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## <a name="task-upgrade-a-service-fabric-application"></a>Tarea: Actualizar una aplicación de servicio tela
Puede actualizar una aplicación de servicio tela implementada anteriormente con un paquete de aplicación actualizado. Esta tarea actualiza la aplicación WordCount implementada en "tareas: implementar una aplicación de servicio tela." Para obtener más información, lea a través de [actualizar la aplicación de servicio tela](service-fabric-application-upgrade.md) .

Para mantener las cosas simples en este ejemplo, sólo el número de versión de aplicación actualizado en el paquete de aplicación WordCountV2 creado en los requisitos previos. Un escenario más realista sería actualizar sus archivos de datos, la configuración o el código de servicio y, a continuación, volver a generar y empaquetado de la aplicación con números de versión actualizada.  

### <a name="step-1-upload-the-updated-application-package"></a>Paso 1: Cargar el paquete de aplicación actualizado
La aplicación de v1 WordCount está lista para actualizarse. Si abre una ventana de PowerShell como administrador y escriba [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx), verá que la versión 1.0.0 del tipo de aplicación WordCount se implementa.  

Ahora copie el paquete de aplicación actualizado en el almacén de imágenes de servicio tela (donde se almacenan los paquetes de aplicaciones por tela de servicio). El parámetro **ApplicationPackagePathInImageStore** informa a tela de servicio donde puede encontrar el paquete de aplicación. El comando siguiente copia el paquete de aplicación en **WordCountV2** en el almacén de imágenes:  

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### <a name="step-2-register-the-updated-application-type"></a>Paso 2: Registrar el tipo de aplicación actualizado
El siguiente paso es registrar la nueva versión de la aplicación con tela de servicio, que pueden realizarse mediante el cmdlet [**ServiceFabricApplicationType de registro**](https://msdn.microsoft.com/library/azure/mt125958.aspx) :

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### <a name="step-3-start-the-upgrade"></a>Paso 3: Iniciar la actualización
Distintos parámetros de actualización, los tiempos de espera y criterios de estado se pueden aplicar a las actualizaciones de la aplicación. Lea los documentos de [parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md) y el [proceso de actualización](service-fabric-application-upgrade.md) para obtener más información. Todos los servicios e instancias debería _buen estado_ tras la actualización.  Configurar la **HealthCheckStableDuration** 60 segundos (para que los servicios existen al menos 20 segundos antes de que la actualización se realiza a la siguiente actualización del dominio).  También puede establecer la **UpgradeDomainTimeout** 1200 segundos y la **UpgradeTimeout** a 3000 segundos. Por último, establezca el **UpgradeFailureAction** a **Deshacer**, que solicita que servicio tela deshace la aplicación a la versión anterior si se encuentran errores durante la actualización.

Ahora puede iniciar la actualización de la aplicación mediante el cmdlet [**ServiceFabricApplicationUpgrade de inicio**](https://msdn.microsoft.com/library/azure/mt125975.aspx) :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Observe que el nombre de la aplicación es el mismo que el nombre de la aplicación de implementada anteriormente v1.0.0 (tela: / WordCount). Servicio tela usa este nombre para identificar qué aplicación está recibiendo actualizada. Si establece los tiempos de espera para ser demasiado corta, puede encontrar un mensaje de error de tiempo de espera que indica el problema. Consulte [solucionar problemas de actualizaciones de aplicación](service-fabric-application-upgrade-troubleshooting.md)o aumentar los tiempos de espera.

### <a name="step-4-check-upgrade-progress"></a>Paso 4: Progreso de la actualización verificación
Puede supervisar el progreso de la actualización de aplicaciones mediante el [Explorador de tela de servicio](service-fabric-visualizing-your-cluster.md)o mediante el cmdlet [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx) :

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

En unos minutos, el cmdlet [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) muestra que se han actualizado todos los dominios de actualización (completa).

## <a name="task-test-a-service-fabric-application"></a>Tarea: Probar una aplicación de servicio tela

Para escribir los servicios de alta calidad, los desarrolladores necesitan puedan provocar errores en la infraestructura poco confiables para probar la estabilidad de sus servicios. Tela de servicio permite a los desarrolladores a provocar acciones de errores y probar servicios en presencia de errores en las situaciones de prueba caos y migración tras error.  Lea [información general de la capacidad de prueba](service-fabric-testability-overview.md) para obtener información adicional.

### <a name="step-1-run-the-chaos-test-scenario"></a>Paso 1: Ejecutar el escenario de prueba de caos
El escenario de prueba de caos genera errores en todo el clúster tela de servicio. El escenario comprime errores generalmente vistos durante meses o años a unas pocas horas. La combinación de errores intercaladas con una tasa de errores alta busca casos de esquina que en caso contrario, se podrían perder. En el ejemplo siguiente se ejecuta el escenario de prueba de caos para 60 minutos.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### <a name="step-2-run-the-failover-test-scenario"></a>Paso 2: Ejecutar el escenario de prueba de migración tras error
La migración tras error probar destinos escenario una partición de servicio específico en lugar de todo el clúster y deja afectados otros servicios. El escenario recorre en una secuencia de simulada errores de validación de servicio mientras se ejecuta la lógica empresarial. Un error de validación de servicio indica un problema que necesita más investigación. La prueba de migración tras error induce solo errores a la vez, en lugar del escenario de prueba de caos, que puede provocar errores varios. En el ejemplo siguiente se ejecuta la prueba de migración tras error de 60 minutos en la estructura: / WordCount/WordCountService servicio.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## <a name="task-remove-a-service-fabric-application"></a>Tarea: Quitar una aplicación de servicio tela
Puede eliminar una instancia de una aplicación implementada, quite el tipo de aplicación de preparación del clúster y quitar el paquete de aplicación de la ImageStore.

### <a name="step-1-remove-an-application-instance"></a>Paso 1: Eliminar una instancia de aplicación
Cuando ya no es necesaria una instancia de la aplicación, puede quitar permanentemente mediante el cmdlet [**Quitar ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx) . Esto quitan automáticamente todos los servicios que pertenecen a la aplicación, quitar todo el estado de servicio de manera permanente. No se puede deshacer esta operación y no se puede recuperar el estado de la aplicación.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### <a name="step-2-unregister-the-application-type"></a>Paso 2: Eliminar del registro el tipo de aplicación
Cuando ya no necesite una versión específica de un tipo de aplicación, eliminar el registro mediante el cmdlet [**Anular ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx) . Anular el registro de los tipos no utilizados libera espacio de almacenamiento utilizado por el paquete de aplicación en el almacén de imágenes. Un tipo de aplicación puede ser no registrado, siempre y cuando no hay ninguna aplicación crea una instancia en él o pendiente actualizaciones de las aplicaciones que hacen referencia a él.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### <a name="step-3-remove-the-application-package"></a>Paso 3: Quitar el paquete de aplicación
Después de que el tipo de aplicación no registrado, se puede quitar el paquete de aplicación de la tienda de imagen mediante el cmdlet [**Quitar ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx) .

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## <a name="next-steps"></a>Pasos siguientes
[Ciclo de vida de aplicación de servicio tela](service-fabric-application-lifecycle.md)

[Implementar una aplicación](service-fabric-deploy-remove-applications.md)

[Actualización de la aplicación](service-fabric-application-upgrade.md)

[Cmdlets de Azure tela de servicio](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Cmdlets de capacidad de prueba de Azure tela de servicio](https://msdn.microsoft.com/library/azure/mt125844.aspx)
