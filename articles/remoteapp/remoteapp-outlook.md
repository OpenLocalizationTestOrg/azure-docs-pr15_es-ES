<properties
    pageTitle="Usar Outlook en Azure RemoteApp | Microsoft Azure" 
    description="Obtenga información sobre cómo configurar y utilizar Outlook en Azure RemoteApp | Microsoft Azure"
    services="remoteapp"
    documentationCenter=""
    authors="pavithir"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-microsoft-outlook-in-azure-remoteapp"></a>Uso de Microsoft Outlook en Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

RemoteApp Azure es compatible con Microsoft Outlook O365. Obtenga más información sobre cómo [funciona Office en Azure RemoteApp](remoteapp-officesubscription.md). Existen algunas opciones de configuración recomendadas para Outlook cuando se usa en Azure RemoteApp.

## <a name="cached-mode"></a>Modo de caché
Modo en caché es una configuración recomendada al usar Outlook en Azure RemoteApp. Al configurar una cuenta de Outlook 2013 para utilizar el modo caché de Exchange, Outlook 2013 funciona desde una copia local del buzón de Microsoft Exchange del usuario que se almacena en un archivo de datos sin conexión (archivo OST) en el equipo del usuario, junto con la libreta de direcciones sin conexión (OAB). El buzón de la caché y OAB se actualizan periódicamente desde el servicio de O365. Obtenga más información acerca de [las diferencias entre el modo caché y en línea](https://technet.microsoft.com/library/jj683103.aspx).

El usuario puede seleccionar **El modo caché de Exchange** o **Modo en línea** durante la configuración de cuenta o cambie la configuración de la cuenta. También puede implementar uno u otro modo mediante la herramienta de personalización de Office (OCT) o la directiva de grupo.  

Obtenga [instrucciones paso a paso acerca de cómo habilitar el modo en caché](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc).

## <a name="search"></a>Búsqueda
En Azure RemoteApp, usando la búsqueda en Outlook tiene limitaciones. RemoteApp Azure usa máquinas virtuales agrupadas para ajustar las sesiones de usuario. Indización de búsqueda depende del ID de máquina es diferente para las máquinas virtuales distintas. Es posible que cada vez que un usuario inicia sesión en Azure RemoteApp, se dirigen a una nueva máquina virtual. Se significaría que, si se habilita la búsqueda local, el indizador se ejecutará cada vez que cambia el ID de máquina (cuando el usuario está en una máquina virtual diferente). Dependiendo del tamaño de la. Archivo OST, el indizador podría tardar mucho tiempo en Finalizar y utilizar los recursos necesarios para otras aplicaciones. Búsqueda no solo es lenta, pero no puede producir resultados. Usar un perfil de la cuenta de modo en línea ¿solucionar este problema, pero el rendimiento general se verán afectadas por la falta de una caché local (consulte el vínculo de arriba para obtener más información sobre la diferencia entre el modo caché y en línea). Desgraciadamente, no se puede deshabilitar la búsqueda de indexado/local y no se puede habilitar la búsqueda en línea de forma predeterminada en Outlook 2013.
