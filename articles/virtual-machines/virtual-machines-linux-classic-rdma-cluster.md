<properties
 pageTitle="Clúster de Linux RDMA para ejecutar aplicaciones de MPI | Microsoft Azure"
 description="Crear un clúster de Linux de tamaño H16r, H16mr, A8 o máquinas virtuales A9 usar la red de Azure RDMA para ejecutar aplicaciones MPI"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Configurar un clúster de Linux RDMA para ejecutar aplicaciones de MPI


Obtenga información sobre cómo configurar un clúster de Linux RDMA en Azure con [máquinas virtuales de una serie de serie de H o de cálculo](virtual-machines-linux-a8-a9-a10-a11-specs.md) para ejecutar aplicaciones de interfaz de paso de mensajes (MPI) paralelas. Este artículo proporciona los pasos necesarios para preparar una imagen de Linux HPC ejecuten MPI Intel en un clúster. A continuación, implementar un clúster de máquinas virtuales usando esta imagen y uno de los tamaños de VM RDMA capaz de Azure (actualmente H16r, H16mr, A8 o A9). Usar el clúster para ejecutar aplicaciones de MPI comunican de forma eficaz sobre una latencia baja, red de alto rendimiento basado en remoto acceso directo a memoria tecnología (RDMA).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="cluster-deployment-options"></a>Opciones de implementación de clúster

Siguientes son métodos que puede utilizar para crear un clúster de Linux RDMA con o sin un programador de tareas.


* **Las secuencias de comandos de azure CLI** - tal como se muestra más adelante en este artículo, use la [interfaz de línea de comandos de Azure](../xplat-cli-install.md) para la implementación de un clúster de VM RDMA capaz de secuencias de comandos. Crea la CLI en modo de administración de servicios de los nodos de clúster en serie en el modelo de implementación clásica, para implementar muchos de los nodos de cálculo puede tardar varios minutos. Para habilitar la conexión de red RDMA cuando se utiliza el modelo de implementación clásica, implementar la VM en el mismo servicio de nube.

