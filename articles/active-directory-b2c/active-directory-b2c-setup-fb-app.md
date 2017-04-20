<properties
    pageTitle="Azure Active B2C de directorio: Configuración de Facebook | Microsoft Azure"
    description="Proporcionar suscripción e inicio de sesión a los usuarios con cuentas de Facebook en las aplicaciones que están protegidas por Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active B2C de directorio: Proporcionar suscripción e inicio de sesión a los usuarios con cuentas de Facebook

## <a name="create-a-facebook-application"></a>Crear una aplicación de Facebook

Para usar Facebook como un proveedor de identidades en B2C de Azure Active Directory (AD Azure), debe crear una aplicación de Facebook y proporcione con los parámetros de la derecha. Se necesita una cuenta de Facebook para realizar esta acción. Si no tiene una, se puede obtener en [https://www.facebook.com/](https://www.facebook.com/).

1. Vaya al sitio Web de [Facebook para desarrolladores](https://developers.facebook.com/) e inicie sesión con sus credenciales de cuenta de Facebook.
2. Si no lo ha hecho ya, necesita registrar como programador de Facebook. Para ello, haga clic en **registrar** (en la esquina superior derecha de la página), acepte las directivas de Facebook y completar los pasos de registro.
3. Haga clic en **Mis aplicaciones** y, a continuación, haga clic en **Agregar una nueva aplicación**. Elija **sitio Web** es la plataforma y, a continuación, haga clic en **Omitir y crear ID de aplicación**.

    ![Facebook - agregar una nueva aplicación](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![Facebook - agregar una nueva aplicación - sitio Web](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![Facebook - crear ID de aplicación](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. En el formulario, proporcione un **Nombre para mostrar**, un **Correo electrónico de contacto**de válida, una **categoría**de apropiada y haga clic en **Crear ID de aplicación**. Esto requiere que acepte las directivas de plataforma de Facebook y completar una comprobación de seguridad en línea.

    ![Facebook - crear un nuevo ID de aplicación](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. En el panel de navegación izquierdo, haga clic en **configuración** .
6. Haga clic en **+ Agregar plataforma** y, a continuación, seleccione **sitio Web**.

    ![Facebook - configuración](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![Sitio Web de Facebook - configuración-](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. Escriba [https://login.microsoftonline.com/](https://login.microsoftonline.com/) en el campo **Dirección URL del sitio** y, a continuación, haga clic en **Guardar cambios**.

    ![Facebook - dirección URL del sitio](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

8. Copie el valor del **Identificador de aplicación**. Haga clic en **Mostrar** y copie el valor de **Secreto de aplicación**. Necesitará dos configurar Facebook como un proveedor de identidades de su inquilino. **Secreto de aplicación** es una credencial de seguridad importantes.

    ![Facebook - ID de aplicación & secreto de aplicación](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)

9. Haga clic en **+ Agregar productos** en el panel de navegación izquierdo y, a continuación, en el botón **Empezar** junto a **Inicio de sesión de Facebook**.

    ![Facebook - inicio de sesión de Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)

10. Escriba `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **válido OAuth redirigir URI** en la sección **Configuración de OAuth de cliente** . Reemplace **{inquilino}** por nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com). Haga clic en **Guardar cambios** en la parte inferior de la página.

    ![Facebook - URI de redireccionamiento de OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

11. Para hacer que la aplicación de Facebook utilizable por Azure AD B2C, debe hacer esté disponible públicamente. Para ello, haga clic en **Revisión de la aplicación** en el panel de navegación izquierdo y activar el modificador en la parte superior de la página en **Sí** y haciendo clic en **Confirmar**.

    ![Facebook - aplicación público](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Configurar Facebook como un proveedor de identidades de su inquilino

1. Siga estos pasos para [navegar hasta el módulo de características B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) en el portal de Azure.
2. En el módulo de características B2C, haga clic en **proveedores de identidades**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. Proporcionar un descriptivo **nombre** para la configuración del proveedor de identidades. Por ejemplo, escriba "FB".
5. Haga clic en **tipo de proveedor de identidades**, seleccione **Facebook**y haga clic en **Aceptar**.
6. Haga clic en **configurar este proveedor de servicios de identidad** y escriba el identificador de aplicación y el secreto de la aplicación (de la aplicación de Facebook que creó anteriormente) en el **Identificador de cliente** y el **secreto de cliente** campos respectivamente.
7. Haga clic en **Aceptar**y, a continuación, haga clic en **crear** para guardar la configuración de Facebook.
