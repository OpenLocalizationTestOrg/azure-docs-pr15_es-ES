<properties
    pageTitle="Procedimientos recomendados: Administración de contraseña de Azure AD | Microsoft Azure"
    description="Las mejores prácticas de implementación y uso, documentación de usuario final de ejemplo y guías de aprendizaje para la administración de contraseñas de Azure Active Directory."
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

# <a name="deploying-password-management-and-training-users-to-use-it"></a>Implementar la administración de la contraseña y formar a los usuarios a usarlo

> [AZURE.IMPORTANT] **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [He aquí cómo puede cambiar y restablecer su propia contraseña](active-directory-passwords-update-your-own-password.md).

Después de restablecer la contraseña de activación, el siguiente paso que debe tomar es que los usuarios con el servicio de su organización. Para ello, debe asegurarse de que los usuarios están configurados para usar el servicio correctamente y también que los usuarios tienen la formación que necesitan para tener éxito en la administración de sus contraseñas. En este artículo se explica que los conceptos siguientes:

* [**Cómo obtener los usuarios configurados para la administración de contraseñas**](#how-to-get-users-configured-for-password-reset)
  * [Qué hace que sea una cuenta configurada para el restablecimiento de contraseña](#what-makes-an-account-configured)
  * [Formas en que puede para rellenar datos de autenticación usted mismo](#ways-to-populate-authentication-data)
* [**Las mejores formas para dar a conocer la contraseña restablecer a su organización**](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [Implementación basada en correo electrónico + muestra las comunicaciones de correo electrónico](#email-based-rollout)
  * [Crear su propio portal de administración de contraseñas personalizadas para sus usuarios](#creating-your-own-password-portal)
  * [Cómo usar el registro obligatoria para obligar a los usuarios registrarse en el inicio de sesión](#using-enforced-registration)
  * [Cómo cargar los datos de autenticación de cuentas de usuario](#uploading-data-yourself)
* [**Usuario de ejemplo y materiales de formación de soporte técnico (próximamente)**](#sample-training-materials)

## <a name="how-to-get-users-configured-for-password-reset"></a>Cómo obtener usuarios configurada para restablecer contraseña
Esta sección describe que diferentes métodos que puede asegurarse de que todos los usuarios de su organización pueden usar la contraseña de autoservicio restablecer eficaz en caso de olvidan su contraseña.

### <a name="what-makes-an-account-configured"></a>Qué hace que sea una cuenta configurada
Antes de que un usuario puede usar el restablecimiento de contraseña, debe cumplir **todas** las condiciones siguientes:

1.  Restablecimiento de contraseña debe estar habilitado en el directorio.  Aprenda a habilitar contraseña restablezca la lectura [permiten a los usuarios restablecer sus contraseñas AD Azure](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) o [Permitir a los usuarios restablecer o cambiar sus contraseñas de AD](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
2.  Debe tener una licencia de usuario.
 - Para los usuarios de la nube, el usuario debe tener ** **cualquier pagada licencia de Office 365**, o un AAD básica** o **Premium AAD licencia** asignada.
 - Para local usuarios (federado o hash sincronizado), el usuario **debe tener una licencia de AAD Premium asignada**.
3.  El usuario debe tener el **conjunto de datos de autenticación definidos mínimo** según la directiva de restablecimiento de contraseña actual.
 - Datos de autenticación se consideran definida si el campo correspondiente en el directorio contiene datos con formato correcto.
 - Un conjunto de datos de autenticación mínimo está definido como al **menos una** de las opciones de autenticación habilitados si se configura una directiva de una puerta o al **menos dos** de las opciones de autenticación habilitados si se configura una directiva de dos puerta.
4.  Si el usuario usa una cuenta local, a continuación, [Reescritura de contraseña](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) debe habilitarse y activado

### <a name="ways-to-populate-authentication-data"></a>Formas para rellenar datos de autenticación
Tiene varias opciones sobre cómo especificar los datos de los usuarios de su organización que se usará para restablecer contraseña.

- Editar usuarios en el [Portal de administración de Azure](https://manage.windowsazure.com) o el [Portal de administración de Office 365](https://portal.microsoftonline.com)
- Usar sincronización de Azure AD sincronizar propiedades de usuario en Azure AD desde un dominio de Active Directory local
- Usar Windows PowerShell para editar las propiedades de usuario siguiendo [los pasos que se indican a continuación](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).
- Permitir a los usuarios registrar sus propios datos, ellos guiar el portal de registro en [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
- Requerir que los usuarios registrar contraseña restablecer cuando inicie sesión en su cuenta de Azure AD estableciendo la [**requerir que los usuarios registrar al iniciar sesión?**](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) la opción de configuración en **Sí**.

Los usuarios no necesitan registrar de restablecimiento de contraseña para que el sistema de trabajo.  Por ejemplo, si tiene mobile existente o números de teléfono de office en el directorio local, puede sincronizar en Azure AD y usaremos para restablecer automáticamente contraseñas.

También puede leer más información sobre [cómo se utilizan los datos por contraseña restablece](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) y [cómo puede rellenar los campos individuales de autenticación con PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).

## <a name="what-is-the-best-way-to-roll-out-password-reset-for-users"></a>¿Qué es la mejor manera para dar a conocer el restablecimiento de contraseña para los usuarios?
Los siguientes son los pasos de desarrollo general de restablecimiento de contraseña:

1.  Habilitar contraseña restablecer en su directorio yendo a la ficha **Configurar** en el [Portal de administración de Azure](https://manage.windowsazure.com) y seleccionando **Sí** para la opción de **Los usuarios habilitados para restablecer la contraseña** .
2.  Asignar las licencias que corresponda a cada usuario a quien le gustaría ofrecer contraseña restablecer en la, vaya a la pestaña de **licencias** en el [Portal de administración de Azure](https://manage.windowsazure.com).
3.  Opcionalmente, restringir contraseña restablecer a un grupo de usuarios para dar a conocer la característica lentamente el tiempo estableciendo la alternancia de **Restringir el acceso a restablecer la contraseña** en **Sí** y selección de un grupo de seguridad para habilitar el restablecimiento de contraseña (tenga en cuenta estos usuarios deben tener licencias asignadas).
4.  Pida a los usuarios usar contraseña restablecer enviándoles en un correo electrónico indicarles que registrar, habilitar aplica el registro en el panel de acceso o cargar los datos de autenticación apropiado para esos usuarios mediante sincronización de directorios, PowerShell o el [Portal de administración de Azure](https://manage.windowsazure.com).  A continuación, se proporcionan más detalles sobre este.
5.  Con el tiempo, revise los usuarios registrar navegar a la pestaña informes y ver el informe de [**Actividad de registro de restablecimiento de contraseña**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity) .
6.  Una vez que haya registrado un buen número de usuarios, vea Usar contraseña restablecer, desplácese hasta la ficha informes y ver el informe de [**Actividad de restablecer la contraseña**](active-directory-passwords-get-insights.md#view-password-reset-activity) .

Hay varias formas para informar a los usuarios que pueden registrarse y utilizar contraseña restablecer de su organización.  Se explica a continuación.

### <a name="email-based-rollout"></a>Implementación basada en correo electrónico
Quizá la manera más sencilla de informar a los usuarios acerca de para registrarse en o usar contraseña restablece es mediante el envío de un correo electrónico indicarles que hacerlo.  A continuación se muestra una plantilla que puede usar para realizar esta acción.  No dude en reemplazar los colores o logotipos con las de su propia elección personalizarla a sus necesidades.

  ![][001]

Puede [Descargar la plantilla de correo electrónico aquí](http://1drv.ms/1xWFtQM).

### <a name="creating-your-own-password-portal"></a>Crear su propio portal de contraseña
Una estrategia que funciona bien para grandes clientes implementar las capacidades de administración de contraseña es crear una única "portal de contraseña" que los usuarios pueden usar para administrar todos los elementos relacionados con las contraseñas en un único lugar.  

Muchos de nuestros clientes mayor elija para crear una entrada DNS, raíz como https://passwords.contoso.com con vínculos a la contraseña de Azure AD restablecer portal, portal de registro de restablecimiento de contraseña y páginas de cambio de contraseña.  De esta forma, en las comunicaciones por correo electrónico o folletos que enviar, puede incluir un único fáciles de recordar, dirección URL que los usuarios pueden ir a cuando tengan una segunda para empezar con el servicio.

Para empezar a continuación, ha creado una página sencilla que utiliza la últimos paradigmas responde del diseño interfaz de usuario y funcionarán en todos los exploradores y dispositivos móviles.

  ![][007]

Puede [Descargar la plantilla de sitio Web aquí](https://github.com/kenhoff/password-reset-page).  Se recomienda personalizar el logotipo y los colores a las necesidades de su organización.

### <a name="using-enforced-registration"></a>Utilizar el registro obligatoria
Si desea que los usuarios para registrar para ellos mismos restablecer contraseñas, también puede forzar que registrar cuando inicie sesión en el panel de acceso en [http://myapps.microsoft.com](http://myapps.microsoft.com).  Puede habilitar esta opción de la ficha de **Configurar** su directorio activando la opción **Requerir a los usuarios registrar al iniciar sesión en el Panel de acceso** .  

Opcionalmente, también puede definir si o no le pedirá que volver a registrar después de un período de tiempo configurable modificando la opción **número de días antes de que los usuarios deben confirmar sus datos de contacto** a un valor distinto de cero. Para obtener más información, vea [Personalizar el comportamiento de administración de contraseña de usuario](active-directory-passwords-customize.md#password-management-behavior) .

  ![][002]

Después de habilitar esta opción, cuando los usuarios iniciar sesión en el panel de acceso, verán un menú emergente que les informa de que su administrador necesario para comprobar su información de contacto. Puede usarlo para restablecer su contraseña si pierde el acceso a su cuenta.

  ![][003]

Haciendo clic en **Comprobar ahora** se pone el **portal de registro de restablecimiento de contraseña** en [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) y se requiere para registrar.  Registro a través de este método se puede descartar haciendo clic en el botón **Cancelar** o cerrar la ventana, pero los usuarios se le avise cada vez inicie sesión si no se registra.

  ![][004]

### <a name="uploading-data-yourself"></a>Cargar los datos
Si desea cargar los datos de autenticación usted mismo, los usuarios no necesitan registrar de restablecimiento de contraseña antes de poder restablecer sus contraseñas.  Siempre y cuando los usuarios tienen los datos de autenticación definidos en su cuenta que cumpla la contraseña restablecer la directiva que ha definido, a continuación, los usuarios podrán restablecer sus contraseñas.

Para obtener información sobre las propiedades que puede establecer a través de AAD conectar o Windows PowerShell, vea [qué datos se utilizan por el restablecimiento de contraseña](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

Puede cargar los datos de autenticación a través del [Portal de administración de Azure](https://manage.windowsazure.com) siguiendo los pasos siguientes:

1.  Vaya al directorio de la **extensión de Active Directory** en el [Portal de administración de Azure](https://manage.windowsazure.com).
2.  Haga clic en la ficha **usuarios** .
3.  Seleccione el usuario que le interesa en la lista.
4.  En la primera pestaña, encontrará el **Correo electrónico alternativa**, que se puede utilizar como una propiedad para habilitar el restablecimiento de contraseña.

    ![][005]

5.  Haga clic en la pestaña **Información de trabajo** .
6.  En esta página, encontrará el **Teléfono de la oficina**, **teléfono móvil**, **Teléfono de autenticación**y **Correo electrónico de autenticación**.  Estas propiedades también se pueden establecer para permitir que un usuario restablecer su contraseña.

    ![][006]

Vea [qué datos se utilizan por el restablecimiento de contraseña](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) para ver cómo se puede utilizar cada una de estas propiedades.

Vea [cómo tener acceso a la contraseña restablece datos para los usuarios de PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) para ver cómo puede leer y establecer estos datos con PowerShell.

## <a name="sample-training-materials"></a>Materiales de aprendizaje de muestra
Estamos trabajando en materiales de aprendizaje de muestra que puede usar para obtener su organización de TI y los usuarios ponerse al día rápidamente sobre cómo implementar y usar el restablecimiento de contraseña.  ¡Esté atento!


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Vínculos a contraseña restablecer documentación
A continuación se muestran vínculos a todas las páginas de documentación de restablecimiento de contraseña de Azure AD:

* **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [He aquí cómo puede cambiar y restablecer su propia contraseña](active-directory-passwords-update-your-own-password.md).
* [**Cómo funciona**](active-directory-passwords-how-it-works.md) - obtener información sobre los distintos seis componentes del servicio y lo que hace cada uno
* [**Introducción**](active-directory-passwords-getting-started.md) : Obtenga información sobre cómo permitir que los usuarios restablecer y cambiar sus contraseñas de nube o local
* [**Personalizar**](active-directory-passwords-customize.md) - obtener información sobre cómo personalizar el aspecto y apariencia y comportamiento del servicio a las necesidades de su organización
* [**Obtener recomendaciones**](active-directory-passwords-get-insights.md) - Obtenga más información sobre nuestras capacidades integradas de reporting
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md) : Obtenga respuestas a las preguntas más frecuentes
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md) - Obtenga información sobre cómo solucionar problemas con el servicio de rápidamente
* [**Más información**](active-directory-passwords-learn-more.md) - vaya profundidad los detalles técnicos de cómo funciona el servicio



[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-best-practices/007.jpg "Image_007.jpg"
