<properties
   pageTitle="Introducción a la implementación y actualización de aplicaciones en el clúster local | Microsoft Azure"
   description="Configurar un clúster de servicio tela local, implementar una aplicación existente a él y, a continuación, actualizar la aplicación."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="ryanwi;mikhegn"/>

# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>Introducción a implementar y actualizar aplicaciones en el clúster local
El SDK de tela de servicio de Azure incluye un entorno de desarrollo local completa que puede usar para comenzar rápidamente con implementar y administrar aplicaciones en un clúster local. En este artículo, crear un clúster local, implementar una aplicación existente a él y, a continuación, actualice la aplicación a una nueva versión, todo ello desde Windows PowerShell.

> [AZURE.NOTE] En este artículo se supone que se ha [configurado el entorno de desarrollo](service-fabric-get-started.md).

## <a name="create-a-local-cluster"></a>Crear un clúster local
Un clúster de servicio tela representa un conjunto de recursos de hardware que se pueden implementar aplicaciones. Normalmente, un clúster se compone de cualquier parte de cinco a miles de equipos. Sin embargo, el SDK de tela de servicio incluye una configuración de clúster que se puede ejecutar en un único equipo.

Es importante entender que el clúster local de tela de servicio no es un emulador o simulator. Se ejecuta el mismo código de plataforma que se encuentra en clústeres varios equipos. La única diferencia es que se ejecuta los procesos de plataforma que normalmente se extienden a través de cinco equipos en un equipo.

El SDK proporciona dos formas de configurar un clúster local: una secuencia de comandos de Windows PowerShell y la aplicación de bandeja del sistema de administrador de clúster Local. En este tutorial, se utiliza la secuencia de comandos de PowerShell.

> [AZURE.NOTE] Si ya ha creado un clúster local al implementar una aplicación de Visual Studio, puede omitir esta sección.


1. Iniciar una nueva ventana de PowerShell como administrador.

