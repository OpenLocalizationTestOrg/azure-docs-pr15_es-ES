<properties
    pageTitle="¿Cuáles son las imágenes de plantilla de RemoteApp de Azure? | Microsoft Azure"
    description="Obtenga información sobre las imágenes de plantilla incluidas con RemoteApp de Azure."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="what-is-in-the-azure-remoteapp-template-images"></a>¿Cuáles son las imágenes de plantilla de RemoteApp de Azure?

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Su suscripción de Azure RemoteApp incluye tres imágenes de plantilla:


- Windows Server 2012
- Microsoft Office 365 ProPlus (suscripción a Office 365 requerido)
- Microsoft Office 2013 Professional Plus (solo prueba)

> [AZURE.IMPORTANT]Su suscripción de Azure RemoteApp permite el que acceso al software en las imágenes, con la excepción de Office 365 ProPlus, que requiere una suscripción independiente y Office 2013, que no pueden usarse en producción. Esto significa que pueden compartir los programas o aplicaciones en las imágenes de plantilla con los usuarios. Por ejemplo, si crea una colección que utiliza la imagen de Windows Server 2012 R2, puede publicar System Center Endpoint Protection a los usuarios para acceder a través de RemoteApp.
>
> Consulte los [Detalles de licencia de RemoteApp](remoteapp-licensing.md) para obtener más información. Y la información de la licencia de [Office usando con Azure RemoteApp](remoteapp-o365.md) de la oficina.

Siga leyendo para obtener más información sobre cada imagen que contiene.

## <a name="windows-server-2012-r2--the-vanilla-image"></a>Windows Server 2012 R2 ("la imagen vainilla")
Esta imagen se basa en el sistema operativo del centro de datos de Microsoft Windows Server 2012 R2 y tiene los siguientes roles y características instaladas para satisfacer los requisitos para las imágenes de plantilla de RemoteApp de Azure:


- .NET framework 4.5, 3.5.1, 3.5
- Experiencia de escritorio
- Servicios de escritura a mano y lápiz
- Media Foundation
- Host de sesión de escritorio remoto
- Windows PowerShell 4.0
- Windows PowerShell ISE
- Soporte técnico de WoW64

Esta imagen también tiene instaladas las siguientes aplicaciones:

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Reproductor de Microsoft Windows Media


## <a name="microsoft-office-365-proplus-subscription-required"></a>Microsoft Office 365 ProPlus (suscripción requerido)
Office 365 es la aplicación más solicitada, por lo que se creó una imagen "personalizada" para trabajar con.

Esta imagen es una extensión de la imagen vainilla y tiene los siguientes componentes de Microsoft Office 365 ProPlus instalado además de los componentes que se describe en la imagen de Windows Server 2012 R2:


- Access
- Excel
- Lync
- OneNote
- OneDrive para la empresa (tenga en cuenta que no se admite el agente de sincronización para su uso con RemoteApp de Azure)
- Outlook
- PowerPoint
- Word
- Herramientas de corrección de Microsoft Office

La imagen también incluye Visio Pro y Project Pro.

Y las aplicaciones siguientes, así:

- SQL Native client
- Controlador ODBC
- Cliente de minería de datos de SQL Server
- Cliente de MasterDataServices
- Microsoft Publisher
- PowerQuery
- PowerMap


La funcionalidad completa de aplicaciones de Office 365 ProPlus sólo está disponible para los usuarios que tienen un plan de Office 365 ProPlus. Para obtener más información sobre Office 365 planes de suscripción ver [planes de servicio de Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx). ¿Sigue teniendo dudas? Consulte la información de [Office 365 + RemoteApp](remoteapp-o365.md) . Consulte también el artículo nuevo, [cómo usar su suscripción a Office 365 con RemoteApp de Azure](remoteapp-officesubscription.md).

Tenga en cuenta que necesita para conceder licencia a Office 365 ProPlus, Visio Pro y Project Pro por separado - a cada uno de ellos tiene su propia licencia.

## <a name="microsoft-office-2013-professional-plus-trial-only"></a>Microsoft Office 2013 Professional Plus (solo prueba)
Durante el período de prueba gratuito, puede probar el servicio con la imagen de Office 2013.

Esta imagen es una extensión de la imagen vainilla y tiene los siguientes componentes de Microsoft Office 2013 Professional Plus además de los componentes que se describe en la imagen de Windows Server 2012 R2:


- Access
- Excel
- Lync
- OneNote
- OneDrive para la empresa (tenga en cuenta que no se admite el agente de sincronización para su uso con RemoteApp de Azure)
- Outlook
- PowerPoint
- Proyecto
- Visio
- Word
- Herramientas de corrección de Microsoft Office

> [AZURE.IMPORTANT]**Información legal:** Esta imagen no incluye una licencia de Microsoft Office y *no se pueden usar para producción*. Imagen está destinada prueba solo Office 2013 Professional Plus. Si desea usar aplicaciones de Office en Azure RemoteApp de producción, debe usar la imagen de Office 365 ProPlus. Para obtener más detalles sobre licencias de Office, vea [utilizar Office 365 con RemoteApp de Azure](remoteapp-o365.md)
