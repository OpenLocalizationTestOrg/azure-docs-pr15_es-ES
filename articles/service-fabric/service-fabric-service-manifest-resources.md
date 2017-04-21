<properties
   pageTitle="Especificar extremos de servicio del servicio tela | Microsoft Azure"
   description="Cómo se describen los recursos de extremo en un manifiesto de servicio, incluido cómo configurar extremos HTTPS"
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

# <a name="specify-resources-in-a-service-manifest"></a>Especificar recursos en un manifiesto de servicio

## <a name="overview"></a>Información general

El manifiesto del servicio permite que los recursos que se usan en el servicio se declara de cambiado sin cambiar el código compilado. Azure tela de servicio admite la configuración de recursos de extremo del servicio. Puede controlar el acceso a los recursos que se especifican en el manifiesto del servicio a través de la SecurityGroup del manifiesto de aplicación. La declaración de recursos permite estos recursos cambiarse en tiempo de implementación, lo que significa que el servicio no es necesario introducir un nuevo mecanismo de configuración. Definición del esquema para el archivo ServiceManifest.xml se instala con el servicio tela SDK y herramientas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Extremos

Cuando un recurso de extremo se define en el manifiesto del servicio, servicio tela asigna puertos desde el intervalo de puertos reservados aplicación cuando no se especifica explícitamente un puerto. Por ejemplo, mire el extremo *ServiceEndpoint1* especificado en el fragmento de código manifiesto proporcionado después de este párrafo. Además, los servicios también pueden solicitar un puerto específico en un recurso. Números de puerto diferentes, pueden asignar réplicas de servicio que se ejecuta en nodos de clúster diferente mientras réplicas de un servicio en ejecución en el mismo nodo comparten el puerto. Las réplicas de servicio, a continuación, pueden usar estos puertos según sea necesario para replicación y escuchar las solicitudes de cliente.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Para obtener más información sobre cómo hacer referencia a extremos de la configuración del paquete de archivos (settings.xml), consulte [configurar con estado servicios confiables](service-fabric-reliable-services-configuration.md) .

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Ejemplo: especificar un extremo HTTP para el servicio

El siguiente manifiesto del servicio define un recurso de extremo TCP y dos recursos de extremo HTTP en el &lt;recursos&gt; elemento.

Los extremos HTTP están automáticamente que ACL haría tela de servicio.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Ejemplo: especificar un extremo HTTPS del servicio

El protocolo HTTPS proporciona autenticación de servidor y también se usa para cifrar la comunicación cliente-servidor. Para habilitar HTTPS en su servicio de tela de servicio, especifique el protocolo en el *recursos -> extremos -> extremo* sección de manifiesto de servicio, tal como se muestra en una versión anterior del extremo de *ServiceEndpoint3*.

>[AZURE.NOTE] Protocolo de un servicio no se puede cambiar durante la actualización de la aplicación sin que constituya un salto cambia.


Aquí tenemos un ejemplo ApplicationManifest que necesita para HTTPS. Debe proporcionar la huella digital para el certificado. El EndpointRef es una referencia a EndpointResource en ServiceManifest, para los que establezca el protocolo HTTPS. Puede agregar más de un EndpointCertificate.  

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```
