<properties
   pageTitle="Script de PowerShell para implementar el clúster HPC de Windows | Microsoft Azure"
   description="Ejecutar un script de PowerShell para implementar un clúster de Windows HPC Pack en máquinas virtuales de Windows Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Crear un clúster de informática de alto rendimiento (HPC) de Windows con la secuencia de comandos de implementación de HPC Pack IaaS

Ejecute la implementación de HPC Pack IaaS script de PowerShell para implementar un clúster HPC completo para cargas de trabajo de Windows en máquinas virtuales de Windows Azure. El clúster consta de un nodo principal Unido de Active Directory con Windows Server y Microsoft HPC Pack y ventanas adicionales calculan recursos que especifique. Si desea implementar un clúster de HPC Pack en Azure para Linux cargas de trabajo, consulte [crear un clúster de HPC Linux con el script de implementación de HPC Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). También puede usar una plantilla de administrador de recursos de Azure para implementar un clúster de HPC Pack. Para obtener ejemplos, vea [crear un clúster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) y [crear un clúster HPC con un personalizado calcular imagen de nodo](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>Archivos de configuración de ejemplo

En los ejemplos siguientes, sustituir sus propios valores para su suscripción identificador o nombre y los nombres de cuenta y servicio.

### <a name="example-1"></a>Ejemplo 1

El archivo de configuración siguiente implementa un clúster HPC Pack que tiene un nodo principal con bases de datos locales y cinco nodos que ejecutan el sistema operativo Windows Server 2012 R2 de cálculo. Todos los servicios de nube se crean directamente en la ubicación de Estados Unidos oeste. El nodo principal actúa como controlador de dominio del bosque de dominios.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>Ejemplo 2

El archivo de configuración siguiente implementa un clúster HPC Pack en un bosque de dominio existente. El clúster tiene 1 nodo principal con bases de datos locales y 12 nodos de cálculo con la extensión BGInfo VM aplicada.
Instalación automática de actualizaciones de Windows está deshabilitada para todas las máquinas virtuales del bosque del dominio. Todos los servicios de nube se crean directamente en la ubicación de Asia oriental. Los nodos de cálculo se crean en tres cuentas de almacenamiento y tres servicios de nube: _MyHPCCN-0001_ a _0005 MyHPCCN_ en _MyHPCCNService01_ y _mycnstorage01_; _MyHPCCN 0006_ a _MyHPCCN0010_ en _MyHPCCNService02_ y _mycnstorage02_; y _MyHPCCN 0011_ a _0012 MyHPCCN_ en _MyHPCCNService03_ y _mycnstorage03_). Los nodos de cálculo se crean a partir de una imagen privada existente genera de un nodo de cálculo. El auto reducir y ampliar servicio está habilitado con predeterminado reducir y ampliar intervalos.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
      </DomainController>
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>Ejemplo 3

El archivo de configuración siguiente implementa un clúster HPC Pack en un bosque de dominio existente. El clúster contiene un nodo principal, servidor de una base de datos con un disco de datos de 500 GB, 2 nodos de agente que ejecutan el sistema operativo Windows Server 2012 R2 y ejecute el sistema operativo Windows Server 2012 R2 cinco nodos de cálculo. El servicio de nube MyHPCCNService se crea en el grupo de afinidad *MyIBAffinityGroup*y otros servicios de nube se crean en el grupo de afinidad *MyAffinityGroup*. La API de REST de HPC trabajo programador y el portal HPC web están habilitados en el nodo principal.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>Ejemplo 4

El archivo de configuración siguiente implementa un clúster HPC Pack en un bosque de dominio existente. El clúster tiene dos nodo principal con bases de datos locales, se crean dos plantillas de nodo Azure y se crean tres nodos de Azure Media de tamaño para plantilla de nodo de Azure _AzureTemplate1_. Un archivo de script se ejecuta en el nodo principal después de configura el nodo principal.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>Solución de problemas


* **Error "No existe VNet"** : si ejecuta la secuencia de comandos para implementar varios clústeres en Azure simultáneamente en una suscripción, pueden fallar una o más implementaciones con el error "VNet *VNet\_nombre* no existe".
Si aparece este error, ejecute el script nuevamente para la implementación error.

* **Problemas de acceso a Internet de la red virtual Azure** - si crea un clúster con un controlador de dominio nuevo con el script de implementación o promover manualmente un nodo principal VM a controlador de dominio, puede experimentar problemas de las máquinas virtuales de conexión a Internet. Este problema puede ocurrir si un servidor de reenviador DNS se configura automáticamente en el controlador de dominio y no se resuelve este servidor DNS de reenviador correctamente.

    Para evitar este problema, inicie sesión en el controlador de dominio y quitar la configuración de reenviador o configurar un servidor DNS de reenviador válida. Para configurar esta opción, en el administrador del servidor, haga clic en **Herramientas** >
    **DNS** para abrir el Administrador de DNS y, a continuación, haga doble clic en **reenviadores**.

* **Problemas de acceso a la red RDMA desde máquinas virtuales de descarga intensiva** - si agrega el cálculo de Windows Server o intermediario máquinas virtuales de nodo con un tamaño capaz de RDMA como A8 o A9, puede experimentar problemas esas máquinas virtuales a conectarse a la red de la aplicación RDMA. Este problema se produce uno de los motivos es si la extensión HpcVmDrivers no está instalada correctamente cuando las máquinas virtuales se agregan al clúster. Por ejemplo, la extensión podría se detiene en el estado de la instalación.

    Para evitar este problema, primero hay que comprobar el estado de la extensión en las máquinas virtuales. Si la extensión no está instalada correctamente, pruebe a quitar los nodos de clúster HPC y, a continuación, agregar los nodos de nuevo. Por ejemplo, puede agregar el nodo de cálculo máquinas virtuales ejecutando el script de HpcIaaSNode.ps1 agregar en el nodo principal.
    
## <a name="next-steps"></a>Pasos siguientes

* Vuelva a ejecutar una carga de trabajo de prueba en el clúster. Para obtener un ejemplo, consulte el paquete de HPC [Guía de introducción](https://technet.microsoft.com/library/jj884144).

* Para que obtener un tutorial para la implementación de clúster de secuencias de comandos y ejecutar una carga de trabajo HPC, vea [Introducción a un clúster de HPC Pack en Azure para ejecutar las cargas de trabajo de Excel y el inicio de autoridad](virtual-machines-windows-excel-cluster-hpcpack.md).

* Pruebe las herramientas del paquete de HPC para iniciar, detener, agregar y quitar nodos de cálculo de un clúster de que crear. Consulte [Administrar nodos de cálculo en un clúster de HPC Pack en Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md).

* Para configurar enviar trabajos al clúster desde un equipo local, consulte [HPC enviar trabajos desde un equipo local a un clúster de HPC Pack en Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).