* **Plantillas de administrador de recursos de azure** - también puede usar el modelo de implementación de administrador de recursos para implementar un clúster de VM RDMA capaz de que se conecta a la red RDMA. Puede [crear su propia plantilla](../resource-group-authoring-templates.md), o consulte el [Tutorial de Azure plantillas](https://azure.microsoft.com/documentation/templates/) para plantillas proporcionadas por Microsoft o de la Comunidad para implementar la solución que desee. Plantillas de administrador de recursos pueden proporciona una manera rápida y confiable para implementar un clúster de Linux. Para habilitar la conexión de red RDMA cuando se usa el modelo de implementación de administrador de recursos, implementar la VM en el mismo conjunto de disponibilidad.

* **Paquete de HPC** - crear un clúster de Microsoft HPC Pack en Azure y agregar nodos RDMA capaz de cálculo que ejecutan una distribución de Linux compatible para tener acceso a la red RDMA. Consulte [Introducción a Linux nodos de cálculo en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-classic-model"></a>Pasos de implementación de ejemplo de modelo clásico

Los siguientes pasos muestran cómo utilizar la CLI Azure implementar una máquina virtual SUSE Linux Enterprise Server (SLES) 12 HPC de SP1 de Azure Marketplace, personalizarla y crear una imagen de máquina virtual personalizada. A continuación, usar la imagen a la implementación de un clúster de VM RDMA capaz de secuencias de comandos. 

>[AZURE.TIP]Siga los mismos pasos para implementar un clúster de VM RDMA capaz basadas en otras imágenes HPC compatibles en Azure Marketplace. Algunos pasos pueden diferir ligeramente, tal como se indica. Por ejemplo, Intel MPI se incluyen y configurado en solo algunas de estas imágenes. Y si implementa una máquina virtual HPC de 12 SLES en lugar de una máquina virtual HPC de SLES 12 SP1, debe actualizar los controladores RDMA. Para obtener más información, consulte [acerca de la A8, A9, A10 y A11 instancias de descarga intensiva](virtual-machines-linux-a8-a9-a10-a11-specs.md#rdma-driver-updates-for-sles-12).


### <a name="prerequisites"></a>Requisitos previos

*   **Equipo cliente** - necesita un Mac, Linux o cliente basado en Windows equipo para comunicarse con Azure. El procedimiento siguiente asume que está usando a un cliente Linux.

*   **Azure suscripción** : si no tiene una suscripción, puede crear un [libre cuenta](https://azure.microsoft.com/free/) de dos minutos. Para clústeres más grandes, considere la posibilidad de una suscripción de pago u otras opciones de compra. 

*   **Disponibilidad VM tamaño** - actualmente los siguientes tamaños de instancia son capaces de RDMA: H16r, H16mr, A8 y A9. Busque [productos disponibles por región](https://azure.microsoft.com/regions/services/) disponibilidad en áreas de Azure. 

*   **Cuota de núcleos** - es posible que necesite aumentar la cuota de núcleos para implementar un clúster de cálculo intensivo VM. Por ejemplo, necesita al menos 128 núcleos si desea implementar máquinas virtuales de A9 8 tal como se muestra en este artículo. La suscripción también podría limitar el número de núcleos que puede implementar en ciertas familias de tamaño de la máquina virtual, incluyendo la serie de H. Para solicitar una cuota aumentar, [Abra una solicitud de soporte técnico en línea para clientes](../azure-supportability/how-to-create-azure-support-request.md) sin cargo. 

*   **Azure CLI** - [instalar](../xplat-cli-install.md) CLI Azure y [conectarse a su suscripción de Azure](../xplat-cli-connect.md) desde el equipo cliente.


### <a name="step-1-provision-a-sles-12-sp1-hpc-vm"></a>Paso 1. Proporciona una máquina virtual HPC de SLES SP1 12

Después de iniciar sesión Azure con CLI Azure, ejecutar `azure config list` para confirmar que la salida muestra el modo de administración de servicios. Si no es así, establezca el modo al ejecutar este comando:


    azure config mode asm


Escriba lo siguiente para obtener una lista de todas las suscripciones que está autorizados a utilizar:


    azure account list

La suscripción activa actual se identifica con `Current` establecido en `true`. Si esta suscripción no sea el que desea usar para crear el clúster, establecer el identificador de la suscripción correspondiente como la suscripción activa:

    azure account set <subscription-Id>

Para ver las imágenes de SLES 12 SP1 HPC disponibles públicamente en Azure, ejecutar un comando similar al siguiente, suponiendo que el de es compatible con entorno de shell **grep**:


    azure vm image list | grep "suse.*hpc"

Aprovisionar ahora una máquina virtual RDMA capaz con una imagen SLES 12 SP1 HPC ejecutando un comando similar al siguiente:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

donde

* El tamaño (A9 en este ejemplo) es uno de los tamaños VM RDMA capaz.

* El número de puerto externo SSH (22 en este ejemplo, que es el valor predeterminado SSH) es un número de puerto válido. El número de puerto interno SSH se establece en 22.

* Se crea un nuevo servicio de nube en la región de Azure especificada por la ubicación. Especifique una ubicación en la que está disponible el tamaño de la memoria virtual que elija.

* El nombre de la imagen SLES 12 SP1 actualmente puede ser `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824` o `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824` asistencia de prioridad SUSE (aplicar cargos adicionales).

### <a name="step-2-customize-the-vm"></a>Paso 2. Personalizar la máquina virtual

Después de la máquina virtual completa aprovisionamiento SSH VM usando la dirección IP externa de la máquina virtual (o nombre DNS) y el puerto externo número que se configura y personalícelo. Para obtener más información de conexión, vea [cómo iniciar sesión en la máquina Virtual ejecuta Linux](virtual-machines-linux-mac-create-ssh-keys.md). Realizar los comandos que el usuario configurado en la máquina virtual, a menos que se requiere acceso raíz para completar un paso.

>[AZURE.IMPORTANT]Microsoft Azure no proporciona acceso a la raíz en máquinas virtuales de Linux. Para obtener acceso administrativo cuando conectado como un usuario de la máquina virtual, ejecute los comandos con `sudo`.

* **Actualizaciones** : instalar actualizaciones mediante **zypper**. También puede instalar utilidades NFS. 

    >[AZURE.IMPORTANT]En una VM de HPC SLES 12 SP1, le recomendamos que no aplicar actualizaciones del núcleo, lo que pueden causar problemas con la RDMA Linux controladores.

* **MPI Intel** - completar la instalación de MPI Intel en la máquina virtual HPC de SLES 12 SP1 ejecutando el siguiente comando:

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

* **Memoria de bloqueo** : códigos de MPI para bloquear la memoria disponible para RDMA, agregar o cambiar los valores siguientes en el archivo /etc/security/limits.conf. (Necesita acceso a editar este archivo raíz). 

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

    >[AZURE.NOTE]Con fines de pruebas, también puede establecer memlock ilimitada. Por ejemplo: `<User or group name>    hard    memlock unlimited`. Para obtener más información, consulte [Métodos mejor conocidos para la configuración de tamaño de memoria bloqueada](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).

* **Teclas SSH para SLES VM** - generar SSH claves para establecer la confianza para su cuenta de usuario entre los nodos de cálculo en el clúster SLES cuando se ejecutan trabajos MPI. (Si implementa una VM HPC basado en CentOS, no siga este paso. Consulte las instrucciones más adelante en el artículo configurar passwordless confianza SSH entre los nodos de clúster después de capturar la imagen e implementar el clúster). 

    Ejecute el comando siguiente para crear claves SSH. Cuando se le solicite la entrada, presione ENTRAR para generar las teclas en la ubicación predeterminada sin configurar una frase de contraseña.

        ssh-keygen

    Anexar la clave pública al archivo authorized_keys para las claves públicas conocidas.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    En el directorio ~/.ssh, editar o crear el archivo de "configuración". Proporcione el intervalo de direcciones IP de la red privada que va a utilizar en Azure (10.32.0.0/16 en este ejemplo):

        host 10.32.0.*
        StrictHostKeyChecking no

    Como alternativa, de la lista la dirección IP de una red privada de cada VM en el clúster como sigue:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]Configurar `StrictHostKeyChecking no` puede crear un riesgo de seguridad cuando no se especifica una dirección IP específica o un rango.

* **Aplicaciones** - instalar las aplicaciones que necesita en esta máquina virtual o realiza otras personalizaciones para capturar la imagen.

### <a name="step-3-capture-the-image"></a>Paso 3. Capturar la imagen

Para capturar la imagen, en primer lugar, ejecute el comando siguiente en la VM Linux. Este comando desactiva la máquina virtual, pero mantiene las cuentas de usuario y las claves SSH que haya configurado.

```
sudo waagent -deprovision
```

A continuación, en el equipo cliente, ejecute los siguientes comandos de Azure CLI para capturar la imagen. Para obtener más información, consulte [cómo capturar una máquina virtual de Linux clásica como una imagen](virtual-machines-linux-classic-capture-image.md) .  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Después de ejecutar estos comandos, se genera la imagen de máquina virtual para su uso y se elimina la máquina virtual. Ahora tiene la imagen personalizada lista para implementar un clúster.

### <a name="step-4-deploy-a-cluster-with-the-image"></a>Paso 4. Implementar un clúster de la imagen

Modificar la siguiente secuencia de comandos de fiesta con los valores apropiados para su entorno y ejecutar desde el equipo cliente. Porque Azure implementa las máquinas virtuales en serie en el modelo de implementación clásico, se necesitan unos minutos para implementar la VM A9 8 sugeridos en esta secuencia de comandos.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Consideraciones para un clúster HPC CentOS

Si desea configurar un clúster basado en una de las imágenes en función de CentOS HPC en el catálogo de soluciones de Azure en lugar de 12 SLES para HPC, siga los pasos generales en la sección anterior. Al aprovisionar y configurar la máquina virtual, tenga en cuenta las siguientes diferencias:

1. Intel MPI ya está instalado en una máquina virtual se aprovisione desde una imagen basada en CentOS HPC. 

2. Configuración de la memoria de bloqueo ya se agrega en el archivo de /etc/security/limits.conf de la máquina virtual.

2. No se generar claves SSH en la máquina virtual que aprovisiona para capturar. En su lugar, se recomienda la configuración de autenticación de usuario después de implementar un clúster de la. Vea la sección siguiente.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Configurar passwordless confianza SSH en el clúster

En un clúster basado en CentOS HPC, hay dos métodos para establecer la confianza entre los nodos de cálculo: autenticación basada en el usuario y la autenticación basada en el host. Autenticación basada en el host está fuera del ámbito de este artículo y, en general, debe realizarse a través de una secuencia de comandos de extensión durante la implementación. Autenticación de usuario es útil para establecer la confianza después de la implementación y requiere la generación y el uso compartido de claves SSH entre los nodos de cálculo en el clúster. Este método se conoce como inicio de sesión SSH passwordless y es necesario cuando se ejecutan trabajos MPI. 

Una secuencia de comandos de ejemplo que ha contribuido de la Comunidad está disponible en [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) para habilitar la autenticación de usuario sencilla en un clúster basado en CentOS HPC. Descargar y usar esta secuencia de comandos con los pasos siguientes. También puede modificar esta secuencia de comandos o utilizar cualquier otro método para establecer la autenticación SSH passwordless entre los nodos de clúster de cálculo.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh
    
Para ejecutar la secuencia de comandos, debe saber el prefijo para las direcciones IP de subred. Obtener el prefijo ejecutando el siguiente comando en uno de los nodos de clúster. El resultado tendrá un aspecto similar 10.1.3.5 y, a continuación, el prefijo es la 10.1.3 parte.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Ejecutar ahora la secuencia de comandos con tres parámetros: el nombre de usuario comunes en los nodos de cálculo, la contraseña comunes para ese usuario en los nodos de cálculo y a continuación, el prefijo de subred que se devolvió desde el comando anterior.


    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Esta secuencia de comandos hace lo siguiente:

* Crea un directorio en el nodo de host denominado .ssh, que es necesario para el inicio de sesión passwordless. 
* Crea un archivo de configuración en el directorio .ssh que indica que el inicio de sesión passwordless para permitir inicio de sesión desde cualquier nodo en el clúster. 
* Crea los archivos que contiene los nombres de nodo y las direcciones IP de nodo para todos los nodos en el clúster. Estos archivos quedan después de ejecuta la secuencia de comandos para consultarlos más adelante. 
* Crea un par de claves público y privado para cada nodo de clúster incluido el nodo de host y las entradas en el archivo authorized_keys.

>[AZURE.WARNING]Ejecute esta secuencia de comandos puede crear un riesgo de seguridad. Asegúrese de que no se distribuye la información de clave pública en ~/.ssh.


## <a name="configure-intel-mpi"></a>Configurar Intel MPI

Para ejecutar aplicaciones de MPI en Azure Linux RDMA, debe configurar ciertas variables de entorno específicas de Intel MPI. Aquí es una fiesta de secuencia de comandos para configurar las variables necesarias para ejecutar una aplicación. Cambiar la ruta de acceso a mpivars.sh según sea necesario para la instalación de Intel MPI.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

El formato del archivo host es la siguiente. Agregar una línea para cada nodo en el clúster. Especificar las direcciones IP privadas desde el VNet definen anteriormente, no los nombres DNS. Por ejemplo, para dos hosts con direcciones IP 10.32.0.1 y 10.32.0.2 el archivo contiene lo siguiente:

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Ejecutar MPI en un clúster de dos nodos básico

Si aún no lo ha hecho, configure en primer lugar el entorno de Intel MPI. 

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-a-simple-mpi-command"></a>Ejecutar un comando MPI simple

Ejecutar un comando MPI simple en uno de los nodos de cálculo para mostrar que MPI está instalado correctamente y que puede comunicarse entre al menos que dos nodos de cálculo. El siguiente comando **mpirun** ejecuta el comando **hostname** en dos nodos.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
El resultado debe enumerar los nombres de todos los nodos que pasa como entrada para `-hosts`. Por ejemplo, un comando **mpirun** con dos nodos devuelve resultados similares a los siguientes:

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Ejecutar una comparativa MPI

El siguiente comando MPI Intel ejecuta una prueba comparativa de pingpong para comprobar la configuración de clúster y la conexión a la red RDMA.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

En un clúster de trabajo con dos nodos, verá un resultado similar al siguiente. En la red de Azure RDMA, espere hasta 512 bytes de tamaños de latencia en o por debajo de 3 microsegundos para el mensaje.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>Pasos siguientes

* Pruebe a implementar y ejecutar su MPI Linux aplicaciones en el clúster de Linux.

* Consulte la [documentación de la biblioteca de MPI Intel](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) para obtener instrucciones sobre MPI Intel.

* Pruebe una [plantilla de tutorial rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) para crear un clúster de Intel Lustre mediante una imagen basada en CentOS HPC. Para obtener información detallada, consulte esta [entrada de blog](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/).
