<properties
   pageTitle="Implementación de aplicaciones de servicio tela | Microsoft Azure"
   description="Cómo implementar y quitar aplicaciones en tela de servicio"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# <a name="deploy-and-remove-applications-using-powershell"></a>Implementar y quitar aplicaciones con PowerShell

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-deploy-remove-applications.md)
- [Visual Studio](service-fabric-publish-app-remote-cluster.md)

<br/>

Una vez un [tipo de aplicación se ha empaquetado][10], está listo para su implementación en un clúster de Azure servicio tela. Implementación implica los siguientes pasos:

1. Cargar el paquete de aplicación
2. Registrar el tipo de aplicación
3. Crear la instancia de aplicación

>[AZURE.NOTE] Si utiliza Visual Studio para implementar y depurar aplicaciones en el clúster de desarrollo local, todos los pasos siguientes se administran automáticamente a través de un script de PowerShell que se encuentra en la carpeta de Scripts del proyecto de aplicación. En este artículo, se proporciona información general acerca de qué hacen esas secuencias de comandos para que pueda realizar las mismas operaciones fuera de Visual Studio.

## <a name="upload-the-application-package"></a>Cargar el paquete de aplicación

Cargar el paquete de aplicación, coloca en una ubicación que sea accesible componentes internos de tela de servicio. Puede usar PowerShell para realizar la carga. Antes de ejecutar los comandos de PowerShell en este artículo, iniciar mediante [ServiceFabricCluster conectar](https://msdn.microsoft.com/library/mt125938.aspx) para conectarse al clúster tela de servicio.

Suponga que tiene una carpeta denominada *MyApplicationType* que contiene el manifiesto de aplicación es necesario, servicio manifiestos y paquetes de configuración de código o datos. El comando [Copiar ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125905.aspx) carga el paquete en el almacén de imágenes de clúster. El cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , que forma parte del módulo de PowerShell de servicio tela SDK, se usa para obtener la cadena de conexión de la tienda de imagen.  Para importar el módulo SDK, ejecute:

```
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Puede copiar un paquete de aplicación de *C:\users\ryanwi\Documents\Visual Studio 2015\Projects\MyApplication\myapplication\pkg\debug* a *c:\temp\MyApplicationType* (cambiar el nombre del directorio "depuración" a "MyApplicationType"). En el ejemplo siguiente se carga el paquete:

~~~
PS C:\temp> dir

    Directory: c:\temp


Mode                LastWriteTime         Length Name                                                                                   
----                -------------         ------ ----                                                                                   
d-----        8/12/2016  10:23 AM                MyApplicationType                                                                          

PS C:\temp> tree /f .\Stateless1Pkg

C:\TEMP\MyApplicationType
│   ApplicationManifest.xml
|
└───Stateless1Pkg
  	|   ServiceManifest.xml
    │
    └───Code
    │   │  Microsoft.ServiceFabric.Data.dll
    │   │  Microsoft.ServiceFabric.Data.Interfaces.dll
    │   │  Microsoft.ServiceFabric.Internal.dll
    │   │  Microsoft.ServiceFabric.Internal.Strings.dll
    │   │  Microsoft.ServiceFabric.Services.dll
    │   │  ServiceFabricServiceModel.dll
    │   │  MyService.exe
    │   │  MyService.exe.config
    │   │  MyService.pdb
    │   │  System.Fabric.dll
    │   │  System.Fabric.Strings.dll
    │   │
    │   └───en-us
  	|         Microsoft.ServiceFabric.Internal.Strings.resources.dll
  	|         System.Fabric.Strings.resources.dll
  	|
    ├───Config
    │     Settings.xml
    │
    └───Data
          init.dat

PS C:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath MyApplicationType -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
Copy application package succeeded

PS D:\temp>
~~~

## <a name="register-the-application-package"></a>Registrar el paquete de aplicación

Registrar el paquete de aplicación hace que el tipo de aplicación y versión declarados manifiesto de aplicación disponible para su uso. El sistema lee el paquete cargado en el paso anterior, compruebe el paquete (equivalente a ejecuta [Prueba ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125950.aspx) localmente), el contenido del paquete de proceso y copiar el paquete transformado en una ubicación de sistema interno.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

El comando [Registrar ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125958.aspx) devuelve solo después de que el sistema ha copiado correctamente el paquete de aplicación. Cuánto tiempo se tarda depende del contenido del paquete de aplicación. Si es necesario, el parámetro **- TimeoutSec** puede utilizarse para proporcionar un tiempo de espera. (El tiempo de espera predeterminado es 60 segundos).

El comando [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125871.aspx) enumera todas las versiones de tipo de aplicación registrada correctamente.

## <a name="create-the-application"></a>Creación de la aplicación

Para crear una instancia de una aplicación con cualquier versión de tipo de aplicación que se ha registrado correctamente mediante el comando [ServiceFabricApplication de nuevo](https://msdn.microsoft.com/library/mt125913.aspx) . El nombre de cada aplicación debe comenzar con la *tela:* esquema y ser única para cada instancia de la aplicación. En este momento, se crean servicios predeterminados definidos en la información de la aplicación de tipo de aplicación de destino.

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

El comando [Get-ServiceFabricApplication](https://msdn.microsoft.com/library/mt163515.aspx) enumera todas las instancias de aplicación que se han creado correctamente, junto con su estado general.

El comando [Get-ServiceFabricService](https://msdn.microsoft.com/library/mt125889.aspx) enumera todas las instancias de servicio que se crearon correctamente dentro de una instancia de la aplicación. Servicios de forma predeterminada (si existe) se muestran aquí.

Pueden crearse varias instancias de aplicación para cualquier versión de un tipo de aplicación registrada determinado. Cada instancia de la aplicación se ejecuta por separado, con su propio directorio de trabajo y el proceso.

## <a name="remove-an-application"></a>Quitar una aplicación

Cuando ya no es necesaria una instancia de la aplicación, puede quitar permanentemente mediante el comando [Quitar ServiceFabricApplication](https://msdn.microsoft.com/library/mt125914.aspx) . Este comando quita automáticamente todos los servicios que pertenecen a la aplicación, quitar todo el estado de servicio de manera permanente. No se puede deshacer esta operación y no se puede recuperar el estado de la aplicación.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

Cuando ya no es necesaria una versión específica de un tipo de aplicación, debe anular el registro mediante el comando [Eliminar del registro ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125885.aspx) . Anular el registro de los tipos no utilizados libera espacio de almacenamiento utilizado por el contenido del paquete de aplicación de ese tipo de almacén de la imagen. Un tipo de aplicación puede ser no registrado, siempre y cuando no se crea una instancia de ninguna aplicación en él y no pendiente de aplicación actualizaciones hacen referencia a él.

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

## <a name="troubleshooting"></a>Solución de problemas

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copiar ServiceFabricApplicationPackage le pide una ImageStoreConnectionString

El entorno de servicio tela SDK ya debería tener configurar los valores predeterminados correctos. Pero, si es necesario, el ImageStoreConnectionString para todos los comandos debe coincidir con el valor que esté usando el clúster tela de servicio. Lo puede encontrar en el manifiesto de clúster recuperado mediante el comando [Get-ServiceFabricClusterManifest](https://msdn.microsoft.com/library/mt126024.aspx) :

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## <a name="next-steps"></a>Pasos siguientes

[Actualización de la aplicación de servicio tela](service-fabric-application-upgrade.md)

[Introducción de estado del servicio tela](service-fabric-health-introduction.md)

[Diagnosticar y solucionar problemas de un servicio de tela de servicio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Una aplicación de servicio tela del modelo](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
