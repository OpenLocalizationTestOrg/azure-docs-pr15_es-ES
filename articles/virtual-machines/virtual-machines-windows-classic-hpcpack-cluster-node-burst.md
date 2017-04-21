<properties
 pageTitle="Agregar ráfaga nodos a un clúster de HPC Pack | Microsoft Azure"
 description="Obtenga información sobre cómo agregar instancias de la función de trabajo ejecutando en un servicio de nube para expandir un clúster HPC Pack en Azure a petición"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/14/2016"
 ms.author="danlep"/>

# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Agregar nodos a petición "ráfaga" a un clúster de HPC Pack en Azure



Si configura un clúster de [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) en Azure, es recomendable ajustar rápidamente la capacidad de clúster hacia arriba o hacia abajo, sin mantener un conjunto de nodo de cálculo preconfigurados máquinas virtuales de una forma. En este artículo muestra cómo agregar nodos a petición "ráfaga" (instancias de rol de trabajo se ejecuta en un servicio de nube) como recursos de cálculo a un nodo principal en Azure. 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

![Nodos de ráfaga][burst]

Los pasos de este artículo de ayuda agregar nodos Azure rápidamente a un basada en nube HPC Pack cabezal nodo VM para una prueba o una implementación de prueba de concepto. Los pasos son los mismos que los pasos para "ráfaga en Azure" Agregar nube calcular capacidad a un clúster de HPC Pack local. Para obtener un tutorial, consulte [configurar un híbrido calcular clúster con Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Para obtener instrucciones detalladas y consideraciones para implementaciones de producción, vea [ráfaga en Azure con Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).


## <a name="prerequisites"></a>Requisitos previos

* **Nodo principal de HPC Pack implementado en una máquina virtual de Azure** - puede utilizar un nodo principal independiente VM o una que forma parte de un clúster de mayor tamaño. Para crear un nodo principal independiente, vea [implementar un nodo de cabeza HPC paquete en una máquina virtual de Azure](virtual-machines-windows-hpcpack-cluster-headnode.md). Para opciones de implementación de clúster de HPC Pack automatizados, consulte [Opciones para crear y administrar un clúster HPC de Windows Azure con Microsoft HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

    >[AZURE.TIP] Si usa el [script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para crear el clúster de Azure, puede incluir nodos de ráfaga Azure en la implementación automatizada. Vea los ejemplos de este artículo.

* **Suscripción de azure** - agregar nodos de Azure, puede elegir la misma suscripción utilizado para implementar el nodo principal VM, o una suscripción diferente (o suscripciones).

* **Cuota de núcleos** - posible que necesite aumentar la cuota de núcleos, especialmente si opta por implementar varios nodos de Azure con tamaños de multinúcleo. Para aumentar una cuota, [Abra una solicitud de soporte técnico en línea para clientes](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sin cargo.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>Paso 1: Crear un servicio de nube y una cuenta de almacenamiento para los nodos de Azure

Usar el portal de clásico Azure o herramientas equivalentes para configurar los recursos siguientes que son necesarios para implementación los nodos de Azure:

* Un nuevo servicio de nube de Azure
* Una nueva cuenta de almacenamiento de Azure

>[AZURE.NOTE] No volver a usar un servicio de nube existente en la suscripción. 

**Consideraciones**

* Configurar un servicio de nube independiente para cada plantilla de nodo de Azure que tiene previsto crear. Sin embargo, puede usar la misma cuenta de almacenamiento para varias plantillas de nodo.

* Le recomendamos que busque el servicio de nube y la cuenta de almacenamiento para la implementación de la misma región de Azure.




## <a name="step-2-configure-an-azure-management-certificate"></a>Paso 2: Configurar un certificado de administración de Azure

Para agregar nodos Azure como recursos de cálculo, es necesario un certificado de administración en el nodo principal y la carga correspondiente a la suscripción de Azure utilizada para la implementación de certificados.

En este escenario, puede elegir el **Certificado de administración de Azure de HPC predeterminado** que HPC Pack instala y configura automáticamente en el nodo principal. Este certificado es útil para probar implementaciones de prueba de concepto y efectos. Para usar este certificado, cargue la 2012\Bin\hpccert.cer C:\Program Files\Microsoft HPC Pack archivo desde el nodo principal VM a la suscripción. Para cargar el certificado en el [portal de clásica Azure](https://manage.windowsazure.com), haga clic en **configuración** > **Certificados de administración**.

Para que ver opciones adicionales configurar la administración de certificados, consulte [escenarios para configurar el certificado de administración de Azure para implementaciones de ráfaga de Azure](http://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>Paso 3: Implementar nodos de Azure en el clúster



Los pasos para agregar e iniciar Azure nodos en este escenario, en general, están la misma que los pasos con un nodo de cabeza local. Para obtener más información, consulte las secciones siguientes en los [pasos para implementar nodos de Azure con Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx):

* Crear una plantilla de nodo de Azure

* Agregar nodos de Azure en el clúster HPC de Windows

* Iniciar los nodos (aprovisionar) la Azure

Después de agregar e iniciar los nodos, está listos para su uso para ejecutar trabajos de clúster.

Si tiene problemas al implementar nodos de Azure, consulte [Solucionar problemas de implementaciones de nodos Azure con Microsoft HPC Pack](http://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>Pasos siguientes

* Para usar un tamaño de la instancia de descarga intensiva para los nodos de ráfaga, consulte las consideraciones [sobre máquinas virtuales de una serie de serie de H y descarga intensiva](virtual-machines-windows-a8-a9-a10-a11-specs.md).

* Si desea aumentar o reducir los recursos informáticos Azure según la carga de trabajo de clúster automáticamente, vea [automáticamente reducir y ampliar los recursos de cálculo Azure en un clúster de HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-cluster-node-burst/burst.png
