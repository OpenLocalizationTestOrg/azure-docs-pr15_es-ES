<properties 
   pageTitle="Comandos de PowerShell comunes para máquinas virtuales | Microsoft Azure"
   description="Comandos de PowerShell comunes para empezar a crear y administrar sus máquinas virtuales en Azure en Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="davidmu1" 
   manager="timlt" 
   editor="tysonn" 
   tags="azure-resource-manager"/>
   
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="09/27/2016"
   ms.author="davidmu" />

# <a name="common-powershell-commands-for-creating-and-managing-vms"></a>Comandos de PowerShell comunes para crear y administrar máquinas virtuales

Este artículo describen algunos de los comandos de PowerShell de Azure que puede usar para crear y administrar máquinas virtuales de su suscripción de Azure.  Para obtener ayuda detallada con opciones y modificadores específicos, puede usar el *comando*de **Obtener ayuda** .

Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener información sobre cómo instalar la última versión de PowerShell de Azure, seleccionar la suscripción e iniciar sesión con su cuenta.

## <a name="create-a-vm"></a>Crear una máquina virtual

Tarea | Comando
-------------- | -------------------------
Crear una configuración de VM | $vm = [Nuevo AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) - VMName "vm_name" - VMSize "vm_size"<BR></BR><BR></BR>La configuración de la máquina virtual se usa para definir o actualizar la configuración de la máquina virtual. La configuración se ha inicializado con el nombre de la máquina virtual y su [tamaño](virtual-machines-windows-sizes.md).
Agregar opciones de configuración | $vm = [AzureRmVMOperatingSystem Set](https://msdn.microsoft.com/library/mt603843.aspx) - VM $vm-Windows - ComputerName "NombreDeEquipo"-credenciales $cred - ProvisionVMAgent - EnableAutoUpdate<BR></BR><BR></BR>Configuración de sistema operativo, incluidas [las credenciales](https://technet.microsoft.com/library/hh849815.aspx) se agrega al objeto de configuración que creó anteriormente con AzureRmVMConfig de nuevo.
Agregar una interfaz de red | $vm = [AzureRmVMNetworkInterface Add](https://msdn.microsoft.com/library/mt619351.aspx) - VM $vm-NIC de Id $. Id.<BR></BR><BR></BR>Una máquina virtual debe tener una [interfaz de red](virtual-machines-windows-ps-create.md) para comunicarse en una red virtual. También puede usar [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) para recuperar un objeto de interfaz de red existente.
Especificar una imagen de la plataforma | $vm = [AzureRmVMSourceImage Set](https://msdn.microsoft.com/library/mt619344.aspx) - VM $vm - PublisherName "publisher_name"-ofrecen "publisher_offer" - SKU "product_sku"-versión "más reciente"<BR></BR><BR></BR>[Información de la imagen](virtual-machines-windows-cli-ps-findimage.md) se agrega al objeto de configuración que creó anteriormente con AzureRmVMConfig de nuevo. El objeto devuelto por este comando solo se utiliza cuando se establece el disco de sistema operativo para usar una imagen de la plataforma.
Establecer el disco de sistema operativo para usar una imagen de la plataforma | $vm = [AzureRmVMOSDisk Set](https://msdn.microsoft.com/library/mt603746.aspx) - VM $vm-nombre "nombre_disco" - VhdUri "http://mystore1.blob.core.windows.net/vhds/disk_name.vhd" - CreateOption FromImage<BR></BR><BR></BR>Se agrega el nombre del disco de sistema operativo y su ubicación de [almacenamiento](../storage/storage-powershell-guide-full.md) para el objeto de configuración que creó anteriormente.
Configurar OS del disco para usar una imagen general | $vm = AzureRmVMOSDisk set - VM $vm-nombre "nombre_disco" - SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk. {guid} .vhd"- VhdUri"https://mystore1.blob.core.windows.net/vhds/disk_name.vhd"- CreateOption FromImage-Windows<BR></BR><BR></BR>Se agrega el nombre del disco de sistema operativo, la ubicación de la imagen de origen y ubicación del disco de [almacenamiento](../storage/storage-powershell-guide-full.md) para el objeto de configuración.
Configurar OS del disco para usar una imagen especializada | $vm = AzureRmVMOSDisk set - VM $vm-adjuntar en el nombre "name_of_disk" - VhdUri "http://mystore1.blob.core.windows.net/vhds/" - CreateOption - Windows
Crear una máquina virtual | [Nuevo AzureRmVM]() - ResourceGroupName "resource_group_name"-ubicación "location_name" - VM $vm<BR></BR><BR></BR>Todos los recursos se crean en un [grupo de recursos](../powershell-azure-resource-manager.md). La máquina virtual debe crearse en la misma [ubicación](https://msdn.microsoft.com/library/azure/dn495177.aspx) que el grupo de recursos. Antes de ejecutar este comando, ejecutar AzureRmVMConfig de nuevo, conjunto AzureRmVMOperatingSystem, conjunto AzureRmVMSourceImage, agregar AzureRmVMNetworkInterface y AzureRmVMOSDisk conjunto.

## <a name="get-information-about-vms"></a>Obtener información acerca de las VM

Tarea | Comando
-------------- | -------------------------
Máquinas virtuales de lista en una suscripción| [Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx)
Máquinas virtuales de lista en un grupo de recursos | Get-AzureRmVM - ResourceGroupName "resource_group_name"<BR></BR><BR></BR>Para obtener una lista de grupos de recursos en la suscripción, use [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx).
Obtener información sobre una máquina virtual | Get-AzureRmVM - ResourceGroupName "resource_group_name"-vm_name"nombre"

## <a name="manage-vms"></a>Administración de máquinas virtuales

Tarea | Comando
-------------- | -------------------------
Iniciar una máquina virtual | [Inicio AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) - ResourceGroupName "resource_group_name"-vm_name"nombre"
Detener una máquina virtual | [Detener AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) - ResourceGroupName "resource_group_name"-vm_name"nombre"
Reiniciar una VM en ejecución | [Reiniciar AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) - ResourceGroupName "resource_group_name"-vm_name"nombre"
Eliminar una máquina virtual | [Quitar AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) - ResourceGroupName "resource_group_name"-vm_name"nombre"
Generalizar una máquina virtual | [Establecer AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) - ResourceGroupName YourResourceGroup-vm_name"nombre"-generalizado<BR></BR><BR></BR>Ejecutar este comando antes de ejecutar AzureRmVMImage guardar.
Capturar una máquina virtual | [Guardar AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) - ResourceGroupName "resource_group_name" - VMName "vm_name" - DestinationContainerName "image_container" - VHDNamePrefix "image_name_prefix"-"C:\filepath\filename.json" de la ruta de acceso<BR></BR><BR></BR>Debe ser una máquina virtual [Apagar y generalizado](virtual-machines-windows-generalize-vhd.md) que se utilizará para crear una imagen. Antes de ejecutar este comando, ejecute Set-AzureRmVm.
Actualizar una máquina virtual | [Actualización AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) - ResourceGroupName "resource_group_name" - VM $vm<BR></BR><BR></BR>Obtener la configuración actual de VM con Get-AzureRmVM, cambiar la configuración en el objeto de máquina virtual y, a continuación, ejecutar este comando.
Agregar un disco de datos a una máquina virtual | [Agregar AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) - VM $vm-nombre "nombre_disco" - VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" LUN-#-almacenamiento en caché ReadWrite - DiskSizeinGB # - CreateOption vacío<BR></BR><BR></BR>Use Get-AzureRmVM para obtener el objeto de máquina virtual. Especifique el número de LUN y el tamaño del disco. Ejecutar la actualización AzureRmVM para aplicar los cambios de configuración para la máquina virtual. El disco que agregue no está inicializado. Para obtener información acerca de inicialización discos a medida que se agregan, vea [administrar máquinas virtuales de Azure con el Administrador de recursos y PowerShell](virtual-machines-windows-ps-manage.md).
Quitar un disco de datos de una máquina virtual | [Quitar AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) - VM $vm-nombre_disco"nombre"<BR></BR><BR></BR>Use Get-AzureRmVM para obtener el objeto de máquina virtual. Ejecutar la actualización AzureRmVM para aplicar los cambios de configuración para la máquina virtual.
Agregar una extensión a una máquina virtual | [Establecer AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) - ResourceGroupName "resource_group_name"-ubicación "azure_location" - VMName "vm_name"-extension_name"nombre"-Publisher "publisher_name"-tipo "extension_type" - TypeHandlerVersion "#. #"-configuración $Settings - ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Ejecutar este comando con la [información de configuración](virtual-machines-windows-extensions-configuration-samples.md) adecuada para la extensión que desea instalar.
Quitar una extensión de VM | [Quitar AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) - ResourceGroupName "resource_group_name"-nombre "extension_name" - VMName "vm_name"

## <a name="next-steps"></a>Pasos siguientes

- Consulte los pasos básicos para crear una máquina virtual en [crear una máquina virtual de Windows con el Administrador de recursos y PowerShell](virtual-machines-windows-ps-create.md).

