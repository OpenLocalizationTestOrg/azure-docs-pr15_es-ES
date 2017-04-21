<properties
   pageTitle="Administrar sus máquinas virtuales con PowerShell de Azure | Microsoft Azure"
   description="Obtenga información sobre los comandos que puede usar para automatizar tareas de administración de sus máquinas virtuales."
   services="virtual-machines-windows"
   documentationCenter="windows"
   authors="singhkays"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/12/2016"
   ms.author="kasing"/>

# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Administrar sus máquinas virtuales con PowerShell de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Realice cada día para administrar sus máquinas virtuales muchas de las tareas se pueden automatizar mediante cmdlets de PowerShell de Azure. Este artículo proporciona ejemplos de comandos de las tareas más sencillas y vínculos a artículos que muestren los comandos para tareas más complejas.

>[AZURE.NOTE] Si no ha instalado y configurado Azure PowerShell todavía, puede obtener instrucciones en el artículo [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

## <a name="how-to-use-the-example-commands"></a>Cómo usar los comandos de ejemplo
Debe reemplazar parte del texto en los comandos con texto que es adecuado para su entorno. La < y > símbolos indican el texto que desea reemplazar. Cuando se reemplaza el texto, quite los símbolos pero dejar las comillas en su lugar.

## <a name="get-a-vm"></a>Obtener una máquina virtual
Se trata de una tarea básica que usará con frecuencia. Utilizar para obtener información acerca de una máquina virtual, realizar tareas en una máquina virtual o un resultado para almacenar en una variable.

Para obtener información acerca de la máquina virtual, ejecute este comando, reemplace todo el contenido de las comillas, incluidos los caracteres < y >:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Para almacenar los resultados en una variable $vm, ejecute:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Inicie sesión en una máquina virtual basado en Windows

Ejecute estos comandos:

>[AZURE.NOTE] Puede obtener el nombre de servicio de máquina virtual y nube desde la pantalla del comando **Get-AzureVM** .
>
    $svcName = "<cloud service name>"
    $vmName = "<virtual machine name>"
    $localPath = "<drive and folder location to store the downloaded RDP file, example: c:\temp >"
    $localFile = $localPath + "\" + $vmname + ".rdp"
    Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## <a name="stop-a-vm"></a>Detener una máquina virtual

Ejecute este comando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Use este parámetro para mantener la IP virtual (VIP) del servicio de nube en caso de que es el última VM en ese servicio de nube. <br><br> Si utiliza el parámetro StayProvisioned, se le facturará para la máquina virtual.

## <a name="start-a-vm"></a>Iniciar una máquina virtual

Ejecute este comando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Adjuntar un disco de datos
Esta tarea requiere algunos pasos. En primer lugar, use la *** Add-AzureDataDisk *** cmdlet para agregar el disco al objeto $vm. A continuación, usar cmdlet **AzureVM actualizar** para actualizar la configuración de la máquina virtual.

También deberá decidir si desea adjuntar un disco nuevo o uno que contiene datos. Para un disco nuevo, el comando crea el archivo .vhd y lo adjunta.

Para adjuntar un disco nuevo, ejecute este comando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Para adjuntar un disco de datos existente, ejecute este comando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Para adjuntar discos de datos desde un archivo .vhd existente en el almacenamiento de blobs, ejecute este comando:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Crear una máquina virtual basado en Windows

Para crear una nueva máquina virtual basado en Windows en Azure, utilice las instrucciones de [PowerShell de Azure usar para crear y configurar previamente máquinas virtuales basadas en Windows](virtual-machines-windows-classic-create-powershell.md). Pasos de este tema a través de la creación de un conjunto de comandos que de Azure PowerShell crea una máquina virtual basado en Windows que puede ser preconfigurada:

- Con la suscripción de dominio de Active Directory.
- Con discos adicionales.
- Como miembro de un equilibrio de carga existente establecer.
- Con una dirección IP estática.
