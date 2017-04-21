<properties
   pageTitle="Usar cmdlets de PowerShell con RemoteApp de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo usar los cmdlets de Windows PowerShell en Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>



# <a name="use-windows-powershell-cmdlets-with-azure-remoteapp"></a>Usar cmdlets de Windows PowerShell con RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

 Puede usar los cmdlets de RemoteApp PowerShell de Azure para administrar y mantener las colecciones. Use la información siguiente para empezar.

## <a name="get-the-cmdlets"></a>Obtener los cmdlets 
-------------
Primero, descargue los cmdlets de Powershell de Azure [aquí](http://go.microsoft.com/?linkid=9811175), el RemoteApp cmdlets que se incluyen en ella. 

Consulte la [Ayuda de cmdlet de RemoteApp de Azure](https://msdn.microsoft.com/library/mt428031.aspx).

## <a name="configure-azure-cmdlets-to-use-your-subscription"></a>Configurar Azure cmdlets para usar su suscripción
------------------
Siga [esta guía](../powershell-install-configure.md) para poder usar los cmdlets frente a su suscripción de Azure.

Puede utilizar estos pasos para empezar rápidamente:

1.  Descargar e instalar los [cmdlets de PowerShell de Azure](http://go.microsoft.com/?linkid=9811175).
2.  Inicie Microsoft Azure PowerShell.
3.  Ejecutar **Complemento AzureAccount** para autenticar a su suscripción de Azure. Cuando se le pida, escriba el mismo nombre de usuario y la contraseña que utiliza para iniciar sesión en el portal de Azure.  
4.  Ejecutar **Get-AzureSubscription** para obtener una lista de las suscripciones asociadas con su cuenta de usuario. 
5.  Ejecute **AzureSubscription seleccione** y especifique el nombre de la suscripción o el identificador que utilizar en la consola de PowerShell.

Enhorabuena, la consola de Azure PowerShell está configurado y listo para usar. Tenga en cuenta que deberá repeate pasos 2 a 5 cada vez que inicie la consola de PowerShell de Azure.  

## <a name="create-a-cloud-collection"></a>Crear una colección de nube
--------------------
Es sencillo, ejecute el siguiente comando:

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

El comando anterior publica automáticamente las aplicaciones de Microsoft Office 365 (Excel, OneNote, Outlook, PowerPoint, Visio y Word).

Creación de la colección puede tardar 30 minutos o más tiempo en completarse. Por lo tanto, este comando devuelve un ID de seguimiento que puede usar como sigue:


    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

Cuando haya terminado la colección, puede agregar usuarios a la colección con el siguiente comando:

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

Y haya acabado! Ese usuario podrá conectarse a la aplicación con el cliente de Azure RemoteApp encuentra [aquí](https://www.remoteapp.windowsazure.com/).

## <a name="available-cmdlets"></a>Cmdlets disponibles
Hay una gran cantidad de otros comandos que tenemos, la documentación de ellos llegarán breve:

Cmdlets de RemoteApp colección básicas: 

- Nueva AzureRemoteAppCollection
- Get-AzureRemoteAppCollection
- Establecer AzureRemoteAppCollection
- Actualización AzureRemoteAppCollection
- Quitar AzureRemoteAppCollection
- AzureRemoteAppUser agregar
- Get-AzureRemoteAppUser
- Quitar AzureRemoteAppUser
- Get-AzureRemoteAppSession
- AzureRemoteAppSession desconectar
- AzureRemoteAppSessionLogoff invocar
- Enviar AzureRemoteAppSessionMessage
- Get-AzureRemoteAppProgram
- Get-AzureRemoteAppStartMenuProgram
- AzureRemoteAppProgram publicar
- AzureRemoteAppProgram quitar
- Get-AzureRemoteAppCollectionUsageDetails
- Get-AzureRemoteAppCollectionUsageSummary
- Get-AzureRemoteAppPlan

Cmdlets de red virtual de RemoteApp:

- Nueva AzureRemoteAppVNet
- Get-AzureRemoteAppVNet
- Establecer AzureRemoteAppVNet
- Quitar AzureRemoteAppVNet
- Get-AzureRemoteAppVpnDevice
- Obtener--AzureRemoteAppVpnDeviceConfigScript
- Restablecer AzureRemoteAppVpnSharedKey

Cmdlets de imagen de plantilla de RemoteApp:

- Nueva AzureRemoteAppTemplateImage
- Get-AzureRemoteAppTemplateImage
- Cambiar nombre AzureRemoteAppTemplateImage
- Quitar AzureRemoteAppTemplateImage

Otros cmdlets RemoteApp:

- Get-AzureRemoteAppLocation
- Get-AzureRemoteAppWorkspace
- Establecer AzureRemoteAppWorkspace
- Get-AzureRemoteAppOperationResult
 
