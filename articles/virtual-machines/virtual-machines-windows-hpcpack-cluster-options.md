<properties
 pageTitle="Opciones de clúster de Windows HPC Pack en la nube | Microsoft Azure"
 description="Obtenga más información sobre opciones con Microsoft HPC Pack para crear y administrar un Windows informática de alto rendimiento clúster en la nube de Azure"
 services="virtual-machines-windows,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# <a name="options-with-hpc-pack-to-create-and-manage-a-windows-hpc-cluster-in-azure"></a>Opciones de paquete HPC para crear y administrar un clúster HPC de Windows Azure

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

En este artículo se centra en Opciones para crear clústeres HPC Pack para ejecutar las cargas de trabajo de Windows. También existen opciones para crear clústeres para ejecutar [las cargas de trabajo de HPC Linux con HPC Pack](virtual-machines-linux-hpcpack-cluster-options.md).


## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Ejecutar un clúster HPC Pack en máquinas virtuales de Azure

### <a name="azure-templates"></a>Plantillas de Azure

* (Marketplace) [Clúster HPC Pack para cargas de trabajo de Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)

* (Marketplace) [Clúster HPC Pack para cargas de trabajo de Excel](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)

* (Tutorial) [Crear un clúster HPC](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)

* (Tutorial) [Crear un clúster HPC con imagen de nodo de cálculo personalizado](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Imágenes de Azure VM

* [Paquete de HPC en Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Nodo de cálculo de HPC Pack en Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Paquete de HPC calcular nodo con Excel en Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### <a name="powershell-deployment-script"></a>Script de implementación de PowerShell

* [Crear un clúster HPC con el script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)

### <a name="tutorials"></a>Tutoriales

* [Tutorial: Introducción a un clúster de HPC Pack en Azure para ejecutar las cargas de trabajo de Excel y el inicio de autoridad](virtual-machines-windows-excel-cluster-hpcpack.md)



### <a name="manual-deployment-with-the-azure-portal"></a>Implementación manual con el portal de Azure

* [Configurar el nodo principal de un clúster de HPC paquete en una máquina virtual de Azure](virtual-machines-windows-hpcpack-cluster-headnode.md)

### <a name="cluster-management"></a>Administración de clúster

* [Administrar los nodos de cálculo en un clúster de HPC Pack en Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)

* [Reducir y ampliar los recursos de cálculo Azure en un clúster de HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

* [Enviar trabajos a un clúster de HPC Pack de Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [Administración del trabajo en el módulo de HPC](https://technet.microsoft.com/library/jj899585.aspx)


## <a name="add-worker-role-nodes-to-an-hpc-pack-cluster"></a>Agregar nodos de rol de trabajo a un clúster de HPC Pack


* [Instancias de trabajo Azure con HPC Pack de ráfaga](https://technet.microsoft.com/library/gg481749.aspx)

* [Tutorial: Configurar un clúster híbrido con HPC Pack en Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Agregar nodos de Azure "ráfaga" a un nodo principal de HPC Pack en Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)


## <a name="integrate-with-azure-batch"></a>Integrar con lote de Azure 

* [Ráfaga lote Azure con HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="create-rdma-clusters-for-mpi-workloads"></a>Crear clústeres RDMA para cargas de trabajo MPI

* [Configurar un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones de MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)
