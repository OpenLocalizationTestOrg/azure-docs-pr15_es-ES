<properties
    pageTitle="Solución de problemas: La administración de contraseñas de Azure AD | Microsoft Azure"
    description="Pasos de solución de problemas comunes Azure AD administración de contraseñas, incluidos restablecer, cambiar, reescritura, registro, y qué información incluir cuando busca temas de ayuda."
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
    ms.date="08/12/2016"
    ms.author="asteen"/>

# <a name="how-to-troubleshoot-password-management"></a>Cómo solucionar problemas de administración de contraseñas

> [AZURE.IMPORTANT] **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [He aquí cómo puede cambiar y restablecer su propia contraseña](active-directory-passwords-update-your-own-password.md).

Si tiene problemas con la administración de contraseñas, estamos aquí para ayudar a. La mayoría de los problemas que puede surgir se puede resolver con unos sencillos pasos para solucionar problemas que puede leer sobre debajo de solucionar problemas de la implementación:

* [**Información que desea incluir cuando necesite ayuda**](#information-to-include-when-you-need-help)
* [**Problemas con la configuración de administración de contraseñas en el Portal de administración de Azure**](#troubleshoot-password-reset-configuration-in-the-azure-management-portal)
* [**Problemas con los informes de administración de la contraseña en el Portal de administración de Azure**](#troubleshoot-password-management-reports-in-the-azure-management-portal)
* [**Problemas con la contraseña restablecer Portal de registro**](#troubleshoot-the-password-reset-registration-portal)
* [**Problemas con la contraseña restablecer Portal**](#troubleshoot-the-password-reset-portal)
* [**Problemas con reescritura de contraseña**](#troubleshoot-password-writeback)
  - [Códigos de error de registro de eventos de reescritura de contraseña](#password-writeback-event-log-error-codes)
  - [Problemas con la conectividad de reescritura de contraseña](#troubleshoot-password-writeback-connectivity)

Si ha probado los siguientes pasos para solucionar problemas y está en ejecución en problemas, puede publicar una pregunta en los [foros de Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) o póngase en contacto con soporte técnico y se le Eche un vistazo a su problema tan pronto como se puede.

## <a name="information-to-include-when-you-need-help"></a>Información que desea incluir cuando necesite ayuda

Si no se resuelve el problema con las instrucciones a continuación, póngase en contacto con los ingenieros de soporte técnico. Cuando se comunique con ellos, se recomienda que incluya la información siguiente:

 - **Descripción general del error** : ¿qué mensaje de error exacto produjo el usuario vea?  Si no había ningún mensaje de error, se describe el comportamiento inesperado observado, en detalle.
 - **Página** : ¿qué página se encontraba en cuando aparece el error (incluya la dirección URL)?
 - **Fecha / hora y zona horaria** : ¿cuál ha sido preciso fecha y hora en que se ha visto el error (incluye la zona horaria)?
 - **Código de soporte técnico** : ¿cuál ha sido el código de soporte que se genera cuando el usuario vio el error (para buscar esta, reproducir el error, a continuación, haga clic en el vínculo de soporte técnico de código en la parte inferior de la pantalla y enviar al técnico de soporte técnico el GUID que se obtiene)
   - Si se encuentra en una página sin código de soporte técnico en la parte inferior, presione F12 y busque SID y CID y enviar los resultados de dos al soporte técnico.

    ![][001]

 - **Identificador de usuario** : ¿cuál ha sido el identificador del usuario que ha visto el error (p. ej.user@contoso.com)?
 - **Información sobre el usuario** : ha sido el usuario federado, hash de contraseña sincronizado, solo la nube?  ¿El usuario tiene asignada una licencia de AAD Premium o básica de AAD?
 - **Registro de eventos de aplicación** : si está usando reescritura de contraseña y el error se encuentra en la infraestructura local, zip, una copia de su registro de eventos de la aplicación de su servidor de Azure AD Connect y enviar junto con su solicitud.

Incluir esta información nos ayudará a resolver el problema lo antes posible.


## <a name="troubleshoot-password-reset-configuration-in-the-azure-management-portal"></a>Solucionar problemas de configuración de restablecimiento de contraseña en el Portal de administración de Azure
Si se produce un error al restablece la configuración de contraseña, es posible que pueda resolver siguiendo los pasos de solución de problemas siguientes:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de error</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>¿Qué tipo de error ve un usuario?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solución</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>No puedo ver la sección de <strong>La directiva de restablecimiento de contraseña de usuario </strong>en la ficha <strong>Configurar</strong> en el portal de administración de Azure</p>
            </td>
            <td>
              <p>La sección de <strong>La directiva de restablecimiento de contraseña de usuario </strong>no está visible en la ficha <strong>Configurar</strong> en el Portal de administración de Azure.</p>
            </td>
            <td>
              <p>Esto puede ocurrir si no tiene una licencia de AAD Premium o básica de AAD asignada a la administración de realizar esta operación. </p>
              <p>Para corregir esto, asigne una licencia de AAD Premium o básica de AAD la cuenta de administrador en cuestión, vaya a la pestaña de <strong>licencias</strong> y vuelva a intentarlo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>No veo ninguna de las opciones de configuración en la sección de <strong>La directiva de restablecimiento de contraseña de usuario</strong> que se describen en la documentación.</p>
            </td>
            <td>
              <p>La sección de <strong>La directiva de restablecimiento de contraseña de usuario </strong>está visible, pero el único indicador que aparece debajo de ella es la marca de <strong>Los usuarios habilitados para restablecer la contraseña</strong> .</p>
            </td>
            <td>
              <p>Aparecerá el resto de la interfaz de usuario cuando se cambia la marca de <strong>Los usuarios habilitados para restablecer la contraseña</strong> para <strong>sí.</strong></p>
            </td>
          </tr>
          <tr>
            <td>
              <p>No veo una opción de configuración en particular.</p>
            </td>
            <td>
              <p>Por ejemplo, no se ve la opción de <strong>número de días antes de que un usuario debe confirmar datos de sus contactos</strong> cuando desplace por la sección de <strong>La directiva de restablecimiento de contraseña de usuario</strong> (o en otros ejemplos del mismo problema).</p>
            </td>
            <td>
              <p>Muchos elementos de interfaz de usuario están ocultas hasta que se necesiten. Intente habilitar todas las opciones en la página si desea ver.</p>
              <p>Para obtener más información acerca de todos los controles que están disponibles para usted, vea <a href="active-directory-passwords-customize.md#password-management-behavior">comportamiento de la administración de contraseñas</a> .</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>No puedo ver la opción de configuración de <strong>Escribir volver contraseñas a local</strong></p>
            </td>
            <td>
              <p>La opción de <strong>Escribir volver contraseñas a local</strong> no está visible en la ficha <strong>Configurar</strong> en el Portal de administración de Azure</p>
            </td>
            <td>
              <p>Esta opción solo está visible si ha descargado Azure AD Connect y configurado reescritura de contraseña. Cuando haya hecho esto, esta opción aparecerá y le permite habilitar o deshabilitar reescritura de la nube.</p>
              <p>Para obtener más información acerca de cómo hacerlo, vea <a href="active-directory-passwords-getting-started.md#step-2-enable-password-writeback-in-azure-ad-connect">Habilitar reescritura de contraseña en Azure AD Connect</a> .</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-management-reports-in-the-azure-management-portal"></a>Solucionar problemas de informes de administración de contraseña en el Portal de administración de Azure
Si se produce un error al usar los informes de administración de la contraseña, es posible que pueda resolver siguiendo los pasos de solución de problemas siguientes:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de error</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>¿Qué tipo de error ve un usuario?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solución</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>No puedo ver los informes de administración de contraseña</p>
            </td>
            <td>
              <p>Los informes de <strong>actividad de restablecimiento de contraseña</strong> y <strong>la actividad de registro de restablecimiento de contraseña</strong> no están visibles en los informes de <strong>Registro de actividad</strong> en la pestaña <strong>informes</strong> .</p>
            </td>
            <td>
              <p>Esto puede ocurrir si no tiene una licencia de AAD Premium o básica de AAD asignada a la administración de realizar esta operación. </p>
              <p>Para corregir esto, asigne una licencia de AAD Premium o básica de AAD la cuenta de administrador en cuestión, vaya a la pestaña de <strong>licencias</strong> y vuelva a intentarlo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Registros de usuario mostrar varias veces</p>
            </td>
            <td>
              <p>Cuando registra un usuario de correo electrónico alternativa, teléfonos móviles y preguntas de seguridad, cada se muestran como líneas separadas en lugar de una sola línea.</p>
            </td>
            <td>
              <p>Actualmente, cuando un usuario se registra, no podemos asumir que se registrará todo presentes en la página de registro. Como resultado, actualmente se inicie cada dato individual que está registrado como un evento independiente.</p>
              <p>Si desea agregar estos datos, puede descargar el informe y abrir los datos como una tabla dinámica de excel para tener más flexibilidad.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Solucionar el portal de registro de restablecimiento de contraseña
Si se produce un error al registrar un usuario para el restablecimiento de contraseña, es posible que pueda resolver siguiendo los pasos de solución de problemas siguientes:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de error</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>¿Qué tipo de error ve un usuario?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solución</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directorio no está habilitado para el restablecimiento de contraseña</p>
            </td>
            <td>
              <p>El administrador no ha habilitado el uso de esta característica.</p>
            </td>
            <td>
              <p>Cambie la marca de <strong>Los usuarios habilitados para restablecer la contraseña</strong> en <strong>Sí</strong> y haga clic en <strong>Guardar</strong> en la ficha de configuración del directorio de Portal de administración de Azure. Debe tener un Azure AD Premium o básica licencia asignada a la administración de realizar esta operación.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>El usuario no tiene una licencia de AAD Premium o básica de AAD asignada</p>
            </td>
            <td>
              <p>El administrador no ha habilitado el uso de esta característica.</p>
            </td>
            <td>
              <p>Asignar una licencia de Azure AD Premium o básica de Azure AD al usuario en la pestaña de <strong>licencias</strong> en el Portal de administración de Azure. Debe tener un Azure AD Premium o básica licencia asignada a la administración de realizar esta operación.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Solicitud de procesamiento de error</p>
            </td>
            <td>
              <p>Verá un error que indica:</p>
              <p>

              </p>
              <p>Solicitud de procesamiento de error </p>
              <p>Al intentar restablecer una contraseña.</p>
            </td>
            <td>
              <p>Esto puede deberse a muchos problemas, pero generalmente este error se produce por alguno un servicio configuración o interrupciones del problema que no se puede resolver. </p>
              <p>Si ve este error y se que afectan a su empresa, póngase en contacto con soporte técnico y le ayudaremos lo antes posible. Vea la <a href="#information-to-include-when-you-need-help">información que desea incluir cuando necesite ayuda</a> para ver lo que debe proporcionar al soporte técnico para ayudar en la resolución rápida.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-the-password-reset-portal"></a>Solucionar problemas del portal de restablecimiento de contraseña
Si se produce un error al restablecer la contraseña de un usuario, es posible que pueda resolver siguiendo los pasos de solución de problemas siguientes:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de error</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>¿Qué tipo de error ve un usuario?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solución</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directorio no está habilitado para el restablecimiento de contraseña</p>
            </td>
            <td>
              <p>Su cuenta no está habilitada para el restablecimiento de contraseña</p>
              <p>Lo sentimos, pero el administrador no ha configurado su cuenta para usar con este servicio. </p>
              <p>

              </p>
              <p>Si le gustaría, nos podemos en contacto con un administrador de su organización para que restablezca su contraseña.</p>
            </td>
            <td>
              <p>Cambie la marca de <strong>Los usuarios habilitados para restablecer la contraseña</strong> en <strong>Sí</strong> y haga clic en <strong>Guardar</strong> en la ficha de configuración del directorio de Portal de administración de Azure. Debe tener un Azure AD Premium o básica licencia asignada a la administración de realizar esta operación.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>El usuario no tiene una licencia de AAD Premium o básica de AAD asignada</p>
            </td>
            <td>
              <p>Aunque no podemos restablecer las contraseñas de cuenta de administrador no automáticamente, nos podemos en contacto con Administrador de su organización para que lo haga por usted.</p>
            </td>
            <td>
              <p>Asignar una licencia de Azure AD Premium o básica de Azure AD al usuario en la pestaña de <strong>licencias</strong> en el Portal de administración de Azure. Debe tener un Azure AD Premium o básica licencia asignada a la administración de realizar esta operación.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directorios está habilitada para el restablecimiento de contraseña, pero el usuario tiene información de autenticación que faltan o incorrecto</p>
            </td>
            <td>
              <p>Su cuenta no está habilitada para el restablecimiento de contraseña</p>
              <p>Lo sentimos, pero el administrador no ha configurado su cuenta para usar con este servicio. </p>
              <p>

              </p>
              <p>Si le gustaría, nos podemos en contacto con un administrador de su organización para que restablezca su contraseña.</p>
            </td>
            <td>
              <p>Asegúrese de que el usuario ha creado correctamente datos de contacto en el archivo en el directorio antes de continuar. Para obtener información sobre cómo configurar la información de autenticación en el directorio para que los usuarios no ven este error, consulte <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">qué datos se utilizan por el restablecimiento de contraseña</a> .</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directorios está habilitada para el restablecimiento de contraseña, pero un usuario tiene solo una parte de datos de contacto en el archivo cuando directiva es requiere dos pasos de comprobación</p>
            </td>
            <td>
              <p>Su cuenta no está habilitada para el restablecimiento de contraseña</p>
              <p>Lo sentimos, pero el administrador no ha configurado su cuenta para usar con este servicio. </p>
              <p>

              </p>
              <p>Si le gustaría, nos podemos en contacto con un administrador de su organización para que restablezca su contraseña.</p>
            </td>
            <td>
              <p>Asegúrese de que el usuario tiene al menos dos métodos de contacto configurado correctamente (por ejemplo, teléfonos móviles y teléfono de la oficina) antes de continuar. Para obtener información sobre cómo configurar la información de autenticación en el directorio para que los usuarios no ven este error, consulte <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">qué datos se utilizan por el restablecimiento de contraseña</a> .</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directorios está habilitada para el restablecimiento de contraseña y usuario está configurado correctamente, pero no pueden ponerse en contacto con </p>
            </td>
            <td>
              <p>¡Vaya!  Hemos encontrado un error inesperado al contacto con usted.</p>
            </td>
            <td>
              <p>Esto podría ser el resultado de un error de servicio temporal o mal configurado datos de contactos que no se ha detectado correctamente. Si el usuario espera 10 segundos, inténtelo de nuevo y aparece el vínculo "Póngase en contacto con el administrador". Vuelva a hacer clic en probar volver a enviar la llamada, mientras que hace clic en "Póngase en contacto con el administrador" le enviará un correo electrónico de formulario al usuario, contraseña o los administradores globales (en ese orden de prioridad) solicitar una restablecimiento de contraseña para realizarse para esa cuenta de usuario.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Usuario nunca recibe el restablecimiento de contraseña SMS o llamada telefónica</p>
            </td>
            <td>
              <p>Usuario hace clic en "texto me" o "Llamarme" y nunca recibe nada.</p>
            </td>
            <td>
              <p>Esto podría deberse a un número de teléfono incorrecto en el directorio. Asegúrese de que es el número de teléfono en el formato "+ ccc xxxyyyzzzzXeeee". Para obtener más información sobre el formato de teléfono números para su uso con el restablecimiento de contraseña ver <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">qué datos se utilizan por el restablecimiento de contraseña</a>.</p>
              <p>Si necesita una extensión de enrutamiento para el usuario en cuestión, tenga en cuenta el restablecimiento de contraseña no admite las extensiones, incluso si se especifica en el directorio (se eliminan antes de la llamada se envía). Pruebe con un número sin una extensión o integrar la extensión en el número de teléfono en su PBX.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Usuario nunca recibe correo electrónico de restablecimiento de contraseña</p>
            </td>
            <td>
              <p>Usuario hace clic en "Enviarme un correo electrónico" y nunca recibe nada.</p>
            </td>
            <td>
              <p>La causa más común para este problema es que el mensaje es rechazado por el filtro de correo no deseado. Comprobar el correo no deseado, la carpeta Elementos eliminados o correo no deseado para el correo electrónico.</p>
              <p>Asegúrese también de comprobación de la derecha de correo electrónico para el mensaje... muchas personas tienen las direcciones de correo electrónico muy similar y finalizar la comprobación de la Bandeja de entrada incorrecta para el mensaje. Si ninguna de estas opciones funcionan, también es posible que la dirección de correo electrónico en el directorio es incorrecta, compruebe para asegurarse de que la dirección de correo electrónico es la adecuada y vuelva a intentarlo. Para obtener más información sobre el formato de correo electrónico direcciones para su uso con el restablecimiento de contraseña ver <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">qué datos se utilizan por el restablecimiento de contraseña</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>He establezco una directiva de restablecimiento de contraseña, pero cuando una cuenta de administrador utiliza el restablecimiento de contraseña, no se aplica esta directiva</p>
            </td>
            <td>
              <p>Restablecer las contraseñas de cuentas de administrador ver las mismas opciones habilitadas para restablecer la contraseña, correo electrónico y teléfono móvil, independientemente de qué directiva se establece en la sección de <strong>La directiva de restablecimiento de contraseña de usuario</strong> de la ficha <strong>Configurar</strong> .</p>
            </td>
            <td>
              <p>Las opciones configuradas en la sección de <strong>La directiva de restablecimiento de contraseña de usuario</strong> de la ficha <strong>Configurar</strong> solo se aplican a los usuarios finales de su organización.</p>
              <p>Microsoft administra y controles de la administración de restablecimiento de contraseña directiva para garantizar el máximo nivel de seguridad</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Impedido intentar restablecer demasiadas veces en un día contraseñas de usuario</p>
            </td>
            <td>
              <p>Verá un error que indica:</p>
              <p>

              </p>
              <p>Use otra opción.</p>
              <p>Ha intentado Compruebe la cuenta demasiadas veces en la última 1 horas. Por motivos de seguridad, debe esperar 24 horas antes de que puede volver a intentarlo. </p>
              <p>Si le gustaría, nos podemos en contacto con un administrador de su organización para que restablezca su contraseña.</p>
            </td>
            <td>
              <p>Se implementa un mecanismo de límite automático para bloquear a los usuarios de intentar restablecer las contraseñas demasiadas veces en un breve período de tiempo. Esto ocurre cuando:</p>
              <ol class="ordered">
                <li>
Usuario intenta validar un número de teléfono 5 veces en una hora.<br\><br\></li>
                <li>
Usuario intenta utilizar la puerta de preguntas de seguridad 5 veces en una hora.<br\><br\></li>
                <li>
Usuario intenta restablecer una contraseña para la misma cuenta de usuario 5 veces en una hora.<br\><br\></li>
              </ol>
              <p>Para corregir este error, indique al usuario que espere 24 horas después del último intento y el usuario podrá restablecer su contraseña.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Verá un error al validar su número de teléfono</p>
            </td>
            <td>
              <p>Al intentar comprobar un teléfono que se utilizará como método de autenticación, el usuario ve un error que indica:</p>
              <p>

              </p>
              <p>Número de teléfono incorrecto especificado.</p>
            </td>
            <td>
              <p>Este error se produce cuando el número de teléfono especificado no coincide con el número de teléfono en el archivo.</p>
              <p>Asegúrese de que el usuario escribe el número de teléfono completo, incluido el código de área y país, al intentar utilizar un método de teléfono para restablecer contraseña.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Solicitud de procesamiento de error</p>
            </td>
            <td>
              <p>Verá un error que indica:</p>
              <p>

              </p>
              <p>Solicitud de procesamiento de error </p>
              <p>Al intentar restablecer una contraseña.</p>
            </td>
            <td>
              <p>Esto puede deberse a muchos problemas, pero generalmente este error se produce por alguno un servicio configuración o interrupciones del problema que no se puede resolver. </p>
              <p>Si ve este error y se que afectan a su empresa, póngase en contacto con soporte técnico y le ayudaremos lo antes posible. Vea la <a href="#information-to-include-when-you-need-help">información que desea incluir cuando necesite ayuda</a> para ver lo que debe proporcionar al soporte técnico para ayudar en la resolución rápida.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-writeback"></a>Solucionar problemas de reescritura de contraseña
Si se produce un error al habilitar, deshabilitar o utilizar reescritura de contraseña, es posible que pueda resolver siguiendo los pasos de solución de problemas siguientes:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de error</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>¿Qué tipo de error ve un usuario?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solución</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errores de inicio y general integrado</p>
            </td>
            <td>
              <p>Servicio de restablecimiento de contraseña no se inicia en local con error 6800 en registro de eventos de aplicación de la máquina de Azure AD Connect.</p>
              <p>

              </p>
              <p>Después de integrado, federado o hash de contraseña usuarios sincronizados no pueden restablecer sus contraseñas.</p>
            </td>
            <td>
              <p>Cuando se habilita la reescritura de contraseña, el motor de sincronización llamará la biblioteca de reescritura para llevar a cabo la configuración (integrado) hablando con el servicio de nube integrado. Los errores encontrados durante integrado o al iniciar el extremo WCF para reescritura de contraseña producirá errores en el registro de eventos de registro de eventos de su equipo de Azure AD Connect.</p>
              <p>Durante el reinicio del servicio ADSync, si se configuró reescritura, se iniciará el extremo WCF hacia arriba. Sin embargo, si se produce un error en el inicio del extremo, simplemente se registrar evento 6800 y permitir que el inicio del servicio de sincronización. Presencia de este evento significa que la reescritura de contraseña no se ha iniciado el extremo hacia arriba. Detalles de registro de eventos de este evento (6800) junto con las entradas de registro de eventos generan PasswordResetService componente indicará por qué el extremo no se pudo iniciar. Revise estos errores de registro de eventos e intente volver a iniciar Azure AD Connect si todavía no funciona la reescritura de contraseña. Si el problema continúa, intente deshabilitar y volver a habilitar reescritura de contraseña.</p>
            </td>
          </tr>
                    <tr>
            <td>
              <p>Cuando un usuario intenta restablecer una contraseña o desbloquear una cuenta con reescritura de contraseña habilitado, se produce un error en la operación. Además, verá un evento en el registro de eventos de Azure AD Connect que contiene: "motor de sincronización devuelve un error hr = 800700CE, mensaje = el nombre de archivo o la extensión es demasiado larga" después de la operación de desbloqueo.
                            </p>
            </td>
            <td>
              <p>Esto puede ocurrir si había actualizado desde versiones anteriores de Azure AD Connect o la sincronización de directorios. Actualizar a las versiones anteriores de Azure AD Connect establece una contraseña de 254 carácter para la cuenta de Azure AD Management Agent (versiones más recientes establecen una contraseña de longitud de 127 caracteres). Trabajar como contraseñas largas para operaciones de conector de AD importar y exportar pero no son compatibles con la operación de desbloqueo.
                            </p>
            </td>
            <td>
              <p>[Buscar la cuenta de Active Directory](active-directory-aadconnect-accounts-permissions.md#active-directory-account) de Azure AD Connect y restablecer la contraseña para tener no más de 127 caracteres. A continuación, abra el **Servicio de sincronización** en el menú Inicio. Vaya a **conectores** y encontrar el **Conector de Active Directory**. Selecciónelo y haga clic en **Propiedades**. Vaya a la página **credenciales** y escriba la nueva contraseña. Seleccione **Aceptar** para cerrar la página.
                            </p>
            </td>
          </tr>
                    <tr>
            <td>
              <p>Error al configurar reescritura durante la instalación de Azure AD Connect.</p>
            </td>
            <td>
              <p>En el último paso del proceso de instalación de Azure AD Connect, verá un error que indica que no se pudo configurar reescritura de contraseña.</p>
              <p>

              </p>
              <p>El registro de eventos de la aplicación para conectar Azure AD contiene error 32009 con el texto "Error al obtener auth token".</p>
            </td>
            <td>
              <p>Este error se produce en los dos casos siguientes:</p>
              <ul>
                <li class="unordered">
Ha especificado una contraseña incorrecta de la cuenta de administrador global especificado al principio del proceso de instalación de Azure AD Connect.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Ha intentado utilizar un usuario federado para la cuenta de administrador global especificada al principio del proceso de instalación de Azure AD Connect.<br\><br\></li>
              </ul>
              <p>Para corregir este error, asegúrese de que no está utilizando una cuenta federada para el administrador global que especificó al principio del proceso de instalación de Azure AD Connect y que la contraseña especificada es correcta.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error al configurar reescritura durante la instalación de Azure AD Connect.</p>
            </td>
            <td>
              <p>El registro de eventos del equipo de Azure AD Connect contiene error 32002 iniciada por la PasswordResetService.</p>
              <p>

              </p>
              <p>Lee el error: "Error conectarse a ServiceBus, el proveedor de tokens no pudo proporcionar un token de seguridad..."</p>
              <p>

              </p>
            </td>
            <td>
              <p>La causa de este error es que el servicio de restablecimiento de contraseña ejecuta en su entorno local no puede conectarse al extremo del bus de servicios en la nube. Este error normalmente normalmente se debe a una regla de firewall que bloquea una conexión saliente a una dirección web o puerto particular.</p>
              <p>

              </p>
              <p>Asegúrese de que el firewall permite las conexiones salientes para lo siguiente:</p>
              <ul>
                <li class="unordered">
Todo el tráfico sobre TCP 443 (HTTPS)<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Conexiones salientes a <br\><br\></li>
              </ul>
              <p>

              </p>
              <p>Una vez que haya actualizado estas reglas, reinicie el equipo de Azure AD Connect y reescritura de contraseña debe comenzar a trabajar de nuevo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Contraseña reescritura extremo local inaccesible</p>
            </td>
            <td>
              <p>Después de trabajar para algunas tiempo, federado o hash de contraseña usuarios sincronizados no pueden restablecer las contraseñas.</p>
            </td>
            <td>
              <p>En algunos casos poco comunes, el servicio de reescritura de contraseña no volver a iniciar cuando se vuelva a ha iniciado Azure AD Connect. En estos casos, en primer lugar, compruebe si parece ser prem. habilitado reescritura de contraseña Esto puede hacerse mediante el Asistente de Azure AD Connect o powershell (consulte procedimientos sección anterior). Si aparece la característica esté habilitada, pruebe a habilitar o deshabilitar la característica de nuevo a través de la interfaz de usuario o PowerShell. Consulte "paso 2: Habilitar reescritura de contraseña en el equipo de sincronización de directorio &amp; configurar reglas de firewall" en <a href="active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords">cómo habilitar o deshabilitar reescritura de contraseña</a> para obtener más información acerca de cómo hacerlo.</p>
              <p>

              </p>
              <p>Si esto no funciona, intente desinstalar completamente y volver a instalar Azure AD Connect.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errores de permisos</p>
            </td>
            <td>
              <p>Federado o contraseña hash sincronizados los usuarios que intenten restablecer su consulte contraseñas un error después de enviar la contraseña que indica que se ha producido un problema de servicio.</p>
              <p>

              </p>
              <p>Además, durante las operaciones de restablecimiento de contraseña, es posible que vea que un error relacionado con el agente de administración ha denegado el acceso en los registros de eventos en local.</p>
            </td>
            <td>
              <p>Si ve estos errores en el registro de eventos, confirme que la cuenta de AD MA (que se especificó en el Asistente en el momento de la configuración) tiene los permisos necesarios para la reescritura de contraseña.</p>
              <p>

              </p>
              <p>Tenga en cuenta que una vez que este permiso puede tardar hasta 1 hora los permisos filtrar hacia abajo a través de la tarea en segundo plano sdprop en el controlador de dominio. </p>
              <p>Para el restablecimiento de contraseña para trabajar, debe quedar marcado en el descriptor de seguridad del objeto de usuario cuya contraseña es se restablece el permiso. Hasta que este permiso aparece en el objeto de usuario, seguirá restablecimiento de contraseña no se realice correctamente con acceso denegado.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error al configurar la reescritura de contraseña desde el Asistente para configuración de Azure AD Connect </p>
            </td>
            <td>
              <p>Error "No se puede localizar MA" Asistente al habilitar o deshabilitar reescritura de contraseña</p>
            </td>
            <td>
              <p>Hay un error conocido en la versión comercial de Azure AD Connect que manifiestos en las situaciones siguientes:</p>
              <ol class="ordered">
                <li>
Configurar Azure AD Connect para abc.com inquilino (dominio comprobado) con credenciales. El resultado es conector AAD con nombre "abc.com – AAD" se ha creado.<br\><br\></li>
                <li>
A continuación, a continuación, cambia las credenciales AAD para el conector (con sincronización antiguo interfaz de usuario) para (tenga en cuenta que es el mismo inquilino, pero el nombre de dominio diferente). <br\><br\></li>
                <li>
Ahora intente habilitar o deshabilitar reescritura de contraseña. El asistente crear el nombre del conector con las credenciales, como "abc.onmicrosoft.com-AAD" y pasar al cmdlet reescritura de contraseña. Esto se producirá un error porque no hay ningún conector creado con este nombre.<br\><br\></li>
              </ol>
              <p>Esto se ha corregido en nuestras las últimas versiones. Si tiene una versión anterior, una solución es usar el cmdlet de powershell para habilitar o deshabilitar la característica. Consulte "paso 2: Habilitar reescritura de contraseña en el equipo de sincronización de directorio &amp; configurar reglas de firewall" en <a href="active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords">cómo habilitar o deshabilitar reescritura de contraseña</a> para obtener más información acerca de cómo hacerlo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>No se puede restablecer la contraseña para los usuarios de los grupos especiales, como los administradores de dominio o etcetera de los administradores de empresa.</p>
            </td>
            <td>
              <p>Federado o contraseña hash sincronizados los usuarios que forman parte de grupos protegidos e intentan restablecer su consulte contraseñas un error después de enviar la contraseña que indica que se ha producido un problema de servicio.</p>
            </td>
            <td>
              <p>Los usuarios con privilegios de Active Directory están protegidos mediante AdminSDHolder. Consulte <a href="https://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx">http://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx</a> para obtener más detalles. </p>
              <p>

              </p>
              <p>Esto significa que los descriptores de seguridad en estos objetos se comprueban periódicamente para que coincida con el AdminSDHolder especificado en uno y se restablecen si son diferentes. Por lo tanto, los permisos adicionales que son necesarias para reescritura de contraseña no filtrar a dichos usuarios. Esto puede provocar reescritura de contraseña no funciona para estos usuarios. Por tanto, no se admiten administrar contraseñas para los usuarios dentro de estos grupos porque cumple el modelo de seguridad de AD.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>No se encontró restablecer operaciones correctamente con objeto</p>
            </td>
            <td>
              <p>Federado o contraseña hash sincronizados los usuarios que intenten restablecer su consulte contraseñas un error después de enviar la contraseña que indica que se ha producido un problema de servicio.</p>
              <p>

              </p>
              <p>Además, durante las operaciones de restablecimiento de contraseña, puede ver un error en los registros de eventos del servicio de Azure AD Connect que indica un error "No se encuentra el objeto".</p>
            </td>
            <td>
              <p>Este error normalmente indica que el motor de sincronización no puede encontrar el objeto de usuario en el espacio del conector AAD o la MV vinculado o el objeto de espacio de conector de Active Directory. </p>
              <p>

              </p>
              <p>Para solucionar este problema, asegúrese de que el usuario en realidad se sincroniza desde local a AAD a través de la instancia actual de Azure AD Connect y compruebe el estado de los objetos de los espacios de conector y MV. Confirme que el objeto de AD CS es conector al objeto MV a través de la regla "Microsoft.InfromADUserAccountEnabled.xxx".</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Restablecer operaciones correctamente con varias coincidencias encontradas eror</p>
            </td>
            <td>
              <p>Federado o contraseña hash sincronizados los usuarios que intenten restablecer su consulte contraseñas un error después de enviar la contraseña que indica que se ha producido un problema de servicio.</p>
              <p>

              </p>
              <p>Además, durante las operaciones de restablecimiento de contraseña, puede ver un error en los registros de eventos del servicio de Azure AD Connect que indica un error "Se encuentra varios maches".</p>
            </td>
            <td>
              <p>Esto indica que el motor de sincronización ha detectado que el objeto de MV está conectado a más de un objetos de AD CS mediante "Microsoft.InfromADUserAccountEnabled.xxx". Esto significa que el usuario tiene una cuenta habilitada en más de un bosque. </p>
              <p>

              </p>
              <p>Actualmente esta situación no es compatible para reescritura de contraseña.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Operaciones de contraseña provocarán un error de configuración.</p>
            </td>
            <td>
              <p>Operaciones de contraseña provocarán un error de configuración. El registro de eventos de aplicación contiene el error de Azure AD Connect 6329 con texto: 0x8023061f (error en la operación porque no está habilitada la sincronización de contraseñas en este agente de administración.)</p>
            </td>
            <td>
              <p>Esto ocurre si se cambia la configuración de Azure AD Connect para agregar&nbsp;un nuevo bosque de AD (o para quitar y volver a agregar un bosque existente) <strong>después de que</strong> ya se ha habilitado la característica de reescritura de contraseña. Operaciones de contraseña para los usuarios de dichos bosques recién agregados se producirá un error. Para solucionar el problema, deshabilitar y volver a habilitar la característica de reescritura de contraseña después de que se han realizado cambios en la configuración del bosque.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Volver a escribir las contraseñas que se restablezcan Works usuarios correctamente, pero volver a escribir las contraseñas de un usuario cambia o restablecer para un usuario por un administrador se produce un error.</p>
            </td>
            <td>
              <p>Al intentar restablecer una contraseña en el nombre de un usuario desde el Portal de administración de Azure, verá el siguiente mensaje: "el servicio de restablecimiento de contraseña ejecuta en su entorno local no admite administradores de restablecimiento de contraseñas de usuario. Actualice a la última versión de Azure AD Connect para resolver este problema."</p>
            </td>
            <td>
              <p>Esto ocurre cuando la versión del motor de sincronización no admite la operación de reescritura de contraseña concreta que se ha usado. Versiones de Azure AD Connect más tarde 1.0.0419.0911 admite todas las operaciones de administración de contraseñas, incluida la contraseña restablece reescritura, reescritura de cambio de contraseña y reescritura de restablecimiento de contraseña iniciados por el administrador desde el Portal de administración de Azure.&nbsp; Sincronización de directorios versiones posteriores a 1.0.6862 con solo reescritura de restablecimiento de contraseña. Para resolver este problema, recomendamos que instale la última versión de Azure AD Connect o conectar de Azure Active Directory (para obtener más información, consulte <a href="active-directory-aadconnect">Herramientas de integración de directorios</a>) para resolver este problema y para sacar el máximo partido de reescritura de la contraseña de su organización.</p>
            </td>
          </tr>
        </tbody></table>


## <a name="password-writeback-event-log-error-codes"></a>Códigos de error de registro de eventos de reescritura de contraseña
Un procedimiento recomendado para solucionar problemas con la reescritura de contraseña es inspeccionar ese registro de eventos de aplicación en el equipo de Azure AD Connect. Este registro de eventos contiene eventos de dos orígenes de interés para la reescritura de contraseña. El origen de PasswordResetService describe las operaciones y problemas relacionados con la operación de reescritura de contraseña. El origen de ADSync describe las operaciones y problemas relacionados con la configuración de contraseñas en su entorno de AD.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Código</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Nombre / mensaje</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Origen</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Descripción</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>6329</p>
            </td>
            <td>
              <p>ESCAPE: MMS(4924) 0x80230619: "una restricción impide que la contraseña se ha cambiado a la actual especificada."</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Este evento se produce cuando el servicio de reescritura de contraseña intenta establecer una contraseña en su directorio local que no alcanza la duración de la contraseña, historial, complejidad o filtrados requisitos del dominio.</p>
              <ul>
                <li class="unordered">
Si tiene una antigüedad mínima de la contraseña y recientemente se ha cambiado la contraseña desde la ventana de tiempo, no podrá cambiar la contraseña de nuevo hasta que alcance la edad especificada en su dominio. Con fines de pruebas, antigüedad mínima se debe establecer a 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si tiene requisitos de historial de contraseña habilitados, debe seleccionar una contraseña que no se ha utilizado en la última N veces, donde N es el valor de historial de contraseñas. Si selecciona una contraseña que se ha utilizado en la última N veces, se muestra un error en este caso. Con fines de pruebas, historial se debe establecer a 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si tiene requisitos de complejidad de contraseña, todos ellos se aplicará cuando el usuario intenta cambiar o restablecer una contraseña.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si tiene filtros de contraseña habilitados y un usuario selecciona una contraseña que no se cumplen los criterios de filtrado, a continuación, restablecer o cambiar se producirá un error en la operación.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>RECURSOS HUMANOS 8023042</p>
            </td>
            <td>
              <p>Motor de sincronización devuelve un error hr = 80230402, mensaje = intentar obtener un error porque hay entradas duplicadas con el mismo delimitador de objeto</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Este evento se produce cuando se habilita el mismo identificador de usuario en varios dominios.  Por ejemplo, si se están sincronizando bosques de cuentas y recursos y tienen el mismo identificador de usuario presente y habilitado en cada uno, este error puede producirse.  </p>
              <p>Este error puede producirse también si está utilizando un atributo no único delimitador (como alias o UPN) y dos usuarios comparten el mismo atributo delimitador.</p>
              <p>Para resolver este problema, asegúrese de que no tiene ningún usuario duplicado dentro de sus dominios y que está usando un atributo delimitador único para cada usuario.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31001</p>
            </td>
            <td>
              <p>PasswordResetStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el servicio local detectado solicitud para un federados de restablecimiento de contraseña o contraseña hash sincronizados usuario que provienen de la nube. Este evento es que el primer evento en cada contraseña restablece la operación de reescritura.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31002</p>
            </td>
            <td>
              <p>PasswordResetSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que un usuario selecciona una nueva contraseña durante la operación de restablecimiento de contraseña, se ha determinado que esta contraseña cumple los requisitos de contraseña corporativo y contraseña se han escrito correctamente en el entorno de AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31003</p>
            </td>
            <td>
              <p>PasswordResetFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que un usuario selecciona una contraseña y que ha llegado contraseña correctamente en el entorno local, pero cuando se intentó establecer la contraseña en el entorno de AD local, se ha producido un error. Esto puede suceder por diversos motivos:</p>
              <ul>
                <li class="unordered">
La contraseña del usuario no cumple la edad, historial, complejidad o filtrar requisitos del dominio. Pruebe una contraseña completamente nueva para resolver este problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
La cuenta de servicio de MA no tiene los permisos adecuados para configurar la nueva contraseña en la cuenta de usuario en cuestión.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
La cuenta del usuario está en un grupo protegido, como los administradores de dominio o de organización, que no permite operaciones de establecer contraseña.<br\><br\></li>
              </ul>
              <p>Vea <a href="#troubleshoot-password-writeback">Solucionar problemas de reescritura de contraseña</a> para obtener más información sobre qué otras situtions puede provocar este error.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31004</p>
            </td>
            <td>
              <p>OnboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento se produce si habilita reescritura de contraseña con Azure AD Connect e indica que se ha iniciado integrado su organización para el servicio web de reescritura de contraseña.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31005</p>
            </td>
            <td>
              <p>OnboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el proceso integrado fue correcto y que la capacidad de reescritura de contraseña está listo para usarse.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31006</p>
            </td>
            <td>
              <p>ChangePasswordStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el servicio local detecta una solicitud de cambio de contraseña para una federado o contraseña hash sincronizados usuario que provienen de la nube. Este evento es la primera en cada operación de reescritura de cambio de contraseña.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31007</p>
            </td>
            <td>
              <p>ChangePasswordSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que un usuario selecciona una nueva contraseña durante una operación de cambio de contraseña, se ha determinado que esta contraseña cumple los requisitos de contraseña corporativo y contraseña se han escrito correctamente en el entorno de AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31008</p>
            </td>
            <td>
              <p>ChangePasswordFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que un usuario selecciona una contraseña y que ha llegado contraseña correctamente en el entorno local, pero cuando se intentó establecer la contraseña en el entorno de AD local, se ha producido un error. Esto puede suceder por diversos motivos:</p>
              <ul>
                <li class="unordered">
La contraseña del usuario no cumple la edad, historial, complejidad o filtrar requisitos del dominio. Pruebe una contraseña completamente nueva para resolver este problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
La cuenta de servicio de MA no tiene los permisos adecuados para configurar la nueva contraseña en la cuenta de usuario en cuestión.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
La cuenta del usuario está en un grupo protegido, como los administradores de dominio o de organización, que no permite operaciones de establecer contraseña.<br\><br\></li>
              </ul>
              <p>Vea <a href="#troubleshoot-password-writeback">Solucionar problemas de reescritura de contraseña</a> para obtener más información sobre qué otras situaciones pueden producir este error.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31009</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>El servicio local detectado solicitud para un federados de restablecimiento de contraseña o contraseña hash sincronizados usuario desde el Administrador de origen en el nombre de un usuario. Este evento es la primera en cada operación de reescritura de restablecimiento de contraseña iniciados por el administrador.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31010</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>El administrador selecciona una nueva contraseña durante la operación de restablecimiento de contraseña iniciados por el administrador, se ha determinado que esta contraseña cumple los requisitos de contraseña corporativo y contraseña se han escrito correctamente en el entorno de AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31011</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>El administrador selecciona una contraseña en el nombre de un usuario y contraseña correctamente llegado al entorno local, pero cuando se intentó establecer la contraseña en el entorno de AD local, se ha producido un error. Esto puede suceder por diversos motivos:</p>
              <ul>
                <li class="unordered">
La contraseña del usuario no cumple la edad, historial, complejidad o filtrar requisitos del dominio. Pruebe una contraseña completamente nueva para resolver este problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
La cuenta de servicio de MA no tiene los permisos adecuados para configurar la nueva contraseña en la cuenta de usuario en cuestión.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
La cuenta del usuario está en un grupo protegido, como los administradores de dominio o de organización, que no permite operaciones de establecer contraseña.<br\><br\></li>
              </ul>
              <p>Vea <a href="#troubleshoot-password-writeback">Solucionar problemas de reescritura de contraseña</a> para obtener más información sobre qué otras situtions puede provocar este error.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31012</p>
            </td>
            <td>
              <p>OffboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento se produce si deshabilita la reescritura de contraseña con Azure AD Connect e indica que se ha iniciado offboarding su organización para el servicio web de reescritura de contraseña.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31013</p>
            </td>
            <td>
              <p>OffboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el proceso de offboarding fue correcta y que se ha deshabilitado correctamente capacidad de reescritura de contraseña.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31014</p>
            </td>
            <td>
              <p>OffboardingEventFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el proceso de offboarding no se realizó correctamente. Esto puede deberse a un error de permisos en la cuenta de administrador de la nube o local especificada durante la configuración, o ya está intentando usar un administrador global de la nube federados al deshabilitar reescritura de contraseña. Para corregir este error, compruebe su administrativo permisos y no está utilizando alguno federación cuenta al configurar la capacidad de reescritura de contraseña.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31015</p>
            </td>
            <td>
              <p>WriteBackServiceStarted </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el servicio de reescritura de la contraseña se ha iniciado correctamente y está listo para aceptar las solicitudes de administración de contraseña de la nube.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31016</p>
            </td>
            <td>
              <p>WriteBackServiceStopped </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el servicio de reescritura de la contraseña se ha detenido y que las solicitudes de administración de la contraseña de la nube no se realizará correctamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31017</p>
            </td>
            <td>
              <p>AuthTokenSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se recuperen correctamente un token de autorización para el administrador global especificado durante la instalación de Azure AD Connect para iniciar el proceso de offboarding o integrado.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31018</p>
            </td>
            <td>
              <p>KeyPairCreationSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se ha creado correctamente la clave de cifrado que se usará para cifrar las contraseñas de la nube que se envíen a su entorno local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32000</p>
            </td>
            <td>
              <p>UnknownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica un error desconocido durante una operación de administración de la contraseña. Busque el texto de excepción en el evento para obtener más detalles. Si está teniendo problemas, intente volver a habilitar y deshabilitar reescritura de contraseña. Si esto no soluciona el problema, incluir una copia del registro de eventos junto con el id de seguimiento de información confidencial de personal de ingeniería de soporte técnico.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32001</p>
            </td>
            <td>
              <p>Depuracuión puede contener </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se ha producido un error al conectar con la contraseña de la nube restablece servicio y generalmente se produce cuando el servicio local no pudo conectar con el servicio web de restablecimiento de contraseña. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32002</p>
            </td>
            <td>
              <p>ServiceBusError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se ha producido un error al conectarse a la instancia de bus de servicio de su inquilino. Esto puede ocurrir debido a está bloqueando las conexiones salientes en su entorno local. Active el firewall para asegurarse de que permite conexiones sobre TCP 443 y <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>e inténtelo de nuevo. Si sigue teniendo problemas, pruebe a deshabilitar y volver a habilitar reescritura de contraseña.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32003</p>
            </td>
            <td>
              <p>InPutValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que la entrada pasada a nuestra API del servicio web no es válida. Vuelva a intentarlo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32004</p>
            </td>
            <td>
              <p>DecryptionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se ha producido un error al descifrar la contraseña que ha llegado de la nube. Esto puede deberse a un error de coincidencia de clave de descifrado entre el servicio de nube y su entorno local. Para resolver este problema, deshabilitar y volver a habilitar reescritura de contraseña en su entorno local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32005</p>
            </td>
            <td>
              <p>ConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Durante integrado, se guarda información específica del inquilino en un archivo de configuración en su entorno local. Este evento indica que se ha producido un error al guardar este archivo o que cuando se inicia el servicio allí estaba un error leyendo el archivo. Para solucionar este problema, intente volver a habilitar y deshabilitar reescritura de contraseña para forzar una reescritura de este archivo de configuración. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32006</p>
            </td>
            <td>
              <p>EndPointConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>OBSOLETO: este evento no está presente en Azure AD Connect, crea solo principio de sincronización de directorios que admite la escritura diferida.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32007</p>
            </td>
            <td>
              <p>OnBoardingConfigUpdateError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Durante integrado, enviar datos de la nube a la contraseña local restablecer el servicio. Datos, a continuación, se escriben en un archivo en la memoria antes de enviarla al servicio de sincronización para almacenar esta información de forma segura en el disco. Este evento indica un problema con escribir o actualizar datos en la memoria. Para solucionar este problema, intente volver a habilitar y deshabilitar reescritura de contraseña para forzar una reescritura de esta configuración.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32008</p>
            </td>
            <td>
              <p>ValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se ha recibido una respuesta no válida desde el servicio web de restablecimiento de contraseña. Para solucionar este problema, intente volver a habilitar y deshabilitar reescritura de contraseña.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32009</p>
            </td>
            <td>
              <p>AuthTokenError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que no se puede obtener una autorización de token de la cuenta de administrador global especificada durante la instalación de Azure AD Connect. Este error puede estar causado por nombre de usuario o contraseña especificada para la cuenta de administrador global o porque la cuenta de administrador global especificada federada. Para solucionar este problema, vuelva a ejecutar la configuración con el nombre de usuario correcto y la contraseña y asegurarse de que el administrador es administrado (solo nube o sincronización de la contraseña) cuenta.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32010</p>
            </td>
            <td>
              <p>CryptoError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se ha producido un error al generar la contraseña de la clave de cifrado o descifrar una contraseña que se llega desde el servicio de nube. Este error es muy probable que indica un problema con su entorno. Ver los detalles de su registro de eventos para obtener más información y resolver este problema. También puede intentar deshabilitar y volver a habilitar el servicio de reescritura de contraseña para resolver este problema.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32011</p>
            </td>
            <td>
              <p>OnBoardingServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el servicio local no pudo comunicarse correctamente con el servicio de web de restablecimiento de contraseña para iniciar el proceso integrado. Esto puede deberse a una regla de firewall o un problema con un símbolo de autenticación para su inquilino. Para corregir este error, asegúrese de que no está bloqueando las conexiones salientes TCP 443 y TCP 9350 9354 o <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>y la cuenta de administrador AAD va a usar para incorporado no federado. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32012</p>
            </td>
            <td>
              <p>OnBoardingServiceDisableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>OBSOLETO: este evento no está presente en Azure AD Connect, crea solo principio de sincronización de directorios que admite la escritura diferida.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32013</p>
            </td>
            <td>
              <p>OffBoardingError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el servicio local no pudo comunicarse correctamente con el servicio de web de restablecimiento de contraseñas para iniciar el proceso de offboarding. Esto puede deberse a una regla de firewall o un problema al obtener un token de autorización de su inquilino. Para corregir este error, asegúrese de que no está bloqueando las conexiones salientes sobre 443 o <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>y la cuenta de administrador AAD utilizan para gestionar el cese no federado. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32014</p>
            </td>
            <td>
              <p>ServiceBusWarning </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que tenemos Reintentar para conectarse a la instancia de bus de servicio de su inquilino. En condiciones normales, esto debería no ser un problema, pero si ve este evento muchas veces, considere la posibilidad de comprobación de la conexión de red al bus de servicio, especialmente si se trata de una conexión de ancho de banda bajo o una latencia alta.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32015</p>
            </td>
            <td>
              <p>ReportServiceHealthError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Fin de controlar el estado de su servicio de reescritura de contraseña, enviar datos de latido a nuestra contraseña restablecer servicio web cada 5 minutos. Este evento indica que se ha producido un error al enviar la información de estado del servicio web de nube. La información de estado no incluye una OII o PII datos y es puramente un latido y estadísticas de servicio básico para que podemos proporcionar información de estado del servicio en la nube.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33001</p>
            </td>
            <td>
              <p>ADUnKnownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se ha producido un error desconocido devuelto por AD, compruebe el registro de eventos de servidor de Azure AD Connect para los eventos del origen de ADSync para obtener más información acerca de este error.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33002</p>
            </td>
            <td>
              <p>ADUserNotFoundError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que no se encontró el usuario que está intentando restablecer o cambiar una contraseña en el directorio local. Esto puede ocurrir cuando el usuario se ha eliminado en local, pero no en la nube, o si hay un problema con la sincronización. Compruebe los registros de sincronización, así como la última sincronización algunos detalles para obtener más información de ejecución.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33003</p>
            </td>
            <td>
              <p>ADMutliMatchError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Al restablece una contraseña o procede de solicitud de cambio de la nube, usamos el delimitador de nube especificado durante el proceso de configuración de Azure AD Connect para determinar cómo vincular esa solicitud de un usuario en su entorno local. Este evento indica que hemos encontrado dos usuarios en el directorio local con el mismo atributo de anclaje de la nube. Compruebe los registros de sincronización, así como la última sincronización algunos detalles para obtener más información de ejecución.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33004</p>
            </td>
            <td>
              <p>ADPermissionsError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que la cuenta de servicio de agente de administración no tiene los permisos adecuados en la cuenta en cuestión de establecer una contraseña nueva. Asegúrese de que la cuenta de MA en el bosque del usuario tiene permisos de restablecer y cambiar contraseña en todos los objetos del bosque.  Para obtener más información sobre cómo hacer a este, vea <a href="active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions">paso 4: configurar los permisos adecuados de Active Directory</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33005</p>
            </td>
            <td>
              <p>ADUserAccountDisabled </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se intentó restablecer o cambiar la contraseña de una cuenta que se haya deshabilitado de forma local. Habilitar la cuenta y vuelva a intentarlo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33006</p>
            </td>
            <td>
              <p>ADUserAccountLockedOut </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Evento indica que se intentó restablecer o cambiar la contraseña de una cuenta que se ha bloqueado de forma local. Bloqueos pueden producirse cuando un usuario ha intentado un cambio o restablecer contraseña demasiadas veces en un período corto. Desbloquear la cuenta y vuelva a intentarlo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33007</p>
            </td>
            <td>
              <p>ADUserIncorrectPassword </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el usuario ha especificado una contraseña incorrecta actual cuando la operación de cambio de efectuar una contraseña. Especifique la contraseña actual correcta y vuelva a intentarlo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33008</p>
            </td>
            <td>
              <p>ADPasswordPolicyError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento se produce cuando el servicio de reescritura de contraseña intenta establecer una contraseña en su directorio local que no alcanza la duración de la contraseña, historial, complejidad o filtrados requisitos del dominio.</p>
              <ul>
                <li class="unordered">
Si tiene una antigüedad mínima de la contraseña y recientemente se ha cambiado la contraseña desde la ventana de tiempo, no podrá cambiar la contraseña de nuevo hasta que alcance la edad especificada en su dominio. Con fines de pruebas, antigüedad mínima se debe establecer a 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si tiene requisitos de historial de contraseña habilitados, debe seleccionar una contraseña que no se ha utilizado en la última N veces, donde N es el valor de historial de contraseñas. Si selecciona una contraseña que se ha utilizado en la última N veces, se muestra un error en este caso. Con fines de pruebas, historial se debe establecer a 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si tiene requisitos de complejidad de contraseña, todos ellos se aplicará cuando el usuario intenta cambiar o restablecer una contraseña.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si tiene filtros de contraseña habilitados y un usuario selecciona una contraseña que no se cumplen los criterios de filtrado, a continuación, restablecer o cambiar se producirá un error en la operación.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>33009</p>
            </td>
            <td>
              <p>ADConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se ha producido una escritura problema copia de una contraseña en su directorio local debido a un problema de configuración con Active Directory. Compruebe el registro de eventos de aplicación de la máquina de Azure AD Connect para mensajes del servicio ADSync para obtener más información sobre cuál fue el error. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34001</p>
            </td>
            <td>
              <p>ADPasswordPolicyOrPermissionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>OBSOLETO: este evento no está presente en Azure AD Connect, crea solo principio de sincronización de directorios que admite la escritura diferida.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34002</p>
            </td>
            <td>
              <p>ADNotReachableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>OBSOLETO: este evento no está presente en Azure AD Connect, crea solo principio de sincronización de directorios que admite la escritura diferida.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34003</p>
            </td>
            <td>
              <p>ADInvalidAnchorError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>OBSOLETO: este evento no está presente en Azure AD Connect, crea solo principio de sincronización de directorios que admite la escritura diferida.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-writeback-connectivity"></a>Solucionar problemas de conectividad de reescritura de contraseña

Si experimenta interrupciones del servicio con el componente de contraseña reescritura de Azure AD Connect, hay algunos pasos rápidos que puede realizar para resolver este problema:

 - [Reiniciar Azure AD conectar servicios de sincronización](#restart-the-azure-AD-Connect-sync-service)
 - [Deshabilitar y volver a habilitar la característica de reescritura de contraseña](#disable-and-re-enable-the-password-writeback-feature)
 - [Instalar la última versión de Azure AD Connect](#install-the-latest-azure-ad-connect-release)
 - [Solucionar problemas de reescritura de contraseña](#troubleshoot-password-writeback)

En general, se recomienda que ejecute estos pasos en el orden anterior para recuperar su servicio de la manera más rápida.

### <a name="restart-the-azure-ad-connect-sync-service"></a>Reiniciar Azure AD conectar servicios de sincronización
Reiniciar el servicio de sincronización de AD Connect Azure puede ayudar a resolver problemas de conectividad u otros problemas temporales con el servicio.

 1. Como administrador, haga clic en **Inicio** , en el servidor de **Azure AD Connect**.
 2. Escriba **"services.msc"** en el cuadro de búsqueda y presione **ENTRAR**.
 3. Busque la entrada de **Microsoft Azure AD Connect** .
 4. Haga clic en la entrada del servicio, haga clic en **reiniciar**y espere a que finalice la operación.

    ![][002]

Estos pasos se vuelve a establecer la conexión con el servicio de nube y resolver las interrupciones que puede estar experimentando.  Si al reiniciar el servicio de sincronización no resuelve el problema, se recomienda que intenta deshabilitar y volver a habilitar la característica de reescritura de contraseña como un paso siguiente.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Deshabilitar y volver a habilitar la característica de reescritura de contraseña
Deshabilitar y volver a habilitar la característica de reescritura de contraseña pueden ayudar a resolver problemas de conectividad.

 1. Como administrador, abra el **Asistente para configuración de Azure AD Connect**.
 2. En el cuadro de diálogo **conectarse a Azure AD** , escriba sus **credenciales de administrador global de Azure AD**
 3. En el cuadro de diálogo **Conectar con AD DS** , escriba sus **credenciales de administrador de servicios de dominio de Active Directory**.
 4. En el cuadro de diálogo **identifica los usuarios** , haga clic en el botón **siguiente** .
 5. En el cuadro de diálogo **características opcionales** , desactive la casilla de verificación de **reescritura de contraseña** .

    ![][003]

 6. Haga clic en **siguiente** a través de las páginas restantes del cuadro de diálogo sin cambiar nada hasta que llegue a la página **listo para configurar** .
 7. Asegúrese de que la página Configurar muestra la **opción de reescritura de contraseña como deshabilitada** y, a continuación, haga clic en el botón de **Configurar** verde para confirmar los cambios.
 8. En el cuadro de diálogo **finalizado** , desactive la opción **Sincronizar ahora** y, a continuación, haga clic en **Finalizar** para cerrar al asistente.
 9. Vuelva a abrir el **Asistente para configuración de Azure AD Connect**.
 10.    **Repita los pasos del 2 al 8**, pero asegúrese de **activar la opción de reescritura de contraseña** en las **características opcionales** de la pantalla para volver a habilitar el servicio.

    ![][004]

Estos pasos se vuelve a establecer la conexión con nuestro servicio de nube y resolver las interrupciones que puede estar experimentando.

Si deshabilitar y volver a habilitar la característica de reescritura de contraseña no resuelve el problema, se recomienda que intente volver a instalar Azure AD Connect como un paso siguiente.

### <a name="install-the-latest-azure-ad-connect-release"></a>Instalar la última versión de Azure AD Connect
Volver a instalar el paquete de Azure AD Connect se resuelva los problemas de configuración que pueden afectar a su capacidad para cualquiera conectan nuestros servicios de nube o administrar contraseñas en su entorno local de AD.
Se recomienda realizar este paso solo después de intentar los dos primeros pasos descritos anteriormente.

 1. Descargar la versión más reciente de Azure AD Connect [aquí](active-directory-aadconnect.md#install-azure-ad-connect).
 2. Puesto que ya ha instalado Azure AD Connect, solo deberá realizar una actualización en contexto para actualizar la instalación de Azure AD Connect a la última versión.
 3. Ejecutar el paquete descargado y siga la pantalla instrucciones para actualizar el equipo de Azure AD Connect.  No hay pasos manuales adicionales son necesarios a menos que se ha personalizado la salida de reglas de sincronización del cuadro, en cuyo caso debe **estos hacer una copia antes de continuar con la actualización y volver a implementar manualmente ellos cuando haya terminado**.

Estos pasos se vuelve a establecer la conexión con nuestro servicio de nube y resolver las interrupciones que puede estar experimentando.

Si instalar la última versión del servidor de Azure AD Connect no resuelve el problema, recomendamos que intente volver a habilitar y deshabilitar reescritura de contraseña como un paso final después de instalar la última sincronización QFE.

Si no se resuelve el problema, se recomienda que eche un vistazo a [Solucionar problemas de reescritura de contraseña](#troubleshoot-password-writeback) y la [contraseña de Azure AD administración preguntas más frecuentes](active-directory-passwords-faq.md) para ver si el problema puede ser tratado.


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Vínculos a contraseña restablecer documentación
A continuación se muestran vínculos a todas las páginas de documentación de restablecimiento de contraseña de Azure AD:

* **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [He aquí cómo puede cambiar y restablecer su propia contraseña](active-directory-passwords-update-your-own-password.md).
* [**Cómo funciona**](active-directory-passwords-how-it-works.md) - obtener información sobre los distintos seis componentes del servicio y lo que hace cada uno
* [**Introducción**](active-directory-passwords-getting-started.md) : Obtenga información sobre cómo permitir que los usuarios restablecer y cambiar sus contraseñas de nube o local
* [**Personalizar**](active-directory-passwords-customize.md) - obtener información sobre cómo personalizar el aspecto y apariencia y comportamiento del servicio a las necesidades de su organización
* [**Procedimientos recomendados**](active-directory-passwords-best-practices.md) : Obtenga información sobre cómo implementar rápidamente y eficaz administrar contraseñas en su organización
* [**Obtener recomendaciones**](active-directory-passwords-get-insights.md) - Obtenga más información sobre nuestras capacidades integradas de reporting
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md) : Obtenga respuestas a las preguntas más frecuentes
* [**Más información**](active-directory-passwords-learn-more.md) - vaya profundidad los detalles técnicos de cómo funciona el servicio



[001]: ./media/active-directory-passwords-troubleshoot/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-troubleshoot/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-troubleshoot/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-troubleshoot/004.jpg "Image_004.jpg"
