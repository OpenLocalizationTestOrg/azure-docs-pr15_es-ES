<properties
    pageTitle="Cómo funciona: administración de contraseñas de Azure AD | Microsoft Azure"
    description="Obtenga más información sobre los distintos componentes de administración de Azure AD contraseña, incluidos donde los usuarios registrar, restablecen y cambian su contraseña y donde los administradores configurar, informar sobre y habilitar la administración de contraseñas de Active Directory local."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="how-password-management-works"></a>Cómo funciona la administración de contraseñas

> [AZURE.IMPORTANT] **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [He aquí cómo puede cambiar y restablecer su propia contraseña](active-directory-passwords-update-your-own-password.md).

Administración de contraseñas de Azure Active Directory consta de varios componentes lógicos que se describen a continuación.  Haga clic en cada vínculo para obtener más información sobre ese componente.

- [**Configuración del Portal de administración de contraseña**](#password-management-configuration-portal) : los administradores pueden controlar diferentes aspectos de cómo se administran las contraseñas en sus inquilinos, vaya a la ficha de configurar su directorio en el [Portal de administración de Azure](https://manage.windowsazure.com).
- [**Portal de registro de usuario**](#user-registration-portal) : los usuarios pueden registrarse para restablecer a través de este portal web contraseñas.
- [**Portal de restablecimiento de contraseña de usuario**](#user-password-reset-portal) : los usuarios pueden restablecer sus contraseñas mediante una serie de retos diferentes según la directiva de restablecimiento de contraseña controlada por el administrador
- [**Portal de cambiar de contraseña de usuario**](#user-password-change-portal) : los usuarios pueden cambiar sus propias contraseñas en cualquier momento introduciendo la contraseña antigua y seleccionando una nueva contraseña este portal web
- [**Informes de administración de contraseña**](#password-management-reports) : los administradores pueden ver y analizar la actividad de registro y restablecer contraseña en su inquilino desplazándose hasta la sección "Informes de actividad" de la pestaña de "Informes" de su directorio en el [Portal de administración de Azure](https://manage.windowsazure.com)
- [**Componente de reescritura de contraseña de Azure AD Connect**](#password-writeback-component-of-azure-ad-connect) : los administradores pueden habilitar opcionalmente, la característica de reescritura de contraseña al instalar Azure AD Connect para habilitar la administración de federado o contraseña sincronizar las contraseñas de usuario de la nube.

## <a name="password-management-configuration-portal"></a>Portal de administración configuración de contraseña
Puede configurar las directivas de administración de la contraseña de un determinado directorio con el [Portal de administración de Azure](https://manage.windowsazure.com) desplazándose hasta la sección de **La directiva de restablecimiento de contraseña de usuario** en la ficha de **Configurar** del directorio.  Desde esta página de configuración, puede controlar muchos aspectos de cómo se administran las contraseñas de su organización, incluidos:

- Habilitar y deshabilitar el restablecimiento de contraseña para todos los usuarios en un directorio
- Establecer al número de retos (uno o dos) de un usuario debe seguir para restablecer su contraseña
- Establecer los tipos específicos de desafíos que desee habilitar para los usuarios de su organización de las opciones siguientes:
 - Teléfono móvil (un código de verificación a través de texto o una llamada de voz)
 - Teléfono de la oficina (una llamada de voz)
 - Correo electrónico alternativa (un código de comprobación por correo electrónico)
 - Preguntas de seguridad (autenticación basada en conocimientos)
- Establecer al número de preguntas de un usuario debe registrarse para usar el método de autenticación de preguntas de seguridad (sólo visible si tiene habilitadas las preguntas de seguridad)
- Establecer al número de preguntas de un usuario debe proporcionar durante el restablecimiento de usar el método de autenticación de preguntas de seguridad (sólo visible si tiene habilitadas las preguntas de seguridad)
- Utilizar las preguntas de seguridad preestablecido, localizados, que un usuario puede optar por usar cuando se registra contraseña restablece (sólo visible si tiene habilitadas las preguntas de seguridad)
- Definir las preguntas de seguridad personalizado que un usuario puede optar por usar cuando se registra contraseña restablece (sólo visible si tiene habilitadas las preguntas de seguridad)
- Requerir que los usuarios puedan registrarse para restablecer cuando accedan a la aplicación de Panel de acceso en [http://myapps.microsoft.com](http://myapps.microsoft.com)contraseñas.
- Los usuarios que necesitan para volver a confirmar sus datos previamente registrados después de un número de días configurable han pasado (solo se puede ver si se habilita el registro obligatoria)
- Proporcionar una dirección URL que se mostrará a los usuarios en caso de que tienen un problema al restablecer su contraseña o el correo electrónico de asistencia personalizada
- Habilitar o deshabilitar la capacidad de reescritura de contraseña (cuando se ha implementado reescritura de contraseña con conexión AAD)
- Ver el estado del agente de reescritura de contraseña (cuando se ha implementado reescritura de contraseña con conexión AAD)
- Habilitar las notificaciones de correo electrónico a los usuarios cuando se ha restablecido su propia contraseña (que se encuentra en la sección **notificaciones** del [Portal de administración de Azure](https://manage.windowsazure.com))
- Activar notificaciones de correo electrónico a los administradores de otros administradores restablezcan sus contraseñas (que se encuentra en la sección **notificaciones** del [Portal de administración de Azure](https://manage.windowsazure.com)
- Marca la contraseña de usuario restablece portal y correos electrónicos con nombre y el logotipo de su organización de restablecimiento de contraseña usando el inquilino (que se encuentra en la sección **Propiedades del directorio** del [Portal de administración de Azure](https://manage.windowsazure.com) de características de personalización de marca

Para obtener más información acerca de cómo configurar la administración de contraseñas de su organización, consulte [Introducción: administración de contraseñas de Azure AD](active-directory-passwords-getting-started.md).

##<a name="user-registration-portal"></a>Portal de registro de usuario
Antes de que los usuarios pueden usar el restablecimiento de contraseña, deben actualizarse sus cuentas de usuario de nube con los datos de autenticación correcta para asegurarse de que pueden pasar por el número de retos de restablecer contraseña definida por su administrador adecuado.  Los administradores también pueden definir esta información de autenticación en nombre de su usuario usando los portales de web Azure u Office DirSync / Azure AD Connect o Windows PowerShell.

Sin embargo, si prefiere que los usuarios registrar sus propios datos, también ofrecemos una página web que los usuarios pueden ir a para proporcionarle esta información.  Esta página permite a los usuarios especificar información de autenticación de acuerdo con la contraseña restablece las directivas que se han habilitado en su organización.  Una vez comprobados estos datos, se almacena en su cuenta de usuario de la nube que se usará para la recuperación de cuenta en un momento posterior. Esto es el aspecto del portal de registro:

  ![][001]

Para obtener más información, vea [Introducción: administración de contraseñas de Azure AD](active-directory-passwords-getting-started.md) y [procedimientos recomendados: administración de contraseñas de Azure AD](active-directory-passwords-best-practices.md).

##<a name="user-password-reset-portal"></a>Portal de restablecimiento de contraseña de usuario
Una vez que se ha habilitado la contraseña autoservicio restablece, configurar la directiva de restablecimiento de contraseña de autoservicio de su organización y garantiza que los usuarios tienen los datos de contacto correspondientes en el directorio, los usuarios de su organización podrán restablecer sus contraseñas automáticamente desde cualquier página web que usa una cuenta de trabajo o la escuela para inicio de sesión (por ejemplo, [portal.microsoftonline.com](https://portal.microsoftonline.com)). En las páginas como estos, los usuarios verán un **no puede acceder a su cuenta?** vínculo.

  ![][002]

Haga clic en este vínculo se iniciará el portal de restablecimiento de contraseña de autoservicio.

  ![][003]

Para obtener más información acerca de cómo los usuarios pueden restablecer sus contraseñas, consulte [Introducción: administración de contraseñas de Azure AD](active-directory-passwords-getting-started.md).

##<a name="user-password-change-portal"></a>Portal de cambio de contraseña de usuario
Si desean que los usuarios cambiar sus contraseñas, puede hacerlo a través del portal de cambio de contraseña en cualquier momento.  Los usuarios pueden acceder al portal de cambio de contraseña a través de la página de perfil del Panel de acceso, o haciendo clic en el vínculo "Cambiar contraseña" desde las aplicaciones de Office 365.  En el caso al expiran sus contraseñas, los usuarios también tendrá que cambiar automáticamente al iniciar sesión.

  ![][004]

En ambos casos, si se ha habilitado la reescritura de contraseña y el usuario federado o bien o haría la sincronización de la contraseña, estas contraseñas cambiadas se vuelven a escribir su Active Directory local. Esto es el aspecto de portal de cambio de contraseña:

  ![][005]

Para obtener más información acerca de cómo los usuarios pueden cambiar sus propias contraseñas de Active Directory local, consulte [Introducción: administración de contraseñas de Azure AD](active-directory-passwords-getting-started.md).

##<a name="password-management-reports"></a>Informes de administración de contraseñas
Navegar a la pestaña **informes** y buscar en la sección **Registros de actividad** , verá dos informes de administración de contraseñas: **actividad de restablecimiento de contraseña** y **la actividad de registro de restablecimiento de contraseñas**.  Con estos dos informes, puede obtener una vista de los usuarios registrarse para y mediante contraseña restablecer de su organización. Esto es el aspecto de estos informes en el [Portal de administración de Azure](https://manage.windowsazure.com):

  ![][006]

Para obtener más información, consulte [obtener perspectivas: informes de administración de contraseñas de Azure AD](active-directory-passwords-get-insights.md).

##<a name="password-writeback-component-of-azure-ad-connect"></a>Componente de reescritura de contraseña de Azure AD Connect
Si las contraseñas de los usuarios de su organización proceden de su entorno local (ya sea a través de la sincronización de contraseñas o la federación), puede instalar la última versión de Azure AD Connect para habilitar la actualización de esas contraseñas directamente desde la nube.  Esto significa que cuando los usuarios olvida o desea modificar su contraseña de AD, puede hacer lo directamente desde la web.  Aquí es dónde encontrar reescritura de contraseña en el Asistente para instalación de Azure AD Connect:

  ![][007]

Para obtener más información acerca de Azure AD Connect, consulte [Introducción: Azure AD Connect](active-directory-aadconnect.md). Para obtener más información sobre la reescritura de contraseña, consulte [Introducción: administración de contraseñas de Azure AD](active-directory-passwords-getting-started.md).


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Vínculos a contraseña restablecer documentación
A continuación se muestran vínculos a todas las páginas de documentación de restablecimiento de contraseña de Azure AD:

* **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [He aquí cómo puede cambiar y restablecer su propia contraseña](active-directory-passwords-update-your-own-password.md).
* [**Introducción**](active-directory-passwords-getting-started.md) : Obtenga información sobre cómo permitir que los usuarios restablecer y cambiar sus contraseñas de nube o local
* [**Personalizar**](active-directory-passwords-customize.md) - obtener información sobre cómo personalizar el aspecto y apariencia y comportamiento del servicio a las necesidades de su organización
* [**Procedimientos recomendados**](active-directory-passwords-best-practices.md) : Obtenga información sobre cómo implementar rápidamente y eficaz administrar contraseñas en su organización
* [**Obtener recomendaciones**](active-directory-passwords-get-insights.md) - Obtenga más información sobre nuestras capacidades integradas de reporting
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md) : Obtenga respuestas a las preguntas más frecuentes
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md) - Obtenga información sobre cómo solucionar problemas con el servicio de rápidamente
* [**Más información**](active-directory-passwords-learn-more.md) - vaya profundidad los detalles técnicos de cómo funciona el servicio



[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"
