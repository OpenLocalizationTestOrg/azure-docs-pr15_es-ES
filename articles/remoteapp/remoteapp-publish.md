<properties
    pageTitle="Publicar una aplicación en Azure RemoteApp | Microsoft Azure"
    description="Obtenga información sobre cómo publicar aplicaciones y recursos de RemoteApp de Azure."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="how-to-publish-an-app-in-remoteapp"></a>Cómo publicar una aplicación de RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Después de crear la colección de RemoteApp, debe publicar las aplicaciones o los recursos que desee que estén disponibles para los usuarios. Las imágenes de plantilla que se incluye con la suscripción sólo tienen unas aplicaciones publicadas de forma predeterminada, compartir las otras aplicaciones, debe publicarlas.

> [AZURE.NOTE] ¿Necesita actualizar una aplicación? Deberá [actualizar la imagen](remoteapp-update.md) en primer lugar.

En la pestaña **publicación** en el portal, haga clic en **Publicar**. Puede agregar una aplicación desde el menú de **Inicio** de la imagen de plantilla, o bien, proporcione la ruta a donde está instalada la aplicación en la imagen de la plantilla. Si elige agregar en el menú **Inicio** , elija la aplicación de publicación de la lista. Si decide incluir la ruta de acceso a la aplicación, escriba un nombre para la aplicación y la ruta de acceso a la aplicación. Utilizar variables en la ruta de acceso: por ejemplo, "unidadDeSistema" en lugar de "c:\".

> [AZURE.NOTE] Si desea agregar la aplicación en el menú **Inicio** , debe tener *agrega esa aplicación a la * *Iniciar* * menú en la imagen de la plantilla.* En caso contrario, RemoteApp sólo verán lo que *está* en el menú **Inicio** y se confuso. 

>Para asegurarse de que la aplicación está en el menú **Inicio** , coloque un archivo de acceso directo - **.lnk** - dentro de la carpeta de Menu\Programs %systemdrive%\ProgramData\Microsoft\Windows\Start.

> Si ha olvidado agregar la aplicación al menú **Inicio** cuando se ha creado la plantilla, elija Agregar la ruta de acceso a la aplicación. (O vuelva a crear la imagen de la plantilla, pero es un poco más de trabajo.)


 