<properties
    pageTitle="Aplicación de Microsoft Authenticator para teléfonos móviles | Microsoft Azure"
    description="Obtenga información sobre cómo actualizar a la última versión de Azure autenticador."
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

# <a name="microsoft-authenticator"></a>Autenticador de Microsoft

La aplicación de Microsoft Authenticator proporciona un nivel de seguridad en su cuenta de Azure adicional (por ejemplo, bsimon@contoso.onmicrosoft.com), cuenta del trabajo en su local (por ejemplo, bsimon@contoso.com), o su cuenta de Microsoft (por ejemplo, bsimon@outlook.com).

La aplicación funciona en una de estas dos maneras:

- **Notificación**. La aplicación puede ayudar a evitar el acceso no autorizado a las cuentas y detener transacciones fraudulentas presionando una notificación a su tableta o smartphone. Simplemente ver la notificación y si es legítima, seleccione **Comprobar**. En caso contrario, puede seleccionar **Denegar**. Para obtener información acerca de las notificaciones de denegar, vea cómo usar la característica de fraude de informe y denegar con autenticación multifactor.

- **Contraseña con código de comprobación**. La aplicación se puede utilizar como un símbolo de software para generar un código de comprobación de OAuth. Escriba el código proporcionado por la aplicación en la pantalla de inicio de sesión, junto con el nombre de usuario y contraseña, cuando se le solicite. El código de verificación proporciona una segunda forma de autenticación.

Con el lanzamiento de la aplicación de Microsoft Authenticator, se va a reemplazar la antigua aplicación autenticador de Azure.  La aplicación de Azure autenticador continuarán funcionando, pero si decide mover a la nueva aplicación de Microsoft Authenticator, en este artículo puede ayudarle.  

## <a name="install-the-app"></a>Instalar la aplicación

