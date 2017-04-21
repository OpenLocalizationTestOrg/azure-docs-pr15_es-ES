<properties
   pageTitle="Servicio tela y contenedores implementar | Microsoft Azure"
   description="Estructura de servicio y el uso de contenedores para implementar aplicaciones de microservice. En este artículo se describe las capacidades que proporciona tela de servicio para los contenedores y cómo implementar una imagen de contenedor de Windows en un clúster"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/24/2016"
   ms.author="msfussell"/>

# <a name="preview-deploy-a-windows-container-to-service-fabric"></a>Vista previa: Implementar un contenedor de Windows en tela de servicio

> [AZURE.SELECTOR]
- [Implementar el contenedor de Windows](service-fabric-deploy-container.md)
- [Implementar Docker contenedor](service-fabric-deploy-container-linux.md)

En este artículo le guiará por la creación de servicios de contenido en los contenedores de Windows. 

>[AZURE.NOTE] Esta característica está en vista previa para Linux y no están disponibles para usar con Windows Server 2016. Esto estarán disponibles en vista previa para Windows Server 2016 en la próxima versión de tela de servicio y admitidas en versiones posteriores después de eso.

Servicio tela tiene varias funciones de contenedor que pueden ayudarle con la creación de aplicaciones que se componen de microservices que son contenedores. Se trata de servicios de contenido. 

Incluyen las capacidades;

- La activación y la implementación de la imagen de contenedor
- Gobernanza de recursos
- Autenticación de repositorio
- Puerto de contenedor para la asignación de puerto de host
- Comunicación y detección de contenedor a
- Capacidad de configurar y establecer variables de entorno

Veamos cada una de las capacidades por separado al empaquetar un servicio de contenido que desea incluir en la aplicación.

## <a name="packaging-a-windows-container"></a>Empaquetar un contenedor de Windows

Al empaquetar un contenedor, puede elegir uno para usar una plantilla de proyecto de Visual Studio o [crear de forma manual el paquete de aplicación](#manually). Con Visual Studio, la estructura de paquete de aplicación y archivos de manifiestos creados por el Asistente para nuevo proyecto para usted (próximamente en la versión siguiente).

## <a name="using-visual-studio-to-package-an-existing-container-image"></a>Uso de Visual Studio para empaquetar una imagen de contenedor existente

>[AZURE.NOTE] En una versión futura de Visual Studio SDK de herramientas, podrá agregar un contenedor en una aplicación de manera similar que puede agregar a un invitado ejecutable hoy. Vea el tema de [implementar invitado ejecutable de tela de servicio](service-fabric-deploy-existing-app.md) . Actualmente se debe hacer empaquetado manual como se describe a continuación.

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-a-container"></a>Manualmente empaquetar e implementar un contenedor
El proceso de empaquetar manualmente un servicio de contenido se basa en los siguientes pasos:

1. Publicar los contenedores en el repositorio.
2. Crear la estructura de directorios de paquete.
3. Editar el archivo de manifiestos de servicio.
4. Editar el archivo de manifiestos de aplicación.

## <a name="container-image-deployment-and-activation"></a>Implementación de la imagen de contenedor y la activación.
En el [modelo de aplicación](service-fabric-application-model.md)de servicio tela, un contenedor representa un host de la aplicación en el que se colocan varias réplicas de servicio. Para implementar y activar un contenedor, poner el nombre de la imagen del contenedor en un `ContainerHost` elemento en el manifiesto del servicio.

En el manifiesto del servicio agregar una `ContainerHost` para la entrada de punto y establezca la `ImageName` en el nombre de la imagen y un repositorio de contenedor. El siguiente manifiesto parcial muestra un ejemplo de implementación el contenedor llamado *myimage:v1* desde un repositorio de *myrepo*

    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimagename:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>

Puede proporcionar los comandos de entrada a la imagen de contenedor especificando la opcional `Commands` elemento con una coma delimitado conjunto de comandos para ejecutar dentro del contenedor. 

## <a name="resource-governance"></a>Gobernanza de recursos
Gobernanza de recursos es una función del contenedor y restringe los recursos que puede usar el contenedor en el host. El `ResourceGovernancePolicy`, especificada en el manifiesto de aplicación, que proporciona la capacidad de declarar los límites de recursos para un paquete de código de servicio. Límites de recursos se pueden establecer para;

- Memoria
- MemorySwap
- CpuShares (CPU relativa de peso)
- MemoryReservationInMB  
- BlkioWeight (peso relativo BlockIO). 

>[AZURE.NOTE] En una versión futura, soporte técnico para especificar los límites de IO bloque específico como IOPs, serán posibles BPS y otros de lectura y escritura.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500" 
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>


## <a name="repository-authentication"></a>Autenticación de repositorio
Para descargar un contenedor debe proporcionar las credenciales de inicio de sesión en el repositorio de contenedor. Las credenciales de inicio de sesión especificadas en el manifiesto de *aplicación* se utilizan para especificar la información de inicio de sesión, o clave SSH, para descargar la imagen de contenedor del repositorio de imágenes.  En el ejemplo siguiente se muestra una cuenta llamada *usuario de prueba* y la contraseña en texto sin cifrar. Esto **no** se recomienda.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

La contraseña puede y se debe cifrar con un certificado que se implementa en el equipo.

En el ejemplo siguiente se muestra una cuenta llamada *usuario de prueba* con la contraseña cifrada con un certificado denominado *MyCert*. Puede usar el `Invoke-ServiceFabricEncryptText` comandos de Powershell para crear el texto de cifrado secreta para la contraseña. Consulte este artículo [administrar información confidencial en las aplicaciones de servicio tela](service-fabric-application-secret-management.md) para obtener más información acerca de cómo. La clave privada del certificado para descifrar la contraseña se debe implementar en el equipo local en un método de banda (en Azure es a través de BRAZO). A continuación, al servicio tela implementa el paquete de servicio en el equipo, es posible descifrar el secreto y junto con el nombre de cuenta, autenticar con el repositorio de contenedor con estas credenciales.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

## <a name="container-port-to-host-port-mapping"></a>Puerto de contenedor para la asignación de puerto de host
Puede configurar un puerto de host utilizado para comunicarse con el contenedor especificando una `PortBinding` manifiesto de aplicación. El enlace de puerto asigna el puerto que el servicio está escuchando dentro del contenedor a un puerto en el host.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>


## <a name="container-to-container-discovery-and-communication"></a>Comunicación y detección de contenedor a
Usar el `PortBinding` directiva se puede asignar un puerto de contenedor para un `Endpoint` en el manifiesto del servicio tal como se muestra en el ejemplo siguiente. El extremo `Endpoint1` puede especificar un puerto fijo, por ejemplo, el puerto 80 o no especificar ningún puerto en absoluto, en cuyo caso se elige un puerto aleatorio del intervalo de puertos de aplicación de los clústeres para usted.

Contenedores de invitado, que especifica un `Endpoint` como esto en el manifiesto del servicio permite tela de servicio publicar automáticamente este extremo en el servicio de nombres para que otros servicios que se ejecutan en el clúster pueden detectar este contenedor mediante las consultas de resolver servicio REST. 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

Registro con el servicio de nombres, puede hacer fácilmente contenedor a comunicación en el código en el contenedor mediante el [proxy inverso](service-fabric-reverseproxy.md). Todo lo que debe hacer es proporcionar el puerto de escucha de http proxy inverso y el nombre de los servicios que desea comunicarse con estableciendo como variables de entorno. Vea la siguiente sección acerca de cómo hacerlo.  

## <a name="configure-and-set-environment-variables"></a>Configurar y establecer variables de entorno
Variables de entorno pueden ser especificado y aparece el compañero cada paquete de código en el servicio de manifiestos para ambos servicios implementados en contenedores o como invitado o procesos ejecutables. Estos valores de variables de entorno se pueden reemplazar específicamente en el manifiesto de aplicación o especificados durante la implementación como parámetros de la aplicación.

El siguiente manifiesto de servicio fragmento XML muestra un ejemplo de cómo especificar las variables de entorno para un paquete de código. 

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>

Pueden reemplazar estas variables de entorno en el nivel de manifiestos de aplicación:

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>

En el ejemplo anterior, nos hemos especifica un valor explícito para la `HttpGateway` variable de entorno (19000) mientras que el valor de `BackendServiceName` parámetro se establece a través de la `[BackendSvc]` parámetro de la aplicación. Esto le permite especificar el valor de `BackendServiceName`de valor en el momento de implementar la aplicación y no tiene un valor fijo en el manifiesto. 

## <a name="complete-examples-for-application-and-service-manifest"></a>Completar los ejemplos de aplicación y manifiesto del servicio
A continuación se muestra un manifiesto de aplicación de ejemplo que muestra las características de contenedor.


    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>


El siguiente es un ejemplo manifiesto del servicio (especificado en el manifiesto de aplicación anterior) que muestra las características de contenedor

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless frontend in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Eendpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Eendpoints>
        </Resources>
    </ServiceManifest>
