<properties
    pageTitle="Crear una imagen VM desde una máquina virtual de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo crear una imagen de máquina virtual generalized desde una máquina virtual de Azure existente creado en el modelo de implementación de administrador de recursos"
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
    ms.date="10/10/2016"
    ms.author="cynthn"/>


# <a name="download-the-template-for-a-vm"></a>Descargar la plantilla para una máquina virtual

Cuando se crea una máquina virtual en Azure con el portal o PowerShell, se creará automáticamente una plantilla de administrador de recursos. Puede usar esta plantilla para duplicar rápidamente una implementación. La plantilla contiene información sobre todos los recursos en un grupo de recursos. Para una máquina virtual, significa que los contenedores de plantilla todo lo que se crea en apoyo de la máquina virtual de ese grupo de recursos, incluidos los recursos de red.

## <a name="download-the-template-using-the-portal"></a>Descargar la plantilla con el portal

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2. En el menú de concentrador, seleccione **máquinas virtuales de Windows**.
3. Seleccione la máquina virtual de la lista.
5. Seleccione la **secuencia de comandos de automatización**.
6. Seleccione **Descargar** y guardar el archivo .zip en el equipo local.
7. Abra el archivo .zip y extraer los archivos a una carpeta. El archivo .zip contiene:
    
    - Deploy.ps1
    - Deploy.sh 
    - deployer.RB
    - DeploymentHelper.cs
    - Parameters.JSON
    - Template.JSON

El archivo .json es la plantilla.
    
## <a name="download-the-template-using-powershell"></a>Descargar la plantilla con PowerShell

También puede descargar el archivo de plantilla de .json mediante el cmdlet [AzureRMResourceGroup de exportación](https://msdn.microsoft.com/library/mt715427.aspx) . Puede usar el `-path` parámetro para proporcionar el nombre de archivo y la ruta de acceso del archivo .json. Este ejemplo muestra cómo descargar la plantilla para el grupo de recursos denominado **myResourceGroup** a la carpeta **C:\users\public\downloads** en el equipo local.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo implementar plantillas de recursos, consulte [el tutorial de plantilla de administrador de recursos](../resource-manager-template-walkthrough.md).