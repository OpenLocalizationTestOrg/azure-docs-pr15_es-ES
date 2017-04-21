<properties
    pageTitle="Diferentes maneras de crear una máquina virtual de Windows | Microsoft Azure"
    description="Enumera las diferentes formas de crear una máquina virtual de Windows con el Administrador de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="different-ways-to-create-a-windows-virtual-machine-with-resource-manager"></a>Diferentes maneras de crear una máquina virtual de Windows con el Administrador de recursos

Azure ofrece varias formas de crear una máquina virtual porque máquinas virtuales son adecuadas para diferentes usuarios y propósitos. Esto significa que debe realizar varias opciones acerca de la máquina virtual y cómo crearlo. En este artículo se proporciona un resumen de estas opciones y vínculos a las instrucciones.

## <a name="azure-portal"></a>Portal de Azure

Con el portal de Azure es una forma sencilla de probar una máquina virtual, especialmente si simplemente, está empezando con Azure. 

[Crear una máquina virtual ejecuta Windows con el portal](virtual-machines-windows-hero-tutorial.md)

## <a name="template"></a>Plantilla

Máquinas virtuales requieren una combinación de recursos (por ejemplo, una disponibilidad cuentas de almacenamiento y conjuntos). En lugar de implementar y administrar cada recurso por separado, puede crear una plantilla de administrador de recursos de Azure que implementa y aprovisiona todos los recursos en una única operación coordinado.

- [Crear una máquina virtual de Windows con una plantilla de administrador de recursos](virtual-machines-windows-ps-template.md)


## <a name="azure-powershell"></a>Azure PowerShell

Si prefiere trabajar en un shell de comandos, puede usar PowerShell de Azure.

- [Crear una máquina virtual de Windows con PowerShell](virtual-machines-windows-ps-create.md)


## <a name="visual-studio"></a>Visual Studio

Use Visual Studio para crear, administrar e implementar máquinas virtuales con las herramientas de Azure para Visual Studio y el SDK de Azure.

[Azure Tools para Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)