La aplicación de Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)y [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="add-accounts-to-the-app"></a>Agregar cuentas a la aplicación

Para cada cuenta que desee agregar a la aplicación de Microsoft Authenticator, use uno de los procedimientos siguientes.

### <a name="add-an-account-to-the-app-by-using-the-qr-code-scanner"></a>Agregar una cuenta a la aplicación mediante el analizador de código QR

1. Vaya a la pantalla de configuración de comprobación de seguridad.  Para obtener información sobre cómo obtener acceso a esta pantalla, vea [cambiar la configuración de seguridad](multi-factor-authentication-end-user-manage-settings.md).

2. Seleccione **Configurar**.

    ![El botón configurar en la pantalla de configuración de comprobación de seguridad](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

    Esto incorpora una pantalla con un código QR en él.

    ![Pantalla que proporciona el código QR](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Abra la aplicación de Microsoft autenticador. En la pantalla de **cuentas** , seleccione **+**y, a continuación, especifique que desea agregar una cuenta profesional o educativa.

    ![La pantalla de cuentas con signo](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

    ![Pantalla para especificar una cuenta profesional o educativa](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. Use la cámara para usar el código QR y, a continuación, seleccione **hecho** para cerrar la pantalla de código QR.

    ![Pantalla para digitalizar un código QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

    Si la cámara no funciona correctamente, puede introducir manualmente el código QR y la dirección URL. Para obtener más información, vea [Agregar una cuenta a la aplicación de forma manual](#add-an-account-to-the-app-manually).

5. Espere mientras se activa la cuenta. Cuando termine de activación, seleccione **contacto Yo**.  Envía una notificación o un código de verificación a su teléfono.  Seleccione **Comprobar**.

    ![Pantalla donde selecciona comprobar para iniciar sesión](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

6. Si su empresa necesita un PIN para aprobar la comprobación de inicio de sesión, escríbalo.

    ![Cuadro para escribir un PIN](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

7. Una vez completada la entrada PIN, seleccione **Cerrar**. En este momento, la comprobación debería tener éxito.
8. Le recomendamos que escriba su número de teléfono móvil en caso de perder el acceso a la aplicación. Especifique su país en la lista desplegable y escriba su número de teléfono móvil en el cuadro situado junto al nombre de país. Seleccione **siguiente**.
9. En este momento, ha configurado el método de contacto. Ahora es el momento para configurar las contraseñas de aplicaciones para aplicaciones no son exploradores, como Outlook 2010 o versiones anteriores. Si no usa estas aplicaciones, seleccione **Listo**. En caso contrario, continúe con el paso siguiente.

    ![Pantalla para crear una contraseña de aplicación](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

10. Si está usando aplicaciones de explorador no, copie la contraseña de aplicación proporcionado y pegue la contraseña en las aplicaciones. Para conocer los pasos en aplicaciones individuales, como Outlook y Lync, vea cómo cambiar la contraseña en su correo electrónico a la contraseña de la aplicación y cómo cambiar la contraseña en la aplicación a la contraseña de aplicación.
11. Seleccione **Listo**.

Ahora debe ver la nueva cuenta en la pestaña **cuentas** .

![Pantalla de cuentas](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Agregar manualmente una cuenta de la aplicación

1. Vaya a la pantalla de configuración de comprobación de seguridad.  Para obtener información sobre cómo obtener acceso a esta pantalla, vea [cambiar la configuración de seguridad](multi-factor-authentication-end-user-manage-settings.md).

2. Seleccione **Configurar**.

    ![El botón configurar en la pantalla de configuración de comprobación de seguridad](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

    Esto incorpora una pantalla con un código QR en él.  Nota el código y la dirección URL.

    ![Pantalla que proporciona el código QR y la dirección URL](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Abra la aplicación de Microsoft autenticador. En la pantalla de **cuentas** , seleccione **+**y, a continuación, especifique que desea agregar una cuenta profesional o educativa.

    ![La pantalla de cuentas con signo](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

    ![Pantalla para especificar una cuenta profesional o educativa](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. En el explorador, seleccione **escribir código manualmente**.

    ![Pantalla para digitalizar un código QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

5. Escriba el código y la dirección URL en los cuadros correspondientes en la aplicación.

    ![Pantalla para escribir código y la dirección URL](./media/multi-factor-authentication-azure-authenticator/manual.png)

    ![Pantalla para escribir código y la dirección URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

6. Espere mientras se activa la cuenta. Cuando finalice la activación, seleccione **contacto Yo**. Envía una notificación o un código de verificación a su teléfono. Seleccione **Comprobar**.

Ahora debe ver la nueva cuenta en la pestaña **cuentas** .

![Pantalla de cuentas](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### <a name="add-an-account-to-the-app-by-using-touch-id"></a>Agregar una cuenta a la aplicación mediante ID táctil

La aplicación de Microsoft Authenticator en iOS admite Id. de entrada táctil  La autenticación multifactor Azure permite a las organizaciones requiere un PIN para dispositivos. Con ID táctil, los usuarios de iOS no necesitan especificar un PIN. En su lugar, puede analizar su huella digital y seleccione **Aprobar**.

Configurar identificador táctil con Microsoft Authenticator es sencillo. Completar un desafío de verificación normal con un PIN. Si el dispositivo admite ID táctil, Microsoft Authenticator se configurarlo automáticamente para la cuenta.

![Comprobación de la configuración de identificador táctil](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

Desde la que adelante, cuando se requiere para comprobar el inicio de sesión, seleccione la notificación de inserción recibido y analizar su huella en lugar de escribir el PIN.

![Notificación de inserción](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## <a name="uninstall-the-old-azure-authentication-app"></a>Desinstale la antigua aplicación de autenticación de Azure

Una vez que haya agregado todas las cuentas a la nueva aplicación, puede desinstalar la antigua aplicación desde el teléfono.

## <a name="delete-an-account"></a>Eliminar una cuenta

Para quitar una cuenta de la aplicación de Microsoft Authenticator, seleccione la cuenta y, a continuación, seleccione **Eliminar**.

![Botón Eliminar](./media/multi-factor-authentication-azure-authenticator/remove.png)
