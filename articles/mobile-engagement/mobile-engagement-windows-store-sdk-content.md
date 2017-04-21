<properties 
    pageTitle="Contenido de Windows SDK de aplicaciones Universal" 
    description="Obtenga información sobre el contenido del SDK Universal de aplicaciones de Windows para Azure Mobile compromiso"                    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-universal-apps-sdk-content"></a>Contenido de Windows SDK de aplicaciones Universal

Este documento se enumera y describe el contenido que se implementa el SDK en la aplicación.

##<a name="the-resources-folder"></a>La `/Resources` carpeta

Esta carpeta contiene todos los recursos que necesita compromiso de móvil. También puede personalizar para ajustarlas a la aplicación.

- `EngagementConfiguration.xml`: Archivo de configuración del compromiso móvil, este es donde puede personalizar la configuración de compromiso de Mobile (cadena de conexión de contratación Mobile, bloqueo de informe...).

### <a name="html-folder"></a>carpeta /HTML más externas

- `EngagementNotification.html`: El `Notification` la vista de diseño de html para cintas y de la aplicación web.

- `EngagementAnnouncement.html`: El `Announcement` vista html diseño para las vistas intersticiales de la aplicación web.

### <a name="images-folder"></a>carpeta/images

- `EngagementIconNotification.png`: El icono de marca que se muestra en la parte izquierda de una notificación, reemplazar este mediante el icono de marca.

- `EngagementIconOk.png`: El `Ok` icono de las páginas de contenido de alcance para el botón de acción o validación.

- `EngagementIconNOK.png`: El `NOK` icono usado cuando se deshabilita el botón de validación de las páginas de contenido de alcance.
 
- `EngagementIconClose.png`: El `Close` icono de las notificaciones de alcance y el contenido en el botón Descartar.

### <a name="overlay-folder"></a>carpeta /Overlay

- `EngagementPageOverlay.cs`: La página de superposición responsable de agregar el compromiso llegar a la interfaz de usuario de la aplicación a su hijo.
  
