<properties
    pageTitle="Aplicación de Microsoft autenticador preguntas más frecuentes"
    description="Proporciona una lista de las preguntas más frecuentes y respuestas relacionadas con la aplicación de Microsoft Authentication y la autenticación multifactor de Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="pblachar, librown"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>

# <a name="microsoft-authenticator-application-faq"></a>Preguntas más frecuentes sobre aplicaciones de Microsoft Authenticator

La aplicación de Microsoft Authenticator reemplaza la aplicación de Azure autenticador y es la aplicación recomendada al usar autenticación multifactor de Azure. Esta aplicación está disponible para Windows Phone, Android y iOS.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

- **¿Qué ha ocurrido con el autenticador de Azure, autenticación multifactor y aplicaciones de la cuenta de Microsoft?**

    La aplicación de Microsoft Authenticator entre sí reemplaza estas aplicaciones. Azure autenticador actualizado a Microsoft Authenticator. Si utiliza la autenticación multifactor o aplicaciones de la cuenta de Microsoft, instale Microsoft Authenticator y volver a agregar sus cuentas. Asegúrese de que termine de agregar sus cuentas a la nueva aplicación antes de eliminar los antiguos.

- **Ya estoy usando la aplicación Microsoft Authenticator para códigos de comprobación. ¿Cómo cambiar a las notificaciones de inserción de un solo clic?**  

    Aprobar un inicio de sesión a través de notificaciones de inserción sólo está disponible para las cuentas de Microsoft, pero no para cuentas de terceros como Google o Facebook. Trabajo o escuela cuentas de Microsoft, puede elegir deshabilitar esta opción, aunque su organización.

    Si usa una cuenta de Microsoft para su cuenta personal y desea cambiar a las notificaciones de inserción, debe agregar su cuenta de nuevo. Volver a registrar el dispositivo con su cuenta y configurar las notificaciones de inserción.  

    Si su cuenta no tiene activó la verificación habilitada, consulte [acerca de la comprobación de dos pasos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) para decidir si es adecuado para usted.  

- **¿Cuándo se que pueda usar las notificaciones de inserción de un solo clic en iPhone o iPad?**  

    Esta característica está en beta hasta el final de agosto, cuando esté ampliamente disponible para las cuentas de Microsoft. Si quiere unirse a nuestro programa beta, enviar correo electrónico a msauthenticator@microsoft.com. Incluir su nombre, apellido y Apple ID en el mensaje.  

- **¿Funcionan las notificaciones de inserción de un solo clic para las cuentas de Microsoft no?**  

    No, las notificaciones de inserción solo funcionan con cuentas de Azure Active Directory y de cuentas de Microsoft. Si su trabajo o escuela utiliza cuentas de Azure AD, puede deshabilitar esta característica.  

- **Restaurar mi dispositivo desde una copia de seguridad y códigos de mi cuenta falta o no funciona. ¿Qué ha pasado?**  

    Por motivos de seguridad, nos no restaurar cuentas desde las copias de seguridad de la aplicación. Si la aplicación se restaura una copia de seguridad, aún se muestran las cuentas, pero no funcionan para recibir las comprobaciones de inicio de sesión o genera códigos de seguridad. Después de restaurar la aplicación, elimine las cuentas y agregar de nuevo.

- **He recibido un nuevo dispositivo. ¿Cómo quitar la aplicación de Microsoft Authenticator de mi dispositivo antiguo y mover a la nueva?**

    Agregar la aplicación de Microsoft Authenticator a un nuevo dispositivo no quitarlo automáticamente de otros dispositivos. Para administrar los dispositivos que se han configurado para su cuenta, visite el mismo sitio Web que usa para administrar activó la verificación y elija Quitar aplicaciones antiguas.

    Para las cuentas personales de Microsoft, este sitio Web es la página de [seguridad de la cuenta](https://account.microsoft.com/security) . Para cuentas de Microsoft profesional o educativa, este sitio Web puede resultar [Mis aplicaciones](https://myapps.microsoft.com) o un portal personalizado que su organización ha configurado.

## <a name="contact-us"></a>Póngase en contacto con nosotros

Si no se responde su pregunta aquí, deja un comentario en la parte inferior de la página. O bien, [póngase en contacto con soporte técnico](https://support.microsoft.com/contactus) y se deberá responder a su problema tan pronto como se puede.

Si necesita soporte técnico, que incluye la siguiente información como sea posible:

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

- [Preguntas más frecuentes de Azure autenticación multifactor](multi-factor-authentication-faq.md)  
- [Acerca de verificación en dos pasos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) para las cuentas de Microsoft
- [¿Tiene problemas con la verificación en dos pasos?](multi-factor-authentication-end-user-troubleshoot.md)
