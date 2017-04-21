<properties
    pageTitle="Crear y cargar un VHD Linux | Microsoft Azure"
    description="Crear y cargar un disco duro virtual Azure (disco duro virtual) con el modelo de implementación clásica que contiene el sistema operativo Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Crear y cargar un disco duro Virtual que contiene el sistema operativo Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]También puede [cargar una imagen de disco personalizado mediante el Administrador de recursos de Azure](virtual-machines-linux-upload-vhd.md).

Este artículo le muestra cómo crear y cargar un disco duro virtual (VHD) para utilizarlo como su propia imagen para crear máquinas virtuales de Azure. Aprenda a preparar el sistema operativo, por lo que puede usar para crear varias máquinas virtuales de Windows basado en dicha imagen. 

>  [AZURE.NOTE] Si tiene unos momentos, Ayúdenos a mejorar la documentación de Azure Linux VM tomando esta [Encuesta rápida](https://aka.ms/linuxdocsurvey) de su experiencia. Cada respuesta nos ayuda a realizar el trabajo de ayuda.

## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que tiene los siguientes elementos:

- **Sistema operativo Linux instalado en un archivo .vhd** - instalado una [distribución de respaldo de Azure Linux](virtual-machines-linux-endorsed-distros.md) (o ver la [información de apoyo no distribuciones](virtual-machines-linux-create-upload-generic.md)) a un disco virtual en el formato de disco duro virtual. Existen varias herramientas para crear una máquina virtual y el disco duro virtual:
    - Instalar y configurar [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](http://www.linux-kvm.org/page/RunningKVM), teniendo cuidado de usar el disco duro virtual como el formato de imagen. Si es necesario, puede [convertir una imagen](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert`.
    - También puede usar Hyper-V [en Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [en Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] El formato más reciente de VHDX no es compatible con Azure. Cuando se crea una máquina virtual, especifique el disco duro virtual como el formato. Si es necesario, puede convertir discos VHDX al uso de disco duro virtual [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o la [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) cmdlet de PowerShell. Además, Azure no admite cargar VHD dinámicos, por lo que necesita convertir a VHD estático antes de cargar. Puede usar herramientas como [Utilidades de disco duro virtual de Azure para ir](https://github.com/Microsoft/azure-vhd-utils-for-go) a convertir discos dinámicos durante el proceso de carga de Azure.

- **Interfaz de línea de comandos de azure** : instalar la última [interfaz de línea de comandos de Azure](../virtual-machines-command-line-tools.md) para cargar el disco duro virtual.

<a id="prepimage"> </a>
## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Paso 1: Preparar la imagen que va a cargar

Azure es compatible con varias distribuciones de Linux (consulte [Distribuciones de apoyo](virtual-machines-linux-endorsed-distros.md)). Los siguientes artículos le guían a preparar las distribuciones de Linux diversos que son compatibles en Azure. Después de completar los pasos descritos en las siguientes guías, tener este sitio una vez que tenga un archivo de disco duro virtual que está preparado para cargar en Azure:

- **[Distribuciones de centOS](virtual-machines-linux-create-upload-centos.md)**
- **[Linux Debian](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Rojos sombrero Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Otras - distribuciones no apoyo](virtual-machines-linux-create-upload-generic.md)**

> [AZURE.NOTE] La plataforma de Windows Azure SLA se aplica a máquinas virtuales que ejecutan el sistema operativo Linux solo cuando se usa una de las distribuciones visadas con los detalles de configuración especificados en compatibles con versiones de [Linux en distribuciones de Azure-Endorsed](virtual-machines-linux-endorsed-distros.md). Todas las distribuciones de Linux en la Galería de imágenes de Azure son distribuciones visadas con la configuración necesaria.

Consulte también las **[Notas de instalación de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** para obtener sugerencias más generales sobre cómo preparar Linux imágenes para Azure.


<a id="connect"> </a>
## <a name="step-2-prepare-the-connection-to-azure"></a>Paso 2: Preparar la conexión de Azure

Asegúrese de que esté usando la CLI de Azure en el modelo de implementación clásica (`azure config mode asm`), a continuación, inicie sesión en su cuenta:

```
azure login
```


<a id="upload"> </a>
## <a name="step-3-upload-the-image-to-azure"></a>Paso 3: Cargar la imagen en Azure

Se necesita una cuenta de almacenamiento para cargar el archivo de disco duro virtual a. Puede seleccionar una cuenta de almacenamiento existente o [cree uno nuevo](../storage/storage-create-storage-account.md).

Use la CLI de Azure para cargar la imagen mediante el comando siguiente:

```bash
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

En el ejemplo anterior:

- **BlobStorageURL** es la dirección URL para la cuenta de almacenamiento que planea usar
- **YourImagesFolder** es el contenedor de almacenamiento de blobs de Windows donde desea almacenar las imágenes
- **VHDName** es la etiqueta que aparece en el portal para identificar el disco duro virtual.
- **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd en su equipo.

A continuación muestra un ejemplo completo:

```bash
azure vm image create UbuntuLTS `
    --blob-url https://teststorage.blob.core.windows.net/vhds/UbuntuLTS.vhd `
    --os Linux /home/ahmet/UbuntuLTS.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Paso 4: Crear una máquina virtual de la imagen
Cree una máquina virtual con `azure vm create` en la misma manera que una máquina virtual normal. Especifique el nombre que le asignó su imagen en el paso anterior. En el ejemplo siguiente, se utiliza el nombre de la imagen de **UbuntuLTS** indicado en el paso anterior:

```bash
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "DeployedUbuntu" UbuntuLTS
```

Para crear su propia VM, proporcione su propio nombre de usuario + contraseña, ubicación, nombre DNS y nombre de la imagen.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte [referencia de Azure CLI para el modelo de implementación clásica Azure](../virtual-machines-command-line-tools.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload
