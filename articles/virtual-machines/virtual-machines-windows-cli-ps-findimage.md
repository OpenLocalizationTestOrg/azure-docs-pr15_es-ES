<properties
   pageTitle="Navegar y seleccionar imágenes de máquina virtual de Windows | Microsoft Azure"
   description="Obtenga información sobre cómo determinar la publisher, oferta y SKU de imágenes al crear una máquina virtual de Windows con el modelo de implementación de administrador de recursos."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell-or-the-cli"></a>Desplácese en y seleccione imágenes de máquina virtual de Windows Azure con PowerShell o CLI

Este tema describe cómo buscar versiones, SKU, ofertas y editores de imagen de máquina virtual para cada ubicación en la que puede implementar. Para dar un ejemplo, algunas imágenes de máquina virtual de Windows utilizados con frecuencia son:

## <a name="table-of-commonly-used-windows-images"></a>Tabla utilizadas con frecuencia de imágenes de Windows


| NOMBREDEPUBLICADOR                        | Oferta                                 | SKU                         |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| MicrosoftDynamicsNAV             | DynamicsNAV                                | 2015                             |
| MicrosoftSharePoint              | MicrosoftSharePointServer                  | 2013                             |
| MicrosoftSQLServer               | SQL2014 WS2012R2                           | Enterprise-optimizado-para-DW      |
| MicrosoftSQLServer               | SQL2014 WS2012R2                           | Enterprise-optimizado-para-OLTP    |
| Microsoft Windows Server           | Windows Server                              | Centro de datos 2012-R2                  |
| Microsoft Windows Server           | Windows Server                              | Centro de datos de 2012               |
| Microsoft Windows Server           | Windows Server                              | SP1 DE 2008 R2 |
| Microsoft Windows Server           | Windows Server                              | Windows Server-técnicos: vista previa |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials                    | WindowsServerEssentials          |
| MicrosoftWindowsServerHPCPack    | WindowsServerHPCPack                       | 2012R2                           |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]
