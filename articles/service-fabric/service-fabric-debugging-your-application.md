<properties
   pageTitle="Depurar la aplicación en Visual Studio | Microsoft Azure"
   description="Mejorar la confiabilidad y el rendimiento de los servicios por desarrollar y depurar en Visual Studio en un clúster de desarrollo local."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="vturecek;mikhegn"/>

# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Depurar la aplicación de servicio tela mediante Visual Studio

## <a name="debug-a-local-service-fabric-application"></a>Depurar una aplicación de servicio tela local

Puede ahorrar tiempo y dinero con la implementación y depuración de la aplicación de Azure servicio tela en un clúster de desarrollo de equipo local. Visual Studio puede implementar la aplicación en el clúster local y conectar automáticamente el depurador a todas las instancias de la aplicación.

1. Iniciar un clúster de desarrollo local siguiendo los pasos de [la configuración de su entorno de desarrollo de tela de servicio](service-fabric-get-started.md).

2. Presione **F5** o haga clic en **Depurar** > **Iniciar depuración**.

    ![Iniciar una aplicación de depuración][startdebugging]

3. Establecer puntos de interrupción en el código y paso a través de la aplicación, haga clic en el menú **Depurar** comandos.

    > [AZURE.NOTE] Visual Studio se asocia a todas las instancias de la aplicación. Mientras está recorrer código, puntos de interrupción pueden obtener alcanzados varios procesos de sesiones simultáneas. Pruebe a deshabilitar los puntos de interrupción después de que están elegidos, realizando cada punto de interrupción condicional en el identificador de subproceso o mediante eventos de diagnóstico.

4. La ventana de **Eventos de diagnóstico** se abrirá automáticamente para que pueda ver eventos de diagnóstico en tiempo real.

    ![Ver eventos de diagnóstico en tiempo real][diagnosticevents]

5. También puede abrir la ventana de **Eventos de diagnóstico** en el Explorador de la nube.  En **Tela de servicio**, haga clic en cualquier nodo y elija **Seguimientos de transmisión de vista**.

    ![Abra la ventana de eventos de diagnóstico][viewdiagnosticevents]

    Si desea filtrar los seguimientos a una aplicación o servicio específico, simplemente habilitar streaming seguimientos en ese servicio o la aplicación.

