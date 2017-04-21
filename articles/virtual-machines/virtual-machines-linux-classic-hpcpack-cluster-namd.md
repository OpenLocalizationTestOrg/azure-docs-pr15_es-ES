<properties
 pageTitle="NAMD con Microsoft HPC Pack en máquinas virtuales de Linux | Microsoft Azure"
 description="Implementar un clúster de Microsoft HPC Pack en Azure y ejecutar una simulación de NAMD con charmrun en varios nodos de cálculo de Linux"
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
 ms.date="10/13/2016"
 ms.author="danlep"/>

# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Ejecutar NAMD con Microsoft HPC Pack Linux nodos de cálculo en Azure

En este artículo se muestra una forma de ejecutar una carga de trabajo de la informática de alto rendimiento (HPC) de Linux en máquinas virtuales de Windows Azure. Aquí, que configura un clúster de [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) en Azure con los nodos de cálculo Linux y ejecutar una simulación de [NAMD](http://www.ks.uiuc.edu/Research/namd/) para calcular y visualizar la estructura de un sistema de biomoleculares grande.  

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** (para el programa de Nanoscale Molecular Dynamics) es un paquete de dynamics molecular paralelo diseñado para simulación de alto rendimiento de sistemas de grandes biomoleculares que contiene hasta millones de átomos. Algunos ejemplos de estos sistemas virus, estructuras de celda y proteínas grandes. NAMD escala a cientos de núcleos para simulaciones típicas y a más de 500.000 núcleos para las simulaciones mayor.

* **Microsoft HPC Pack** proporciona características para ejecutar HPC a gran escala y aplicaciones paralelas en clústeres de equipos local o máquinas virtuales de Windows Azure. Desarrollado originalmente como una solución para cargas de trabajo de Windows HPC, HPC Pack ahora es compatible con ejecutar Linux HPC aplicaciones en Linux calcular máquinas virtuales de nodo implementadas en un clúster de HPC Pack. Para obtener una introducción, vea [Introducción a Linux nodos de cálculo en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md) .

Para que ver otras opciones ejecutar Linux HPC cargas de trabajo en Azure, consulte [recursos técnicos por lotes y de alto rendimiento informática](../batch/batch-hpc-solutions.md).




## <a name="prerequisites"></a>Requisitos previos

* **Nodos de cálculo de clúster de HPC Pack con Linux** - implementar un clúster de HPC Pack con Linux nodos de cálculo en Azure con una [plantilla de administrador de recursos de Azure](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) o una [secuencia de comandos de PowerShell de Azure](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Para los requisitos previos y los pasos para cualquiera de estas opciones, vea [Introducción a Linux nodos de cálculo en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md) . Si elige la opción de implementación de secuencia de comandos de PowerShell, vea el archivo de configuración de ejemplo en los archivos de ejemplo al final de este artículo. Este archivo configura un clúster basados en Azure HPC Pack que consta de un nodo principal de Windows Server 2012 R2 y cuatro nodos de cálculo grande 6.6 CentOS de tamaño. Personalizar este archivo según sea necesario para su entorno.


* **Archivos de software y tutorial NAMD** : software de descargar NAMD para Linux desde el sitio [NAMD](http://www.ks.uiuc.edu/Research/namd/) (es necesario registrarse). En este artículo se basa en NAMD versión 2.10 y usa el archivo [Linux-x86_64 (64 bits Intel/AMD con Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) . También descargar los [archivos de tutorial NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). La descarga de archivos .tar y necesita una herramienta de Windows para extraer los archivos en el nodo de cabeza. Para extraer los archivos, siga las instrucciones más adelante en este artículo. 

* **VMD** (opcional) - para ver los resultados de su trabajo NAMD, descargue e instale el programa de visualización molecular [VMD](http://www.ks.uiuc.edu/Research/vmd/) en un equipo de su elección. La versión actual es 1.9.2. Vea la VMD Descargar sitio para empezar.  


## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configurar confianza mutua entre nodos de cálculo
Ejecutar un trabajo de nodo entre en varios nodos de Linux requiere los nodos confiar en ellos ( **rsh** o **ssh**). Cuando se crea el clúster HPC Pack con el script de implementación de Microsoft HPC Pack IaaS, la secuencia de comandos configurará automáticamente permanente confianza mutua para la cuenta de administrador que especifique. Para crear en el dominio del clúster de los usuarios no es administrador, debe configurar confianza mutua temporal entre los nodos cuando se les asigna una tarea. A continuación, destroy la relación después de completar el trabajo. Para hacer esto para cada usuario, proporcione un par de claves RSA al clúster que HPC Pack se usa para establecer la relación de confianza. Siguen las instrucciones.

### <a name="generate-an-rsa-key-pair"></a>Generar un par de claves RSA
Es fácil generar un par de claves RSA, que contiene una clave pública y una clave privada, ejecute el comando de **ssh keygen** Linux.

1.  Inicie sesión en un equipo Linux.

2.  Ejecute el siguiente comando:

    ```bash
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] Presione **ENTRAR** para usar la configuración predeterminada hasta que se haya completado el comando. No escriba una frase de contraseña aquí; Cuando se le pide una contraseña, simplemente, presione **ENTRAR**.

    ![Generar un par de claves RSA][keygen]

3.  Cambie al directorio de ~/.ssh. La clave privada se almacena en id_rsa y la clave pública en id_rsa.pub.

    ![Claves públicas y privadas][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Agregar el par de claves al clúster HPC Pack
1.  [Conectarse al escritorio remoto](virtual-machines-windows-connect-logon.md) para el nodo principal VM usando el dominio credenciales siempre cuando se implementa el clúster (por ejemplo, hpc\clusteradmin). Administrar el clúster desde el nodo principal.

2. Use los procedimientos estándar de Windows Server para crear una cuenta de usuario de dominio en el dominio de Active Directory del clúster. Por ejemplo, use la herramienta de usuario de Active Directory y equipos en el nodo principal. Los ejemplos de este artículo se suponen que se crea un usuario de dominio denominado hpcuser en el dominio hpclab (hpclab\hpcuser).

3. Agregar al usuario de dominio para el clúster HPC Pack como un usuario de clúster. Para obtener instrucciones, vea [Agregar o quitar usuarios de clúster](https://technet.microsoft.com/library/ff919330.aspx).

2.  Cree un archivo llamado C:\cred.xml y copie los datos de clave RSA en él. Puede encontrar un ejemplo en los archivos de ejemplo al final de este artículo.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

3.  Abra un símbolo del sistema y escriba el comando siguiente para configurar los datos de credenciales para la cuenta de hpclab\hpcuser. Use el parámetro **extendeddata** para pasar el nombre del archivo C:\cred.xml que creó para los datos de clave.

    ```command
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Este comando se completa correctamente sin salida. Después de establecer las credenciales de las cuentas de usuario que necesita para ejecutar trabajos, almacenar el archivo de cred.xml en una ubicación segura o eliminarlo.

5.  Si genera el par de claves RSA en uno de los nodos de Linux, no olvide eliminar las claves cuando termine de usarlos. Paquete de HPC no configurar confianza mutua si encuentra un archivo de id_rsa existente o id_rsa.pub.

>[AZURE.IMPORTANT] No se recomienda ejecutar un trabajo de Linux como administrador de clústeres en un clúster compartido, como un trabajo enviado por un administrador se ejecuta en la cuenta raíz en los nodos de Linux. Un trabajo enviado por un usuario que no es administrador se ejecuta en una cuenta de usuario de Linux local con el mismo nombre que el usuario de trabajo. En este caso, HPC Pack configurará mutua confianza para este usuario Linux en todos los nodos asignados a la tarea. Puede configurar el usuario Linux manualmente en los nodos de Linux antes de ejecutar el trabajo o HPC Pack crea el usuario automáticamente cuando se envía el trabajo. Si el paquete de HPC crea el usuario, HPC Pack elimina después de que finalice el trabajo. Para reducir la amenaza de seguridad, se quitan las teclas después de que finalice el trabajo en los nodos.

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configurar un recurso compartido de archivos para los nodos de Linux

Ahora configurar un recurso compartido de archivos SMB y montaje de la carpeta compartida en todos los nodos de Linux para permitir que los nodos de Linux tener acceso a archivos NAMD con una ruta de acceso común. Siguientes son los pasos para montaje de una carpeta compartida en el nodo principal. Se recomienda un recurso compartido de distribuciones como CentOS 6.6 que actualmente no son compatibles con el servicio de archivos de Azure. Si los nodos de Linux admiten un recurso compartido de archivos de Azure, consulte [cómo usar el almacenamiento de archivos de Azure con Linux](../storage/storage-how-to-use-files-linux.md). Para archivo adicional opciones de uso compartido con HPC Pack, vea [Introducción a Linux nodos de cálculo en un clúster de paquete HPC en Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

1.  Crear una carpeta en el nodo principal y compartirlo a todos los usuarios estableciendo privilegios de lectura y escritura. En este ejemplo, \\ \\CentOS66HN\Namd es el nombre de la carpeta, donde CentOS66HN es el nombre de host del nodo principal.

2. Cree una subcarpeta llamada namd2 en la carpeta compartida. En namd2, cree otra subcarpeta denominada namdsample.

3. Extraer los archivos NAMD en la carpeta con una versión de Windows de **alquitrán** u otra utilidad de Windows que opera en archivos .tar. 
    * Extraer el archivo tar NAMD a \\ \\CentOS66HN\Namd\namd2.
    
    * Extraer los archivos de tutorial en \\ \\CentOS66HN\Namd\namd2\namdsample.

4. Abra una ventana de Windows PowerShell y ejecute los comandos siguientes a la carpeta compartida de montaje en los nodos de Linux.

    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2

    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

El primer comando crea una carpeta denominada /namd2 en todos los nodos en el grupo LinuxNodes. El segundo comando montajes la //CentOS66HN/Namd/namd2 carpeta compartida en la carpeta con dir_mode y file_mode bits establecen en 777. El *nombre de usuario* y la *contraseña* en el comando debería las credenciales de un usuario en el nodo principal.

>[AZURE.NOTE]La "\`" symbol en el segundo comando es un símbolo de escape para PowerShell. "\`," significa "," (coma) es una parte del comando.


## <a name="create-a-bash-script-to-run-a-namd-job"></a>Crear un script de fiesta para ejecutar un trabajo NAMD

Su trabajo NAMD necesita un archivo de la *lista de nodos* para **charmrun** determinar el número de nodos para usar al iniciar procesos NAMD. Use una secuencia de comandos de fiesta que genera el archivo de la lista de nodos y ejecuta **charmrun** con este archivo de la lista de nodos. A continuación, puede enviar un trabajo NAMD en el administrador del clúster de HPC que llama esta secuencia de comandos.

Con un editor de texto de su elección, crear un script de fiesta en la carpeta /namd2 que contiene los archivos de programa NAMD y asígnele el nombre hpccharmrun.sh. Para una prueba rápida de concepto, copie la secuencia de comandos de ejemplo hpccharmrun.sh proporcionada al final de este artículo y vaya a [Enviar un trabajo NAMD](#submit-a-namd-job).

>[AZURE.TIP] Guardar la secuencia de comandos como un archivo de texto con Linux finales (sólo avance de línea, no rc al) de línea. Esto garantiza que se ejecuta correctamente en los nodos de Linux.

Continuación encontrará detalles sobre lo que hace esta secuencia de comandos de fiesta. 

1.  Definir algunas variables.

    ```bash
    #!/bin/bash

    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    # Charmrun command
    CHARMRUN=${SCRIPT_PATH}/charmrun
    # Argument of ++nodelist
    NODELIST_OPT="++nodelist"
    # Argument of ++p
    NUMPROCESS="+p"
    ```

2.  Obtener información de nodo de las variables de entorno. $NODESCORES almacena una lista de palabras dividido en CCP_NODES_CORES $. $COUNT es el tamaño de $NODESCORES.
    ```
    # Get node information from the environment variables
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    ```    
    
    El formato de la variable CCP_NODES_CORES $ es el siguiente:

    ```
    <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
    ```

    Esta variable muestra el número total de nodos, nombres de nodo y número de núcleos en cada nodo que están asignados a la tarea. Por ejemplo, si el trabajo debe 10 núcleos para ejecutar, el valor de $CCP_NODES_CORES es similar a:

    ```
    3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
    ```
        
3.  Si no se establece la variable CCP_NODES_CORES $, iniciar **charmrun** directamente. (Esto solo debe ocurrir al ejecutar esta secuencia de comandos directamente en los nodos de Linux.)

    ```
    if [ ${COUNT} -eq 0 ]
    then
        # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
        #echo ${CHARMRUN} $*
        ${CHARMRUN} $*
    ```

4.  O cree un archivo de la lista de nodos para **charmrun**.

    ```
    else
        # Create the nodelist file
        NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

        # Write the head line
        echo "group main" > ${NODELIST_PATH}

        # Get every node name and number of cores and write into the nodelist file
        I=1
        while [ ${I} -lt ${COUNT} ]
        do
            echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
            let "I=${I}+2"
        done
```
5.  Ejecutar **charmrun** con el archivo de la lista de nodos, obtener su estado de devolución y quitar el archivo de la lista de nodos al final.

    ${CCP_NUMCPUS} es otra variable de entorno establecida por el nodo principal de HPC Pack. Almacena el número de núcleos total asignados a este trabajo. Para usarlo para especificar el número de procesos de charmrun.

    ```
    # Run charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
    fi

    ```
6.  Salir con el estado de retorno **charmrun** .

    ```
    exit ${RTNSTS}
    ```



A continuación se muestra la información en el archivo de la lista de nodos, que genera la secuencia de comandos:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Por ejemplo:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>Enviar un trabajo NAMD

Ahora está listo para enviar un trabajo NAMD en el Administrador de clúster de HPC.

1.  Conectar con el nodo de clúster principal e inicie el Administrador de clúster de HPC.

2.  En **Administración de recursos**, asegúrese de que los nodos de cálculo Linux están en el estado **en línea** . Si no lo están, selecciónelas y haga clic en **Conectar**.

2.  En **Administración del trabajo**, haga clic en **Nueva tarea**.

3.  Escriba un nombre para el trabajo como *hpccharmrun*.

    ![Nuevo trabajo HPC][namd_job]

4.  En la página de **Detalles del trabajo** , en **Los recursos de trabajo**, seleccione el tipo de recurso como **nodo** y establecer el **valor mínimo** de 3. , se ejecuta el trabajo en tres nodos de Linux y cada nodo tiene cuatro núcleos.

    ![Recursos de trabajo][job_resources]

5. Haga clic en **Editar tareas** en el panel de navegación izquierdo y, a continuación, haga clic en **Agregar** para agregar una tarea a la tarea.    


6. En la página **Detalles de la tarea y la redirección de i/OS** , establezca los siguientes valores:

    * **Línea de comandos** -
`/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`

        >[AZURE.TIP] La línea de comandos anterior es un comando único sin saltos de línea. Ajusta para que aparezca en varias líneas en **línea de comandos**.

    * **Directorio de trabajo** - /namd2

    * **Mínimo** - 3

    ![Detalles de la tarea][task_details]

    >[AZURE.NOTE] Establecer el directorio de trabajo aquí porque **charmrun** intenta navegar hasta el mismo directorio de trabajo en cada nodo. Si el directorio de trabajo no está configurado, HPC Pack inicia el comando en una carpeta con nombre aleatorio creada en uno de los nodos de Linux. Esto provoca el siguiente error en el resto de los nodos: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` para evitar este problema, especifique una ruta de acceso que puede tener acceso a todos los nodos como el directorio de trabajo.

5.  Haga clic en **Aceptar** y, a continuación, haga clic en **Enviar** para ejecutar este trabajo.

    De forma predeterminada, el paquete de HPC envía el trabajo como su cuenta de usuario de inicio de sesión actual. Un cuadro de diálogo posible pida que introduzca el nombre de usuario y contraseña después de hacer clic en **Enviar**.

    ![Credenciales de trabajo][creds]

    En algunas condiciones, HPC Pack recuerda la información de usuario antes de entrada y no mostrar este cuadro de diálogo. Para hacer HPC Pack volver a mostrar, escriba el siguiente comando en un símbolo del sistema y, a continuación, enviar el trabajo.

    ```command
    hpccred delcreds
    ```

6.  El trabajo tarda varios minutos en Finalizar.

7.  Busque el registro de trabajo en \\ <headnodeName>\Namd\namd2\namd2_hpccharmrun.log y los archivos de salida en \\ <headnodeName>\Namd\namd2\namdsample\1-2-sphere\.

8.  Opcionalmente, iniciar VMD para ver los resultados de la tarea. Los pasos para visualizar la NAMD de salida de archivos (en este caso, un ubiquitin proteínas molécula en un ámbito de agua) quedan fuera del ámbito de este artículo. Para obtener información detallada, vea [Tutorial NAMD](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) .

    ![Resultados de la tarea][vmd_view]

## <a name="sample-files"></a>Archivos de ejemplo

### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Archivo de configuración XML de ejemplo para una implementación de clúster mediante script de PowerShell

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
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

### <a name="sample-hpccharmrunsh-script"></a>Secuencia de comandos de ejemplo hpccharmrun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keys.png
[namd_job]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/namd_job.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/job_resources.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/creds.png
[task_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/task_details.png
[vmd_view]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/vmd_view.png
