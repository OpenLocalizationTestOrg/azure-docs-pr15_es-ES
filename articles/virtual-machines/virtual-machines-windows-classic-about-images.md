<properties
    pageTitle="Acerca de las imágenes para máquinas virtuales de Windows | Microsoft Azure"
    description="Obtenga información sobre cómo se usan las imágenes con máquinas virtuales de Windows en Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/21/2016"
    ms.author="cynthn"/>

# <a name="about-images-for-windows-virtual-machines"></a>Acerca de las imágenes para máquinas virtuales de Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]



## <a name="working-with-images"></a>Trabajar con imágenes

Puede usar el módulo de PowerShell de Azure para administrar las imágenes disponibles para su suscripción de Azure. También puede usar el portal de clásico Azure para algunas tareas de imagen, pero la línea de comandos le ofrece más opciones.


-   **Obtener todas las imágenes**:`Get-AzureVMImage`devuelve una lista de todas las imágenes disponibles en su suscripción actual: las imágenes, así como los proporcionados por Azure o socios. Esto significa que obtendría una lista grande. Los ejemplos siguientes muestran cómo obtener una lista más corta.
-   **Obtener familias de imagen**:`Get-AzureVMImage | select ImageFamily` Obtiene una lista de familias de imagen mostrando cadenas **ImageFamily** propiedad.
-   **Obtener todas las imágenes en una familia específica**:`Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-   **Buscar imágenes de máquina virtual**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` esto funciona filtrando la propiedad DataDiskConfiguration, que solo se aplica a las imágenes de máquina virtual. En este ejemplo también se filtra el resultado solo el nombre de etiqueta y la imagen.
-   **Guardar una imagen general**:`Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-   **Guardar una imagen especializada**:`Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip] Si desea crear una imagen de máquina virtual, que incluye discos de datos, así como el disco de sistema operativo, se requiere el parámetro de OSState. Si no usa el parámetro, el cmdlet crea una imagen de sistema operativo. El valor del parámetro indica si la imagen está generalizada o especializada, en función de si se ha preparado el disco de sistema operativo para volver a usarlo.
-   **Eliminar una imagen**:`Remove-AzureVMImage –ImageName "MyOldVmImage"`


## <a name="next-steps"></a>Pasos siguientes

También puede [crear una máquina de Windows con el portal clásico](virtual-machines-windows-classic-tutorial.md)

