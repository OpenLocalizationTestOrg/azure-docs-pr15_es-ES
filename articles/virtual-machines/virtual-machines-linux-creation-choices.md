<properties
    pageTitle="Diferentes maneras de crear una VM Linux | Microsoft Azure"
    description="Obtenga información sobre las diferentes formas de crear una máquina virtual Linux en Azure, incluidos los vínculos a herramientas y tutoriales para cada método."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>Diferentes maneras de crear una máquina virtual Linux en Azure

Tiene la flexibilidad en Azure para crear una máquina virtual de Linux (VM) mediante herramientas y flujos de trabajo cómodos para usted. Este artículo resume estas diferencias y ejemplos para crear sus máquinas virtuales de Linux.


## <a name="azure-cli"></a>CLI de Azure 

CLI Azure está disponible en las plataformas a través de un paquete de npm, siempre distro paquetes o contenedor Docker. Puede obtener más información sobre [cómo instalar y configurar la CLI de Azure](../xplat-cli-install.md). Los tutoriales siguientes proporcionan ejemplos sobre el uso de la CLI de Azure. Lea cada artículo para obtener más detalles acerca de los comandos de inicio rápido CLI que se muestra:

- [Crear una VM Linux desde la CLI Azure para desarrollo y pruebas](virtual-machines-linux-quick-create-cli.md)
    - En el ejemplo siguiente se crea una VM CoreOS con una clave pública denominada `azure_id_rsa.pub`:

    ```bash
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
        --image-urn CoreOS
    ```

- [Crear una VM Linux protegidos mediante una plantilla de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
    - En el ejemplo siguiente se crea una máquina virtual usando una plantilla que se almacena en GitHub:

    ```bash
    azure group create --name TestRG --location WestUS 
        --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```

- [Crear un entorno de Linux completo con la CLI de Azure](virtual-machines-linux-create-cli-complete.md)
    - Incluye la creación de un equilibrador de carga y varios VM en un conjunto de disponibilidad.

- [Agregar un disco a una VM Linux](virtual-machines-linux-add-disk.md)
    - En el ejemplo siguiente se agrega un disco de 5 Gb a una máquina virtual existente denominada `TestVM`:

    ```bash
    azure vm disk attach-new --resource-group TestRG --vm-name TestVM \
        --size-in-GB 5
    ```

## <a name="azure-portal"></a>Portal de Azure

El [portal de Azure](https://portal.azure.com) le permite crear rápidamente una máquina virtual, ya que no hay nada que instalar en el sistema. Usar el portal de Azure para crear la máquina virtual:

- [Crear una VM Linux con el portal de Azure](virtual-machines-linux-quick-create-portal.md) 
- [Adjuntar un disco con el portal de Azure](virtual-machines-linux-attach-disk-portal.md)


## <a name="operating-system-and-image-choices"></a>Sistema operativo y las opciones de imagen
Al crear una máquina virtual, elija una imagen basada en el sistema operativo que desea ejecutar. Azure y sus socios ofrecen muchas imágenes, algunos de los cuales incluyen aplicaciones y herramientas preinstaladas. O bien, cargue una de sus propias imágenes (consulte [la sección siguiente](#use-your-own-image)).

### <a name="azure-images"></a>Imágenes de Azure
Usar el `azure vm image` comandos CLI para ver qué está disponible, publisher, versión distro y versiones.

La lista editores disponibles como sigue:

```bash
azure vm image list-publishers --location WestUS
```

Lista productos disponibles (ofertas) de un editor determinado como sigue:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Enumerar SKU disponibles (versiones distro) de una oferta dada como sigue:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Una lista de todas las imágenes disponibles para una versión determinada sigue:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Para obtener más ejemplos de búsqueda y el uso de imágenes disponibles, vea [navegar y seleccionar máquina virtual Azure imágenes con la CLI de Azure](virtual-machines-linux-cli-ps-findimage.md).

La `azure vm quick-create` y `azure vm create` comandos tienen alias que puede usar para obtener acceso rápidamente a los distros más comunes y sus versiones más recientes. Uso de alias a menudo es más rápido que especifica el publisher, la oferta, la SKU y la versión cada vez que cree una máquina virtual:

| Alias     | Publisher | Oferta        | SKU         | Versión |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | CentOS       | 7.2         | más reciente  |
| CoreOS    | CoreOS    | CoreOS       | Estable      | más reciente  |
| Debian    | credativ  | Debian       | 8           | más reciente  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | más reciente  |
| RHEL      | RedHat    | RHEL         | 7.2         | más reciente  |
| SLES      | SLES      | SLES         | 12 SP1      | más reciente  |
| UbuntuLTS | Canónico | UbuntuServer | 14.04.4-LTS | más reciente  |

### <a name="use-your-own-image"></a>Usar su propia imagen

Si necesita personalizaciones específicas, puede usar una imagen basada en una máquina virtual de Azure existente al *capturar* máquina. También puede cargar una imagen creada en local. Para obtener más información sobre distros compatibles y cómo usar sus propias imágenes, consulte los siguientes artículos:

- [Distribuciones de respaldo de Azure](virtual-machines-linux-endorsed-distros.md)

- [Información de apoyo no distribuciones](virtual-machines-linux-create-upload-generic.md)

- [Cómo capturar una máquina virtual de Linux como una plantilla de administrador de recursos](virtual-machines-linux-capture-image.md).
    - Inicio rápido de comandos de ejemplo para capturar una máquina virtual existente:

    ```bash
    azure vm deallocate --resource-group TestRG --vm-name TestVM
    azure vm generalize --resource-group TestRG --vm-name TestVM
    azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
    ```

## <a name="next-steps"></a>Pasos siguientes

- Crear una VM Linux desde el [portal](virtual-machines-linux-quick-create-portal.md), con [CLI](virtual-machines-linux-quick-create-cli.md), o usar una [plantilla de administrador de recursos de Azure](virtual-machines-linux-cli-deploy-templates.md).

- Después de crear una VM Linux, [Agregar un disco de datos](virtual-machines-linux-add-disk.md).

- Pasos rápidos para [Restablecer una contraseña o claves SSH y administrar usuarios](virtual-machines-linux-using-vmaccess-extension.md)
