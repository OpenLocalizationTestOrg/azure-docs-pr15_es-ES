<properties
   pageTitle="Crear una identidad profesional o educativa en AAD | Microsoft Azure"
   description="Obtenga información sobre cómo crear una identidad profesional o educativa en Azure Active Directory para usarlos con las máquinas virtuales de Linux."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="creating-a-work-or-school-identity-in-azure-active-directory-to-use-with-linux-vms"></a>Creación de una identidad de trabajo o la escuela en Azure Active Directory para usarlos con máquinas virtuales de Linux

Si creó una cuenta personal de Azure o tiene una suscripción de MSDN personal y crear la cuenta de Azure para aprovechar las ventajas de créditos de Azure de MSDN: utiliza una identidad de la *cuenta de Microsoft* para crearlo. Muchas características de Azure-- [plantillas de grupo de recursos](../azure-resource-manager/resource-group-overview.md) es un ejemplo: requiere una cuenta de trabajo o escuela (una identidad administrado por Azure Active Directory) para que funcione. Puede seguir las instrucciones siguientes para crear una nueva cuenta profesional o educativa porque Afortunadamente, una de las mejores cosas sobre su cuenta de Azure personal es que se suministra con un dominio de Azure Active Directory predeterminada que puede usar para crear una nueva cuenta profesional o educativa que puede usar con las características de Azure que lo requieren.

Sin embargo, los cambios recientes hacen posible administrar su suscripción con cualquier tipo de cuenta de Azure mediante la `azure login` método de inicio de sesión interactivo descrito [a continuación](../xplat-cli-connect.md). Puede usar ese mecanismo o puede seguir las instrucciones que le siguen. También puede [crear un trabajo o escuela identidad de Azure Active Directory para usarlos con máquinas virtuales de Windows](virtual-machines-windows-create-aad-work-id.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-create-aad-work-id](../../includes/virtual-machines-common-create-aad-work-id.md)]
