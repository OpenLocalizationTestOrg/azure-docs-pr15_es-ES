<properties
   pageTitle="Script de PowerShell para implementar el clúster HPC Linux | Microsoft Azure"
   description="Ejecutar un script de PowerShell para implementar un clúster de Linux HPC Pack en máquinas virtuales de Windows Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Crear un cálculo clúster con el script de implementación de HPC Pack IaaS de Linux alto rendimiento

Ejecute la implementación de HPC Pack IaaS script de PowerShell para implementar un clúster HPC completo para cargas de trabajo de Linux en máquinas virtuales de Windows Azure. El clúster consta de un nodo principal Unido de Active Directory con Windows Server y Microsoft HPC Pack y nodos de cálculo que ejecutan una de las distribuciones de Linux compatibles con HPC Pack. Si desea implementar un clúster de HPC Pack en cargas de trabajo de Azure para Windows, vea [crear un clúster de Windows HPC con el script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md). También puede usar una plantilla de administrador de recursos de Azure para implementar un clúster de HPC Pack. Para obtener un ejemplo, consulte [crear un clúster HPC con Linux nodos de cálculo](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>Archivo de configuración de ejemplo

El archivo de configuración siguiente crea un nuevo controlador de dominio y bosque de dominio e implementa un clúster HPC Pack que tiene 1 nodo principal con bases de datos locales y 10 nodos de cálculo de Linux. Todos los servicios de nube se crean directamente en la ubicación de Asia oriental. Los nodos de cálculo Linux se crean en servicios de nube 2 y cuentas de almacenamiento 2 (es decir, _MyLnxCN-0001_ a _0005 MyLnxCN_ en _MyLnxCNService01_ y _mylnxstorage01_) y _MyLnxCN 0006_ a _0010 MyLnxCN_ en _MyLnxCNService02_ y _mylnxstorage02_. Los nodos de cálculo se crean a partir de una imagen de CentOS OpenLogic versión 7.0 Linux. 

Sustituir sus propios valores para su nombre de suscripción y los nombres de cuenta y servicio.

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
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>Solución de problemas

* **Error "No existe VNet"** : si ejecuta la secuencia de comandos de implementación de HPC Pack IaaS para implementar varios clústeres en Azure simultáneamente en una suscripción, pueden fallar una o más implementaciones con el error "VNet *VNet\_nombre* no existe".
Si aparece este error, vuelva a ejecutar la secuencia de comandos para la implementación error.

* **Problemas de acceso a Internet de la red virtual Azure** - si crea un clúster HPC Pack con un controlador de dominio nuevo con el script de implementación o promover manualmente un nodo principal VM a controlador de dominio, puede experimentar problemas para conectarse a las máquinas virtuales de la red virtual Azure a Internet. Esto puede ocurrir si un servidor de reenviador DNS se configura automáticamente en el controlador de dominio y no se resuelve este servidor DNS de reenviador correctamente.

    Para evitar este problema, inicie sesión en el controlador de dominio y quitar la configuración de reenviador o configurar un servidor DNS de reenviador válida. Para ello, en el administrador del servidor, haga clic en **Herramientas** >
    **DNS** para abrir el Administrador de DNS y, a continuación, haga doble clic en **reenviadores**.
    
## <a name="next-steps"></a>Pasos siguientes

* Consulte [Introducción a Linux nodos de cálculo en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md) para obtener información sobre distribuciones Linux admitidas, mover los datos y a continuación, enviar trabajos a un clúster de HPC Pack con Linux nodos de cálculo.
* Para obtener tutoriales que use la secuencia de comandos para crear un clúster y ejecutar una carga de trabajo de Linux HPC, vea:
    * [Ejecutar NAMD con Microsoft HPC Pack Linux nodos de cálculo en Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
    * [Ejecutar OpenFOAM con Microsoft HPC Pack Linux nodos de cálculo en Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
    * [Ejecutar estrella-CCM + con Microsoft HPC Pack en Linux nodos de cálculo en Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)
