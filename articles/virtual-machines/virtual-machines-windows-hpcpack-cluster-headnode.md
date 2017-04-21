<properties
 pageTitle="Crear un nodo principal de HPC paquete en una máquina virtual de Azure | Microsoft Azure"
 description="Obtenga información sobre cómo usar el portal de Azure y el modelo de implementación de administrador de recursos para crear un nodo principal de Microsoft HPC Pack en una máquina virtual de Azure."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/17/2016"
 ms.author="danlep"/>

# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Crear el nodo principal de un clúster de HPC paquete en una máquina virtual de Azure con una imagen de Marketplace


Usar una [imagen de la máquina virtual de Microsoft HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) desde el portal de Azure y Azure Marketplace para crear el nodo principal de un clúster de HPC. Esta imagen HPC Pack VM se basa en el centro de datos de Windows Server 2012 R2 con HPC Pack 2012 R2 actualización 3 preinstalada. Use este nodo principal para una prueba de implementación de concepto de paquete HPC en Azure. A continuación, puede agregar nodos de cálculo en el clúster para ejecutar las cargas de trabajo HPC.



>[AZURE.TIP]Para implementar un clúster de HPC Pack completo en Azure que incluye el nodo principal y los nodos de cálculo, le recomendamos que use un método automatizado. Las opciones incluyen el [script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) y la plantilla de administrador de recursos de [clúster de HPC Pack para cargas de trabajo de Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/) . Consulte [Opciones de clúster de HPC Pack en Azure](virtual-machines-windows-hpcpack-cluster-options.md) plantillas adicionales. 


## <a name="planning-considerations"></a>Consideraciones de planeación

Como se muestra en la siguiente ilustración, implemente el nodo principal de HPC Pack en un dominio de Active Directory en una red virtual Azure.

![Nodo principal de HPC Pack][headnode]

* **Dominio de active Directory** : el paquete de HPC nodo principal debe estar unido a un dominio de Active Directory en Azure antes de comenzar con los servicios HPC en la máquina virtual. Como se muestra en este artículo, para una prueba de implementación de concepto, puede aumentar el nivel de la máquina virtual de crear para el nodo principal como un controlador de dominio antes de iniciar los servicios HPC. Otra opción es implementar un controlador de dominio independiente y bosques en Azure al que se une a la máquina virtual de nodo principal.

* **Una red virtual azure** - cuando se utiliza el modelo de implementación de administrador de recursos para implementar el nodo principal, especificar o crear una red virtual Azure. Usar la red virtual si debe unirse el nodo principal a un dominio de Active Directory existente. También debe posterior para agregar el nodo de cálculo máquinas virtuales al clúster.

    
## <a name="steps-to-create-the-head-node"></a>Pasos para crear el nodo principal

Siguientes son los pasos para usar el portal de Azure para crear una máquina virtual de Azure para el nodo principal de HPC Pack mediante el modelo de implementación de administrador de recursos. 


1. Si desea crear un nuevo bosque de Active Directory en Azure con el controlador de dominio independiente máquinas virtuales, una opción es utilizar una [plantilla de administrador de recursos](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/). Para una prueba simple de implementación de concepto, es adecuado omitir este paso y configurar el nodo principal VM propio como un controlador de dominio. Esta opción se describe más adelante en este artículo.
    
2. En [HPC Pack 2012 R2 en la página Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) en Azure Marketplace, haga clic en la **Máquina Virtual de crear**. 

3. En el portal, en la página **HPC Pack 2012 R2 en Windows Server 2012 R2** , seleccione el modelo de implementación de **Administrador de recursos** y, a continuación, haga clic en **crear**.

    ![Imagen de HPC Pack][marketplace]

4. Usar el portal para configurar las opciones y crear la máquina virtual. Si es nuevo en Azure, siga el tutorial [crear una máquina virtual de Windows en el portal de Azure](virtual-machines-windows-hero-tutorial.md). Para una prueba de implementación de concepto, normalmente puede aceptar el valor predeterminado o configuración recomendada.

    >[AZURE.NOTE]Si desea unir el nodo principal a un dominio de Active Directory existente en Azure, asegúrese de que especifique la red virtual de ese dominio al crear la máquina virtual.
       
4. Después de crear la máquina virtual y se está ejecutando la máquina virtual, [conectarse a la máquina virtual de](virtual-machines-windows-connect-logon.md) escritorio remoto. 

5. Unirse a la máquina virtual a un bosque de dominio existente o crear un bosque de dominio en la máquina virtual propiamente dicho.

    * Si ha creado la máquina virtual en una red virtual Azure con un bosque de dominio existente, unirse a la máquina virtual al bosque mediante herramientas estándares de administrador del servidor o Windows PowerShell. A continuación, reinicie.

    * Si ha creado la máquina virtual en una red virtual nueva (sin un bosque de dominio existente), a continuación, aumentar el nivel de la máquina virtual como un controlador de dominio. Realice los pasos estándares para instalar y configurar el rol de servicios de dominio de Active Directory en el nodo principal. Para conocer los pasos detallados, consulte [instalar un nuevo bosque Windows Server 2012 Active Directory](https://technet.microsoft.com/library/jj574166.aspx).

5. Después de la máquina virtual se está ejecutando y se ha unido a un bosque de Active Directory, inicie los servicios de HPC Pack como sigue:

    una. Conectar con el nodo principal VM con una cuenta de dominio que sea miembro del grupo de administradores locales. Por ejemplo, use la cuenta de administrador que configurar cuando creó el nodo principal VM.

    b. Para una configuración de nodo principal predeterminada, inicie Windows PowerShell como administrador y escriba lo siguiente:

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Puede tardar varios minutos para que los servicios de HPC Pack comenzar.

    Opciones de configuración de nodo principal adicionales, escriba `get-help HPCHNPrepare.ps1`.


## <a name="next-steps"></a>Pasos siguientes

* Ahora puede trabajar con el nodo principal del clúster HPC Pack. Por ejemplo, inicie el Administrador de clúster de HPC y completar la [Lista de tareas pendientes de implementación](https://technet.microsoft.com/library/jj884141.aspx).
* Si desea aumentar el clúster calcular capacidad a petición, agregue [los nodos de ráfaga Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md) en un servicio de nube. 

* Vuelva a ejecutar una carga de trabajo de prueba en el clúster. Para obtener un ejemplo, consulte el paquete de HPC [Guía de introducción](https://technet.microsoft.com/library/jj884144).

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png
