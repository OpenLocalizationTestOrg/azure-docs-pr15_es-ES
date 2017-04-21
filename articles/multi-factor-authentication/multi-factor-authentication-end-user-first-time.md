<properties
    pageTitle="Configurar activó la verificación de mi cuenta profesional o educativa"
    description="Cuando su empresa configura autenticación multifactor de Azure, le pedirá para registrarse a activó la verificación. Obtenga información sobre cómo configurar el. "
    services="multi-factor-authentication"
    keywords="cómo usar directorio de azure, active directory en la nube, tutorial de active directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="pblachar"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="set-up-my-account-for-two-step-verification"></a>Configurar mi cuenta para la verificación en dos pasos

Activó la verificación es un paso adicional de seguridad que ayuda a proteger su cuenta, para que otras personas un salto en. Si está leyendo este artículo, probablemente había recibido un correo electrónico desde el Administrador de trabajo o escuela acerca de la autenticación multifactor. O tal vez intentó iniciar sesión y un mensaje que le pide que configurar la comprobación de seguridad adicional. Si es el caso, **que no puede iniciar sesión hasta que haya finalizado el proceso de inscripción automática**.

Este artículo le ayudará a configurar su **trabajo o escuela cuenta**. Si desea habilitar la comprobación de dos pasos para su cuenta, cuenta personal de Microsoft, consulte [acerca de la verificación en dos pasos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="determine-how-you-will-use-multi-factor-authentication"></a>Determinar cómo utilizar autenticación multifactor

Activó la verificación funciona que solicita dos datos de identificación al iniciar sesión. En primer lugar, le pedimos su nombre de usuario y contraseña como de costumbre. A continuación, nos contacto al que pertenece un teléfono que sabemos y confirme que el intento de inicio de sesión fue legítimo.  

Para empezar el proceso de instalación, intente iniciar sesión en su cuenta como lo haría. Si el administrador ha configurado su cuenta para la verificación en dos pasos, se le pedirá que inicie el proceso de inscripción automática. Iniciar este proceso, haga clic en **ahora configurarlo.**

![Programa de instalación](./media/multi-factor-authentication-end-user-first-time/first.png)

La primera pregunta en el proceso de inscripción es cómo desea ponernos en contacto con usted. Eche un vistazo a las opciones de la tabla y use los vínculos para ir a los pasos de configuración para cada método.

| Método de contacto | Descripción |
| --- | --- |
[Aplicación móvil](#use-a-mobile-app-as-the-contact-method) | - **Recibir notificaciones para la verificación.** Esta opción inserta una notificación en la aplicación de autenticador en su tableta o smartphone. Ver la notificación y, si es legítima, seleccione **autenticar** en la aplicación. Puede obligarle a su trabajo o escuela que especifica un PIN para autenticar.<br>- **Use el código de comprobación.** En este modo, la aplicación de autenticador genera un código de comprobación que se actualiza cada 30 segundos. Escriba el código de comprobación más reciente de la interfaz de inicio de sesión.<br>La aplicación de Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)y [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
[Llamada de teléfono móvil o de texto](#use-your-mobile-phone-as-the-contact-method) | - **Llamada de teléfono** coloca una llamada de voz automatizado al número de teléfono que proporcione. Responder a la llamada y presione # en el teclado del teléfono para autenticar.<br>- **Mensaje de texto** finaliza un mensaje de texto que contiene un código de comprobación. Después de la pregunta en el texto, responda al mensaje de texto o escriba el código de verificación proporcionado en la interfaz de inicio de sesión. |  
[Llamada de teléfono de Office](#use-your-office-phone-as-the-contact-method) | Coloca una llamada de voz automatizado al número de teléfono que proporcione. Responder a la llamada y presiona # en el teclado del teléfono para autenticar. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Usar una aplicación móvil como método de contacto

Con este método requiere que instale una aplicación autenticador en su teléfono o tableta. Los pasos de este artículo se basan en la aplicación Microsoft Authenticator, que está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)y [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

1. Seleccione la **aplicación móvil** de la lista desplegable.
2. Seleccione **recibir notificaciones para la verificación** o **código de comprobación de uso**, seleccione **Configurar**.

    ![Pantalla de comprobación de seguridad adicional](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

3. En el teléfono o la tableta, abra la aplicación y seleccione **+** para agregar una cuenta. (En dispositivos Android, seleccione los tres puntos, a continuación, **Agregar una cuenta**).
4. Especificar que desea agregar una cuenta profesional o educativa. Se abre el analizador de código QR en su teléfono. Si la cámara no funciona correctamente, puede seleccionar para escribir información de su organización. Para obtener más información, vea [Agregar una cuenta de forma manual](#add-an-account-manually).  
5. Observe la imagen de código QR que aparecían con la pantalla para configurar la aplicación móvil.  Seleccione **hecho** para cerrar la pantalla de código QR.  

    ![Pantalla de código QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

6. Cuando termine de activación en el teléfono, seleccione **contacto Yo**.  Este paso, envía una notificación o un código de verificación a su teléfono. Seleccione **Comprobar**.  
7. Si su empresa necesita un PIN para aprobar la comprobación de inicio de sesión, escríbalo.

    ![Cuadro para escribir un PIN](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

8. Una vez completada la entrada PIN, seleccione **Cerrar**. En este momento, la comprobación debería tener éxito.
9. Le recomendamos que escriba su número de teléfono móvil en caso de perder el acceso a la aplicación móvil. Especifique su país en la lista desplegable y escriba su número de teléfono móvil en el cuadro situado junto al nombre de país. Seleccione **siguiente**.
10. En este momento, deberá configurar las contraseñas de aplicación de explorador no aplicaciones como Outlook 2010 o anterior o la aplicación de correo electrónico nativo en dispositivos de Apple. Esto es porque algunas aplicaciones no son compatibles con la verificación en dos pasos. Si no utiliza estas aplicaciones, haga clic en **Listo** y omita el resto de los pasos.
11. Si está utilizando estas aplicaciones, copiar la contraseña de aplicación proporcionado y pegarlo en la aplicación en lugar de la contraseña normal. Puede usar la misma contraseña de aplicación para varias aplicaciones. Para obtener más información, [ayuda con las contraseñas de aplicación].
12. Haga clic en **Listo**.


### <a name="add-an-account-manually"></a>Agregar una cuenta de forma manual
Si desea agregar una cuenta a la aplicación móvil manualmente, en lugar de usar el lector QR, siga estos pasos.

1. Seleccione el botón **Introducir cuenta manualmente** .  
2. Escriba el código y la dirección URL que se proporcionan en la misma página que muestra el código de barras. Esta información aparece en los cuadros de **código** y la **dirección URL** en la aplicación móvil.

    ![Programa de instalación](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

3. Cuando haya finalizado la activación, seleccione **contacto Yo**. Este paso, envía una notificación o un código de verificación a su teléfono. Seleccione **Comprobar**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Usar su teléfono móvil como método de contacto

1. Seleccione **Autenticación de teléfono** de la lista desplegable.  

    ![Programa de instalación](./media/multi-factor-authentication-end-user-first-time-mobile-phone/phone.png)  

2. Elija su país en la lista desplegable y escriba su número de teléfono móvil.
3. Seleccione el método que desea usar con su teléfono móvil - llamada o texto.
4. Seleccione **contacto** para verificar su número de teléfono. Según el modo en que ha seleccionado, se envía un texto o le llame. Siga las instrucciones que aparecen en la pantalla y luego seleccione **Comprobar**.
5. En este momento, deberá configurar las contraseñas de aplicación de explorador no aplicaciones como Outlook 2010 o anterior o la aplicación de correo electrónico nativo en dispositivos de Apple. Esto es porque algunas aplicaciones no son compatibles con la verificación en dos pasos. Si no utiliza estas aplicaciones, haga clic en **Listo** y omita el resto de los pasos.
6. Si está utilizando estas aplicaciones, copiar la contraseña de aplicación proporcionado y pegarlo en la aplicación en lugar de la contraseña normal. Puede usar la misma contraseña de aplicación para varias aplicaciones. Para obtener más información, [ayuda con las contraseñas de aplicación].
7. Haga clic en **Listo**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Usar el teléfono de office como el método de contacto

1. Seleccione el **Teléfono de la oficina** de la lista desplegable  

    ![Programa de instalación](./media/multi-factor-authentication-end-user-first-time-office-phone/office.png)  

2. El cuadro de número de teléfono se rellena automáticamente con la información de contacto de la empresa. Si el número es incorrecta o que faltan, pida a su administrador para realizar cambios.
4. Seleccione **contacto** para verificar su número de teléfono y llamamos el número. Siga las instrucciones que aparecen en la pantalla y luego seleccione **Comprobar**.
5. En este momento, deberá configurar las contraseñas de aplicación de explorador no aplicaciones como Outlook 2010 o anterior o la aplicación de correo electrónico nativo en dispositivos de Apple. Esto es porque algunas aplicaciones no son compatibles con la verificación en dos pasos. Si no utiliza estas aplicaciones, haga clic en **Listo** y omita el resto de los pasos.
6. Si está utilizando estas aplicaciones, copiar la contraseña de aplicación proporcionado y pegarlo en la aplicación en lugar de la contraseña normal. Puede usar la misma contraseña de aplicación para varias aplicaciones. Para obtener más información, consulte [¿Qué son las contraseñas de la aplicación](multi-factor-authentication-end-user-app-passwords.md).
7. Haga clic en **Listo**.

## <a name="next-steps"></a>Pasos siguientes

- Cambiar las opciones preferidos y [Administrar la configuración de verificación en dos pasos](multi-factor-authentication-end-user-manage-settings.md)
- Configurar las [contraseñas de aplicaciones](multi-factor-authentication-end-user-app-passwords.md) para aplicaciones de dispositivos nativos que no son compatibles con la verificación en dos pasos.
- Consulte la [aplicación de Microsoft autenticador](multi-factor-authentication-microsoft-authenticator.md) para autenticación rápida segura incluso cuando no tiene el servicio de celda.
