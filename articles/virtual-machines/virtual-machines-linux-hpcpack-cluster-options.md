<properties
 pageTitle="Opciones de clúster de Linux HPC Pack en la nube | Microsoft Azure"
 description="Obtenga más información sobre opciones con Microsoft HPC Pack para crear y administrar un alto rendimiento de Linux informática clúster en la nube de Azure"
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# <a name="options-with-hpc-pack-to-create-and-manage-an-hpc-cluster-in-azure-for-linux-workloads"></a>Opciones de paquete HPC para crear y administrar un clúster HPC en Azure para Linux cargas de trabajo

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

En este artículo se centra en Opciones para usar HPC Pack para ejecutar las cargas de trabajo de Linux. También existen opciones para ejecutar [las cargas de trabajo de Windows HPC con HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Ejecutar un clúster HPC Pack en máquinas virtuales de Azure

### <a name="azure-templates"></a>Plantillas de Azure


* (Marketplace) [Clúster HPC Pack para Linux cargas de trabajo](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* (Tutorial) [Crear un clúster HPC con Linux nodos de cálculo](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)


### <a name="powershell-deployment-script"></a>Script de implementación de PowerShell

* [Crear un clúster HPC Linux con el script de implementación de HPC Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### <a name="tutorials"></a>Tutoriales

* [Tutorial: Introducción a Linux nodos de cálculo en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

* [Tutorial: Ejecutar NAMD con Microsoft HPC Pack en Linux nodos de cálculo en Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Tutorial: Ejecutar OpenFOAM con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Tutorial: Ejecutar estrella-CCM + con Microsoft HPC Pack en un RDMA Linux clúster de Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### <a name="cluster-management"></a>Administración de clúster

* [Enviar trabajos a un clúster de HPC Pack de Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [Administración del trabajo en el módulo de HPC](https://technet.microsoft.com/library/jj899585.aspx)


## <a name="create-rdma-clusters-for-mpi-workloads"></a>Crear clústeres RDMA para cargas de trabajo MPI

* [Tutorial: Ejecutar OpenFOAM con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Configurar un clúster de Linux RDMA para ejecutar aplicaciones de MPI](virtual-machines-linux-classic-rdma-cluster.md)

