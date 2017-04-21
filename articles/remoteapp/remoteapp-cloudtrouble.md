
<properties
    pageTitle="Solucionar problemas de colecciones de nube de RemoteApp - creación | Microsoft Azure"
    description="Obtenga información sobre cómo solucionar problemas de creación de colección de nube de RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="troubleshoot-creating-remoteapp-cloud-collections"></a>Crear colecciones de nube de RemoteApp de solución de problemas

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Si tiene problemas para crear una colección de nube, consulte la siguiente información.

## <a name="your-image-is-invalid"></a>La imagen no es válida ##
Si ve un mensaje como "GoldImageInvalid" cuando está esperando Azure proporcionando su colección, significa que la imagen de la plantilla no cumple el [define los requisitos de la imagen](remoteapp-imagereqs.md). Por lo tanto, vaya leer dichos [requisitos](remoteapp-imagereqs.md), arregle la imagen y a continuación, trata de crear la colección de nuevo.

## <a name="common-errors-seen-in-the-azure-management-portal"></a>Errores comunes en el portal de administración de Azure

    DNS server could not be reached
    ProvisioningTimeout

En la nube a menudo colecciones de errores durante la creación debido a está utilizando imágenes personalizadas.  Si ve alguno de los errores anteriores y está utilizando una imagen personalizada para crear la colección, compruebe lo siguiente:

- Asegúrese de que la imagen personalizada que ha cargado cumple los requisitos de la imagen.
- Más a menudo el problema común es que la imagen no fue correctamente preparada con Sysprep.  
- Comprobar la imagen pueda arrancar dentro de Hyper-V o intente crear un VM IAAS directamente en su suscripción de Azure con la imagen. Si se produce un error en la máquina virtual arrancar y no iniciar, a continuación, esto suele indicar que la imagen personalizada no se ha preparado correctamente.  Comprobar la imagen personalizada se creó siguiendo cómo crear una imagen de la plantilla personalizada para RemoteApp

Si está utilizando una de las imágenes de Microsoft que se incluye con la suscripción, pruebe a crear la colección de nuevo. Si el problema persiste, a continuación, póngase en contacto con soporte técnico de Microsoft.

    PlatformImageTrialModeOnly

Si ve este error normalmente, esto significa que se han actualizado a una cuenta de pagada, pero está intentando usar una imagen de Microsoft proporciona solo es válida durante el modo de prueba del servicio. En este caso, pruebe a crear la colección de nube de nuevo, pero asegúrese de especificar la imagen correcta.
