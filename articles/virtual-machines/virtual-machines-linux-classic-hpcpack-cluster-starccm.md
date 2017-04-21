<properties
 pageTitle="Ejecutar estrella-CCM + con HPC Pack en máquinas virtuales de Linux | Microsoft Azure"
 description="Implementar un clúster de Microsoft HPC Pack en Azure y ejecutar una estrella-CCM + trabajo en Linux varios nodos de cálculo a través de una red RDMA."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="xpillons"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/13/2016"
 ms.author="xpillons"/>

# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Ejecutar estrella-CCM + con Microsoft HPC Pack en un RDMA Linux clúster de Azure
Este artículo le muestra cómo implementar un clúster de Microsoft HPC Pack en Azure y ejecutar una [CD-adapco estrella-CCM +](http://www.cd-adapco.com/products/star-ccm%C2%AE) trabajo en varios nodos de cálculo Linux interconectados con InfiniBand.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC Pack proporciona características para ejecutar una gran variedad de HPC a gran escala y aplicaciones paralelas, incluidas MPI, en grupos de máquinas virtuales de Microsoft Azure. HPC Pack también es compatible con aplicaciones HPC Linux en ejecución en máquinas virtuales de nodo de cálculo de Linux que se implementan en un clúster de HPC Pack. Para obtener una introducción al uso de Linux nodos con HPC Pack de cálculo, vea [Introducción a Linux nodos de cálculo en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="set-up-an-hpc-pack-cluster"></a>Configurar un clúster de HPC Pack
Descargar las secuencias de comandos de implementación de HPC Pack IaaS desde el [Centro de descarga](https://www.microsoft.com/en-us/download/details.aspx?id=44949) y extraer localmente.

PowerShell Azure es necesario. Si no está configurado PowerShell en el equipo local, lea el artículo [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

En el momento de escribir este documento, las imágenes de Linux de Azure Marketplace (que contiene los controladores de InfiniBand para Azure) son para SLES 12, CentOS 6.5 y CentOS 7.1. En este artículo se basa en el uso de SLES 12. Para recuperar el nombre de todas las imágenes de Linux que admiten HPC en el catálogo de soluciones, puede ejecutar el siguiente comando PowerShell:

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

El resultado muestra la ubicación en la que están disponibles estas imágenes y el nombre de la imagen (**ImageName**) que se utilizará en la plantilla de implementación más adelante.

Antes de implementar el clúster, debe crear un archivo de plantilla de implementación de HPC Pack. Dado que estamos dirige a un clúster pequeño, el nodo principal se el controlador de dominio y hospedar una base de datos local de SQL.

La siguiente plantilla implementar nodo central, cree un archivo XML denominado **MyCluster.xml**y reemplace los valores de **SubscriptionId**, **StorageAccount**, **ubicación**, **VMName**y **ServiceName** por la suya.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Iniciar la creación de nodos de cabeza ejecutando el siguiente comando PowerShell en un símbolo del sistema con privilegios elevados:

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Después de 20 a 30 minutos, el nodo principal debería estar listo. Puede conectarse a él desde el portal de Azure haciendo clic en el icono de **conexión** de la máquina virtual.

Finalmente, es posible que tenga que corregir el reenviador DNS. Para ello, inicie el Administrador de DNS.

1.  Haga clic en el nombre del servidor en el Administrador de DNS, seleccione **Propiedades**y, a continuación, haga clic en la ficha **reenviadores** .

2.  Haga clic en el botón **Editar** para quitar los reenviadores y, a continuación, haga clic en **Aceptar**.

3.  Asegúrese de que está seleccionada la casilla de verificación **usar sugerencias de raíz si los reenviadores no están disponibles** y, a continuación, haga clic en **Aceptar**.

## <a name="set-up-linux-compute-nodes"></a>Configurar los nodos de cálculo Linux
Implementar los nodos de cálculo Linux mediante la misma plantilla de implementación que se usó para crear el nodo principal.

Copie el archivo **MyCluster.xml** desde el equipo local al nodo principal y a continuación, actualizar la etiqueta **NodeCount** con el número de nodos que desea distribuir (< = 20). Tenga cuidado de tiene suficiente núcleos disponibles en su cuota de Azure, porque cada instancia A9 ocupará 16 núcleos en su suscripción. Puede usar A8 instancias (8 núcleos) en lugar de A9 si desea usar más de máquinas virtuales en el mismo presupuesto.

En el nodo principal, copie las secuencias de comandos de implementación de HPC Pack IaaS.

Ejecute los siguientes comandos de PowerShell de Azure en un símbolo del sistema con privilegios elevados:

1.  Ejecutar **Complemento AzureAccount** para conectarse a su suscripción de Azure.

2.  Si tiene varias suscripciones, ejecute **Get-AzureSubscription** para obtener una lista de ellos.

3.  Configurar una suscripción de forma predeterminada, ejecute el **xxxx seleccione AzureSubscription - SubscriptionName-predeterminado** comando.

4.  Ejecutar **.\New-HPCIaaSCluster.ps1 - ConfigFile MyCluster.xml** para empezar a implementar Linux nodos de cálculo.

    ![Implementación de nodo de cabeza en acción][hndeploy]

Abra la herramienta de administrador de clúster de HPC Pack. Después de algunos minutos, nodos de cálculo Linux con regularidad aparecerá en la lista de nodos de cálculo de clúster. Con el modo clásico de implementación, máquinas virtuales de IaaS se crean secuencialmente. Si el número de nodos es importante, obtener todos implementado puede Eche una gran cantidad de tiempo.

![En el Administrador de clúster de HPC Pack de nodos de Linux][clustermanager]

Ahora que todos los nodos están en funcionamiento en el clúster, hay una configuración de la infraestructura adicional para realizar.

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>Configurar un recurso compartido de archivos de Azure para los nodos de Windows y Linux
Puede usar el servicio de archivo de Azure para almacenar las secuencias de comandos, paquetes de aplicaciones y archivos de datos. Archivo de Azure ofrece capacidades CIFS sobre el almacenamiento de blobs de Windows Azure como un almacén persistente. Tenga en cuenta que esto no es la solución más scalable, pero es la más sencilla y no requiere VM dedicadas.

Crear un recurso compartido de archivos de Azure siguiendo las instrucciones en el artículo [Introducción a almacenamiento de archivos de Azure en Windows](..\storage\storage-dotnet-how-to-use-files.md).

Mantener el nombre de su cuenta de almacenamiento como **saname**, el nombre de archivo compartido como **nombre de recurso compartido**y la clave de cuenta de almacenamiento como **sakey**.

### <a name="mount-the-azure-file-share-on-the-head-node"></a>Montaje de recurso compartido de archivos de Azure en el nodo principal
Abra un símbolo del sistema con privilegios elevados y ejecute el comando siguiente para almacenar las credenciales de la cámara del equipo local:

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

A continuación, para el recurso compartido de archivos de Azure de montaje, ejecute:

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>Montaje de recurso compartido de archivos de Azure Linux nodos de cálculo
Una herramienta útil que viene con HPC Pack es la herramienta clusrun. Puede usar esta herramienta de línea de comandos para ejecutar el mismo comando simultáneamente en un conjunto de nodos de cálculo. En este caso, se usa para el recurso compartido de archivos de Azure de montaje y conservarlo superar reiniciar el equipo.
En un símbolo elevado en el nodo principal, ejecute los comandos siguientes.

Para crear el directorio de montaje:

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Montaje recurso compartido de archivos de Azure:

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

Para conservar el recurso compartido de montaje:

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>Instalar estrella-CCM +
Azure instancias VM A8 y A9 proporcionan soporte técnico de InfiniBand y las capacidades RDMA. Los controladores de kernel que habilitan las capacidades están disponibles para Windows Server 2012 R2, SUSE 12, CentOS 6.5 e imágenes de CentOS 7.1 en Azure Marketplace. Microsoft MPI y MPI Intel (versión 5.x) son las dos bibliotecas MPI compatibles con los controladores de Azure.

ESTRELLA de CD adapco-CCM + suelte 11.x y más adelante se incluye con la versión de MPI Intel 5.x, por lo que se incluye compatibilidad con InfiniBand para Azure.

Obtener la Linux64 estrella-paquete CCM + desde el [portal de CD-adapco](https://steve.cd-adapco.com). En este caso, hemos usado versión 11.02.010 en precisión mixto.

En el nodo principal, en el recurso compartido de archivos de Azure **/hpcdata** , cree una secuencia de comandos de shell denominada **setupstarccm.sh** con el siguiente contenido. Esta secuencia de comandos se ejecutará en cada nodo de cálculo para configurar estrella-CCM + localmente.

#### <a name="sample-setupstarcmsh-script"></a>Secuencia de comandos de ejemplo setupstarcm.sh
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Ahora, configurar estrella-CCM + en todos su Linux nodos de cálculo, abra un símbolo del sistema con privilegios elevados y ejecute el siguiente comando:

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Mientras se ejecuta el comando, puede supervisar el uso de CPU mediante el mapa de calor del Administrador de clúster. Después de algunos minutos, todos los nodos deben estar configurados correctamente.

## <a name="run-star-ccm-jobs"></a>Ejecutar estrella-CCM + trabajos
Paquete de HPC se utiliza para sus capacidades del programador de trabajo para poder ejecutar estrella-CCM + trabajos. Para ello, se necesita la compatibilidad de algunas secuencias de comandos que se utilizan para iniciar el trabajo y ejecutar estrella-CCM +. Los datos de entrada se guardan en el recurso compartido de archivos de Azure primeros para simplificar.

Se usa el siguiente script de PowerShell para poner en cola una estrella-CCM + trabajo. Tarda tres argumentos:

*  El nombre del modelo

*  El número de nodos para usarse

*  El número de núcleos en cada nodo para usarse

Porque estrella-CCM + pueden usar para rellenar el ancho de banda de la memoria, normalmente es mejor usar menos núcleos por nodos de cálculo y agregar nuevos nodos. El número exacto de núcleos por nodo dependerá de la familia de procesadores y la velocidad de interconexión.

Los nodos se asignan exclusivamente para el trabajo y no se puede compartir con otros trabajos. El trabajo no se ha iniciado como un trabajo MPI directamente. La secuencia de comandos de shell **runstarccm.sh** iniciará el selector MPI.

El modelo de entrada y la secuencia de comandos de **runstarccm.sh** se almacenan en el recurso compartido **/hpcdata** que anteriormente estaba montaje.

Archivos de registro se denominan con el ID de trabajo y se almacenan en el **/hpcdata compartir**, junto con la estrella-CCM + archivos de salida.


#### <a name="sample-submitstarccmjobps1-script"></a>Secuencia de comandos de ejemplo SubmitStarccmJob.ps1
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job    
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Reemplace **runner.java** con una estrella preferida-selector de modelo CCM + Java y código de registro.

#### <a name="sample-runstarccmsh-script"></a>Secuencia de comandos de ejemplo runstarccm.sh
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

En nuestra prueba, hemos usado un símbolo de licencia de Power a petición. Para ese token, tendrá que configurar la variable de entorno **CDLMD_LICENSE_FILE $** **1999@flex.cd-adapco.com** y la clave en la opción **- podkey** de la línea de comandos.

Después de alguna inicialización, la secuencia de comandos extrae--de variables de entorno **$CCP_NODES_CORES** que establece el paquete de HPC: la lista de nodos para crear un archivo de host que utiliza el selector MPI. Este archivo host contendrá la lista de nombres de nodo de cálculo que se usan para la tarea, un nombre por línea.

El formato de **$CCP_NODES_CORES** sigue este patrón:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

Donde:

* `<Number of nodes>`es el número de nodos asignados a este trabajo.

* `<Name of node_n_...>`es el nombre de cada nodo asignado a este trabajo.

* `<Cores of node_n_...>`es el número de núcleos el nodo asignados a este trabajo.

El número de núcleos (**$NBCORES**) también se calcula según el número de nodos (**$NBNODES**) y el número de núcleos por nodo (suministrada como parámetro **$NBCORESPERNODE**).

Para las opciones de MPI, los que se usan con MPI Intel en Azure son:

*   `-mpi intel`para especificar MPI Intel.

*   `-fabric UDAPL`Para utilizar verbos de Azure InfiniBand.

*   `-cpubind bandwidth,v`para optimizar el ancho de banda MPI con estrella-CCM +.

*   `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"`para asegurarse de trabajar con Azure InfiniBand MPI Intel y establecer el número requerido de núcleos por nodo.

*   `-batch`Para iniciar estrella-CCM + en modo por lotes sin interfaz de usuario.


Por último, para iniciar un trabajo, asegúrese de que los nodos están en funcionamiento y que están en línea en el Administrador de clúster. A continuación, desde el símbolo del sistema de PowerShell, ejecute esto:

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>Dejar de nodos
Más adelante cuando haya terminado con las pruebas, puede usar los siguientes comandos de PowerShell de paquete HPC para detener e iniciar los nodos:

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>Pasos siguientes
Intente ejecutar otras cargas de trabajo de Linux. Por ejemplo, consulte:

* [Ejecutar NAMD con Microsoft HPC Pack Linux nodos de cálculo en Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Ejecutar OpenFOAM con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)


<!--Image references-->
[hndeploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/hndeploy.png
[clustermanager]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/ClusterManager.png
