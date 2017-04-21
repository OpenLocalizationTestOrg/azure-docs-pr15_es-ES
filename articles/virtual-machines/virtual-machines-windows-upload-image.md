<properties
    pageTitle="Cargar un disco duro virtual de Windows para el Administrador de recursos | Microsoft Azure"
    description="Aprenda a cargar una Windows máquina virtual disco duro virtual locales Azure, se utiliza el modelo de implementación de administrador de recursos. Puede cargar un disco duro virtual de, ya sea un generalized o una máquina virtual especializada."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>

# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>Cargar un disco duro virtual de una máquina virtual de local a Azure de Windows 


Este artículo le muestra cómo crear y cargar un disco duro (disco duro virtual) que se utilizará en la creación de una máquina virtual de Azure virtual Windows. Puede cargar un disco duro virtual de una máquina virtual generalized o una máquina virtual especializada. 

Para obtener más detalles sobre discos y VHD en Azure, consulte [acerca de discos y VHD para máquinas virtuales](virtual-machines-linux-about-disks-vhds.md).


## <a name="prepare-the-vm"></a>Preparar la máquina virtual 

Puede cargar VHD generalizado y especializadas para Azure. Cada tipo requiere que preparar la máquina virtual antes de empezar.

- **Disco duro de virtual generalizado** - un disco duro virtual generalizado ha tenido toda la información de cuenta personal quitada mediante Sysprep. Si desea usar el disco duro virtual como una imagen para crear nuevas máquinas virtuales de, debe:
    - [Preparar un disco duro virtual de Windows para cargar en Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). 
    - [Uso de la máquina virtual de Generalize Sysprep](virtual-machines-windows-generalize-vhd.md). 

- **Especializadas disco duro virtual** - un disco duro virtual especializado mantiene las cuentas de usuario, aplicaciones y otros datos del estado de la máquina virtual original. Si va a usar el disco duro virtual como-es crear una nueva máquina virtual, asegúrese de completar los pasos siguientes. 
    - [Preparar un disco duro virtual de Windows para cargar en Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). **No hacer** generaliza la máquina virtual con Sysprep.
    - Quitar las herramientas de virtualización de invitado y agentes que están instalados en la máquina virtual (es decir, herramientas de VMware).
    - Asegúrese de que la máquina virtual está configurada para extraer su dirección IP y configuración de DNS a través de DHCP. Así se garantiza que el servidor obtiene una dirección IP dentro de la VNet cuando se inicia. 

## <a name="log-in-to-azure"></a>Inicie sesión en Azure

Si aún no dispone de PowerShell versión 1.4 o posterior instalado, lectura [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

1. Abra Azure PowerShell e inicie sesión su cuenta de Azure. Se abrirá una ventana emergente para que escribir sus credenciales de cuenta de Azure.

    ```powershell
    Login-AzureRmAccount
    ```


2. Obtener la identificadores de suscripción para las suscripciones disponibles.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Establecer la suscripción correcta utilizando el identificador de suscripción. Reemplazar `<subscriptionID>` con el identificador de la suscripción correcta.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Obtener la cuenta de almacenamiento

Se necesita una cuenta de almacenamiento en Azure para almacenar la imagen de VM cargada. Puede usar una cuenta de almacenamiento existente o cree uno nuevo. 

Para mostrar las cuentas de almacenamiento disponible, escriba:

```powershell
Get-AzureRmStorageAccount
```

Si desea usar una cuenta de almacenamiento existente, vaya a la sección [cargar la imagen de la máquina virtual](#upload-the-vm-vhd-to-your-storage-account) .

Si necesita crear una cuenta de almacenamiento, siga estos pasos:

1. Es necesario que el nombre del grupo de recursos donde se debe crear la cuenta de almacenamiento. Para obtener todos los grupos de recursos que se encuentran en la suscripción, escriba:

    ```powershell
    Get-AzureRmResourceGroup
    ```

Para crear un grupo de recursos denominado **myResourceGroup** en la región **Oeste de Estados Unidos** , escriba:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Crear una cuenta de almacenamiento denominada **mystorageaccount** en este grupo de recursos mediante el cmdlet [AzureRmStorageAccount nuevo](https://msdn.microsoft.com/library/mt607148.aspx) :

    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" -SkuName "Standard_LRS" -Kind "Storage"
    ```
            
    Valores válidos para - SkuName son:

    - **Standard_LRS** - almacenamiento redundante local. 
    - **Standard_ZRS** - almacenamiento redundante de zona.
    - **Standard_GRS** - Geo redundante almacenamiento. 
    - **Standard_RAGRS** - almacenamiento de acceso de lectura geo redundante. 
    - **Premium_LRS** - almacenamiento redundante local de Premium. 



## <a name="upload-the-vhd-to-your-storage-account"></a>Cargar el disco duro virtual a su cuenta de almacenamiento

Usar el cmdlet [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) para cargar la imagen en un contenedor en su cuenta de almacenamiento. Este ejemplo carga el archivo **myVHD.vhd** desde `"C:\Users\Public\Documents\Virtual hard disks\"` al almacenamiento de un cuenta denominada **mystorageaccount** en el grupo de recursos de **myResourceGroup** . El archivo se colocarán en el contenedor denominado **mycontainer** y el nuevo nombre de archivo se **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Si se realiza correctamente, recibe una respuesta que tiene un aspecto similar a este:

```
  C:\> Add-AzureRmVhd -ResourceGroupName myResourceGroup -Destination https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
  MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
  MD5 hash calculation is completed.
  Elapsed time for the operation: 00:03:35
  Creating new page blob of size 53687091712...
  Elapsed time for upload: 01:12:49

  LocalFilePath           DestinationUri
  -------------           --------------
  C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Dependiendo de la conexión de red y el tamaño de archivo de su disco duro virtual, este comando puede tardar varios minutos en completarse


## <a name="next-steps"></a>Pasos siguientes

- [Crear una máquina virtual en Azure desde un disco duro virtual generalizado](virtual-machines-windows-create-vm-generalized.md)
- [Crear una máquina virtual en Azure desde un disco duro virtual especializado](virtual-machines-windows-create-vm-specialized.md) adjuntándolos como un disco de sistema operativo cuando se crea una nueva máquina virtual.


