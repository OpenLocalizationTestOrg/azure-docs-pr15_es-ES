<properties
    pageTitle="Solucionar problemas de Proxy de la aplicación | Microsoft Azure"
    description="Explica cómo solucionar problemas en el Proxy de aplicación de Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="kgremban"/>



# <a name="troubleshoot-application-proxy"></a>Solucionar problemas de Proxy de aplicación

Si se producen errores en el acceso a una aplicación publicada o en aplicaciones de publicación, compruebe las siguientes opciones para ver si Proxy de aplicación de Microsoft Azure AD funciona correctamente:

- Abra la consola de servicios de Windows y compruebe que el servicio de **Conector de Proxy de aplicación de Microsoft AAD** está habilitado y ejecutando. También puede buscar en la página de propiedades del servicio de Proxy de la aplicación, tal como se muestra en la siguiente imagen:  
  ![Captura de pantalla de ventana de propiedades de conector de Proxy de aplicación de Microsoft AAD](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)

- Abra el Visor de sucesos y busque eventos de conector de Proxy de la aplicación en **registros de los servicios de aplicaciones y** > **Microsoft** > **AadApplicationProxy** > **conector** > **Administrador**.
- Si es necesario, registros más detallados están disponibles al activar el análisis de registros de depuración y activar el registro de sesión de conector de Proxy de la aplicación.

## <a name="the-page-is-not-rendered-correctly"></a>La página no se procesa correctamente

Si no recibe un mensaje de error específico, puede todavía tiene problemas con la aplicación representar o funciona incorrectamente. Esto puede ocurrir si publica la ruta de acceso de artículo, pero la aplicación requiere el contenido que se encuentra fuera de la ruta de acceso.

Por ejemplo, si publica el https://yourapp/app ruta de acceso pero la aplicación llama imágenes de https://yourapp/media, que se no procesar. Asegúrese de publicar la aplicación con la ruta de acceso más alto nivel que debe incluir todo el contenido relevante. En este ejemplo, sería http://yourapp/.

Si cambia la ruta de acceso para incluir el contenido que se hace referencia, pero aún necesitan los usuarios land en un vínculo en la ruta de acceso más profundo, consulte el blog [establecer el vínculo adecuado para las aplicaciones de Proxy de aplicación en el panel de acceso de Azure AD y el iniciador de aplicaciones de Office 365](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="general-errors"></a>Errores generales

Error | Descripción | Resolución
--- | --- | ---
No puede tener acceso a esta aplicación corporativa. No está autorizado para tener acceso a esta aplicación. Error de autorización. Asegúrese de asignar al usuario acceso a esta aplicación. | Puede que no haya asignado al usuario para esta aplicación. | Vaya a la pestaña de la **aplicación** y, en **usuarios y grupos**, asigne este usuario o grupo de usuarios para esta aplicación.
No puede tener acceso a esta aplicación corporativa. No está autorizado para tener acceso a esta aplicación. Error de autorización. Asegúrese de que el usuario tiene una licencia para Azure Active Directory Premium o Basic. | Los usuarios pueden obtener este error al intentar obtener acceso a la aplicación que ha publicado si explícitamente no se han asignado a una licencia de Premium básico administrador del suscriptor. | Vaya a la pestaña de **licencias** de Active Directory del suscriptor y asegúrese de que este usuario o grupo de usuario se asigna una licencia básica o Premium.


## <a name="connector-troubleshooting"></a>Solución de problemas de conector
Si se produce un error en el registro durante la instalación del Asistente para conector, hay dos formas de ver la causa del error. Puede buscar en el registro de eventos en **aplicaciones y servicios Logs\Microsoft\AadApplicationProxy\Connector\Admin**o ejecute el siguiente comando de Windows PowerShell.

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

| Error | Descripción | Resolución |
| --- | --- | --- |
| Registro de conector: asegúrese de que ha habilitado Proxy de aplicación en el Portal de administración de Azure y que ha introducido correctamente su nombre de usuario de Active Directory y la contraseña. Error: 'uno o más errores al.' | Cierra la ventana de registro sin realizar el inicio de sesión de Azure AD. | Vuelva a ejecutar al Asistente para conector y registrar el conector. |
| Registro de conector: asegúrese de que ha habilitado Proxy de aplicación en el Portal de administración de Azure y que ha introducido correctamente su nombre de usuario de Active Directory y la contraseña. Error: ' AADSTS50001: recursos `https://proxy.cloudwebappproxy.net /registerapp` está deshabilitado. " | Proxy de la aplicación está deshabilitado.  | Asegúrese de que la opción Habilitar a Proxy de aplicación en el portal de clásico Azure antes de intentar registrar el conector. Para obtener más información sobre cómo habilitar Proxy de aplicación, vea [habilitar Proxy de aplicación de servicios](active-directory-application-proxy-enable.md). |
| Registro de conector: asegúrese de que ha habilitado Proxy de aplicación en el Portal de administración de Azure y que ha introducido correctamente su nombre de usuario de Active Directory y la contraseña. Error: 'uno o más errores al.' | Si abre la ventana de registro y, a continuación, se cierra inmediatamente sin iniciar sesión en permitir, probablemente recibirá este error. Este error se produce cuando hay un error de red en el sistema. | Asegúrese de que es posible conectarse desde un explorador a un sitio Web público y que los puertos estén abiertos tal como se especifica en [los requisitos previos de Proxy de la aplicación](active-directory-application-proxy-enable.md). |
| Registro de conector: asegúrese de que su equipo está conectado a Internet. Error: "no había ningún extremo escuchando en `https://connector.msappproxy.net :9090/register/RegisterConnector` que puede aceptar el mensaje. A menudo, esto se debe a una dirección incorrecta o acción SOAP. Consulte InnerException, si está presente, para obtener más detalles.' | Si iniciar sesión con su nombre de usuario de Azure AD y la contraseña, pero a continuación, recibe este error, puede que se bloquean todos los puertos encima 8081. | Asegúrese de que los puertos necesarios están abiertos. Para obtener más información, vea [requisitos previos de Proxy de la aplicación](active-directory-application-proxy-enable.md). |
| Borrar error se presenta en la ventana de registro. No puede continuar: solo para cerrar la ventana. | Especificar la contraseña o el nombre de usuario incorrecta. | Vuelve a intentarlo. |
| Registro de conector: asegúrese de que ha habilitado Proxy de aplicación en el Portal de administración de Azure y que ha introducido correctamente su nombre de usuario de Active Directory y la contraseña. Error: ' AADSTS50059: ninguna información de identificación del inquilino encuentre en la solicitud o implícita por las credenciales proporcionadas y no se pudo realizar la búsqueda por principio URI de servicio. | Está intentando iniciar sesión con una Account de Microsoft y no un dominio que forma parte de los identificadores de organización del directorio que intenta obtener acceso. | Asegúrese de que el administrador es parte del mismo nombre de dominio como el dominio del inquilino, por ejemplo, si el dominio de Azure AD es contoso.com, el administrador debe ser admin@contoso.com. |
| Error al recuperar la directiva de ejecución actual de ejecución de scripts de PowerShell. | Si se produce un error en la instalación de conector, compruebe que la directiva de ejecución de PowerShell no está deshabilitada. | Abra el Editor de directivas de grupo. Vaya a **Configuración del equipo** > **Plantillas administrativas** > **Componentes de Windows** > **Windows PowerShell** y haga doble clic en **activar la ejecución de scripts**. Se puede establecer en **No configurado** o **habilitado**. Si establece en **habilitado**, asegúrese de que, en Opciones, se establece la directiva de ejecución alguno **Permitir locales secuencias de comandos y remotos firmados** o **Permitir que todos los scripts**. |
| No se pudo descargar la configuración del conector. | Certificado de cliente del conector, que se utiliza para la autenticación, expirado. Esto también puede ocurrir si tiene el conector instalado detrás de un proxy. En este caso, el conector no tiene acceso a Internet y no pueda proporcionar aplicaciones a los usuarios remotos. | Renovar confianza manualmente mediante la `Register-AppProxyConnector` cmdlet de Windows PowerShell. Si el conector está detrás de un servidor proxy, es necesario conceder acceso a Internet a las cuentas de conector "servicios de red" y "sistema local". Es posible conceder acceso al servidor Proxy o estableciendo a utilizar al servidor proxy. |
| Registro de conector: asegúrese de que es un administrador Global de Active Directory para registrar el conector. Error: "el Registro denegado.' | El alias que intenta iniciar sesión no es un administrador en este dominio. Siempre se instala el conector para el directorio que posee el dominio del usuario. | Asegúrese de que el administrador está intentando iniciar sesión tiene permisos globales para el inquilino de Azure AD.|


## <a name="kerberos-errors"></a>Errores de Kerberos

| Error | Descripción | Resolución |
| --- | --- | --- |
| Error al recuperar la directiva de ejecución actual de ejecución de scripts de PowerShell. | Si se produce un error en la instalación de conector, compruebe que la directiva de ejecución de PowerShell no está deshabilitada. | Abra el Editor de directivas de grupo. Vaya a **Configuración del equipo** > **Plantillas administrativas** > **Componentes de Windows** > **Windows PowerShell** y haga doble clic en **activar la ejecución de scripts**. Se puede establecer en **No configurado** o **habilitado**. Si establece en **habilitado**, asegúrese de que, en Opciones, se establece la directiva de ejecución alguno **Permitir locales secuencias de comandos y remotos firmados** o **Permitir que todos los scripts**. |
| 12008 - azure AD había excedido el número máximo de intentos de autenticación Kerberos permitidos en el servidor back-end. | Este evento puede indicar una configuración incorrecta entre Azure AD y el servidor de aplicaciones de back-end o un problema de configuración de fecha y hora en ambos equipos. | El servidor back-end rechazado el vale Kerberos creado por Azure AD. Compruebe que Azure AD y el servidor de aplicaciones de back-end están configurados correctamente. Asegúrese de que la configuración de fecha y hora en Azure AD y se sincronizan el servidor de aplicaciones de back-end. |
| 13016 - azure AD no puede recuperar un vale de Kerberos en nombre del usuario porque no hay ningún UPN en el token de borde o en cookies de access. | Hay un problema con la configuración de STS. | Corregir la configuración de notificación UPN en STS. |
| 13019 - azure AD no puede recuperar un vale de Kerberos en nombre del usuario debido al siguiente error API general. | Este evento puede indicar una configuración incorrecta entre Azure AD y el servidor de controlador de dominio o un problema de configuración de fecha y hora en ambos equipos. | El controlador de dominio rechazado el vale Kerberos creado por Azure AD. Compruebe que Azure AD y el servidor de aplicaciones de back-end están configurados correctamente, especialmente la configuración de SPN. Asegúrese de que Azure AD es dominio unido al mismo dominio como el controlador de dominio para asegurarse de que el controlador de dominio establece la confianza con Azure AD. Asegúrese de que la configuración de fecha y hora en Azure AD y se sincronizan el controlador de dominio. |
| 13020 - azure AD no puede recuperar un vale de Kerberos en nombre del usuario porque el servidor back-end SPN no está definido. | Este evento puede indicar una configuración incorrecta entre Azure AD y el servidor de controlador de dominio o un problema de configuración de fecha y hora en ambos equipos. | El controlador de dominio rechazado el vale Kerberos creado por Azure AD. Compruebe que Azure AD y el servidor de aplicaciones de back-end están configurados correctamente, especialmente la configuración de SPN. Asegúrese de que Azure AD es dominio unido al mismo dominio como el controlador de dominio para asegurarse de que el controlador de dominio establece la confianza con Azure AD. Asegúrese de que la configuración de fecha y hora en Azure AD y se sincronizan el controlador de dominio. |
| 13022 - azure AD no puede autenticar al usuario porque el servidor back-end responde a los intentos de autenticación Kerberos con un error de HTTP 401. | Este evento puede indicar una configuración incorrecta entre Azure AD y el servidor de aplicaciones de back-end o un problema de configuración de fecha y hora en ambos equipos. | El servidor back-end rechazado el vale Kerberos creado por Azure AD. Compruebe que Azure AD y el servidor de aplicaciones de back-end están configurados correctamente. Asegúrese de que la configuración de fecha y hora en Azure AD y se sincronizan el servidor de aplicaciones de back-end. |
| El sitio Web no puede mostrar la página. | El usuario puede recibir este error al intentar obtener acceso a la aplicación que ha publicado si la aplicación es una aplicación de IWA. El SPN definido para esta aplicación puede ser incorrecto. | Para las aplicaciones IWA: asegúrese de que el SPN configurado para esta aplicación es correcto. |
| El sitio Web no puede mostrar la página. | El usuario puede recibir este error al intentar obtener acceso a la aplicación que ha publicado si la aplicación es una aplicación de OWA. Esto podría estar causado por uno de estos procedimientos: <br> -El SPN definido para esta aplicación es incorrecto. <br> -El usuario que intentó tener acceso a la aplicación está utilizando una cuenta de Microsoft, en lugar de la cuenta corporativa adecuada para iniciar sesión o el usuario es un usuario de invitado. <br> -El usuario que intentó tener acceso a la aplicación no está definido correctamente para esta aplicación en el lado local. | Las medidas para mitigar según corresponda: <br> -Compruebe que el SPN configurado para esta aplicación es correcto. <br> -Asegúrese de que el usuario inicia sesión con su cuenta corporativa que coincida con el dominio de la aplicación publicada. Invitado y los usuarios de Microsoft Account no pueden acceder a las aplicaciones IWA. <br> -Asegúrese de Asegúrese de que este usuario tiene los permisos adecuados según se define para esta aplicación back-end en el equipo local. |
| No puede tener acceso a esta aplicación corporativa. No está autorizado para tener acceso a esta aplicación. Error de autorización. Asegúrese de asignar al usuario acceso a esta aplicación. | Los usuarios pueden obtener este error al intentar obtener acceso a la aplicación que ha publicado si utilizan cuentas de Microsoft en lugar de su cuenta corporativa para iniciar sesión. Los usuarios invitados también pueden recibir este error. | Los invitados y usuarios de Microsoft Account no pueden acceder a las aplicaciones IWA. Asegúrese de que el usuario inicia sesión con su cuenta corporativa que coincida con el dominio de la aplicación publicada. |
| Esta aplicación corporativa no pueden acceder a este momento. Vuelva a intentarlo más tarde... Se ha agotado el tiempo de espera el conector. | Los usuarios pueden obtener este error al intentar obtener acceso a la aplicación que ha publicado si no está definidas correctamente para esta aplicación en el lado local. | Asegúrese de que los usuarios tienen los permisos adecuados según se define para esta aplicación back-end en el equipo local. |


## <a name="see-also"></a>Vea también

- [Habilitar a Proxy de aplicación para Azure Active Directory](active-directory-application-proxy-enable.md)
- [Publicar aplicaciones con Proxy de aplicación](active-directory-application-proxy-publish.md)
- [Habilitar inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)

Para las últimas noticias y actualizaciones, consulte el [blog de Proxy de aplicación](http://blogs.technet.com/b/applicationproxyblog/)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