2. Ejecute la secuencia de comandos de instalación de clúster de la carpeta SDK:

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```

    Instalación de clúster lleva unos minutos. Una vez finalizada la instalación, debería ver resultado similar al:

    ![Resultados de la configuración de clúster][cluster-setup-success]

    Ya está preparado para intentar implementar una aplicación en el clúster.

## <a name="deploy-an-application"></a>Implementar una aplicación
El SDK de tela de servicio incluye un amplio conjunto de marcos y programador de herramientas para la creación de aplicaciones. Si le interesa aprender a crear aplicaciones en Visual Studio, vea [crear su primera aplicación de servicio tela en Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

En este tutorial, se utilizará una aplicación de ejemplo existente (llamada WordCount) para que nos podemos centrar en los aspectos de administración de la plataforma, incluida la implementación, supervisión y actualización.


1. Iniciar una nueva ventana de PowerShell como administrador.

2. Importar el módulo de PowerShell de servicio tela SDK.

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. Cree un directorio para almacenar la aplicación que descargar e implementar, como C:\ServiceFabric.

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. [Descargar la aplicación WordCount](http://aka.ms/servicefabric-wordcountapp) a la ubicación que creó.  Nota: el explorador Microsoft Edge guarda el archivo con una extensión *.zip* .  Cambiar la extensión de archivo a *.sfpkg*.

5. Conectar con el clúster local:

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. Crear una nueva aplicación mediante el comando de implementación del SDK con un nombre y una ruta de acceso para el paquete de aplicación.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    Si todo va bien, debería ver resultados como los siguientes:

    ![Implementar una aplicación en el clúster local][deploy-app-to-local-cluster]

7. Para ver la aplicación en acción, inicie el explorador y vaya a [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Debe ver:

    ![Interfaz de usuario de aplicación implementada][deployed-app-ui]

    La aplicación WordCount es muy sencilla. Incluye código de JavaScript de cliente para generar aleatorios cinco caracteres "palabras", que, a continuación, se envían a la aplicación a través de la API de Web de ASP.NET. Un servicio con estado realiza un seguimiento del número de palabras en cuenta. Se dividen según el primer carácter de la palabra. Puede encontrar el código fuente para la aplicación WordCount en la [Introducción a ejemplos](https://azure.microsoft.com/documentation/samples/service-fabric-dotnet-getting-started/).

    La aplicación que hemos implementado contiene cuatro particiones. Por lo que empiezan por G de palabras se almacenan en la primera partición, palabras que comienzan con H a N se almacenan en la segunda partición y así sucesivamente.

## <a name="view-application-details-and-status"></a>Ver detalles de la aplicación y el estado
Ahora que hemos implementado la aplicación, echemos un vistazo a algunos de los detalles de la aplicación en PowerShell.

1. Consulta implementadas todas las aplicaciones en el clúster:

    ```powershell
    Get-ServiceFabricApplication
    ```

    Suponiendo que solo ha implementado la aplicación WordCount, verá algo parecido a:

    ![Consultar implementadas todas las aplicaciones de PowerShell][ps-getsfapp]

2. Vaya al siguiente nivel consultando el conjunto de servicios que se incluyen en la aplicación WordCount.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Lista de servicios de la aplicación en PowerShell][ps-getsfsvc]

    La aplicación se compone de dos servicios: web front-end y el servicio de estado que administra las palabras.

3. Por último, eche un vistazo a la lista de particiones para WordCountService:

    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```

    ![Ver las particiones del servicio de PowerShell][ps-getsfpartitions]

    El conjunto de comandos que ha usado, como todos los comandos de PowerShell de tela de servicio, están disponibles para cualquier clúster que puede conectarse a local o remota.

    Para que una forma más visual interactuar con el clúster, puede usar la herramienta de servicio tela Explorer basada en web desplazándose hasta [Http://localhost:19080/explorador](http://localhost:19080/Explorer) en el explorador.

    ![Ver detalles de aplicación en el Explorador de tela de servicio][sfx-service-overview]

    > [AZURE.NOTE] Para obtener más información acerca del explorador de tela de servicio, consulte [visualizar el clúster con el Explorador de tela de servicio](service-fabric-visualizing-your-cluster.md).

## <a name="upgrade-an-application"></a>Actualizar una aplicación
Servicio tela proporciona actualizaciones de inactividad no supervisar el estado de la aplicación mientras avanza en el clúster. Vamos a realizar una sencilla actualización de la aplicación WordCount.

La nueva versión de la aplicación ahora cuenta sólo las palabras que comienzan con una vocal. Mientras se implementa la actualización, vemos dos cambios de comportamiento de la aplicación. En primer lugar, debe disminuir la velocidad a la que crece el recuento de, ya que se cuentan los menos palabras. En segundo término, puesto que la primera partición tiene dos vocales (A y E) y todas las demás particiones sólo contienen cada, su cuenta debe empezar finalmente superar a los demás.

1. [Descargar el paquete de v2 WordCount](http://aka.ms/servicefabric-wordcountappv2) en la misma ubicación que descargó el paquete de v1.

2. Volver a la ventana de PowerShell y use el comando de actualización del SDK para registrar la nueva versión del clúster. A continuación, comience a actualizar la estructura: / aplicación WordCount.

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    Debería ver el resultado de PowerShell que comienza el aspecto similar al siguiente como la actualización.

    ![Progreso de la actualización de PowerShell][ps-appupgradeprogress]

3. Mientras que la actualización se está realizando, puede resultarle más fácil supervisar su estado desde el Explorador de tela de servicio. Abrir una ventana del explorador y vaya a [Http://localhost:19080/explorador](http://localhost:19080/Explorer). Expanda **aplicaciones** en el árbol de la izquierda y elija **WordCount**y por último **tela: / WordCount**. En la pestaña essentials, verá el estado de la actualización recorre dominios de actualización del clúster.

    ![Progreso de la actualización en el Explorador de tela de servicio][sfx-upgradeprogress]

    Mientras se realiza la actualización a través de cada dominio, se realizan comprobaciones de estado para asegurarse de que la aplicación se comporta correctamente.

4. Si volver a ejecutar la consulta anterior para el conjunto de servicios en la estructura: / aplicación WordCount, observe que la versión de WordCountService cambiado pero la versión de WordCountWebService no:

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Servicios de aplicación de la consulta después de la actualización][ps-getsfsvc-postupgrade]

    Esto resalta cómo servicio tela administra las actualizaciones de la aplicación. Toque solo el conjunto de servicios (o paquetes de código y configuración de estos servicios) que ha cambiado, que hace que el proceso de actualización de forma más rápida y más fiable.

5. Por último, vuelva al explorador para observar el comportamiento de la nueva versión de la aplicación. Según lo esperado, el recuento de avanza más despacio y la primera partición termina con un poco más del volumen.

    ![Ver la nueva versión de la aplicación en el explorador][deployed-app-ui-v2]

## <a name="cleaning-up"></a>Limpiar

Antes de conclusión, es importante recordar que el clúster local es real. Continuarán aplicaciones ejecutar en segundo plano hasta que se quiten.  Según la naturaleza de las aplicaciones, una aplicación de ejecución puede tardar hasta recursos significativos en su equipo. Tiene varias opciones para administrar las aplicaciones y el clúster:

1. Para quitar una aplicación individual y todos sus datos, ejecute lo siguiente:

    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```

    O bien, elimine la aplicación en el menú de **acciones** de servicio tela Explorer o del menú contextual de la vista de lista de la aplicación del panel izquierdo.

    ![Eliminar una aplicación es el Explorador de tela de servicio][sfe-delete-application]

2. Después de eliminar la aplicación de clúster, puede eliminar el registro de las versiones 1.0.0 y 2.0.0 del tipo de aplicación WordCount. La eliminación quita los paquetes de aplicaciones, incluido el código de configuración de almacén de imágenes del clúster.

    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```

    O bien, en el Explorador de tela de servicio, elija el **Tipo de deshacer aprovisionamiento** de la aplicación.

3. Para cerrar el clúster pero mantener los datos de la aplicación y trazas, haga clic en **Dejar de clúster Local** en la aplicación de la Bandeja de sistema.

4. Para eliminar por completo el clúster, haga clic en **Quitar clúster Local** en la aplicación de la Bandeja de sistema. Esta opción se traducirán en otra implementación lenta la próxima vez que presione F5 en Visual Studio. Quitar el clúster local solo si no desea usar para algún tiempo o si necesita recuperar los recursos.

## <a name="1-node-and-5-node-cluster-mode"></a>1 nodo y 5 modo clúster de nodo

Cuando se trabaja con el clúster local para desarrollar aplicaciones, se encuentre a menudo realizando iteraciones rápidas de escribir código, depuración, cambiar el código, depuración etcetera. Para optimizar este proceso, puede ejecutar el clúster local en dos modos: 1 nodo o 5. Dos modos de clúster tienen sus ventajas.
Modo de clúster de nodo 5 le permite trabajar con un clúster real. Puede probar escenarios de error, trabajar con más instancias y réplicas de los servicios.
Modo de clúster de nodo 1 está optimizado para realizar la implementación rápida y registro de los servicios que le ayudarán a validar rápidamente con el tiempo de ejecución de tela de servicio de código.

Modo de clúster 1 nodo tanto 5 modo clúster de nodo no es un emulador o simulator. Se ejecuta el mismo código de plataforma que se encuentra en clústeres varios equipos.

> [AZURE.NOTE] Esta característica está disponible en el SDK versión 5.2 y versiones posteriores.

Para cambiar el modo de clúster a un clúster de nodo 1, utilice el Administrador de clúster de servicios tela Local o usar PowerShell del siguiente modo:

1. Iniciar una nueva ventana de PowerShell como administrador.

2. Ejecute la secuencia de comandos de instalación de clúster de la carpeta SDK:

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```

    Instalación de clúster lleva unos minutos. Una vez finalizada la instalación, debería ver resultado similar al:
    
    ![Resultados de la configuración de clúster][cluster-setup-success-1-node]

Si está utilizando el Administrador de clúster de servicio tela Local:

![Cambiar el modo de diseño][switch-cluster-mode]

> [AZURE.WARNING] Cuando se cambia el modo de clúster, el clúster actual es que se quita de su sistema y se crea un nuevo clúster. Los datos que se ha almacenado en el clúster, se eliminarán cuando se cambia el modo de diseño.

## <a name="next-steps"></a>Pasos siguientes
- Ahora que ha implementado y actualizado algunas aplicaciones predefinidas, puede [intentar crear sus propios en Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
- Todas las acciones en el clúster local en este artículo se pueden realizar en un [clúster de Azure](service-fabric-cluster-creation-via-portal.md) .
- La actualización que se realicen en este artículo era básica. Consulte la [documentación de actualización](service-fabric-application-upgrade.md) para obtener más información sobre la eficacia y flexibilidad de actualizaciones de servicio tela.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png
