<properties
   pageTitle="Probar NetWeaver SAP en máquinas virtuales de Microsoft Azure SUSE Linux | Microsoft Azure"
   description="Probar NetWeaver SAP en máquinas virtuales de Microsoft Azure SUSE Linux"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Ejecución de SAP NetWeaver en máquinas virtuales de Microsoft Azure SUSE Linux


En este artículo se describe varias cosas para tener en cuenta cuando utiliza SAP NetWeaver en máquinas virtuales de Microsoft Azure SUSE Linux (VM). A partir 19 de mayo de 2016 SAP NetWeaver oficialmente es compatible en máquinas virtuales de Linux SUSE en Azure. Encontrará todos los detalles acerca de versiones de Linux, las versiones de kernel SAP y así sucesivamente en SAP nota 1928533 "aplicaciones de SAP en Azure: productos compatibles y los tipos de Azure VM".
Encontrará más información acerca de SAP en máquinas virtuales de Linux pueden encontrarse aquí: [Uso de SAP en máquinas virtuales de Linux (VM)](virtual-machines-linux-sap-get-started.md).


La siguiente información debe ayudar a evitar algunos problemas potenciales.

## <a name="suse-images-on-azure-for-running-sap"></a>Imágenes SUSE en Azure para ejecutar SAP

