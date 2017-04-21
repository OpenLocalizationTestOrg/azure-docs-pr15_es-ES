<properties
    pageTitle="Crear una copia de una máquina virtual especializada en Azure | Microsoft Azure"
    description="Aprenda a crear una copia de una VM de Windows especializadas que se ejecuta en Azure, en el modelo de implementación de administrador de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>
    
    
    
# <a name="create-a-copy-of-a-specialized-windows-vm-running-in-azure"></a>Crear una copia de una VM de Windows especializadas que se ejecuta en Azure 

Este artículo le muestra cómo usar la herramienta AZCopy para crear una copia del disco duro virtual de una máquina virtual Windows especializadas que se ejecuta en Azure. A continuación, puede usar la copia del disco duro virtual para crear una nueva máquina virtual. 

- Si desea copiar una VM generalizada, consulte [cómo crear una imagen de VM desde una máquina virtual de Azure generalized existente](virtual-machines-windows-capture-image.md).

- Si desea cargar un disco duro virtual de una máquina virtual local, como creados con Hyper-V, consulte [cargar un disco duro virtual de Windows desde una máquina virtual de local a Azure](virtual-machines-windows-upload-image.md).


## <a name="before-you-begin"></a>Antes de empezar

Asegúrese de:

- Tiene información acerca de las **cuentas de almacenamiento de origen y de destino**. Para el origen de la máquina virtual, necesitará los nombres de cuenta y contenedor de almacenamiento. Normalmente, el nombre del contenedor será **VHD**. También debe tener una cuenta de almacenamiento de destino. Si ya no tiene una, puede crearla con el portal de ambos (**Más servicios** > cuentas de almacenamiento > Agregar) o mediante el cmdlet [AzureRmStorageAccount de nuevo](https://msdn.microsoft.com/library/mt607148.aspx) . 

- Tienen Azure [PowerShell 1.0](../powershell-install-configure.md) (o posterior) instalado.

- Descargar e instalar la [herramienta de AzCopy](../storage/storage-use-azcopy.md). 


## <a name="deallocate-the-vm"></a>Desasignar la máquina virtual

Cancelar la asignación de la máquina virtual, que libera el disco duro virtual para copiar. 

- **Portal**: haga clic en **máquinas virtuales** > **myVM** > Detener
- **PowerShell**: `Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM` cancela la asignación de la máquina virtual denominada **myVM** en el grupo de recursos **myResourceGroup**.

Cambia el **estado** de la máquina virtual en el portal de Azure de **Stopped** a **Stopped (asignación)**.


## <a name="get-the-storage-account-urls"></a>Obtener la URL de la cuenta de almacenamiento

Es necesario que las direcciones URL de las cuentas de almacenamiento de origen y de destino. El aspecto de las direcciones URL: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Si ya conoce el nombre de cuenta y contenedor de almacenamiento, solo puede reemplazar la información entre los corchetes para crear la dirección URL. 

Puede usar el portal de Azure o Azure Powershell para obtener la dirección URL:

- **Portal**: haga clic en **más servicios** > **cuentas de almacenamiento**  >  <storage account> **BLOB** y el archivo de disco duro virtual de origen esté en el contenedor de **VHD** . Haga clic en **Propiedades** para el contenedor y copie el texto de la etiqueta de **dirección URL**. Necesitará las direcciones URL de los contenedores de origen y de destino. 

- **PowerShell**: `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` Obtiene la información de máquina virtual denominado **myVM** en el grupo de recursos **myResourceGroup**. En los resultados, busque en la sección de **perfil de almacenamiento** para el **Disco duro virtual Uri**. La primera parte del Uri es la dirección URL al contenedor y la última parte es el nombre de disco duro virtual de sistema operativo de la máquina virtual.

## <a name="get-the-storage-access-keys"></a>Obtener las teclas de acceso de almacenamiento

Busque las teclas de acceso de origen y destino cuentas de almacenamiento. Para obtener más información acerca de las teclas de acceso, vea [cuentas de almacenamiento de Azure sobre](../storage/storage-create-storage-account.md).

- **Portal**: haga clic en **más servicios** > **cuentas de almacenamiento**  >  <storage account> **Toda la configuración** > **teclas de acceso**. Copie la clave marcada como **clave1**.
- **PowerShell**: `Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup` Obtiene la clave de almacenamiento para la cuenta de almacenamiento **mystorageaccount** en el grupo de recursos **myResourceGroup**. Copie la clave marcada como **clave1**.


## <a name="copy-the-vhd"></a>Copiar el disco duro virtual 

Puede copiar archivos entre cuentas de almacenamiento usando AzCopy. Para el contenedor de destino, si el contenedor especificado no existe, se creará para usted. 

Para usar AzCopy, abra un símbolo del sistema en el equipo local y desplácese a la carpeta donde está instalado AzCopy. Será similar a *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Para copiar todos los archivos dentro de un contenedor, utilice el modificador **/S** . Esto puede utilizarse para copiar el disco duro virtual de sistema operativo y todos los discos de datos si se encuentran en el mismo contenedor. Este ejemplo muestra cómo copiar todos los archivos en el contenedor **mysourcecontainer** de almacenamiento cuenta **mysourcestorageaccount** en el contenedor **mydestinationcontainer** en la cuenta de almacenamiento de **mydestinationstorageaccount** . Reemplace los nombres de las cuentas de almacenamiento y contenedores con su propio. Reemplazar `<sourceStorageAccountKey1>` y `<destinationStorageAccountKey1>` con sus propio teclas.

```
    AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
        /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
        /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Si solo desea copiar un disco duro virtual específico en un contenedor con varios archivos, también puede especificar el nombre de archivo con el modificador /Pattern. En este ejemplo, se copiarán sólo el archivo con el nombre **myFileName.vhd** .

```
    AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
        /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
        /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
        /Pattern:myFileName.vhd
```


Cuando haya finalizado, recibirá un mensaje que tiene un aspecto similar:

```
  Finished 2 of total 2 file(s).
  [2016/10/07 17:37:41] Transfer summary:
  -----------------
  Total files transferred: 2
  Transfer successfully:   2
  Transfer skipped:        0
  Transfer failed:         0
  Elapsed time:            00.00:13:07
```

## <a name="troubleshooting"></a>Solución de problemas

- Cuándo usar AZCopy, si ve el error "el servidor no se puede autenticar la solicitud. Asegúrese de que el valor del encabezado de autorización se ha creado correctamente, incluidas la firma." y está usando 2 de clave o la clave de almacenamiento secundario, pruebe a usar la clave principal o 1ª almacenamiento.


## <a name="next-steps"></a>Pasos siguientes

- Puede crear una nueva máquina virtual adjuntando [la copia del disco duro virtual a una máquina virtual como un disco de sistema operativo](virtual-machines-windows-create-vm-specialized.md).












