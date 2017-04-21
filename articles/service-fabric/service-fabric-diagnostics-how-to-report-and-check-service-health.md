<properties
   pageTitle="Informes y comprobar el estado con tela de servicio de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo enviar informes de estado desde el código de servicio y cómo comprobar el estado de su servicio mediante las herramientas de supervisión de estado que proporciona la estructura del servicio de Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="toddabel"
   manager="mfussell"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/06/2016"
   ms.author="toddabel"/>

# <a name="report-and-check-service-health"></a>Informes y comprobar el estado del servicio
Cuando los servicios tiene problemas, su capacidad para responder a y corregir las incidencias e interrupciones depende de su capacidad para detectar los problemas rápidamente. Si se informar de problemas y errores al administrador de estado de Azure tela de servicio desde el código de servicio, puede usar herramientas de servicio tela proporciona para comprobar el estado de salud de supervisión de estado estándar.

Hay dos maneras de informar de estado del servicio:

- Utilizar objetos de [partición](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.aspx) o [CodePackageActivationContext](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.aspx) .  
Puede usar el `Partition` y `CodePackageActivationContext` objetos para informar del estado de los elementos que forman parte del contexto actual. Por ejemplo, código que se ejecuta como parte de una réplica puede informar del estado solo en esa réplica, la partición que pertenece y la aplicación que forma parte de.

- Usar `FabricClient`.   
Puede usar `FabricClient` a estado del informe desde el código de servicio si el clúster no es [segura](service-fabric-cluster-security.md) o si el servicio se está ejecutando con privilegios de administrador. Esto puede no es así en la mayoría de los escenarios reales. Con `FabricClient`, puede informar del estado en cualquier entidad que forma parte del clúster. Lo ideal es que, sin embargo, el código de servicio sólo debe enviar informes que están relacionados con su propio estado.

En este artículo le guiará a través de un ejemplo que informes de estado desde el código de servicio. El ejemplo también muestra cómo las herramientas de servicio tela pueden utilizarse para comprobar el estado de salud. Este artículo está pensado para ser una introducción rápida a las capacidades de servicio de supervisión de estado. Para obtener más información, puede leer la serie de artículos en profundidad sobre el estado que comienzan con el vínculo al final de este artículo.

## <a name="prerequisites"></a>Requisitos previos
Debe tener instalado lo siguiente:

   * Visual Studio de 2015
   * Servicio tela SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Para crear un clúster de desarrollo de seguridad local
- Abra el PowerShell con privilegios de administrador y ejecute los comandos siguientes.

![Comandos que muestran cómo crear un clúster de desarrollo seguro](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Implementar una aplicación y comprobar su estado

1. Abra Visual Studio como administrador.

2. Crear un proyecto mediante la plantilla de **Servicio de estado** .

    ![Crear una aplicación de servicio tela con el servicio de estado](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Presione **F5** para ejecutar la aplicación en modo de depuración. La aplicación se implementará en el clúster local.

4. Después de la aplicación se está ejecutando, haga clic en el icono de administrador de clúster Local en el área de notificación y seleccione **Administrar clúster Local** en el menú contextual para abrir el Explorador de tela de servicio.

    ![Abra el Explorador de tela de servicio desde el área de notificación](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. El estado de la aplicación debe mostrarse como en esta imagen. En este momento, la aplicación debería buen estada sin errores.

    ![Aplicación correcto en el Explorador de tela de servicio](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. También puede comprobar el estado mediante PowerShell. Puede usar ```Get-ServiceFabricApplicationHealth``` comprobar el estado de la aplicación y se puede usar ```Get-ServiceFabricServiceHealth``` para comprobar el estado del servicio. El informe de estado para la misma aplicación en PowerShell está en esta imagen.

    ![Aplicación correcto en PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Para agregar eventos de estado personalizado a su código de servicio
Las plantillas de proyecto de servicio tela en Visual Studio contienen código de ejemplo. Los pasos siguientes muestran cómo puede informar de eventos de estado personalizados desde el código de servicio. Estos informes se muestran automáticamente en las herramientas de supervisión de estado que proporciona servicio tela como servicio tela Explorer, la vista de estado de portal de Azure y PowerShell estándar.

1. Vuelva a abrir la aplicación que ha creado previamente en Visual Studio, o cree una nueva aplicación con la plantilla de Visual Studio de **Servicio de estado** .

2. Abra el archivo Stateful1.cs y busque el `myDictionary.TryGetValueAsync` llamar la `RunAsync` método. Puede ver que este método devuelve un `result` que contiene el valor actual del contador porque es la clave lógica de esta aplicación mantener un recuento ejecutando. Si se tratara de una aplicación real, y si la falta de resultado representa un error, desee marcar ese evento.

3. Para informar sobre un evento de estado cuando la falta de resultado representa un error, agregue los siguientes pasos.

    una. Agregar la `System.Fabric.Health` espacio de nombres en el archivo Stateful1.cs.

    ```csharp
    using System.Fabric.Health;
    ```

    b. Agregue el código siguiente después de la `myDictionary.TryGetValueAsync` de llamadas

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Nos informe de estado de réplica porque se notifica de un servicio de estado. La `HealthInformation` parámetro almacena información sobre el problema de estado que se notifica.

    Si ha creado un servicio independiente, utilice el código siguiente

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```

4. Si su servicio se está ejecutando con privilegios de administrador o si el clúster no está [seguro](service-fabric-cluster-security.md), también puede usar `FabricClient` a estado del informe tal como se muestra en los siguientes pasos.  

    una. Crear la `FabricClient` de instancia después de la `var myDictionary` declaración.

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    b. Agregue el código siguiente después de la `myDictionary.TryGetValueAsync` de llamadas.

    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. Vamos a simular este error y verlo aparecen en las herramientas de supervisión de estado. Para simular el error, comente la primera línea en el código de generación de informes de estado que agregó anteriormente. Comentar la primera línea, el código tendrá un aspecto similar al ejemplo siguiente.

    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
 Este código ahora aplicará este informe de estado cada vez que `RunAsync` se ejecuta. Después de realizar el cambio, presione **F5** para ejecutar la aplicación.

6. Después de la aplicación se está ejecutando, abra el Explorador de tela de servicio para comprobar el estado de la aplicación. En este momento, servicio tela explorador mostrará que la aplicación está en mal estada. Esto es debido al error que se indica en el código que hemos agregado anteriormente.

    ![Aplicación mal estado en el Explorador de tela de servicio](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. Si selecciona la réplica principal en la vista de árbol del explorador de tela de servicio, verá que **Estado** indica un error, demasiado. Explorador de tela de servicio también muestra los detalles del informe de estado que se agregaron a la `HealthInformation` parámetro en el código. Puede ver los mismos informes de estado de PowerShell y el portal de Azure.

    ![Estado de réplica en el Explorador de tela de servicio](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Este informe permanecerán en el Administrador de estado hasta que se reemplaza por otro informe o hasta que se elimine esta réplica. Porque no se ha definido `TimeToLive` para este informe de estado en la `HealthInformation` objeto, nunca expira el informe.

Se recomienda que se debe registrar estado en el nivel más granular, que en este caso es la réplica. También puede informar del estado en `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

A estado del informe en `Application`, `DeployedApplication`, y `DeployedServicePackage`, use `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Pasos siguientes
[Análisis detallado sobre el mantenimiento de tela de servicio](service-fabric-health-introduction.md)
