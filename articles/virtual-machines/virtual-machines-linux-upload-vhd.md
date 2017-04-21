<properties
    pageTitle="Crear y cargar una imagen personalizada de Linux | Microsoft Azure"
    description="Crear y cargar un disco duro virtual (disco duro virtual) en Azure con una imagen personalizada de Linux utiliza el modelo de implementación de administrador de recursos."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="iainfou"/>

# <a name="upload-and-create-a-linux-vm-from-custom-disk-image"></a>Cargar y crear una VM Linux de la imagen de disco personalizada

Este artículo le muestra cómo cargar un disco duro virtual (VHD) que utiliza el modelo de implementación de administrador de recursos de Azure y crear máquinas virtuales de Linux de esta imagen personalizada. Esta funcionalidad le permite instalar y configurar un distro Linux a sus necesidades y, a continuación, usar ese disco duro virtual para crear rápidamente Azure máquinas virtuales de Windows (VM).

## <a name="quick-commands"></a>Comandos rápidos
Si necesita realizar rápidamente la tarea, los detalles de la sección siguiente de la base de comandos para cargar una máquina virtual en Azure. Obtener más información y el contexto de cada paso pueden encontrarse el resto del documento, a [partir de aquí](#requirements).

Asegúrese de que tiene [La CLI Azure](../xplat-cli-install.md) inició sesión y usar el modo de administrador de recursos:

```bash
azure config mode arm
```

En los ejemplos siguientes, reemplace los nombres de parámetro de ejemplo con sus propios valores. Nombres de parámetro de ejemplo incluidos `myResourceGroup`, `mystorageaccount`, y `myimages`.

Primero, cree un grupo de recursos. En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la `WestUs` ubicación:

```bash
azure group create myResourceGroup --location "WestUS"
```

Crear una cuenta de almacenamiento para mantener los discos virtuales. En el ejemplo siguiente se crea una cuenta de almacenamiento denominada `mystorageaccount`:

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

Lista de las teclas de acceso para su cuenta de almacenamiento. Tome nota de `key1`:

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Crear un contenedor dentro de su cuenta de almacenamiento mediante la clave de almacenamiento obtuvo. En el ejemplo siguiente se crea un contenedor denominado `myimages` mediante el valor de clave de almacenamiento de `key1`:

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Por último, cargue su disco duro virtual al contenedor que creó. Especificar la ruta de acceso local de su disco duro virtual en `/path/to/disk/mydisk.vhd`:

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

Ahora puede crear una máquina virtual desde el disco virtual cargados [mediante una plantilla de administrador de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). También puede utilizar la CLI especificando la URI en el disco (`--image-urn`). En el ejemplo siguiente se crea una máquina virtual denominada `myVM` usando el disco virtual cargados previamente:

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

La cuenta de almacenamiento de destino tiene que ser el mismo que donde ha cargado el disco virtual para. También debe especificar o solicita respuesta, todos los parámetros adicionales necesarios para que la `azure vm create` comando como una red virtual, dirección IP pública, nombre de usuario y claves SSH. Puede obtener más información acerca de los [parámetros disponibles de administrador de recursos de CLI](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Requisitos
Para completar estos pasos, debe:

- **Sistema operativo Linux se instala en un archivo .vhd** : instalar una [distribución de respaldo de Azure Linux](virtual-machines-linux-endorsed-distros.md) (o ver la [información de apoyo no distribuciones](virtual-machines-linux-create-upload-generic.md)) a un disco virtual en el formato de disco duro virtual. Existen varias herramientas para crear una máquina virtual y el disco duro virtual:
    - Instalar y configurar [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](http://www.linux-kvm.org/page/RunningKVM), teniendo cuidado de usar el disco duro virtual como el formato de imagen. Si es necesario, puede [convertir una imagen](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert`.
    - También puede usar Hyper-V [en Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [en Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] El formato más reciente de VHDX no es compatible con Azure. Cuando se crea una máquina virtual, especifique el disco duro virtual como el formato. Si es necesario, puede convertir discos VHDX al uso de disco duro virtual [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o la [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) cmdlet de PowerShell. Además, Azure no admite cargar VHD dinámicos, por lo que necesita convertir a VHD estático antes de cargar. Puede usar herramientas como [Utilidades de disco duro virtual de Azure para ir](https://github.com/Microsoft/azure-vhd-utils-for-go) a convertir discos dinámicos durante el proceso de carga de Azure.

- Máquinas virtuales creadas a partir de la imagen personalizada deben estar en la misma cuenta de almacenamiento que la propia imagen
    - Crear una cuenta de almacenamiento y el contenedor para mantener su imagen personalizada y la VM creadas
    - Después de haber creado todos sus máquinas virtuales, puede eliminar la imagen

Asegúrese de que tiene [La CLI Azure](../xplat-cli-install.md) inició sesión y usar el modo de administrador de recursos:

```bash
azure config mode arm
```

En los ejemplos siguientes, reemplace los nombres de parámetro de ejemplo con sus propios valores. Nombres de parámetro de ejemplo incluidos `myResourceGroup`, `mystorageaccount`, y `myimages`.


<a id="prepimage"> </a>
## <a name="prepare-the-image-to-be-uploaded"></a>Preparar la imagen de cargarse

Azure es compatible con varias distribuciones de Linux (consulte [Distribuciones de apoyo](virtual-machines-linux-endorsed-distros.md)). Los artículos siguientes le guiará a través de cómo preparar las distribuciones de Linux diversos que son compatibles en Azure:

- **[Distribuciones de centOS](virtual-machines-linux-create-upload-centos.md)**
- **[Linux Debian](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Rojos sombrero Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Otras - distribuciones no apoyo](virtual-machines-linux-create-upload-generic.md)**

Consulte también las **[Notas de instalación de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** para obtener sugerencias más generales sobre cómo preparar Linux imágenes para Azure.

> [AZURE.NOTE] La [plataforma de Windows Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) se aplica a máquinas virtuales con Linux solo cuando se usa una de las distribuciones visadas con los detalles de configuración especificados en compatibles con versiones de [Linux en distribuciones de Azure-Endorsed](virtual-machines-linux-endorsed-distros.md).


## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Grupos de recursos lógicamente juntar todos los recursos de Azure a sus máquinas virtuales, como la red virtual y el almacenamiento de soporte técnico. Obtenga más información acerca de los [grupos de recursos de Azure aquí](../azure-resource-manager/resource-group-overview.md). Antes de cargar la imagen de disco personalizado y crear máquinas virtuales, debe crear un grupo de recursos. 

En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la `WestUS` ubicación:

```bash
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
Máquinas virtuales se almacenan como blobs de página dentro de una cuenta de almacenamiento. Más información sobre el [almacenamiento de blobs de Windows Azure aquí](../storage/storage-introduction.md#blob-storage). Crear una cuenta de almacenamiento de su imagen de disco personalizado y máquinas virtuales. Cualquier VM creada a partir de la imagen de disco personalizado deben estar en la misma cuenta de almacenamiento como imagen.

En el ejemplo siguiente se crea una cuenta de almacenamiento denominada `mystorageaccount` en el grupo de recursos que creó anteriormente:

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Teclas de cuenta de almacenamiento de lista
Azure genera dos teclas de acceso de 512 bits para cada cuenta de almacenamiento. Estas teclas de acceso se usan para autenticar con la cuenta de almacenamiento, como por ejemplo para llevar a cabo operaciones de escritura. Obtenga más información acerca de cómo [administrar el acceso al almacenamiento aquí](../storage/storage-create-storage-account.md#manage-your-storage-account). Puede ver las teclas de acceso con la `azure storage account keys list` comando.

Ver las teclas de acceso para la cuenta de almacenamiento que creó:

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

El resultado es similar a:

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
Tome nota de `key1` si se usa para interactuar con su cuenta de almacenamiento en los siguientes pasos.

## <a name="create-a-storage-container"></a>Crear un contenedor de almacenamiento
En la misma manera que cree directorios diferentes para organizar lógicamente el sistema de archivos local, crear contenedores dentro de una cuenta de almacenamiento para organizar las imágenes y los discos virtuales. Una cuenta de almacenamiento puede contener cualquier número de contenedores. 

En el ejemplo siguiente se crea un contenedor denominado `myimages`, que especifica la tecla de acceso que se obtiene en el paso anterior (`key1`):

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Cargar el disco duro virtual
Ahora realmente puede cargar la imagen de disco personalizado. Como con todos los discos virtuales usados por VM, carga y almacena la imagen de disco personalizado como un blob de página.

Especifique la clave de acceso, el contenedor que creó en el paso anterior y, a continuación, en la ruta de acceso a la imagen de disco personalizada en el equipo local:

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Crear VM de imagen personalizada
Al crear máquinas virtuales de la imagen de disco personalizado, especifique el URI a la imagen de disco. Asegúrese de que coincide con la cuenta de almacenamiento de destino donde se almacena la imagen de disco personalizada. Puede crear su máquina virtual con la plantilla de Azure CLI o JSON de administrador de recursos.


### <a name="create-a-vm-using-the-azure-cli"></a>Crear una máquina virtual con la CLI de Azure
Especifique la `--image-urn` parámetro con el `azure vm create` comando para que apunten a la imagen de disco personalizado. Asegúrese de que `--storage-account-name` coincide con la cuenta de almacenamiento donde se almacena la imagen de disco personalizada. No debe utilizar el mismo contenedor como la imagen de disco personalizada para almacenar sus máquinas virtuales. Asegúrese de crear los contenedores adicionales de la misma manera que los pasos anteriores antes de cargar las imágenes de disco personalizada.

En el ejemplo siguiente se crea una máquina virtual denominada `myVM` de la imagen de disco personalizado:

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

Necesitará especificar o solicita respuesta, todos los parámetros adicionales necesarios para que la `azure vm create` comando como una red virtual, dirección IP pública, nombre de usuario y claves SSH. Obtenga más información acerca de los [parámetros disponibles de administrador de recursos de CLI](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>Crear una máquina virtual usando una plantilla JSON
Plantillas de administrador de recursos Azure son archivos de notación de objetos JavaScript (JSON) que definen el entorno que desea crear. Las plantillas se dividen proveedores de recurso diferente como calcular o red. Puede usar las plantillas existentes o escriba su propio. Obtenga más acerca de cómo [utilizar el Administrador de recursos y plantillas](../azure-resource-manager/resource-group-overview.md).

Dentro de la `Microsoft.Compute/virtualMachines` proveedor de la plantilla, tiene una `storageProfile` nodo que contiene los detalles de configuración de la máquina virtual. Son los dos parámetros principales para editar el `image` y `vhd` URI que apuntan a la imagen de disco personalizado y disco virtual de su nueva VM. La siguiente muestra un ejemplo de la JSON para usar una imagen de disco personalizado:

```bash
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Puede usar [esta plantilla para crear una máquina virtual de una imagen personalizada existente](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) o lea acerca de cómo [crear sus propias plantillas de administrador de recursos de Azure](../resource-group-authoring-templates.md). 

Una vez que tenga una plantilla configurada, crear sus máquinas virtuales con la `azure group deployment create` comando. Especifique el URI de la plantilla JSON con la `--template-uri` parámetro:

```bash
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Si tiene un archivo JSON que se almacenan localmente en el equipo, puede usar el `--template-file` parámetro en su lugar:

```bash
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Pasos siguientes
Después de haber preparado y cargado el disco virtual personalizado, puede obtener más información sobre cómo [usar el Administrador de recursos y plantillas](../azure-resource-manager/resource-group-overview.md). Puede también desea [Agregar un disco de datos](virtual-machines-linux-add-disk.md) a sus máquinas virtuales nuevas. Si tiene aplicaciones que se ejecutan en sus máquinas virtuales que necesitan tener acceso, asegúrese de [puertos abiertos y extremos](virtual-machines-linux-nsg-quickstart.md).