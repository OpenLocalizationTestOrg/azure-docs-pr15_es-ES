<properties
   pageTitle="Creación de plantillas con las extensiones de máquina virtual de Windows | Microsoft Azure"
   description="Obtenga más información sobre la creación de plantillas de administrador de recursos de Azure con extensiones para máquinas virtuales de Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="authoring-azure-resource-manager-templates-with-windows-vm-extensions"></a>Creación de plantillas de administrador de recursos de Azure con extensiones de máquina virtual de Windows

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

De Azure PowerShell, ejecute el siguiente cmdlet de PowerShell de Azure:

      Get-AzureVMAvailableExtension


Este cmdlet devuelve el nombre del Editor, el nombre de la extensión y la versión como sigue:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Asignan estas tres propiedades "publisher", "tipo" y "typeHandlerVersion" respectivamente en el fragmento de código de la plantilla anterior.

>[AZURE.NOTE]Siempre se recomienda usar la última versión de extensión para obtener la funcionalidad más actualizada.

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>Identifica el esquema para los parámetros de configuración de extensión

El paso siguiente con la creación de una plantilla de extensión es identificar el formato para proporcionar parámetros de configuración. Cada extensión es compatible con su propio conjunto de parámetros.

Para ver ejemplos de configuraciones para las extensiones de Windows, vea [ejemplos de extensiones de Windows](virtual-machines-windows-extensions-configuration-samples.md).


Consulte los procedimientos siguientes para obtener una plantilla se completará con extensiones de máquina virtual.

[Extensión de scripts personalizados en una máquina virtual de Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)


Después de crear la plantilla, se puede implementar con PowerShell de Azure.
