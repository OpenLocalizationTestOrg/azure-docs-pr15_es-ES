<properties
    pageTitle="Usar plantillas de administrador de recursos de Azure en la pila de Azure (desarrolladores inquilino) | Microsoft Azure"
    description="Obtenga información sobre cómo usar las plantillas de administrador de recursos de Azure en pila de Azure a implementar y aprovisionar todos los recursos para la aplicación en una única operación coordinada."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="helaw"/>

# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Usar plantillas de administrador de recursos de Azure en la pila de Azure

Plantillas de administrador de recursos de Azure implementación y aprovisionar todos los recursos para la aplicación en una única operación coordinada. Definir los recursos de la aplicación y cómo se implementará.  También puede implementar plantillas para realizar cambios en los recursos del grupo de recursos.

Estas plantillas se pueden implementar con el portal de Microsoft Azure pila, PowerShell, la línea de comandos y Visual Studio.

[AZURE.VIDEO microsoft-azure-stack-tp1--foundational-skills-1-deploying-json-templates]

Las siguientes plantillas están disponibles en [GitHub](http://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-non-high-availability"></a>Implementar SharePoint (sin alta disponibilidad)

Usar la extensión DSC de PowerShell para crear una granja de SharePoint 2013 que incluye lo siguiente:

-   Una red virtual

-   Tres cuentas de almacenamiento

-   Dos equilibradores de carga externos

-   Una VM está configurada como un controlador de dominio para un nuevo bosque con un dominio único

-   Una VM está configurada como un servidor independiente de SQL Server de 2014

-   Una VM está configurada como un conjunto de servidores de SharePoint 2013 de un equipo

## <a name="deploy-ad-non-high-availability"></a>Implementar AD (sin alta disponibilidad)

Usar la extensión DSC de PowerShell para crear un servidor de controlador de dominio de AD que incluye lo siguiente:

-   Una red virtual

-   Una cuenta de almacenamiento

-   Un equilibrador de carga externo

-   Una VM está configurada como un controlador de dominio para un nuevo bosque con un dominio único

## <a name="deploy-adsql-non-high-availability"></a>Implementar Active Directory y SQL (sin alta disponibilidad)

Usar la extensión DSC de PowerShell para crear un servidor de SQL Server 2014 independiente que incluye lo siguiente:

-   Una red virtual

-   Dos cuentas de almacenamiento

-   Un equilibrador de carga externo

-   Una VM está configurada como un controlador de dominio para un nuevo bosque con un dominio único

-   Una VM está configurada como un servidor independiente de SQL Server de 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Utilice la extensión DSC de PowerShell para configurar una máquina virtual existente Administrador de configuración Local (LCM) y registrar a un servidor de extracción de Azure automatización cuenta DSC.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Crear una máquina virtual de la imagen de un usuario

Crear una máquina virtual de una imagen de usuario personalizada. Esta plantilla también implementa una red virtual (con DNS), dirección IP pública y una interfaz de red.

## <a name="simple-vm"></a>Máquina virtual de simple

Implementar un simple VM de Windows que incluye una red virtual (con DNS), dirección IP pública y una interfaz de red.

## <a name="cancel-a-running-template-deployment"></a>Cancelar una ejecución implementación de plantilla

Para cancelar una implementación de plantilla está ejecutando, utilice la `Stop-AzureRmResourceGroupDeployment` cmdlet de PowerShell.


## <a name="next-steps"></a>Pasos siguientes

[Implementar plantillas con el portal](azure-stack-deploy-template-portal.md)

[Introducción al administrador de recursos Azure](../azure-resource-manager/resource-group-overview.md)

