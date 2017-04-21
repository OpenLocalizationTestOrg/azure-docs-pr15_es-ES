<properties
   pageTitle="Actualización de la aplicación de servicio tela con PowerShell | Microsoft Azure"
   description="En este artículo le guía a través de la experiencia de implementar una aplicación de servicio tela, cambiar el código y distribuir una actualización con PowerShell."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>


# <a name="service-fabric-application-upgrade-using-powershell"></a>Actualización de la aplicación de servicio tela con PowerShell

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
- [Visual Studio](service-fabric-application-upgrade-tutorial.md)

<br/>

Se suelen usar y enfoque de actualización recomendado es la supervisadas actualización sucesiva.  Azure tela servicio supervisa el estado de la aplicación que está actualizando basándose en un conjunto de directivas de mantenimiento. Una vez que se actualiza un dominio de actualización (Ud.), servicio tela evalúa el estado de la aplicación y continúa a la siguiente actualización del dominio o falla la actualización dependiendo de las directivas de mantenimiento.

Puede realizar una actualización de la aplicación supervisados con el administrado o nativo API, PowerShell o resto. Para obtener instrucciones sobre cómo realizar una actualización en Visual Studio, vea [actualizar su aplicación mediante Visual Studio](service-fabric-application-upgrade-tutorial.md).

Con las actualizaciones graduales tela servicio supervisadas, Administrador de la aplicación puede configurar la directiva de evaluación de mantenimiento tela de servicio se utiliza para determinar si la aplicación es correcto. Además, el administrador puede configurar la acción que se realizará cuando se produce un error en la evaluación de estado (por ejemplo, realizar una recuperación automática). En esta sección se recorre una actualización supervisada por uno de los ejemplos de SDK que usa PowerShell.

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Paso 1: Crear e implementar la muestra objetos visuales


Crear y publicar la aplicación, el botón secundario en el proyecto de la aplicación, **VisualObjectsApplication** y seleccione el comando **Publicar** .  Para obtener más información, consulte [actualizar el tutorial de aplicación de servicio tela](service-fabric-application-upgrade-tutorial.md).  Como alternativa, puede usar PowerShell para implementar la aplicación.

> [AZURE.NOTE] Antes de cualquiera de los comandos de tela de servicio se puede usar en PowerShell, primero tendrá que conectarse al clúster mediante la `Connect-ServiceFabricCluster` cmdlet. Del mismo modo, se supone que el clúster ya se han configurado en el equipo local. Vea el artículo sobre [cómo configurar su entorno de desarrollo de tela de servicio](service-fabric-get-started.md).

Después de crear el proyecto de Visual Studio, puede usar el comando PowerShell **ServiceFabricApplicationPackage copiar** para copiar el paquete de aplicación a la ImageStore. El siguiente paso es registrar la aplicación para la ejecución del servicio tela mediante el cmdlet **Registrar ServiceFabricApplicationPackage** . El último paso es iniciar una instancia de la aplicación mediante el cmdlet **ServiceFabricApplication de nuevo** .  Estos tres pasos son similares a usar el elemento de menú **implementar** en Visual Studio.

Ahora, puede usar el [Explorador de tela de servicio para ver el clúster y la aplicación](service-fabric-visualizing-your-cluster.md). La aplicación tiene un servicio web que se puede navegar en Internet Explorer escribiendo [http://localhost: 8081/visualobjects](http://localhost:8081/visualobjects) en la barra de direcciones.  Debería ver algunos objetos visuales flotantes moverse por la pantalla.  Además, puede usar **Get-ServiceFabricApplication** para comprobar el estado de la aplicación.

## <a name="step-2-update-the-visual-objects-sample"></a>Paso 2: Actualizar los datos de ejemplo objetos visuales

Es posible que note que con la versión que se ha implementado en el paso 1, los objetos visuales no girar. Vamos a actualizar esta aplicación a uno donde también girar los objetos visuales.

Seleccione el proyecto VisualObjects.ActorService dentro de la solución VisualObjects y abra el archivo StatefulVisualObjectActor.cs. En el archivo, vaya al método `MoveObject`, comente `this.State.Move()`y quite los comentarios de `this.State.Move(true)`. Este cambio gira los objetos después de actualiza el servicio.

También es necesario actualizar el archivo *ServiceManifest.xml* (en PackageRoot) del proyecto **VisualObjects.ActorService**. Actualizar la *CodePackage* y la versión de servicio 2.0 y las líneas correspondientes en el archivo *ServiceManifest.xml* .
Puede usar la opción *Editar archivos de manifiestos* de Visual Studio después de que haga clic en la solución para realizar los cambios de archivo manifiestos.


Después de realizan los cambios, el manifiesto debe tener un aspecto parecido al siguiente (partes resaltados mostrar los cambios):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Ahora se actualiza el archivo *ApplicationManifest.xml* (que se encuentra en el proyecto **VisualObjects** en la solución **VisualObjects** ) a la versión 2.0 del proyecto **VisualObjects.ActorService** . Además, se actualiza la versión de la aplicación a 2.0.0.0 de 1.0.0.0. El *ApplicationManifest.xml* debe tener un aspecto como el fragmento de código siguiente:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```


Ahora, genere el proyecto, seleccione el proyecto de **ActorService** y, a continuación, el botón secundario y seleccionando la opción **Generar** en Visual Studio. Si selecciona **volver a generar todo**, debe actualizar las versiones para todos los proyectos, ya que hubiera cambiado el código. A continuación, vamos a empaquetar la aplicación actualizada, haciendo doble clic en ***VisualObjectsApplication***, seleccionar el menú de la estructura de servicio y elija **paquete**. Esta acción crea un paquete de aplicación que se puede implementar.  La aplicación actualizada está lista para su implementación.


## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Paso 3: Decida en directivas de mantenimiento y actualización de parámetros

Familiarizarse con los [parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md) y el [proceso de actualización](service-fabric-application-upgrade.md) para obtener una buena comprensión de los distintos parámetros de actualización, los tiempos de espera y criterio de salud aplicado. En este tutorial, el criterio de evaluación de estado de servicio es el valor predeterminado (y recomendado) valores, lo que significa que todos los servicios e instancias deben ser _correcto_ tras la actualización.  

Sin embargo, vamos a aumentar la *HealthCheckStableDuration* 60 segundos (para que los servicios existen al menos 20 segundos antes de que la actualización se realiza a la siguiente actualización del dominio).  Vamos a establecer el *UpgradeDomainTimeout* ser 1200 segundos y la *UpgradeTimeout* ser 3000 segundos.

Por último, vamos a establecer el *UpgradeFailureAction* en Deshacer. Esta opción requiere servicio tela deshacer la aplicación a la versión anterior si encuentra problemas durante la actualización. Por lo tanto, al iniciar la actualización (en el paso 4), se especifican los parámetros siguientes:

FailureAction = deshacer

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000


## <a name="step-4-prepare-application-for-upgrade"></a>Paso 4: Preparar la aplicación para la actualización

Ahora la aplicación está integrado y listo para actualizarse. Si abre una ventana de PowerShell como administrador y tipo **Get-ServiceFabricApplication**, debe le avise que es el tipo de aplicación 1.0.0.0 de **VisualObjects** que se han implementado.  

El paquete de aplicación se almacena en la siguiente ruta de acceso relativa donde descomprimido el SDK de tela servicio - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Encontrará una carpeta "Paquete" en el directorio, donde se encuentra almacenado el paquete de aplicación. Comprobar las marcas de tiempo para asegurarse de que es la versión más reciente (que puede que deba modificar las rutas de acceso correctamente también).

Ahora vamos a copiar el paquete de aplicación actualizado en la ImageStore de tela de servicio (donde se almacenan los paquetes de aplicaciones por tela de servicio). El parámetro *ApplicationPackagePathInImageStore* informa a tela de servicio donde puede encontrar el paquete de aplicación. Hemos la aplicación actualizada "VisualObjects\_V2" con el comando siguiente (que puede que deba modificar rutas de nuevo correctamente).

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

El siguiente paso es registrar esta aplicación con tela de servicio, que se pueden realizar mediante el siguiente comando:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Si el comando anterior no se realiza correctamente, es probable que necesite una regeneración de todos los servicios. Como se mencionó en el paso 2, debe actualizar la versión del servicio Web.

## <a name="step-5-start-the-application-upgrade"></a>Paso 5: Iniciar la actualización de la aplicación

Ahora, todo está listo para iniciar la actualización de la aplicación mediante el siguiente comando:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


El nombre de la aplicación es el mismo, como se describe en el archivo *ApplicationManifest.xml* . Servicio tela usa este nombre para identificar qué aplicación está recibiendo actualizada. Si establece los tiempos de espera para ser demasiado corta, puede encontrar un mensaje de error que indica el problema. Consulte la sección de solución de problemas o aumentar los tiempos de espera.

Ahora, como la recaudación de actualización de la aplicación, puede supervisar mediante el Explorador de tela de servicio o mediante el siguiente PowerShell comando: **Get ServiceFabricApplicationUpgrade tela: / VisualObjects**.

En unos minutos, debe indicar el estado que obtuvo mediante el comando anterior de PowerShell, que se han actualizado todos los dominios de actualización (completa). Y encontrará que se han iniciado de girar los objetos visuales en la ventana del explorador.

Puede intentar actualizar desde la versión 2 a la versión 3 o de la versión 2 a la versión 1 como ejercicio. Pasar de la versión 2 a la versión 1, también se considera una actualización. Reproducir con los tiempos de espera y directivas de estado para ganar familiarizados con ellos. Cuando se implementa en un clúster de Azure, necesitan configurar adecuadamente los parámetros. Es conveniente establecer los tiempos de espera de cautela.


## <a name="next-steps"></a>Pasos siguientes

[Actualizar la aplicación mediante Visual Studio](service-fabric-application-upgrade-tutorial.md) le guiará a través de la actualización de una aplicación mediante Visual Studio.

Controlar cómo se actualiza la aplicación con [upgrade parámetros](service-fabric-application-upgrade-parameters.md).

Hacer que las actualizaciones de aplicación compatibles por aprender a usar la [serialización de datos](service-fabric-application-upgrade-data-serialization.md).

Obtenga información sobre cómo usar funciones avanzadas mientras actualiza su aplicación puede hacer referencia a [temas avanzados](service-fabric-application-upgrade-advanced.md).

Solucionar problemas comunes en las actualizaciones de la aplicación puede hacer referencia a los pasos de [solución de problemas actualizaciones de las aplicaciones](service-fabric-application-upgrade-troubleshooting.md).
