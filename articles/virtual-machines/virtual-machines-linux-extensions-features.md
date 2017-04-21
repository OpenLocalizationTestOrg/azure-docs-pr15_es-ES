<properties
 pageTitle="Características y extensiones de máquina virtual | Microsoft Azure"
 description="Obtenga información sobre qué extensiones están disponibles para Azure máquinas virtuales, agrupados por lo que proporcionan o mejoran."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="neilpeterson"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/22/2016"
 ms.author="nepeters"/>

# <a name="about-virtual-machine-extensions-and-features"></a>Sobre las características y extensiones de máquina virtual

## <a name="azure-vm-extensions"></a>Extensiones de Azure VM

Extensiones de máquina Virtual Azure son pequeñas aplicaciones que proporcionan tareas de configuración y automatización de implementación de publicación en Azure máquinas virtuales de Windows. Por ejemplo, si una máquina Virtual requiere software de protección antivirus instalada o configuración de Docker, una extensión de VM puede usarse para realizar estas tareas. Extensiones de Azure VM se pueden ejecutar mediante la CLI de Azure, PowerShell, administrar recursos plantillas y el portal de Azure. Extensiones pueden incluidas con una implementación de máquina virtual nuevo o se ejecutar en cualquier sistema existente.

Este documento proporciona los requisitos previos para extensión de máquina Virtual de Azure e instrucciones sobre cómo detectar extensiones VM disponibles. 

## <a name="azure-vm-agent"></a>Agente de Azure VM

El agente de Azure VM administra la interacción entre una máquina Virtual de Azure y el controlador de tela de Azure. El agente VM es responsable de varios aspectos funcionales de implementación y administración de máquinas virtuales de Azure, incluida la ejecución de extensiones de máquina virtual. El agente de Azure VM está preinstalado en imágenes de la Galería de Azure y se puede instalar en sistemas operativos compatibles. 

Para obtener información sobre las instrucciones de instalación y sistemas operativos compatibles, consulte [Guía del usuario de agente de Azure Linux](./virtual-machines-linux-agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Descubra las extensiones VM

Muchas extensiones VM diferentes están disponibles para su uso con Azure máquinas virtuales de Windows. Para ver una lista completa, ejecute el comando siguiente con la CLI de Azure, reemplazar la ubicación con la ubicación que prefiera.

```none
azure vm extension-image list westus
```

<br />

## <a name="common-vm-extensions"></a>Extensiones VM comunes

|Nombre de la extensión   |Descripción   |Más información   |
|---|---|---|
|Extensión de scripts personalizados para Linux  | Ejecutar secuencias de comandos con una máquina Virtual de Azure  |[Extensión de scripts personalizados para Linux](./virtual-machines-linux-extensions-customscript.md)   |
|Extensión de docker |Instala el daemon Docker para admitir comandos Docker remotos.  | [Extensión VM docker](./virtual-machines-linux-dockerextension.md)  |
|Extensión de acceso VM | Recuperar el acceso a máquina Virtual de Azure  |[Extensión de acceso VM](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
|Extensión de diagnóstico de Azure |Administrar diagnóstico de Azure |[Extensión de diagnóstico de Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |

