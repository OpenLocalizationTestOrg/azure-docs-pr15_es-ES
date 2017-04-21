<properties
    pageTitle="Experiencia de inicio de sesión de AMF Azure con autenticación multifactor de Azure"
    description="Esta página le proporcionará información sobre dónde ir para ver los diferentes métodos de inicio de sesión disponibles con Azure AMF."
    keywords="autenticación de usuario, la experiencia de inicio de sesión, iniciar sesión con el teléfono móvil, iniciar sesión con el teléfono de la oficina"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kgremban"/>

# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>El inicio de sesión experiencia con autenticación multifactor de Azure
> [AZURE.NOTE]  La siguiente documentación de esta página muestra una experiencia de inicio de sesión típica.  Para obtener ayuda con el inicio de sesión, consulte [¿tiene problemas con la autenticación multifactor de Azure](multi-factor-authentication-end-user-manage-settings.md)



## <a name="what-will-your-sign-in-experience-be"></a>¿Cuál será la experiencia de inicio de sesión?
Dependiendo de cómo iniciar sesión y usar autenticación multifactor, se diferencian su experiencia.  En esta sección se le proporcionará información sobre lo que pasará al iniciar sesión.  Elija la opción que mejor describe lo que hace:


¿Qué estás haciendo?|Descripción
:------------- | :------------- |
[Iniciar sesión con el teléfono móvil o de office](#signing-in-with-mobile-or-office-phone) | Esto es lo que puede esperar de iniciar sesión con su teléfono móvil o de office.
[Iniciar sesión con la aplicación de Microsoft Authenticator con notificación](#signing-in-with-the-microsoft-authenticator-app-using-notification) | Esto es lo que puede esperar mediante la aplicación de Microsoft Authenticator con las notificaciones.
[Iniciar sesión con la aplicación de Microsoft Authenticator con código de comprobación](#signing-in-with-the-microsoft-authenticator-app-using-verification-code)|Esto es lo que puede esperar utilizando la thapp Microsoft Authenticator con un código de comprobación.
[Iniciar sesión con un método alternativo](#signing-in-with-an-alternate-method)|Esto le mostrará qué cabe esperar si desea usar un método alternativo.

## <a name="signing-in-with-mobile-or-office-phone"></a>Iniciar sesión con el teléfono móvil o de office

La información siguiente describe la experiencia de uso de la autenticación multifactor con el teléfono móvil o de office.

### <a name="to-sign-in-with-a-call-to-your-office-or-mobile-phone"></a>Iniciar sesión con una llamada a su oficina o un teléfono móvil

- Inicie sesión en una aplicación o servicio, como Office 365 con su nombre de usuario y contraseña.
- Microsoft le llame.

![Llamadas de Microsoft](./media/multi-factor-authentication-end-user-signin-phone/call.png)

- Responder el teléfono y presionar la tecla #.

![Respuesta](./media/multi-factor-authentication-end-user-signin-phone/phone.png)

- Ahora debe haber iniciado sesión.</li>

## <a name="signing-in-with-the-microsoft-authenticator-app-using-notification"></a>Iniciar sesión con la aplicación de Microsoft Authenticator con notificación

La información siguiente describe la experiencia de uso de la autenticación multifactor con la aplicación Microsoft Authenticator cuando se le envía una notificación.

### <a name="to-sign-in-with-a-notification-sent-the-microsoft-authenticator-app"></a>Para iniciar sesión con una notificación envía la aplicación de Microsoft Authenticator

- Inicie sesión en una aplicación o servicio, como Office 365 con su nombre de usuario y contraseña.
- Microsoft le enviará una notificación.

![Microsoft envía una notificación](./media/multi-factor-authentication-end-user-signin-app-notify/notify.png)


- Responder el teléfono y presionar la tecla de comprobar.  Si su empresa necesita un PIN le pedirá que aquí.

![Comprobar](./media/multi-factor-authentication-end-user-signin-app-notify/phone.png)

![Programa de instalación](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

- Ahora debe haber iniciado sesión.


## <a name="signing-in-with-the-microsoft-authenticator-app-using-verification-code"></a>Iniciar sesión con la aplicación de Microsoft Authenticator con código de comprobación

La información siguiente describe la experiencia de uso de la autenticación multifactor con la aplicación Microsoft Authenticator cuando se usa con un código de comprobación.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Iniciar sesión usando un código de comprobación con la aplicación de Microsoft Authenticator

- Inicie sesión en una aplicación o servicio, como Office 365 con su nombre de usuario y contraseña.
- Microsoft le pedirá un código de comprobación.

![Escriba el código de comprobación](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)

- Abra la aplicación de Microsoft Authenticator en el teléfono y escriba el código en el cuadro dónde iniciar sesión en.

![Obtener código](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)



- Ahora debe haber iniciado sesión.


## <a name="signing-in-with-an-alternate-method"></a>Iniciar sesión con un método alternativo


En la sección siguiente le mostrará cómo iniciar sesión con un método alternativo al método principal no esté disponible.

### <a name="to-sign-in-with-an-alternate-method"></a>Iniciar sesión con un método alternativo

- Inicie sesión en una aplicación o servicio, como Office 365 con su nombre de usuario y contraseña.
- Seleccione usar otra opción de verificación.  Puede presentar una selección de diferentes opciones. El número que aparece le basarse en cuántos tiene el programa de instalación.

![Use el método alternativo](./media/multi-factor-authentication-end-user-signin-alt/alt.png)

- Elija un método alternativo e inicie sesión.
