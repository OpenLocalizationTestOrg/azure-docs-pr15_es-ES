<properties
   pageTitle="Configurar PowerShell para crear una máquina virtual para el catálogo de soluciones | Microsoft Azure"
   description="Instrucciones para configurar Azure PowerShell y usarlo como un proceso opcional de flujo para crear imágenes de máquina virtual para implementar y venden en Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/04/2016"
   ms.author="hascipio"/>

# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Configurar Azure PowerShell para crear una oferta de Azure Marketplace
Para obtener información detallada sobre cómo configurar PowerShell en Azure, consulte [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md). Un enfoque sencillo es usar el método de certificados, que se descarga e importa un certificado que sea necesario para la autenticación. Para obtener el certificado que sea necesario, use el cmdlet **Get-AzurePublishSettingsFile** . Cuando se le solicite, guarde el archivo. Para importar el certificado en una sesión PowerShell, use el cmdlet **AzurePublishSettingsFile de importación** .

Para configurar y almacenar la configuración de suscripción de Microsoft Azure comunes para la sesión de PowerShell, use los cmdlets **Set-AzureSubscription** y **Seleccione AzureSubscription** :

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

El primer comando asocia una cuenta de almacenamiento predeterminada a la suscripción (es necesaria para algunas operaciones de aprovisionamiento de VM).  El segundo convierte la suscripción el actual (reconoce otros cmdlets).

## <a name="see-also"></a>Vea también
- [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)
- [Crear una imagen de máquina virtual para el catálogo de soluciones](marketplace-publishing-vm-image-creation.md)
