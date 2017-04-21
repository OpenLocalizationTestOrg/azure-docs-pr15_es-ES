<properties
   pageTitle="Solucionar problemas de la configuración de clúster de servicio tela local | Microsoft Azure"
   description="En este artículo trata sobre un conjunto de sugerencias para solucionar problemas de su clúster de desarrollo local"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# <a name="troubleshoot-your-local-development-cluster-setup"></a>Solucionar problemas de la configuración de clúster de desarrollo local

Si tiene un problema al interactuar con el clúster de desarrollo de Azure servicio tela local, revise las siguientes sugerencias para posibles soluciones.

## <a name="cluster-setup-failures"></a>Errores de instalación de clúster

### <a name="cannot-clean-up-service-fabric-logs"></a>No se puede limpiar los registros de la estructura de servicio

#### <a name="problem"></a>Problema

Mientras se ejecuta la secuencia de comandos de DevClusterSetup, verá un error como este:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Solución

Cierre la ventana de PowerShell actual y abrir una nueva ventana de PowerShell como administrador. Ahora debería poder ejecutar correctamente la secuencia de comandos.

## <a name="cluster-connection-failures"></a>Errores de conexión de clúster

### <a name="service-fabric-powershell-cmdlets-are-not-recognized-in-azure-powershell"></a>Cmdlets de PowerShell de tela de servicio no reconoce en PowerShell de Azure

#### <a name="problem"></a>Problema

Si intenta ejecutar cualquiera de los cmdlets de PowerShell de tela de servicio, como por ejemplo `Connect-ServiceFabricCluster` en una ventana de PowerShell de Azure, se produce un error, que indica que no se reconoce el cmdlet. El motivo es que Azure PowerShell utiliza la versión de 32 bits de Windows PowerShell (incluso en las versiones de sistema operativo de 64 bits), mientras que los cmdlets de servicio tela solo funcionan en entornos de 64 bits.

#### <a name="solution"></a>Solución

Ejecutar siempre cmdlets de servicio tela directamente desde Windows PowerShell.

>[AZURE.NOTE] La última versión de Azure PowerShell no crea un acceso directo especial, por lo que ya no debe aparecer.

### <a name="type-initialization-exception"></a>Escriba la excepción de inicialización

#### <a name="problem"></a>Problema

Cuando se conecta al clúster de PowerShell, vea el error TypeInitializationException para System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Solución

La variable de ruta de acceso no se estableció correctamente durante la instalación. Por favor, cerrar la sesión de Windows y vuelva a iniciarla. Totalmente actualiza la ruta de acceso.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Se produce un error en la conexión de clúster con "Se cierra el objeto"

#### <a name="problem"></a>Problema

Una llamada a conectar ServiceFabricCluster se produce un error como este:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Solución

Cierre la ventana de PowerShell actual y abrir una nueva ventana de PowerShell como administrador. Ahora debería poder conectarse correctamente.

### <a name="fabric-connection-denied-exception"></a>Excepción de tela conexión denegado

#### <a name="problem"></a>Problema

Al depurar de Visual Studio, recibe un error de FabricConnectionDeniedException.

#### <a name="solution"></a>Solución

Este error se produce normalmente al probar a intentar iniciar un servicio de host procesar manualmente, en lugar de permitir la ejecución del servicio tela iniciar automáticamente.

Asegúrese de que no tiene los proyectos de servicio establecer como proyectos de inicio de la solución. Solo los proyectos de aplicación de servicio tela se deben establecer como proyectos de inicio.

>[AZURE.TIP] Si, tras la instalación, el clúster local comienza a comportarse de forma anómala, puede restablecerla con la aplicación de bandeja del sistema de administrador de clúster local. Esto quitará el clúster existente y configurar uno nuevo. Tenga en cuenta que todas las aplicaciones de implementada y se quitarán los datos asociados.

## <a name="next-steps"></a>Pasos siguientes

- [Entender y solucionar problemas de clúster con informes de estado del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Visualizar el clúster con el Explorador de tela de servicio](service-fabric-visualizing-your-cluster.md)
