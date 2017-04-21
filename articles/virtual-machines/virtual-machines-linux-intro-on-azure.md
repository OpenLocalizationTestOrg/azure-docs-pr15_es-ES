<properties
    pageTitle="Introducción a Linux en Azure | Microsoft Azure"
    description="Obtenga información sobre cómo usar máquinas virtuales Linux en Azure."
    services="virtual-machines-linux"
    documentationCenter="python"
    authors="szarkos"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

#<a name="introduction-to-linux-on-azure"></a>Introducción a Linux en Azure

Este tema proporciona una descripción general de algunos aspectos de utilizar máquinas virtuales de Linux en la nube de Azure. Implementar una máquina virtual Linux es un proceso sencillo usar una imagen de la galería.


## <a name="authentication-usernames-passwords-and-ssh-keys"></a>Autenticación: Nombres de usuario, contraseñas y claves SSH

Al crear una máquina virtual de Linux con el portal clásico Azure, le pedirá que proporcione un nombre de usuario, contraseña o una clave pública de SSH. La elección de un nombre de usuario para implementar una máquina virtual de Linux en Azure está sujeta a la siguiente restricción: nombres de las cuentas del sistema (UID < 100) ya está presente en la máquina virtual no se permiten, 'root' por ejemplo.


 - Consulte [crear una máquina Virtual con Linux](virtual-machines-linux-quick-create-cli.md)
 - Vea [cómo utilizar SSH con Linux en Azure](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="obtaining-superuser-privileges-using-sudo"></a>Obtener privilegios de superusuario con`sudo`

La cuenta de usuario que se especifica durante la implementación de la instancia de máquina virtual en Azure es una cuenta con privilegios. Esta cuenta se ha configurado el agente de Linux Azure puedan elevar privilegios a raíz (cuenta de superusuario) usando la `sudo` utilidad. Una vez que ha iniciado sesión en el uso de esta cuenta de usuario, podrá ejecutar comandos como raíz mediante la sintaxis de comandos

    # sudo <COMMAND>

Opcionalmente, puede obtener un shell raíz mediante **s sudo**.

- Vea [con privilegios de raíz en máquinas virtuales de Linux en Azure](virtual-machines-linux-use-root-privileges.md)


## <a name="firewall-configuration"></a>Configuración del Firewall

Azure proporciona un filtro de paquete entrante que restringe la conectividad a los puertos especificados en el portal de clásico de Azure. De forma predeterminada, el único puerto permitido es SSH. Puede abrir el acceso a los puertos adicionales en la máquina virtual de Linux configurando extremos en el portal de clásico Azure:

 - Vea: [cómo configurar los extremos de una máquina Virtual](virtual-machines-windows-classic-setup-endpoints.md)

Las imágenes de Linux en la Galería de Azure no habilita el firewall *iptables* de forma predeterminada. Si lo desea, puede configurar el firewall para proporcionar filtrado adicional.


## <a name="hostname-changes"></a>Cambios de nombre de host

Al implementar una instancia de una imagen de Linux inicialmente, deberá proporcionar un nombre de host de la máquina virtual. Una vez que se está ejecutando la máquina virtual, este nombre de host se publica a los servidores DNS de plataforma para que varias máquinas virtuales de Windows conectado a ellos puedan realizar búsquedas de direcciones IP con nombres de host.

Si desea realizar cambios de nombre de host después de que se ha implementado una máquina virtual, utilice el comando

    # sudo hostname <newname>

El agente de Azure Linux incluye funcionalidad para detectar automáticamente este cambio de nombre y configure correctamente la máquina virtual para conservar este cambio y publicar este cambio a los servidores DNS de plataforma.

 - [Guía de usuario de Azure agente Linux](virtual-machines-linux-agent-user-guide.md)

### <a name="cloud-init"></a>Inicialización de nube
Imágenes de **Ubuntu** y **CoreOS** utilizan nube al inicio en Azure, que proporciona funciones adicionales para el inicio de una máquina virtual.

 - [Cómo insertar datos personalizados](virtual-machines-windows-classic-inject-custom-data.md)
 - [Inicialización de nube de Microsoft Azure y datos personalizados](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
 - [Crear particiones de intercambio de Azure con nube al inicio](https://wiki.ubuntu.com/AzureSwapPartitions)
 - [Cómo usar CoreOS en Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)


## <a name="virtual-machine-image-capture"></a>Captura de imágenes de máquina virtual

Azure proporciona la capacidad para capturar el estado de un equipo virtual existente a una imagen que puede usarse posteriormente para implementar instancias de máquina virtual adicional. Puede ser el agente de Azure Linux utiliza para deshacer parte de la personalización que se realizó durante el proceso de aprovisionamiento. Puede seguir los pasos siguientes para capturar una máquina virtual como una imagen:

1. Ejecutar **waagent-deprovision** para deshacer el aprovisionamiento de personalización. O **waagent-deprovision + usuario** para eliminar de manera opcional, la cuenta de usuario especificada durante aprovisionamiento y todos los datos asociados.

2. Cierre o alimentación desactivar la máquina virtual.

3. Haga clic en *captura* en el portal de clásico Azure o use las herramientas de Powershell o CLI para capturar la máquina virtual como una imagen.

 - Vea: [cómo capturar una máquina Virtual de Linux para usarlo como una plantilla](virtual-machines-linux-classic-capture-image.md)


## <a name="attaching-disks"></a>Conectar discos

Cada máquina virtual tiene un temporal, local *disco del recurso* adjunta. Porque los datos en un disco del recurso pueden no ser resistentes a través de reiniciar el equipo, se usa a menudo por aplicaciones y procesos que se ejecutan en la máquina virtual para transitorio y **temporal** el almacenamiento de datos. También sirve para almacenar la página o el intercambio de archivos para el sistema operativo.

En Linux, el disco del recurso es normalmente administrado por el agente de Linux de Azure y montaje automáticamente a **/mnt/resource** (o **/mnt/mnt** en Ubuntu imágenes).


>[AZURE.NOTE] Observe que el disco del recurso es un disco **temporal** y podría ser eliminado y volver a dar formato al reinicia la máquina virtual.

En Linux el disco de datos podría denominarse el núcleo como `/dev/sdc`, y los usuarios tendrán que crear particiones, dar formato y montaje de ese recurso. Se trata de paso a paso en el tutorial: [cómo adjuntar un disco de datos a una máquina Virtual](virtual-machines-linux-classic-attach-disk.md).

 - **Vea también:** [Configurar el Software RAID en Linux](virtual-machines-linux-configure-raid.md)  &  [Configurar LVM en una máquina virtual de Linux en Azure](virtual-machines-linux-configure-lvm.md)

