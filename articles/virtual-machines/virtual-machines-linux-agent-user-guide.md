<properties 
    pageTitle="Guía de usuario de Linux agente | Microsoft Azure" 
    description="Obtenga información sobre cómo instalar y configurar el agente de Linux (waagent) para administrar la interacción de su máquina virtual con controlador de tela de Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>



# <a name="azure-linux-agent-user-guide"></a>Guía de usuario de Azure agente Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="introduction"></a>Introducción

Agente de Linux (waagent) de Microsoft Azure administra Linux y FreeBSD aprovisionamiento e interacción VM con el controlador de tela de Azure. Proporciona las siguientes funciones para Linux y FreeBSD IaaS implementaciones:

> [AZURE.NOTE] Vea al agente de Azure Linux [Léame](https://github.com/Azure/WALinuxAgent/blob/master/README.md) para obtener más información.

* **Aprovisionamiento de imagen**
  - Creación de una cuenta de usuario
  - Configuración de tipos de autenticación SSH
  - Implementación de SSH las claves públicas y pares de claves
  - Establecer el nombre de host
  - El nombre de host de publicación a la plataforma de DNS
  - Creación de informes SSH host huella digital de clave a la plataforma
  - Administración de recursos de disco
  - Formato y montaje el disco del recurso
  - Configurar el espacio de intercambio

* **Redes**
  - Administra las rutas para mejorar la compatibilidad con servidores de plataforma de DHCP
  - Garantiza la estabilidad del nombre de la interfaz de red

* **Núcleo**
  - Configura NUMA virtual (deshabilitar para kernel < 2.6.37)
  - Consume entropía Hyper-V para/dev/Random
  - Configura el tiempo de espera de SCSI para el dispositivo de raíz (que puede ser remoto)

* **Diagnósticos**
  - Redirección de la consola al puerto serie

* **Implementaciones de SCVMM**
    - Detecta y ejecuta un bootstrap al agente VMM para Linux cuando se ejecuta en un entorno de administrador de la máquina Virtual de System Center 2012 R2

* **Extensión VM**
    - Insertar componente creado por Microsoft y socios a Linux VM (IaaS) para habilitar software y automatización de configuración
    - Implementación de referencia de extensión VM en [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)


## <a name="communication"></a>Comunicación
El flujo de información de la plataforma al agente se produce a través de dos canales:

* Un tiempo de arranque adjunta DVD para implementaciones de IaaS. Este DVD incluye un archivo de configuración compatible con OVF que incluye toda la información de aprovisionamiento distintos de los pares de claves SSH real.
* Un extremo TCP exponer una API de REST se utiliza para obtener la implementación y configuración de la topología.


## <a name="requirements"></a>Requisitos
Los siguientes sistemas se han probado y funcionan con el agente de Azure Linux:

> [AZURE.NOTE] Esta lista puede diferir de la lista oficial de sistemas admitidos en la plataforma de Microsoft Azure, como se describe a continuación: [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)

* CoreOS
* CentOS 6.3 +
* Rojos sombrero Enterprise Linux 6,7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Otros sistemas compatibles:

* FreeBSD 10 + (agente de Azure Linux v2.0.10 +)

El agente de Linux depende de algunos paquetes de sistema para funcionar correctamente:

* Python 2.6 +
* OpenSSL 1.0 +
* OpenSSH 5.3 +
* Utilidades de sistema de archivos: sfdisk, fdisk, mkfs, divididas
* Herramientas de contraseña: chpasswd, sudo
* Herramientas de procesamiento de texto: sed, grep
* Herramientas de red: ruta ip
* Núcleo de soporte técnico para montaje File Systems UDF.

## <a name="installation"></a>Instalación
Utilizar un RPM o un paquete de deuda de repositorio de paquete de la distribución de instalación es el método preferido de instalar y actualizar al agente de Azure Linux. Todo el [respaldo de proveedores de distribución](virtual-machines-linux-endorsed-distros.md) integrar el paquete de agente de Azure Linux en sus imágenes y repositorios.

Consulte la documentación en el [agente de Azure Linux repo en Github](https://github.com/Azure/WALinuxAgent) para opciones de instalación avanzada, como la instalación de origen o a ubicaciones personalizados o prefijos.


## <a name="command-line-options"></a>Opciones de línea de comandos

### <a name="flags"></a>Indicadores

- detallado: aumentar nivel de detalle de comando especificado
- forzar: Omitir confirmación interactiva para algunos comandos

### <a name="commands"></a>Comandos

- Ayuda: se enumeran los comandos admitidos y los indicadores.

- deprovision: intentar eliminar del sistema y sea adecuado para su configuración. Esta operación elimina la siguiente:
 * Todas las claves de host SSH (si Provisioning.RegenerateSshHostKeyPair es 'y' en el archivo de configuración)
 * Configuración del servidor de nombres en /etc/resolv.conf
 * Contraseña de raíz de/etc/shadow (si Provisioning.DeleteRootPassword es 'y' en el archivo de configuración)
 * Cliente concesiones en caché
 * Nombre de host se restablecerán localhost.localdomain


> [AZURE.WARNING] Baja no garantiza que la imagen está desactivada de toda la información confidencial y adecuado para su redistribución.


- deprovision + usuario: realiza todo bajo - deprovision (arriba) y también se elimina la última cuenta de usuario de preparación (que se obtienen de /var/lib/waagent) y datos asociados. Este parámetro es cuando anule el aprovisionamiento de una imagen que anteriormente se aprovisionamiento en Azure, por lo que puede capturar y volver a utilizar.

- versión: muestra la versión de waagent

- serialconsole: configura GRUB para marcar ttyS0 (el primer puerto serie) como la consola de inicio. Esto garantiza que los registros de inicio del núcleo se envían al puerto serie y disponible para depuración.

- daemon: ejecutar waagent como un daemon para administrar la interacción con la plataforma. Este argumento se especifica waagent en la secuencia de comandos de inicialización waagent.

- inicio: ejecutar waagent como un proceso en segundo plano


## <a name="configuration"></a>Configuración

Un archivo de configuración (/ etc/waagent.conf) controla las acciones de waagent. A continuación se muestra un archivo de configuración de ejemplo:

    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None

Las opciones de configuración se describen en detalle a continuación. Opciones de configuración son de tres tipos; Valor booleano, cadena o entero. Las opciones de configuración de tipo Boolean pueden especificarse como "y" o "n". La palabra clave especial "Ninguno" puede usarse para algunas entradas de configuración de tipo de cadena como se explica a continuación.

**Provisioning.Enabled:**  
Tipo: booleano  
Predeterminado: y

Esto permite al usuario habilitar o deshabilitar la funcionalidad de aprovisionamiento en el agente. Los valores válidos son "y" o "n". Si se deshabilita el aprovisionamiento, se mantienen las claves de usuario y host SSH en la imagen y se omite cualquier configuración especificada en la API de aprovisionamiento de Azure.

> [AZURE.NOTE] La `Provisioning.Enabled` parámetro el valor predeterminado es "n" en las imágenes de nube de Ubuntu que usan nube al inicio de aprovisionamiento.

  
**Provisioning.DeleteRootPassword:**  
Tipo: booleano  
Predeterminado: n

Si se eliminará de conjunto, la contraseña de raíz en el archivo de sombra/etcetera/durante el proceso de aprovisionamiento.


**Provisioning.RegenerateSshHostKeyPair:**  
Tipo: booleano  
Predeterminado: y

Si se elimina conjunto, todos los SSH host pares de claves (ecdsa, dsa y rsa) durante el proceso de aprovisionamiento de/etc./ssh /. Y se genera un único par de claves actualizado.

El tipo de cifrado para el nuevo par de claves es configurable por la entrada Provisioning.SshHostKeyPairType. Tenga en cuenta que algunas distribuciones volver a crear pares de claves SSH para cualquier tipo de cifrado que faltan al reinicia el daemon SSH (por ejemplo, al reiniciar).


**Provisioning.SshHostKeyPairType:**  
Tipo: cadena  
Predeterminado: rsa

Esto se puede establecer en un tipo de algoritmo de cifrado es compatible con el daemon SSH en la máquina virtual. Los valores normalmente compatibles son "rsa", "dsa" y "ecdsa". Tenga en cuenta que "putty.exe" en Windows no admite "ecdsa". Por lo tanto, si desea usar putty.exe en Windows para conectarse a una implementación de Linux, utilice "rsa" o "dsa".


**Provisioning.MonitorHostName:**  
Tipo: booleano  
Predeterminado: y

Si se establece, waagent se supervisar la máquina virtual de Linux para los cambios de nombre de host (tal como lo devuelve el comando "hostname") y actualizar automáticamente la configuración de red en la imagen para reflejar el cambio. Para poder insertar el cambio de nombre a los servidores DNS, las redes se iniciará en la máquina virtual. El resultado será en resumen pérdida de conectividad a Internet.


**Provisioning.DecodeCustomData**  
Tipo: booleano  
Predeterminado: n

Si se establece, waagent se descodificar CustomData de base 64.


**Provisioning.ExecuteCustomData**  
Tipo: booleano  
Predeterminado: n

Si establece, waagent ejecutará CustomData después de aprovisionamiento.


**Provisioning.PasswordCryptId**  
Cadena de tipo:  
6: predeterminado

Algoritmo de cifrado utilizado al generar hash de la contraseña.  
 1 - MD5  
 2a - Blowfish  
 5 - SHA-256  
 6 - SHA-512  


**Provisioning.PasswordCryptSaltLength**  
Cadena de tipo:  
Valor predeterminado: 10

Longitud del valor salt aleatorio que utiliza al generar hash de contraseña.


**ResourceDisk.Format:**  
Tipo: booleano  
Predeterminado: y

Si establece, el disco de recursos proporcionado por la plataforma será formateado y montaje por waagent si el tipo de sistema de archivos solicitado por el usuario en "ResourceDisk.Filesystem" es distinto de "ntfs". Una sola partición de tipo Linux (83) estará disponible en el disco. Tenga en cuenta que esta partición no estarán si puede instalarse correctamente.


**ResourceDisk.Filesystem:**  
Tipo: cadena  
Predeterminado: ext4

Especifica el tipo de sistema de archivos para el disco del recurso. Valores admitidos varían en función de la distribución de Linux. Si la cadena es X, a continuación, mkfs. X debe estar en la imagen de Linux. Imágenes de 11 SLES suele usar 'ext3'. Imágenes de FreeBSD deben utilizar 'ufs2' aquí.


**ResourceDisk.MountPoint:**  
Tipo: cadena  
Predeterminado: recurso/mnt / 

Especifica la ruta de acceso a la que se montaje el disco del recurso. Observe que el disco del recurso es un disco *temporal* y puede ser vaciado cuando la máquina virtual quedará con aprovisionamiento anulada.


**ResourceDisk.MountOptions**  
Tipo: cadena  
Predeterminado: ninguno

Especifica las opciones de montaje de disco que se pasan al comando de montaje -o. Esta es una lista separada por comas de valores, ex. 'nodev nosuid'. Consulte mount(8) para obtener más detalles.


**ResourceDisk.EnableSwap:**  
Tipo: booleano  
Predeterminado: n

Si establece un archivo de intercambio (o archivo de intercambio) en el disco del recurso y se agrega el espacio de intercambio de sistema.


**ResourceDisk.SwapSizeMB:**  
Tipo: entero  
Valor predeterminado: 0

El tamaño del archivo de intercambio en megabytes.


**Logs.Verbose:**  
Tipo: booleano  
Predeterminado: n

Si aumenta el conjunto de nivel de detalle de registro. Waagent registra a /var/log/waagent.log y aprovecha la funcionalidad de logrotate del sistema para girar registros.


**SISTEMA OPERATIVO. EnableRDMA**  
Tipo: booleano  
Predeterminado: n

Si establece, el agente intentará instalar y, a continuación, cargar un controlador de kernel RDMA que coincida con la versión del firmware del hardware subyacente.


**SISTEMA OPERATIVO. RootDeviceScsiTimeout:**  
Tipo: entero  
Predeterminado: 300

Configura el tiempo de espera de SCSI en segundos en las unidades de disco y los datos del sistema operativo. Si no está activado, el sistema se usan los valores predeterminados.


**SISTEMA OPERATIVO. OpensslPath:**  
Tipo: cadena  
Predeterminado: ninguno

Esto puede utilizarse para especificar una ruta alternativa para el openssl binario para las operaciones de cifrado.


**HttpProxy.Host, HttpProxy.Port**  
Tipo: cadena  
Predeterminado: ninguno

Si establece, el agente usará este servidor proxy de acceso a internet. 


## <a name="ubuntu-cloud-images"></a>Imágenes de nube Ubuntu

Tenga en cuenta que las imágenes de nube Ubuntu utilizan [nube al inicio](https://launchpad.net/ubuntu/+source/cloud-init) para llevar a cabo muchas de las tareas de configuración en caso contrario, administrará el agente de Azure Linux.  Tenga en cuenta las siguientes diferencias:


- **Provisioning.Enabled** predeterminado es "n" en las imágenes de la nube de Ubuntu que usar nube al inicio para realizar tareas de aprovisionamiento.

- Los siguientes parámetros de configuración no tienen efecto en las imágenes de la nube de Ubuntu que usar nube al inicio para administrar el disco del recurso y espacio de intercambio:

 - **ResourceDisk.Format**
 - **ResourceDisk.Filesystem**
 - **ResourceDisk.MountPoint**
 - **ResourceDisk.EnableSwap**
 - **ResourceDisk.SwapSizeMB**

- Consulte los siguientes recursos para configurar el punto de montaje de disco recursos e intercambiar espacio en las imágenes de nube Ubuntu durante el aprovisionamiento de:

 - [Ubuntu Wiki: Configurar las particiones de intercambio](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
 - [Insertar datos personalizados en una máquina Virtual de Azure](virtual-machines-windows-classic-inject-custom-data.md)

