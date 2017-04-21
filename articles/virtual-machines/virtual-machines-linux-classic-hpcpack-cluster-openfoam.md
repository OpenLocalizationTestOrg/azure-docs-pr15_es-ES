<properties
 pageTitle="Ejecutar OpenFOAM con HPC Pack en máquinas virtuales de Linux | Microsoft Azure"
 description="Implementar un clúster de Microsoft HPC Pack en Azure y ejecutar un trabajo OpenFOAM en varios nodos de cálculo Linux a través de una red RDMA."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Ejecutar OpenFoam con Microsoft HPC Pack en un clúster de Linux RDMA en Azure

En este artículo se muestra una forma de ejecutar OpenFoam en máquinas virtuales de Windows Azure. A continuación, implementar un clúster de Microsoft HPC Pack con Linux nodos de cálculo en Azure y ejecutar una [OpenFoam](http://openfoam.com/) del trabajo con MPI Intel. Puede usar máquinas virtuales de Azure RDMA capaz de los nodos de cálculo, para que los nodos de cálculo comunican a través de la red de Azure RDMA. Otras opciones para ejecutar OpenFoam en Azure incluyen imágenes comerciales completamente configuradas disponibles en el catálogo de soluciones, como [OpenFoam 2.3 en CentOS 6 de UberCloud](https://azure.microsoft.com/marketplace/partners/ubercloud/openfoam-v2dot3-centos-v6/)y ejecutando en [Lotes de Azure](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/). 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM (para abrir la operación de campo y manipulación) es un paquete de software de código abierto dinámica de fluidos (CFD) que se utiliza ampliamente en ingeniería y ciencia en organizaciones académicas y comerciales. Incluye herramientas para meshing snappyHexMesh en particular, un mesher paralelizada para geometrías complejas de CAD y procesamiento anterior y posterior. Casi todos los procesos que se ejecutan en paralelo, habilitar a los usuarios aprovechar al máximo de hardware del equipo a su disposición.  

Microsoft HPC Pack proporciona características para ejecutar HPC a gran escala y aplicaciones paralelas, incluidas MPI, en grupos de máquinas virtuales de Microsoft Azure. HPC Pack también es compatible con la ejecución Linux HPC máquinas virtuales implementadas en un clúster de HPC Pack de nodo de cálculo de aplicaciones en Linux. Para obtener una introducción al uso de los nodos de cálculo Linux con HPC Pack, vea [Introducción a Linux nodos de cálculo en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md) .

>[AZURE.NOTE] En este artículo muestra cómo realizar una carga de trabajo de MPI Linux con HPC Pack. Se supone que tiene algunos estar familiarizado con la administración del sistema Linux y ejecutan cargas de trabajo MPI en clústeres de Linux. Si usa las versiones de MPI y OpenFOAM distinto de los que se muestra en este artículo, debe modificar algunos pasos de instalación y configuración. 

## <a name="prerequisites"></a>Requisitos previos

*   **Clúster HPC Pack con Linux RDMA capaz de nodos de cálculo** : implementar un clúster de HPC Pack con tamaño A8, A9, H16r o H16rm Linux nodos de cálculo mediante una [plantilla de administrador de recursos de Azure](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) o una [secuencia de comandos de PowerShell de Azure](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Para los requisitos previos y los pasos para cualquiera de estas opciones, vea [Introducción a Linux nodos de cálculo en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md) . Si elige la opción de implementación de secuencia de comandos de PowerShell, vea el archivo de configuración de ejemplo en los archivos de ejemplo al final de este artículo. Use esta configuración para implementar un clúster de basados en Azure HPC Pack que consta de un nodo principal A8 Windows Server 2012 R2 de tamaño y 2 nodos de cálculo A8 SUSE Linux Enterprise Server 12 de tamaño. Sustituir los valores apropiados para los nombres de servicio y suscripción. 

    **Otros aspectos que debe conocer**

    *   Para Linux RDMA redes los requisitos previos en Azure, consulte [acerca de la serie de H y descarga intensiva máquinas virtuales de una serie](virtual-machines-windows-a8-a9-a10-a11-specs.md).

    *   Si utiliza la opción de implementación de secuencia de comandos de Powershell, implemente todos los nodos de cálculo Linux dentro de servicio de una nube para utilizar la conexión de red RDMA.

    *   Después de implementar los nodos de Linux, conectar SSH para realizar las tareas administrativas adicionales. Encontrar los detalles de la conexión SSH para cada VM Linux en el portal de Azure.  
        
*   **MPI Intel** - ejecutar OpenFOAM en SLES 12 HPC nodos de cálculo en Azure, debe instalar el tiempo de ejecución de Intel MPI biblioteca 5 desde el [sitio de Intel.com](https://software.intel.com/en-us/intel-mpi-library/). (Intel MPI 5 está preinstalado en imágenes en función de CentOS HPC).  En un paso más adelante, si es necesario, instale MPI Intel en los nodos de cálculo Linux. Para preparar este paso, después de registrar con Intel, siga el vínculo del mensaje de confirmación a la página web relacionados. A continuación, copie el vínculo de descarga del archivo .tgz para la versión adecuada de Intel MPI. En este artículo está basado en Intel MPI versión 5.0.3.048.

*   **Paquete de origen de OpenFOAM** - descarga del software del paquete de origen de OpenFOAM para Linux desde el [sitio OpenFOAM Foundation](http://openfoam.org/download/2-3-1-source/). En este artículo se basa en versión de paquete de origen 2.3.1, disponible para su descarga como OpenFOAM 2.3.1.tgz. Siga las instrucciones más adelante en este artículo para descomprimir y compilar OpenFOAM en los nodos de cálculo Linux.

*   **EnSight** (opcional) - para ver los resultados de la simulación de OpenFOAM, descargue e instale el programa de visualización y análisis de [EnSight](https://www.ceisoftware.com/download/) . Información de licencia y descarga se encuentra en el sitio EnSight.


## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configurar confianza mutua entre nodos de cálculo

Ejecutar un trabajo de nodo entre en varios nodos de Linux requiere los nodos confiar en ellos ( **rsh** o **ssh**). Cuando se crea el clúster HPC Pack con el script de implementación de Microsoft HPC Pack IaaS, la secuencia de comandos configurará automáticamente permanente confianza mutua para la cuenta de administrador que especifique. Para crear en el dominio del clúster de los usuarios no es administrador, debe configurar confianza mutua temporal entre los nodos cuando se les asigna un trabajo y destroy la relación después de completar el trabajo. Para establecer la confianza para cada usuario, proporcione un par de claves RSA en el clúster de HPC Pack para la relación de confianza.

### <a name="generate-an-rsa-key-pair"></a>Generar un par de claves RSA

Es fácil generar un par de claves RSA, que contiene una clave pública y una clave privada, ejecute el comando de **ssh keygen** Linux.

1.  Inicie sesión en un equipo Linux.

2.  Ejecute el siguiente comando:

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] Presione **ENTRAR** para usar la configuración predeterminada hasta que se haya completado el comando. No escriba una frase de contraseña aquí; Cuando se le pide una contraseña, simplemente, presione **ENTRAR**.

    ![Generar un par de claves RSA][keygen]

3.  Cambie al directorio de ~/.ssh. La clave privada se almacena en id_rsa y la clave pública en id_rsa.pub.

    ![Claves públicas y privadas][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Agregar el par de claves al clúster HPC Pack
1.  Realizar una conexión a Escritorio remoto para el nodo principal con su cuenta de administrador de HPC Pack (la cuenta de administrador configurar cuando ejecutó el script de implementación).

2. Use los procedimientos estándar de Windows Server para crear una cuenta de usuario de dominio en el dominio de Active Directory del clúster. Por ejemplo, use la herramienta de usuario de Active Directory y equipos en el nodo principal. Los ejemplos de este artículo se suponen que se crea un usuario de dominio denominado hpclab\hpcuser.

3.  Cree un archivo llamado C:\cred.xml y copie los datos de clave RSA en él. Es un archivo de cred.xml de ejemplo al final de este artículo.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

4.  Abra un símbolo del sistema y escriba el comando siguiente para configurar los datos de credenciales para la cuenta de hpclab\hpcuser. Use el parámetro **extendeddata** para pasar el nombre de archivo C:\cred.xml que creó para los datos de clave.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Este comando se completa correctamente sin salida. Después de establecer las credenciales de las cuentas de usuario que necesita para ejecutar trabajos, almacenar el archivo de cred.xml en una ubicación segura o eliminarlo.

5.  Si genera el par de claves RSA en uno de los nodos de Linux, no olvide eliminar las claves cuando termine de usarlos. Si el paquete de HPC encuentra un archivo de id_rsa existente o id_rsa.pub, no configurar confianza mutua.

>[AZURE.IMPORTANT] No se recomienda ejecutar un trabajo de Linux como administrador de clústeres en un clúster compartido, como un trabajo enviado por un administrador se ejecuta en la cuenta raíz en los nodos de Linux. Sin embargo, un trabajo enviado por un usuario que no es administrador se ejecuta en una cuenta de usuario de Linux local con el mismo nombre que el usuario de trabajo. En este caso, HPC Pack configurará mutua confianza para este usuario Linux a través de los nodos asignados a la tarea. Puede configurar el usuario Linux manualmente en los nodos de Linux antes de ejecutar el trabajo o HPC Pack crea el usuario automáticamente cuando se envía el trabajo. Si el paquete de HPC crea el usuario, HPC Pack elimina después de que finalice el trabajo. Para reducir las amenazas de seguridad, HPC Pack quita las claves después de finalización del trabajo.

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configurar un recurso compartido de archivos para los nodos de Linux

Ahora, configure un recurso compartido de SMB estándar en una carpeta en el nodo principal. Para permitir que los nodos de Linux tener acceso a archivos de la aplicación con una ruta de acceso común, montaje de la carpeta compartida en los nodos de Linux. Si lo desea, puede utilizar otra opción para compartir archivos, como un recurso compartido de archivos de Azure - recomendado para muchos escenarios - o un recurso compartido NFS. Ver el archivo de compartir información y conocer los pasos detallados en [Introducción a Linux nodos de cálculo en un clúster de paquete HPC en Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

1.  Crear una carpeta en el nodo principal y compartirlo a todos los usuarios estableciendo privilegios de lectura y escritura. Por ejemplo, compartir C:\OpenFOAM en el nodo principal como \\ \\SUSE12RDMA HN\OpenFOAM. Aquí, *HN SUSE12RDMA* es el nombre de host del nodo principal.

2.  Abra una ventana de Windows PowerShell y ejecute los comandos siguientes:

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

    clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

El primer comando crea una carpeta denominada /openfoam en todos los nodos en el grupo LinuxNodes. El segundo comando montajes la //SUSE12RDMA-HN/OpenFOAM carpeta compartida en los nodos de Linux con dir_mode y file_mode bits establecen en 777. El *nombre de usuario* y la *contraseña* en el comando debería las credenciales de un usuario en el nodo principal.

>[AZURE.NOTE]La "\`" symbol en el segundo comando es un símbolo de escape para PowerShell. "\`," significa "," (coma) es una parte del comando.

## <a name="install-mpi-and-openfoam"></a>Instalar MPI y OpenFOAM

Para ejecutar OpenFOAM como un trabajo MPI en la red RDMA, debe compilar OpenFOAM con las bibliotecas de Intel MPI. 

En primer lugar, ejecute varios comandos **clusrun** para instalar las bibliotecas de MPI Intel (si no está ya instalado) y OpenFOAM en los nodos de Linux. Use la opción compartir nodo principal configurada anteriormente para compartir los archivos de instalación entre los nodos de Linux.

>[AZURE.IMPORTANT]Estos instalación y compilar pasos son ejemplos. Necesita algunos conocimientos de administración del sistema Linux para asegurarse de que las bibliotecas y compiladores dependientes están instaladas correctamente. Debe modificar ciertas variables de entorno u otras opciones para las versiones de Intel MPI y OpenFOAM. Para obtener más información, vea [Biblioteca de MPI Intel de la Guía de instalación de Linux](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) y [OpenFOAM origen de paquete de instalación](http://openfoam.org/download/2-3-1-source/) en su entorno.


### <a name="install-intel-mpi"></a>Instalar Intel MPI

Guarde el paquete de instalación descargado para MPI Intel (l_mpi_p_5.0.3.048.tgz en este ejemplo) en C:\OpenFoam en el nodo principal para que los nodos de Linux pueden acceder a este archivo de /openfoam. A continuación, ejecute **clusrun** para instalar Biblioteca MPI Intel en todos los nodos de Linux.

1.  Los siguientes comandos copiar el paquete de instalación y extraer a /opt/intel en cada nodo.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel

    clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
    ```

2.  Para instalar la Biblioteca MPI Intel silenciosamente, use un archivo de silent.cfg. Puede encontrar un ejemplo en los archivos de ejemplo al final de este artículo. Coloque este archivo en el /openfoam de la carpeta compartida. Para obtener más información acerca del archivo silent.cfg, vea [Biblioteca de MPI Intel de la Guía de instalación de Linux - instalación silencioso](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall).

    >[AZURE.TIP]Asegúrese de que ha guardado el archivo silent.cfg como un archivo de texto con Linux finales (sólo avance de línea, no rc al) de línea. Este paso asegura que se ejecuta correctamente en los nodos de Linux.

3.  Instale la Biblioteca MPI Intel en modo silencioso.
 
    ```
    clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
    ```
    
### <a name="configure-mpi"></a>Configurar MPI

Para realizar pruebas, debe agregar las siguientes líneas a la /etc/security/limits.conf en cada uno de los nodos de Linux:


    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


Reinicie los nodos Linux después de actualizar el archivo limits.conf. Por ejemplo, use el comando **clusrun** siguientes:

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Después de reiniciar, asegúrese de que la carpeta compartida se montaje como /openfoam.

### <a name="compile-and-install-openfoam"></a>Compile e instalar OpenFOAM

Guarde el paquete de instalación descargado para el paquete de origen de OpenFOAM (OpenFOAM 2.3.1.tgz en este ejemplo) a C:\OpenFoam en el nodo principal para que los nodos de Linux pueden acceder a este archivo de /openfoam. Ejecute los comandos **clusrun** para compilar OpenFOAM en todos los nodos de Linux.


1.  Crear una carpeta /opt/OpenFOAM en cada nodo Linux, copie el paquete de origen a esta carpeta y extraer de allí.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM

    clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
    
    clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
    ```

2.  Para compilar OpenFOAM con la biblioteca de MPI Intel, configure en primer lugar algunas variables de entorno para MPI Intel y OpenFOAM. Utilice una secuencia de comandos de fiesta denominado settings.sh para establecer las variables. Puede encontrar un ejemplo en los archivos de ejemplo al final de este artículo. Coloque este archivo (guardado con el fin de línea de Linux) en la /openfoam de la carpeta compartida. Este archivo también contiene valores para los tiempos de ejecución MPI y OpenFOAM más adelante usa para ejecutar un trabajo OpenFOAM.

3. Instalar paquetes dependientes necesarios para compilar OpenFOAM. Dependiendo de la distribución de Linux, primero debe agregar un repositorio. Ejecute los comandos **clusrun** similares al siguiente:

    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
    
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
    
    Si es necesario, SSH para cada nodo Linux para ejecutar los comandos para confirmar que funcionan correctamente.

4.  Ejecute el comando siguiente para compilar OpenFOAM. El proceso de compilación tarda algún tiempo en completarse y genera una gran cantidad de información de registro en la salida estándar, así que use la opción **Intercalar** para mostrar los resultados intercalar.

    ```
    clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
    ```
    
    >[AZURE.NOTE]La "\`" symbol en el comando es un símbolo de escape para PowerShell. "\`&" significa el "&" es una parte del comando.

## <a name="prepare-to-run-an-openfoam-job"></a>Prepararse para ejecutar un trabajo OpenFOAM

Prepare ahora ejecutar un trabajo de MPI denominado sloshingTank3D, que es una de las muestras OpenFoam, dos nodos de Linux. 

### <a name="set-up-the-runtime-environment"></a>Configurar el entorno de tiempo de ejecución

Para configurar los entornos de tiempo de ejecución de MPI y OpenFOAM en los nodos de Linux, ejecute el comando siguiente en una ventana de Windows PowerShell en el nodo principal. (Este comando sólo es válida para SUSE Linux.)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>Preparar datos de ejemplo

Use la opción compartir nodo principal configurado anteriormente para compartir archivos entre los nodos de Linux (montados como /openfoam).

1.  SSH a uno de su Linux nodos de cálculo.

2.  Ejecute el comando siguiente para configurar el entorno de tiempo de ejecución OpenFOAM, si todavía no lo ha hecho ya.

    ```
    $ source /openfoam/settings.sh
    ```
    
3.  Copiar los datos de ejemplo sloshingTank3D a la carpeta compartida y desplácese a él.

    ```
    $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/

    $ cd /openfoam/sloshingTank3D
    ```

4.  Cuando utiliza los parámetros predeterminados de este ejemplo, puede tardar decenas de minutos para ejecutar, por lo que desea modificar algunos parámetros para hacer que se ejecute más rápido. Es una opción simple modificar el tiempo paso variables deltaT y writeInterval en el archivo de sistema/controlDict. Este archivo almacena todos los datos de entrada relacionados con el control de tiempo de lectura y escritura y datos de soluciones. Por ejemplo, puede cambiar el valor de deltaT de 0,05 a 0,5 y el valor de writeInterval de 0,05 a 0,5.

    ![Modificar variables de paso][step_variables]

5.  Especifique los valores que desee para las variables en el archivo de sistema/decomposeParDict. Este ejemplo usa dos nodos Linux con 8 núcleos, así que establezca numberOfSubdomains 16 y n de hierarchicalCoeffs a (1 1 16), lo que significa ejecuta OpenFOAM en paralelo con los procesos de 16. Para obtener más información, consulte [OpenFOAM Guía del usuario: 3.4 aplicaciones de ejecución en paralelo](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).

    ![Descomponer procesos][decompose]

6.  Ejecute los comandos siguientes desde el directorio sloshingTank3D para preparar los datos de ejemplo.

    ```
    $ . $WM_PROJECT_DIR/bin/tools/RunFunctions

    $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict

    $ runApplication blockMesh

    $ cp 0/alpha.water.org 0/alpha.water

    $ runApplication setFields  
    ```
    
7.  En el nodo principal, verá que los archivos de datos de ejemplo se copian en C:\OpenFoam\sloshingTank3D. (C:\OpenFoam es la carpeta compartida en el nodo principal).

    ![Archivos de datos en el nodo principal][data_files]

### <a name="host-file-for-mpirun"></a>Archivo de host para mpirun

En este paso, creará un archivo de host (una lista de nodos de cálculo) que usa el comando **mpirun** .

1.  En uno de los nodos de Linux, cree un archivo llamado archivo host en /openfoam, para que pueda encontrar este archivo /openfoam/hostfile en todos los nodos de Linux.

2.  Escribir los nombres de los nodos Linux en este archivo. En este ejemplo, el archivo contiene los nombres de los siguientes:
    
    ```       
    SUSE12RDMA-LN1
    SUSE12RDMA-LN2
    ```
    
    >[AZURE.TIP]También puede crear este archivo en C:\OpenFoam\hostfile en el nodo principal. Si elige esta opción, guardarlo como un archivo de texto con Linux finales de línea (sólo avance de línea, no rc al). Esto garantiza que se ejecuta correctamente en los nodos de Linux.

    **Contenedor de script de fiesta**

    Si tiene muchos nodos de Linux y desea que su trabajo para que se ejecute en sólo algunos de ellos, no es aconsejable usar un archivo de host fijo, porque no sabe qué nodos se asignará a su trabajo. En este caso, escribir un contenedor de script de fiesta de **mpirun** crear el archivo de host automáticamente. Puede encontrar un contenedor de script de fiesta de ejemplo denominado hpcimpirun.sh al final de este artículo y guárdelo como /openfoam/hpcimpirun.sh. Esta secuencia de comandos de ejemplo hace lo siguiente:

    1.  Configura las variables de entorno para **mpirun**y algunos parámetros de comando de adición para ejecutar el trabajo MPI a través de la red RDMA. En este caso, Establece las variables siguientes:

        *   I_MPI_FABRICS = shm:dapl
        *   I_MPI_DAPL_PROVIDER = ofa-v2-ib0
        *   I_MPI_DYNAMIC_CONNECTION = 0

    2.  Crea un archivo de host según el entorno $ variable CCP_NODES_CORES, que está establecida de nodo principal cuando se activa el trabajo.

        El formato de $CCP_NODES_CORES sigue este patrón:

        ```
        <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
        ```

        donde

        * `<Number of nodes>`-el número de nodos asignados a este trabajo.  
        
        * `<Name of node_n_...>`-el nombre de cada nodo asignado a este trabajo.
        
        * `<Cores of node_n_...>`-el número de núcleos en el nodo asignados a este trabajo.

        Por ejemplo, si el trabajo debe dos nodos para ejecutar, es similar a CCP_NODES_CORES $
        
        ```
        2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
        ```
        
    3.  Llame al comando **mpirun** y anexa dos parámetros a la línea de comandos.

        * `--hostfile <hostfilepath>: <hostfilepath>`-la ruta de acceso del archivo host crea la secuencia de comandos

        * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}`-una variable de entorno establecida por el nodo principal de HPC Pack, que almacena el número de núcleos total asignados a este trabajo. En este caso, especifica el número de procesos de **mpirun**.


## <a name="submit-an-openfoam-job"></a>Enviar un trabajo OpenFOAM

Ahora puede enviar un trabajo en el Administrador de clúster de HPC. Debe pasar hpcimpirun.sh de script en las líneas de comandos para algunas de las tareas de trabajo.

1. Conectar con el nodo de clúster principal e inicie el Administrador de clúster de HPC.

2. **Administración de recursos**, asegúrese de que los nodos de cálculo Linux están en el estado **en línea** . Si no lo están, selecciónelas y haga clic en **Conectar**.

3.  En **Administración del trabajo**, haga clic en **Nueva tarea**.

4.  Escriba un nombre para el trabajo como _sloshingTank3D_.

    ![Detalles del trabajo][job_details]

5.  En los **recursos de trabajo**, elija el tipo de recurso como "Nodo" y establezca el mínimo a 2. Esta configuración ejecuta el trabajo en dos nodos de Linux, cada una de las cuales tiene ocho núcleos en este ejemplo.

    ![Recursos de trabajo][job_resources]

6. Haga clic en **Editar tareas** en el panel de navegación izquierdo y, a continuación, haga clic en **Agregar** para agregar una tarea a la tarea. Agregar cuatro tareas al trabajo con las siguientes líneas de comandos y opciones.

    >[AZURE.NOTE]Ejecutar `source /openfoam/settings.sh` configura los entornos de tiempo de ejecución OpenFOAM y MPI, para cada una de las siguientes tareas llama antes del comando OpenFOAM.

    *   **Tarea 1**. Ejecute **decomposePar** para generar los archivos de datos para ejecutar **interDyMFoam** en paralelo.
    
        *   Asignar un nodo a la tarea

        *   **Línea de comandos** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
    
        *   **Directorio de trabajo** - / openfoam/sloshingTank3D
        
        Vea la siguiente ilustración. Configurar las tareas restantes de forma similar.

        ![Detalles de la tarea 1][task_details1]

    *   **Tarea 2**. Ejecutar **interDyMFoam** en paralelo para calcular los datos de ejemplo.

        *   Asignar dos nodos a la tarea

        *   **Línea de comandos** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`

        *   **Directorio de trabajo** - / openfoam/sloshingTank3D

    *   **Tarea 3**. Ejecute **reconstructPar** para combinar los conjuntos de directorios de tiempo de cada directorio processor_N_ en un único conjunto.

        *   Asignar un nodo a la tarea

        *   **Línea de comandos** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`

        *   **Directorio de trabajo** - / openfoam/sloshingTank3D

    *   **Tarea 4**. Ejecutar **foamToEnsight** en paralelo para convertir los archivos de resultado OpenFOAM en formato de EnSight y coloque los archivos de EnSight en un directorio denominado Ensight en el directorio de mayúsculas y minúsculas.

        *   Asignar dos nodos a la tarea

        *   **Línea de comandos** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`

        *   **Directorio de trabajo** - / openfoam/sloshingTank3D

6.  Agregar dependencias a estas tareas de forma ascendente de la tarea.

    ![Dependencias entre tareas][task_dependencies]

7.  Haga clic en **Enviar** para ejecutar esta tarea.

    De forma predeterminada, el paquete de HPC envía el trabajo como su cuenta de usuario de inicio de sesión actual. Después de hacer clic en **Enviar**, es posible que vea un cuadro de diálogo que le pide que escriba el nombre de usuario y contraseña.

    ![Credenciales de trabajo][creds]

    En algunas condiciones, HPC Pack recuerda la información de usuario antes de entrada y no mostrar este cuadro de diálogo. Para hacer HPC Pack volver a mostrar, escriba el siguiente comando en un símbolo del sistema y, a continuación, enviar el trabajo.

    ```
    hpccred delcreds
    ```

8.  El trabajo de toma de decenas de minutos a varias horas según los parámetros que haya configurado para la muestra. En el mapa de calor, verá el trabajo que se ejecute en los nodos de Linux. 

    ![Mapa de calor][heat_map]

    En cada nodo, se inician los procesos de ocho.

    ![Procesos de Linux][linux_processes]

9.  Cuando finalice el trabajo, encontrará los resultados del trabajo en carpetas en C:\OpenFoam\sloshingTank3D y los archivos de registro en C:\OpenFoam.


## <a name="view-results-in-ensight"></a>Ver los resultados en EnSight

Opcionalmente, use [EnSight](https://www.ceisoftware.com/) para visualizar y analizar los resultados de la tarea OpenFOAM. Para obtener más información sobre la visualización y animación en EnSight, consulte esta [Guía de vídeo](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1.  Después de instalar EnSight en el nodo principal, iniciarlo.

2.  Abra C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case.

    Vea un depósito en el Visor.

    ![Cisterna en EnSight][tank]

3.  Crear un **Isosurface** de **internalMesh**y, a continuación, elija la variable **alpha_water**.

    ![Crear un isosurface][isosurface]

4.  Establecer el color de **Isosurface_part** creado en el paso anterior. Por ejemplo, establezca agua azul.

    ![Editar isosurface color][isosurface_color]

5.  Crear un **volumen Iso** de **paredes** seleccionando **paredes** en el panel **elementos** y haga clic en el botón **Isosurfaces** en la barra de herramientas.

6.  En el cuadro de diálogo seleccione **tipo** como **Isovolume** y establezca el mínimo de **rango Isovolume** en 0,5. Para crear el isovolume, haga clic en **crear con los elementos seleccionados**.

7.  Establecer el color de **Iso_volume_part** creado en el paso anterior. Por ejemplo, establecer profundidad agua azul.

8.  Establecer el color de **las paredes**. Por ejemplo, establecer a blanco transparente.

9. Ahora, haga clic en **Reproducir** para ver los resultados de la simulación.

    ![Resultado del tanque][tank_result]

## <a name="sample-files"></a>Archivos de ejemplo

### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Archivo de configuración XML de ejemplo para una implementación de clúster mediante script de PowerShell

 ```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>suse12rdmavnet</VNetName>
    <SubnetName>SUSE12RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>SUSE12RDMA-HN</VMName>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <NodeCount>2</NodeCount>
      <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

### <a name="sample-credxml-file"></a>Ejemplo de archivo de cred.xml

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### <a name="sample-silentcfg-file-to-install-mpi"></a>Archivo de silent.cfg de ejemplo para instalar MPI

```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no


```

### <a name="sample-settingssh-script"></a>Secuencia de comandos de ejemplo settings.sh

```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


###<a name="sample-hpcimpirunsh-script"></a>Secuencia de comandos de ejemplo hpcimpirun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
    ${MPIRUN} $*
else
    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

    # Get every node name and write into the hostfile file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the mpirun with hostfile arg
    ${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keys.png
[step_variables]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/step_variables.png
[data_files]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/data_files.png
[decompose]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/decompose.png
[job_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_details.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_resources.png
[task_details1]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_details1.png
[task_dependencies]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_dependencies.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/creds.png
[heat_map]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/heat_map.png
[tank]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank.png
[tank_result]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank_result.png
[isosurface]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface.png
[isosurface_color]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface_color.png
[linux_processes]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/linux_processes.png