Para ejecutar NetWeaver de SAP en Azure, use solo SUSE Linux Enterprise Server SLES 12 (SPx): vea también Nota SAP 1928533. Una imagen SUSE especial está en el catálogo de soluciones de Azure ("SLES 11 SP3 para SAP CAL"), pero esto no está pensado para uso general. No utilice esta imagen porque está reservado para la solución de [Biblioteca de dispositivo de nube de SAP](https://cal.sap.com/) .  

Debe usar el Administrador de recursos de Azure para todas las instalaciones en Azure y nuevas pruebas. Para buscar imágenes de SUSE SLES y versiones mediante la interfaz de línea de comandos de Azure (CLI) o de Azure PowerShell, ejecute los comandos siguientes. A continuación, puede usar el resultado, por ejemplo, para definir la imagen del SO en una plantilla JSON para implementar una nueva máquina virtual Linux de SUSE.
Estos comandos de PowerShell son válidos para Azure PowerShell versión 1.0.1 y versiones posteriores.

* Busque editores existentes, incluidos SUSE:

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* Busque las ofertas existentes de SUSE:

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* Busque ofertas de SUSE SLES:

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* Busque una versión específica de un SKU SLES:

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>Cómo instalar WALinuxAgent en una máquina virtual de SUSE

El agente de WALinuxAgent forma parte de las imágenes SLES de Azure Marketplace. Para obtener información acerca de cómo instalar manualmente (por ejemplo, al cargar un sistema operativo SLES virtual en el disco duro (disco duro virtual) locales), consulte:

- [OpenSUSE] (http://software.opensuse.org/package/WALinuxAgent)

- [Azure] (virtual-máquinas-linux-apoyo-distros.md)

- [SUSE] (https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>"Supervisión mejorada" de SAP

SAP "mejorado supervisión" es obligatorio necesario para ejecutar SAP en Azure. Compruebe si hay detalles en SAP nota 2191498 "SAP en Linux con Azure: mejorado supervisión".

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Conectar discos de datos de Azure a una máquina virtual Linux de Azure

Nunca debería discos de datos de Azure montaje a una máquina virtual Linux de Azure usando el Id. En su lugar, use el identificador único universal (UUID). Tenga cuidado al usar herramientas gráficas a los discos de datos de Azure montaje, por ejemplo. Vuelva a comprobar las entradas en/etc/fstab.

El problema con el identificador de dispositivo es que es posible que cambien y, a continuación, pueden bloquearse la máquina virtual de Azure en el proceso de inicio. Para mitigar el problema, puede agregar el parámetro nofail en/etc/fstab. No obstante, tenga cuidado con nofail porque aplicaciones pueden usar el punto de montaje como antes y pueden escribir en el sistema de archivos raíz en caso de que un disco de datos externos de Azure no montaje durante el inicio.

La única excepción a montaje a través de UUID es asociar un disco de sistema operativo para solucionar problemas, como se describe en la sección siguiente.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>Solución de problemas de una VM SUSE que ya no es accesible

Puede haber situaciones donde se bloquea una VM SUSE en Azure en el proceso de inicio (por ejemplo, con un error relacionado con los discos de montaje). Puede comprobar este problema con la característica de diagnósticos de inicio para v2 máquinas virtuales de Azure en el portal de Azure. Para obtener más información, consulte [inicio diagnósticos] (https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Una forma de resolver el problema es adjuntar el disco de sistema operativo de la máquina virtual dañada a otro SUSE VM en Azure. A continuación, realice los cambios correspondientes como/etc/fstab de edición o eliminación de red udev reglas, como se describe en la siguiente sección.

Hay algo importante para tener en cuenta. Implementar varias máquinas virtuales de SUSE desde la misma imagen de Azure Marketplace (por ejemplo, SLES 11 Service Pack 4) hace que el disco de sistema operativo para instalarse siempre por el mismo UUID. Por lo tanto, si se utiliza el UUID adjuntar un disco de sistema operativo de una máquina virtual diferente que se ha implementado mediante el uso de la misma imagen de Azure Marketplace, se producirá en dos UUID idénticos. Esto causa problemas y puede deberse a que la máquina virtual pensada para solucionar problemas de hecho se iniciará desde el disco de sistema operativo adjunto y dañado en lugar del original.

Hay dos formas de evitar este problema:

* Usar una imagen de Azure Marketplace diferente para la máquina virtual de solución de problemas (por ejemplo, SLES 11 SPx en lugar de SLES 12).
* No adjuntar el disco de sistema operativo dañado desde otra VM mediante UUID: usar algo más.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Cargar una VM SUSE locales con Azure

Para obtener una descripción de los pasos para cargar una VM SUSE locales con Azure, consulte [preparar una máquina virtual SLES o openSUSE para Azure] (virtual-machines-linux-suse-create-upload-vhd.md).

Si desea cargar una máquina virtual sin el paso deprovision al final (por ejemplo, para mantener una instalación existente de SAP, así como el nombre de host), compruebe los siguientes elementos:

* Asegúrese de que el disco de sistema operativo es montaje mediante UUID y no el Id. Cambiar a UUID solo en/etc/fstab no es suficiente para el disco de sistema operativo. Además, no olvide adaptar el cargador de inicio a través de YaST o editando /boot/grub/menu.lst.
* Si utiliza el formato VHDX para el disco de sistema operativo SUSE y convertirla en el disco duro virtual para cargar en Azure, es muy probable que el dispositivo de red cambiará eth0 a eth1. Cambiar a eth0 para evitar problemas cuando está iniciando en Azure más adelante, como se describe en [solución eth0 en VMware de 11 SLES duplicado] (https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Además de lo que se describe en el artículo, se recomienda que quite:

   /lib/udev/Rules.d/75-persistent-NET-Generator.Rules

También puede instalar el agente de Azure Linux (waagent) para ayudar a evitar posibles problemas, siempre y cuando no hay varias NIC.

## <a name="deploying-a-suse-vm-on-azure"></a>Implementar una VM SUSE en Azure

Debe crear nuevas máquinas virtuales de SUSE mediante JSON archivos de plantilla en el nuevo modelo de administrador de recursos de Azure. Después de crea el archivo de plantilla JSON, puede implementar la máquina virtual mediante el siguiente comando CLI como alternativa a PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Para obtener más detalles acerca de los archivos de plantilla JSON, vea [plantillas de administrador de recursos de Azure creación] (recursos-grupo-creación-templates.md) y [plantillas de Azure tutorial] (https://azure.microsoft.com/documentation/templates/).

Para obtener más detalles acerca de CLI y el Administrador de recursos de Azure, consulte [usar CLI Azure para Mac, Linux y Windows con el Administrador de recursos de Azure] (xplat-cli-azure-recursos-manager.md).

## <a name="sap-license-and-hardware-key"></a>Clave de licencia y hardware SAP

Para la certificación oficial de Azure de SAP, se introdujo un nuevo mecanismo para calcular la clave de hardware SAP que se usa para la licencia SAP. El núcleo SAP tenía adaptación para asegurarse de utilizar esta propiedad. Las versiones de kernel SAP antiguos para Linux no incluyen este cambio de código. Por lo tanto, en determinadas situaciones (por ejemplo, Azure VM el cambio de tamaño), cambiar la clave de hardware SAP y la licencia SAP se ya no sea válido. Esto se soluciona en los kernels Linux SAP más recientes. Para obtener información detallada, consulte la nota SAP 1928533.

## <a name="suse-sapconf-package--tuned-adm"></a>Paquete SUSE sapconf / adm ajustado

SUSE proporciona un paquete denominado "sapconf" que administra un conjunto de opciones específicas de SAP. Para obtener más detalles sobre qué hace este paquete y cómo instalar y usar, consulte [usando sapconf para preparar SUSE Linux Enterprise Server para ejecutar sistemas SAP] (https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) y [¿qué es sapconf o para preparar un SUSE Linux Enterprise Server ejecutando sistemas SAP?] (http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

Mientras tanto, hay una nueva herramienta que reemplaza sapconf - adm ajustado Uno puede encontrar más detalles acerca de esta herramienta los dos vínculos siguientes.

Puede encontrar documentación SLES sobre adm ajustado de perfil sap hana [aquí](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

Sistemas de optimización para cargas de trabajo de SAP con adm optimizado - pueden encontrarse [aquí](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) en capítulo 6.2


## <a name="nfs-share-in-distributed-sap-installations"></a>Compartir NFS en instalaciones distribuidas de SAP

Si tiene una instalación distribuida: por ejemplo, donde desea instalar la base de datos y los servidores de aplicaciones de SAP en VM independientes, puede compartir el directorio /sapmnt a través de sistema de archivo de red (NFS). Si tiene problemas con los pasos de instalación después de crear el recurso compartido NFS para /sapmnt, compruebe si se establece "no_root_squash" para el recurso compartido.

## <a name="logical-volumes"></a>Volúmenes lógicos

En el pasado si falta un gran volumen lógico en varios discos de datos de Azure (por ejemplo, para la base de datos SAP), se recomienda usar mdadm como lvm totalmente no validada todavía en Azure. Para obtener información sobre cómo configurar RAID de Linux en Azure mediante mdadm, consulte [configurar RAID de software en Linux](virtual-machines-linux-configure-raid.md). Mientras tanto a partir del principio de mayo de 2016 también lvm se admite totalmente en Azure y puede utilizarse como alternativa a mdadm. Para obtener información adicional sobre lvm en Azure, vea [Configurar LVM en una máquina virtual de Linux en Azure](virtual-machines-linux-configure-lvm.md).


## <a name="azure-suse-repository"></a>Repositorio SUSE Azure

Si tiene un problema con el acceso al repositorio de Azure SUSE estándar, puede utilizar un comando simple para restablecerlo. Es posible crear una imagen de sistema operativo privada en un área de Azure y, a continuación, copie la imagen a una región distinta donde desea implementar nuevas máquinas virtuales que se basan en esta imagen de sistema operativo privada. Simplemente ejecute el siguiente comando dentro de la máquina virtual:

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Escritorio GNOME

Si desea usar la versión de escritorio de Gnome para instalar un sistema de demostración SAP completo dentro de una única VM--incluidos en un gráfico de SAP, explorador y la consola de administración de SAP: utilizan esta sugerencia para instalarlo en las imágenes de Azure SLES:

   Para SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   Para SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>Soporte SAP para Oracle en Linux en la nube

Hay una restricción de soporte técnico de Oracle en Linux en entornos virtualizados. Aunque no es un tema específico de Azure, es importante conocer. SAP no admite Oracle en SUSE o sombrero rojo en una nube pública como Azure. Para obtener información sobre este tema, póngase en contacto con Oracle directamente.
