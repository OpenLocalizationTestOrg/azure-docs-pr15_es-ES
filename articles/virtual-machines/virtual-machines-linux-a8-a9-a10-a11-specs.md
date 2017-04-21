<properties
 pageTitle="Acerca de máquinas virtuales de descarga intensiva con Linux | Microsoft Azure"
 description="Obtener información general y consideraciones sobre el uso de los tamaños de descarga intensiva H serie y A8, A9, A10 y A11 para máquinas virtuales de Linux"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>Acerca de la serie de H y descarga intensiva máquinas virtuales de una serie 

Aquí tiene información general y algunas consideraciones para el uso de la serie de H Azure más reciente y la anterior A8, A9, A10 y A11 tamaños, también conocido como *descarga intensiva* instancias. En este artículo se centra en usar estos tamaños para máquinas virtuales de Linux. En este artículo también está disponible para [Máquinas virtuales de Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md).




[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Acceso a la red RDMA

Puede crear clústeres de RDMA capaz de máquinas virtuales de Linux que ejecutar una de las siguientes admite las distribuciones de Linux HPC y una implementación de MPI compatible para aprovechar las ventajas de la red de Azure RDMA. Pasos de configuración de ejemplo y las opciones de implementación, consulte [configurar un clúster de Linux RDMA para ejecutar aplicaciones de MPI](virtual-machines-linux-classic-rdma-cluster.md) .

* **Distribuciones** - debe implementar máquinas virtuales de imágenes compatible con RDMA SUSE Linux Enterprise Server (SLES) o basado en OpenLogic CentOS HPC en Azure Marketplace. Las siguientes imágenes de catálogo de soluciones es compatible con los controladores de Linux RDMA necesarios:

    * SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium)
    
    * SLES 12 para HPC, SLES 12 para HPC (Premium)
    
    * En función de centOS HPC 7.1
    
    * En función de centOS HPC 6.5
    
    >[AZURE.NOTE]Para máquinas virtuales de serie H, se recomienda en un 12 SLES SP1 de una imagen HPC o imagen HPC 7.1 basada en CentOS.
    >
    >En las imágenes en función de CentOS HPC, actualizaciones del núcleo están deshabilitadas en el archivo de configuración de **yum** . Esto es porque los controladores Linux RDMA se distribuyen como un paquete RPM y actualizaciones de controladores podrían no funcionar si se actualiza el núcleo.

* **MPI** - Intel MPI biblioteca 5.x

    Dependiendo de la imagen del catálogo de soluciones elija, la instalación de licencia, independiente, o puede que se necesiten configuración de Intel MPI, como sigue: 
    
    * **SLES 12 SP1 de una imagen HPC** - instalar paquetes de Intel MPI distribuidos en la máquina virtual, ejecute el siguiente comando:
    
            sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

    * **SLES 12 de una imagen HPC** - por separado debe registrarse para descargar e instalar MPI Intel. Consulte la [Guía de instalación de la Biblioteca MPI Intel](https://software.intel.com/sites/default/files/managed/7c/2c/intelmpi-2017-installguide-linux.pdf).
    
    * **Imágenes HPC basado en centOS** - Intel MPI 5.1 ya está instalado.  

    Configuración adicional del sistema es necesaria para ejecutar trabajos MPI en máquinas virtuales agrupadas. Por ejemplo, en un clúster de máquinas virtuales, debe establecer la confianza entre los nodos de cálculo. Configuración típica, vea [configurar un clúster de Linux RDMA para ejecutar aplicaciones de MPI](virtual-machines-linux-classic-rdma-cluster.md).


## <a name="considerations-for-hpc-pack-and-linux"></a>Consideraciones para el paquete de HPC y Linux

[Paquete de HPC](https://technet.microsoft.com/library/jj899572.aspx), clúster HPC gratuita de Microsoft y solución de administración de trabajo, proporciona una opción Usar las instancias de descarga intensiva con Linux. Las últimas versiones de soporte técnico de HPC Pack 2012 R2 varias distribuciones Linux para que se ejecute calculan nodos implementados en máquinas virtuales de Azure, administrado por un nodo principal de Windows Server. Con Linux RDMA capaz nodos de cálculo ejecutando MPI Intel, HPC Pack puede programar y ejecutar las aplicaciones que tienen acceso a la red RDMA MPI Linux. Para obtener más información, vea [Introducción a Linux nodos de cálculo en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="network-topology-considerations"></a>Consideraciones de la topología de red

* En habilitado RDMA máquinas virtuales de Linux en Azure, Eth1 está reservado RDMA tráfico de red. No cambie cualquier configuración Eth1 o cualquier otra información del archivo de configuración que hacen referencia a esta red. Eth0 está reservado para el tráfico de red de Azure normal.

* En Azure, IP sobre InfiniBand (i b) no es compatible. Solo RDMA sobre i b es compatible.

## <a name="rdma-driver-updates-for-sles-12"></a>Actualizaciones de controladores RDMA para SLES 12

Después de crear una máquina virtual basada en una imagen SLES 12 HPC, debe actualizar los controladores RDMA en las VM RDMA conectividad de red. 

>[AZURE.IMPORTANT]Este paso es **necesario** para SLES 12 para implementaciones de HPC VM en todas las regiones de Azure. 
>Este paso no es necesario si implementa una SLES 12 SP1 para HPC, basado en CentOS HPC 7.1 o basado en CentOS 6.5 HPC VM. 

Antes de actualizar los controladores, detenga todos los procesos de **zypper** o los procesos que bloquean las bases de datos de repo SUSE en la máquina virtual. En caso contrario los controladores no pueden actualizar correctamente.  

Para actualizar los controladores de Linux RDMA en cada máquina virtual, ejecute los siguientes conjuntos de comandos CLI de Azure desde el equipo cliente.

**SLES 12 para HPC VM aprovisionado en el modelo de implementación clásica**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**SLES 12 para HPC VM aprovisionado en el modelo de implementación de administrador de recursos**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]Puede tardar algún tiempo para instalar a los controladores y, a continuación, el comando devuelve sin salida. Tras la actualización, la VM se reinicie y está preparado para su uso en unos minutos.

### <a name="sample-script-for-driver-updates"></a>Secuencia de comandos de actualizaciones de controlador

Si tiene un clúster de 12 SLES para máquinas virtuales de HPC, puede escribir scripts actualizar el controlador a través de todos los nodos en el clúster. Por ejemplo, la siguiente secuencia de comandos actualiza los controladores en un clúster de 8 nodos.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model, use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre la disponibilidad y precios de los tamaños de descarga intensiva, vea [máquinas virtuales de precios](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).

* Para las capacidades de almacenamiento y detalles de disco, vea [tamaños para máquinas virtuales](virtual-machines-linux-sizes.md).

* Para empezar a implementar y usar tamaños de descarga intensiva con RDMA en Linux, consulte [configurar un clúster de Linux RDMA para ejecutar aplicaciones de MPI](virtual-machines-linux-classic-rdma-cluster.md).


