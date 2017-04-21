<properties
    pageTitle="Solucionar problemas de comprobación de dos pasos | Microsoft Azure"
    description="Este documento le proporcionará a los usuarios información sobre qué hacer si se ejecutan en un problema con la autenticación multifactor de Azure."
    services="multi-factor-authentication"
    keywords = "cliente de la autenticación, el problema de autenticación, Id. de correlación"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="having-trouble-with-two-step-verification"></a>¿Tiene problemas con la verificación en dos pasos

En este artículo se describe algunos problemas que puede experimentar con activó la verificación. Si el problema que tenga no está incluido, proporcione comentarios detallados en la sección Comentarios para que podamos mejorar.

## <a name="i-lost-my-phone-or-it-was-stolen"></a>He perdido mi teléfono o se robe

Hay dos formas acceder a su cuenta. La primera es iniciar sesión con su número de teléfono alternativo de autenticación, si ha configurado una. El segundo es pida a su administrador para borrar la configuración.

Si se ha perdido o robe su teléfono, le recomendamos que tiene el Administrador de restablecer las contraseñas de la aplicación y desactive cualquier recuerda dispositivos. Si el administrador no está seguro de cómo hacerlo, apunte a este artículo: [Administrar usuarios y dispositivos](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords).


### <a name="use-an-alternate-phone-number"></a>Usar un número de teléfono

Si ha configurado varias opciones de comprobación, como un número de teléfono secundario o una aplicación de autenticador en un dispositivo diferente, puede usar uno de estos iniciar sesión.

Para iniciar sesión con el número de teléfono alternativo, siga estos pasos:

1. Inicie sesión como lo haría normalmente.
2. Cuando se le solicite para comprobar su cuenta, seleccione **usar otra opción de verificación**.

    ![Verificación diferente](./media/multi-factor-authentication-end-user-manage/differentverification.png)

3. Seleccione el número de teléfono que tiene acceso.

    ![Teléfono alternativo](./media/multi-factor-authentication-end-user-manage/altphone2.png)

4. Una vez que haya en su cuenta, [Administrar la configuración](multi-factor-authentication-end-user-manage-settings.md) cambiar el número de teléfono de autenticación.

>[AZURE.IMPORTANT]
>Es importante configurar un número de teléfono de autenticación secundaria. Si el número de teléfono principal y la aplicación móvil se encuentran en el mismo teléfono, necesita una tercera opción si su teléfono pérdido o robo.

### <a name="clear-your-settings"></a>Desactive la configuración

Si no ha configurado un número de teléfono de autenticación secundaria, tendrá que ponerse en contacto con el administrador para obtener ayuda. Hacer que desactive la configuración para que la próxima vez que inicie sesión, le pedirá que [Configure su cuenta de](multi-factor-authentication-end-user-first-time.md) nuevo.


## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Que no está recibiendo un texto o llamar a mi teléfono

Existen varios motivos por qué puede intentar iniciar sesión, pero no recibirá el texto o una llamada de teléfono. Si ha recibido correctamente textos o llamadas de teléfono a su teléfono en el pasado, a continuación, probablemente es un problema con el proveedor de teléfono, no a su cuenta. Asegúrese de que tiene señal de buena celda y, si está intentando recibir un mensaje de texto Asegúrese de que su plan de servicio y teléfono compatible con mensajes de texto.

Si has esperado unos minutos para una llamada o texto, la forma más rápida para acceder a su cuenta es pruebe otra opción.

1. Seleccione **utilizar otra opción de verificación** en la página que está en espera para su verificación.

    ![Verificación diferente](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

2. Seleccione el método de entrega o número de teléfono que desea usar.

    Si ha recibido varios códigos de verificación, el más reciente solo funciona.

Si no tiene otro método configurado, póngase en contacto con el administrador y pídale que desactive la configuración. La próxima vez que inicie sesión, le pedirá para [Configurar la autenticación multifactor](multi-factor-authentication-end-user-first-time.md) nuevamente.


Si a menudo tiene retrasos debido a señal de celda incorrecta, le recomendamos que use la [aplicación de Microsoft autenticador](multi-factor-authentication-microsoft-authenticator.md) en su smartphone. La aplicación puede generar los códigos de seguridad aleatorio que utiliza para iniciar sesión y estos códigos no requieren cualquier celda señal o conexión a internet.


## <a name="app-passwords-are-not-working"></a>Contraseñas de aplicaciones no funcionan

En primer lugar, asegúrese de que ha escrito correctamente la contraseña de aplicación.  Si sigue sin funcionar, intente iniciar sesión en y [crear una nueva contraseña de aplicación](multi-factor-authentication-end-user-app-passwords.md).  Si esto no funciona, póngase en contacto con el administrador y pedirles que [Eliminar las contraseñas de aplicación existente](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords) y, a continuación, puede crear una nueva.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>No se encontró una respuesta a mi problema.

Si ha probado estos pasos pero aún están en ejecución en problemas, póngase en contacto con el administrador o la persona que configuró la autenticación multifactor. Debería puede ayudarle.

Además, puede publicar una pregunta en los [foros de Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) o [póngase en contacto con soporte técnico](https://support.microsoft.com/contactus) y se deberá responder a su problema tan pronto como se puede.

Si necesita soporte técnico, incluya la siguiente información:

- De **ID de usuario** : ¿cuál es la dirección de correo electrónico que intentó iniciar sesión?
- **Descripción general del error** : ¿qué mensaje de error exacto quiso ver?  Si no había ningún mensaje de error, se describe el comportamiento inesperado observado, en detalle.
- **Página** : ¿qué página se encontraba en cuando aparece el error (incluya la dirección URL)?
- **Código de error** : el código de error específico está recibiendo.
- **ID** - el id específico que recibe.
- **Id. de correlación** : ¿cuál ha sido el código de Id. de correlación se genera cuando el usuario vio el error
- **Marca de tiempo** : ¿cuál ha sido preciso fecha y hora en que se ha visto el error (incluye la zona horaria)?

Gran parte de esta información se puede encontrar en la página de inicio de sesión. Cuando no comprobar la sesión en el tiempo, seleccione **Ver detalles**.

![Detalles del error de inicio de sesión](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

Incluir información nos ayuda a resolver el problema lo antes posible.

## <a name="related-topics"></a>Temas relacionados
- [Administrar la configuración de verificación en dos pasos](multi-factor-authentication-end-user-manage-settings.md)  
- [Preguntas más frecuentes sobre aplicaciones de Microsoft Authenticator](multi-factor-authentication-app-faq.md)
