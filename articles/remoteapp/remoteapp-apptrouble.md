<properties 
    pageTitle="Solución de problemas de RemoteApp Azure - errores de inicio y la conexión de la aplicación | Microsoft Azure" 
    description="Obtenga información sobre cómo solucionar problemas de inicio y conectarse a las aplicaciones de Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



#<a name="troubleshoot-azure-remoteapp---application-launch-and-connection-failures"></a>Solucionar problemas de Azure RemoteApp - errores de inicio y la conexión de la aplicación 

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Aplicaciones hospedadas en Azure RemoteApp pueden fallar iniciar por varios motivos diferentes. En este artículo se describe varias razones y mensajes de error que los usuarios pueden recibir cuando intenta iniciar aplicaciones. También habla de errores de conexión. (Pero en este artículo describen problemas al iniciar sesión en el cliente de RemoteApp de Azure).  

Siga leyendo para obtener más información acerca de los mensajes de error comunes debido a errores de inicio y la conexión de la aplicación.

##<a name="were-getting-you-set-up-try-again-in-10-minutes"></a>Obtenemos configurar... Inténtelo de nuevo en 10 minutos.

Este error significa que RemoteApp de Azure escalar para satisfacer las necesidades de capacidad de los usuarios. En segundo plano está se crean más instancias de Azure RemoteApp VM para gestionar las necesidades de capacidad de los usuarios. Normalmente, esto tarda unos cinco minutos pero puede tardar hasta 10 minutos. A veces, esto no ocurre lo suficientemente rápido y se necesitan recursos inmediatamente. Por ejemplo un escenario de 9 A.M. donde muchos usuarios necesitan usar la aplicación en Azure RemoteAppn al mismo tiempo. Si esto sucede podemos habilitar el **modo de capacidad** de fondo. Para hacer esto abrir una incidencia de soporte técnico de Azure y o un correo electrónico a [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com). Asegúrese de incluir el identificador de la suscripción en la solicitud.  

![Obtenemos configurar](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## <a name="could-not-auto-reconnect-to-your-applications-please-re-launch-your-application"></a>Automática no pudo conectar a sus aplicaciones, por favor, vuelva a iniciar la aplicación  

Este mensaje de error se ve a menudo si estaba utilizando RemoteApp de Azure y luego poner su PC en suspensión de más de 4 horas y, a continuación, despertó su PC y el cliente de Azure RemoteApp intenta automático de volver a conectar y se ha superado el tiempo de espera.  Indique a los usuarios para volver a la aplicación e intenta abrir desde el cliente de RemoteApp de Azure.

![Podría no automático y volver a conectar a sus aplicaciones](./media/remoteapp-apptrouble/ra-apptrouble2.png) 

## <a name="problems-with-the-temp-profile"></a>Problemas con el perfil temporal 

Este error se produce cuando el perfil de usuario (disco de perfil de usuario) no pudo montaje y el usuario ha recibido un perfil temporal.  Los administradores deben navegar a la colección en el portal de Azure y, a continuación, vaya a la pestaña **sesiones** e intente **Cerrar sesión** el usuario. Esto se forzar un cierre de sesión completa con la sesión de usuario -, a continuación, hacer que el usuario intenta iniciar una aplicación de nuevo. Si se produce un error que ponerse en contacto con soporte técnico de Azure y o un correo electrónico a [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

## <a name="azure-remoteapp-has-stopped-working"></a>Azure RemoteApp ha dejado de funcionar

Este mensaje de error significa que el cliente de Azure RemoteApp está teniendo problemas y es necesario reiniciar. Indique a los usuarios a cerrar: seleccione **cerrar programa** y, a continuación, vuelva a iniciar el cliente de RemoteApp de Azure.  Si el problema continúa abrir e incidencia de soporte técnico de Azure y o un correo electrónico a [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

![Azure RemoteApp ha dejado de funcionar](./media/remoteapp-apptrouble/ra-apptrouble3.png)  

## <a name="an-error-occurred-while-remote-desktop-connection-was-accessing-this-resource-retry-the-connection-or-contact-your-system-administrator"></a>Se produjo un error mientras la conexión a Escritorio remoto estaba teniendo acceso a este recurso. Vuelva a intentar la conexión o póngase en contacto con el administrador del sistema

Este es un mensaje de error genérico: póngase en contacto con soporte técnico de Azure y o un correo electrónico a [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com) para que podamos investigar. 

![Mensaje de Azure RemoteApp genérico](./media/remoteapp-apptrouble/ra-apptrouble4.png) 