6. Los eventos de diagnósticos pueden verse en el archivo **ServiceEventSource.cs** generado automáticamente y se llaman desde el código de la aplicación.

    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```

7. Es compatible con la ventana de **Eventos de diagnóstico** filtrado, pausar y la inspección de eventos en tiempo real.  El filtro es una búsqueda de cadena simple de los mensajes de evento, incluidos su contenido.

    ![Filtrar, pausar y reanudar o inspeccionar eventos en tiempo real][diagnosticeventsactions]

8. Servicios de depuración es como cualquier otra aplicación de depuración. Normalmente se establecer puntos de interrupción a través de Visual Studio para depurar fácil. Aunque colecciones fiable replicar en varios nodos, aún implementan IEnumerable. Esto significa que puede usar la vista de resultados en Visual Studio al depurar para ver lo que ha almacenado dentro. Simplemente establecer un punto de interrupción en cualquier lugar en el código.

    ![Iniciar una aplicación de depuración][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Depurar una aplicación de servicio tela remota

Si las aplicaciones de servicio tela se están ejecutando en un clúster de servicio tela en Azure, es posible depurar de forma remota estos directamente desde Visual Studio.

> [AZURE.NOTE] La característica requiere [servicio tela SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) y el [SDK de Azure para .NET 2,9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [AZURE.WARNING] Depuración remota está pensada para escenarios de desarrollo/prueba y no debe ser usado en entornos de producción, debido a su influencia sobre las aplicaciones en ejecución.

1. Desplácese hasta el clúster en el **Explorador de la nube**, haga clic en y elija **Habilitar depuración**

    ![Habilitar la depuración remota][enableremotedebugging]

    Esto saque el proceso de habilitar la extensión de depuración remoto en los nodos de clúster, así como las configuraciones de red necesarios.

2. Haga clic en el nodo de clúster en el **Explorador de la nube**y a continuación, elija **Adjuntar depurador**

    ![Adjuntar depurador][attachdebugger]

3. En el cuadro de diálogo **asociar al proceso** , elija el proceso que desee depurar y haga clic en **adjuntar**

    ![Elija el proceso][chooseprocess]

    El nombre del proceso que desea adjuntar a, es igual al nombre de su nombre de ensamblado del proyecto de servicio.

    El depurador se conectará a todos los nodos de ejecutar el proceso.
    - En el caso donde depuración de un servicio independiente, todas las instancias del servicio en todos los nodos forman parte de la sesión de depuración.
    - Si va a depurar un servicio con estado, solo la réplica principal de cualquier partición estará activa y, por tanto, capturadas por el depurador. Si se mueve la réplica principal durante la sesión de depuración, el procesamiento de réplica seguirán siendo parte de la sesión de depuración.
    - Para detectar sólo particiones pertinentes o instancias de un servicio determinado, puede utilizar puntos de interrupción condicionales interrumpir solo una partición específica o una instancia.

    ![Punto de interrupción condicional][conditionalbreakpoint]

    > [AZURE.NOTE] Actualmente no se admiten depurar un clúster de servicio tela con varias instancias del mismo nombre ejecutable de servicio.

4. Una vez que termine de depuración de la aplicación, puede deshabilitar la extensión de depuración remoto haciendo clic en el clúster en el **Explorador de la nube** y elija **Deshabilitar depuración**

    ![Deshabilitar depuración remota][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Transmisión seguimientos de un nodo de clúster remoto

También es posible seguimientos de secuencia directamente desde un nodo de clúster remoto para Visual Studio. Esta característica le permite a los eventos de seguimiento ETW secuencia, creados en un nodo de clúster de servicio tela directamente en Visual Studio.

> [AZURE.NOTE] La característica requiere [servicio tela SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) y el [SDK de Azure para .NET 2,9](https://azure.microsoft.com/downloads/).

<!-- -->
> [AZURE.WARNING]Transmisión trazas está pensado para escenarios de desarrollo/prueba y no debe ser usado en entornos de producción, debido a su influencia sobre las aplicaciones en ejecución.
> En un escenario de producción, debe confiar en el reenvío de eventos de uso de diagnósticos de Azure.

1. Desplácese hasta el clúster en el **Explorador de nube**, haga clic en y elija **Habilitar seguimientos de transmisión**

    ![Habilitar trazas streaming remotos][enablestreamingtraces]

    Esto saque el proceso de habilitar la extensión de trazas streaming en los nodos de clúster, así como las configuraciones de red necesarios.

2. Expanda el elemento de **nodos** en el **Explorador de la nube**, haga clic en el nodo que desea transmitir seguimientos de y elija **Seguimientos de transmisión de vista**

    ![Vista remota transmisión trazas][viewremotestreamingtraces]

    Repita el paso 2 para tantos nodos como desee ver seguimientos de. Cada flujo de nodos se muestra en una ventana dedicada.

    Ahora está vean los seguimientos emitidos por la estructura de servicio y los servicios. Si desea filtrar los eventos para que muestre solo una aplicación específica, simplemente escriba el nombre de la aplicación en el filtro.

    ![Visualización de transmisión trazas][viewingstreamingtraces]

4. Una vez haya terminado trazas streaming del clúster, puede deshabilitar trazas streaming remotos, haciendo clic en el clúster en el **Explorador de la nube** y elija **Deshabilitar seguimientos de transmisión**

    ![Deshabilitar remotos trazas streaming][disablestreamingtraces]

## <a name="next-steps"></a>Pasos siguientes

- [Prueba de un servicio de tela de servicio](service-fabric-testability-overview.md).
- [Administrar las aplicaciones de servicio tela en Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
