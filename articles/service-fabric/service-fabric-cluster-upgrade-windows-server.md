<properties
   pageTitle="Actualizar un clúster de tela de servicio independiente en Windows Server | Microsoft Azure"
   description="Actualizar el código de servicio tela o la configuración que se ejecuta un clúster de tela de servicio independiente, incluida la configuración de modo de actualización de clúster"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/10/2016"
   ms.author="chackdan"/>


# <a name="upgrade-your-standalone-service-fabric-cluster-on-windows-server"></a>Actualizar el clúster de servicio tela independiente en Windows Server

> [AZURE.SELECTOR]
- [Clúster de Azure](service-fabric-cluster-upgrade.md)
- [Clúster independiente](service-fabric-cluster-upgrade-windows-server.md)

Para cualquier sistema moderna, diseño para la actualización es clave para tener éxito a largo plazo de su producto. Un clúster de servicio tela es un recurso que es el propietario. Este artículo describe cómo puede asegurarse de que el clúster siempre ejecuta versiones compatibles de la estructura de servicio código y configuraciones.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlar la versión de tela que se ejecuta en el clúster

Puede configurar el clúster para descargar las actualizaciones de tela de servicio, cuando Microsoft publica una versión nueva o seleccionar una versión compatible tela que desea en el clúster. 

Para ello, establezca la configuración del clúster "fabricClusterAutoupgradeEnabled" en true o false.


