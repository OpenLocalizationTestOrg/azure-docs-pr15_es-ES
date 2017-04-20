<properties
 pageTitle="Acerca de máquinas virtuales de descarga intensiva con Windows | Microsoft Azure"
 description="Obtener información general y consideraciones sobre el uso de los tamaños de descarga intensiva H serie y A8, A9, A10 y A11 Azure para servicios de máquinas virtuales de Windows y la nube"
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>Acerca de la serie de H y descarga intensiva máquinas virtuales de una serie

Aquí tiene información general y algunas consideraciones para el uso de la serie de H Azure más reciente y la anterior A8, A9, A10 y A11 instancias, también conocido como *descarga intensiva* instancias. En este artículo se centra en usar estas instancias para máquinas virtuales de Windows. En este artículo también está disponible para [Máquinas virtuales de Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md).


[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Acceso a la red RDMA

Puede crear grupos de instancias de RDMA capaz de Windows Server e implementar una de las implementaciones MPI admitidas para aprovechar las ventajas de la red de Azure RDMA. Esta red baja latencia y alto rendimiento está reservada para el tráfico MPI solo.

* **Sistema operativo**
    * **Máquinas virtuales** - Windows Server 2012 R2, Windows Server 2012
    * **Servicios de nube** - Windows Server 2012 R2, Windows Server 2012, familia de Windows Server 2008 R2 invitado OS

* **MPI** - MPI Microsoft (MS-MPI) 2012 R2 o posterior, la Biblioteca MPI Intel 5.x

Implementaciones MPI admitidas usar la interfaz directa de red de Microsoft para comunicarse entre instancias. Pasos de configuración de ejemplo y las opciones de implementación, consulte [configurar un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones de MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) y [Utilice las tareas de instancias múltiples para ejecutar aplicaciones de la interfaz de paso de mensajes (MPI) en el lote de Azure](../batch/batch-mpi.md) .


>[AZURE.NOTE]En máquinas virtuales RDMA capaz de descarga intensiva, la extensión HpcVmDrivers debe agregarse a las VM para instalar los controladores de dispositivos de red de Windows que son necesarias para conectividad RDMA. En la mayoría de las implementaciones, la extensión HpcVmDrivers se agrega automáticamente. Si necesita agregarse la extensión, vea [Administrar VM extensiones](virtual-machines-windows-classic-manage-extensions.md).

## <a name="considerations-for-hpc-pack-and-windows"></a>Consideraciones para el paquete de HPC y Windows

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), clúster HPC gratuita de Microsoft y solución de administración de trabajo, no es necesario para utilizar las instancias de descarga intensiva con Windows Server. Sin embargo, es una opción para crear un clúster de cálculo en Azure ejecutar aplicaciones de MPI basado en Windows y otras cargas de trabajo HPC. HPC Pack 2012 R2 y versiones posteriores incluyen un entorno de tiempo de ejecución de MS-MPI que puede usar la red de Azure RDMA cuando se implementa en máquinas virtuales RDMA capaz.

Para que obtener más información y listas de comprobación para utilizar las instancias de descarga intensiva con paquete de HPC en Windows Server, consulte [configurar un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones de MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).




## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre la disponibilidad y precios de los tamaños de descarga intensiva, consulte [máquinas virtuales de precios](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) y [precios de servicios en la nube](https://azure.microsoft.com/pricing/details/cloud-services/).

* Para las capacidades de almacenamiento y detalles de disco, vea [tamaños para máquinas virtuales](virtual-machines-linux-sizes.md).

* Para empezar a implementar y utilizar instancias de descarga intensiva con HPC Pack en Windows, consulte [configurar un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones de MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Para obtener información sobre el uso de instancias A8 y A9 para ejecutar aplicaciones de MPI con Azure lote, vea [tareas de instancias múltiples de uso para ejecutar aplicaciones de la interfaz de paso de mensajes (MPI) en el lote de Azure](../batch/batch-mpi.md).
