<properties
    pageTitle="Azure Active B2C de directorio: Configuración Google + | Microsoft Azure"
    description="Proporcionar suscripción e inicio de sesión a los usuarios con cuentas de Google + en las aplicaciones que están protegidas por Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure Active B2C de directorio: Proporcionar suscripción e inicio de sesión a los usuarios con cuentas de Google +

## <a name="create-a-google-application"></a>Crear una aplicación de Google +

Para usar Google + como un proveedor de identidades en B2C de Azure Active Directory (AD Azure), debe crear una aplicación de Google + y proporcione con los parámetros de la derecha. Se necesita una cuenta de Google + para realizar esta acción. Si no tiene una, se puede obtener en [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Vaya a la [Consola de programadores de Google](https://console.developers.google.com/) e inicie sesión con sus credenciales de cuenta de Google +.
2. Haga clic en **crear un proyecto**, escriba un **nombre de proyecto**y, a continuación, haga clic en **crear**.

    ![Google + - Introducción](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![Google + - nuevo proyecto](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. Haga clic en **Administrador de API** y, a continuación, haga clic en **credenciales** en el panel de navegación izquierdo.
4. Haga clic en la ficha **OAuth consentimiento pantalla** en la parte superior.

    ![Google + - credenciales](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Seleccione o especifique una **dirección de correo electrónico**de válida, proporcione un **nombre de producto**y haga clic en **Guardar**.

    ![Google + - OAuth consentimiento pantalla](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. Haga clic en **credenciales nuevas** y elija **el identificador del cliente de OAuth**.

    ![Google + - OAuth consentimiento pantalla](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. En **tipo de aplicación**, seleccione la **aplicación Web**.

    ![Google + - OAuth consentimiento pantalla](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. Proporcione un **nombre** para la aplicación, escriba `https://login.microsoftonline.com` en el campo de **orígenes de JavaScript autorizados** , y `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **autorizados redirigir URI** . Reemplace **{inquilino}** por nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com). El valor de **{inquilino}** distingue mayúsculas de minúsculas. Haga clic en **crear**.

    ![Google + - crear ID de cliente](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. Copie los valores del **Identificador de cliente** y el **secreto de cliente**. Necesitará dos configurar Google + como un proveedor de identidades de su inquilino. **Secreto cliente** es una credencial de seguridad importantes.

    ![Google + - secreto de cliente](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Configurar Google + como un proveedor de identidades de su inquilino

1. Siga estos pasos para [navegar hasta el módulo de características B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) en el portal de Azure.
2. En el módulo de características B2C, haga clic en **proveedores de identidades**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. Proporcionar un descriptivo **nombre** para la configuración del proveedor de identidades. Por ejemplo, escriba "G +".
5. Haga clic en **tipo de proveedor de identidades**, seleccione **Google**y haga clic en **Aceptar**.
6. Haga clic en **configurar este proveedor de servicios de identidad** y escriba el identificador de cliente y el secreto de cliente de la aplicación de Google + que creó anteriormente.
7. Haga clic en **Aceptar** y, a continuación, haga clic en **crear** para guardar la configuración de Google +.