>[AZURE.NOTE] Asegúrese de mantener el clúster siempre se ejecuta una versión compatible de tela de servicio. Como y cuando se anunciar el lanzamiento de una nueva versión de tela de servicio, la versión anterior se marca de fin de soporte después de un mínimo de 60 días de la fecha. Las nuevas versiones están anunciada [en el blog del equipo de tela de servicio](https://blogs.msdn.microsoft.com/azureservicefabric/ ). La nueva versión está disponible para, a continuación, elija. 


Puede actualizar el clúster a la nueva versión solo si está utilizando una configuración de nodo de estilo de producción, donde cada nodo de servicio tela se asigna una física independiente o la máquina virtual. Si tiene un clúster de desarrollo, donde hay de más de un servicio de nodos de tela en una sola máquina física o virtual, debe anular el clúster y volver a crearla con la nueva versión.


Existen dos distintos flujos de trabajo para actualizar el clúster a la información más reciente o una versión de tela de servicio admitido. Para clústeres que tiene conectividad para descargar la versión más reciente automáticamente y el segundo para clústeres que no están conectividad a descargar la última versión de tela de servicio.

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>Actualizar los clústeres con conectividad para descargar las últimas código y configuración 

Siga estos pasos para actualizar el clúster a una versión compatible, si los nodos del clúster tienen conectividad a internet [http://download.Microsoft.com](http://download.microsoft.com) 

Para clústeres que tienen conectividad [http://download.Microsoft.com](http://download.microsoft.com), nos Compruebe periódicamente la disponibilidad de nuevas versiones de tela de servicio.


Cuando esté disponible una nueva versión de tela de servicio, el paquete se descargan localmente en el clúster y aprovisionado para la actualización. Además para informarle acerca de esta nueva versión, el sistema coloca una advertencia de estado de clúster explícitas similar al siguiente:

"El clúster versión actual [versión #] el soporte técnico finaliza [fecha].", una vez que el clúster está ejecutando la última versión, desaparece la advertencia.


#### <a name="cluster-upgrade-workflow"></a>Flujo de trabajo de actualización de clúster.
 
Una vez que aparezca la advertencia de salud de clúster, debe hacer lo siguiente:

1. Conectarse al clúster desde cualquier equipo que tenga acceso de administrador a todos los equipos que se muestran como nodos en el clúster. El equipo que se ejecute esta secuencia de comandos en no tiene que ser parte del clúster

    ```powershell

    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Obtener versiones que puede actualizar a la lista de tela de servicio

    ```powershell

    ###### Get the list of available service fabric versions 
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    obtendrá un resultado similar al siguiente:

    ![obtener a tela versiones][getfabversions]

3. Empezar una actualización de clúster a una de las versiones que está disponible mediante el [Inicio ServiceFabricClusterUpgrade PowerShell cmd](https://msdn.microsoft.com/library/mt125872.aspx)

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    
    ```
Puede supervisar el progreso de la actualización en el Explorador de servicio tela o ejecutando el siguiente comando de shell de energía

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    If the cluster health policies are not met, the upgrade is rolled back. You can specify custom health policies at the time for the Start-ServiceFabricClusterUpgrade command refer to [this document](https://msdn.microsoft.com/library/mt125872.aspx) for details. 

Una vez que se han corregido los problemas que ha generado la restauración, debe iniciar la actualización de nuevo, siguiendo los mismos pasos que antes.


### <a name="upgrade-the-clusters-with-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>Actualizar los clústeres con <U>sin conectividad</u> para descargar las últimas código y configuración

Siga estos pasos para actualizar el clúster a una versión compatible, si su clúster nodos **no tiene** conexión a internet [http://download.Microsoft.com](http://download.microsoft.com) 


>[AZURE.NOTE]Si está ejecutando un clúster que no está conectado a internet, tendrá que supervisar el blog del equipo de tela de servicio para recibir una notificación de una nueva versión. El sistema **no** ponga ningún mensaje de advertencia de salud de clúster para avisarle de la misma.  

1. Modificar la configuración del clúster para establecer la propiedad siguiente en false.

        "fabricClusterAutoupgradeEnabled": false,

y empezar una actualización de la configuración. Para obtener más información de uso, consulte [Inicio ServiceFabricClusterUpgrade PS cmd](https://msdn.microsoft.com/library/mt125872.aspx) . La versión de manifiestos de clúster es la versión que tiene en el clusterConfig.JSON. Asegúrese de actualizar antes de la actualización de la configuración de lanzamiento.

```powershell

    Start-ServiceFabricClusterUpgrade [-Config] [-ClusterConfigVersion] -FailureAction Rollback -Monitored 

```

#### <a name="cluster-upgrade-workflow"></a>Flujo de trabajo de actualización de clúster.
 


1. Descargar la versión más reciente del paquete de documento de [crear el clúster de tela de servicio de windows server](service-fabric-cluster-creation-for-windows-server.md) 


1. Conectarse al clúster desde cualquier equipo que tenga acceso de administrador a todos los equipos que se muestran como nodos en el clúster. El equipo que se ejecute esta secuencia de comandos en no tiene que ser parte del clúster 

    ```powershell

    ###### connect to the cluster
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Copie el paquete descargado en el almacén de imágenes de clúster.

    ```powershell

    ###### Get the list of available service fabric versions 
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"


    ```

2. Registrar el paquete copiado 

    ```powershell

    ###### Get the list of available service fabric versions 
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file> 

    ###### Here is a filled out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```


3. Empezar una actualización de clúster a una de las versiones que están disponibles. 

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    
    ```
Puede supervisar el progreso de la actualización en el Explorador de servicio tela o ejecutando el siguiente comando de shell de energía

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    If the cluster health policies are not met, the upgrade is rolled back. You can specify custom health policies at the time for the start-serviceFabricClusterUpgrade command refer to [this document](https://msdn.microsoft.com/library/mt125872.aspx) for details. 

Una vez que se han corregido los problemas que ha generado la restauración, debe iniciar la actualización de nuevo, siguiendo los mismos pasos que antes.



## <a name="next-steps"></a>Pasos siguientes
- Aprenda a personalizar parte de la [Configuración del servicio tela clúster tela](service-fabric-cluster-fabric-settings.md)
- Obtenga información sobre cómo [ajustar el clúster de entrada y salida](service-fabric-cluster-scale-up-down.md)
- Obtenga más información sobre [las actualizaciones de la aplicación](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
