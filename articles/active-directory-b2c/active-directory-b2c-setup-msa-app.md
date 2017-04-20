<properties
    pageTitle="Azure Active B2C de directorio: Configuración de la cuenta de Microsoft | Microsoft Azure"
    description="Proporcionar suscripción e inicio de sesión a los usuarios con cuentas de Microsoft en las aplicaciones que están protegidas por Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure Active B2C de directorio: Proporcionar suscripción e inicio de sesión a los usuarios con cuentas de Microsoft

## <a name="create-a-microsoft-account-application"></a>Cree una aplicación de la cuenta de Microsoft

Para usar la cuenta de Microsoft como proveedor de identidad de Azure Active Directory (AD Azure) B2C, debe crear una aplicación de la cuenta de Microsoft y proporcione con los parámetros de la derecha. Se necesita una cuenta de Microsoft para realizar esta acción. Si no tiene una, se puede obtener en [https://www.live.com/](https://www.live.com/).

1. Vaya al [Portal de registro de aplicación de Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) e inicie sesión con sus credenciales de cuenta de Microsoft.
2. Haga clic en **Agregar una aplicación**.

    ![Microsoft cuenta - agregar una nueva aplicación](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. Proporcione un **nombre** para la aplicación y haga clic en **crear una aplicación**.

    ![Cuenta de Microsoft - nombre de la aplicación](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. Copie el valor del **Identificador de la aplicación**. Necesitará para configurar la cuenta de Microsoft como proveedor de identidad de su inquilino.

    ![Cuenta de Microsoft - Id de aplicación](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)

5. Haga clic en **Agregar plataforma** y elija **Web**.

    ![Microsoft cuenta - Agregar plataforma](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)

    ![Cuenta de Microsoft - Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)

6. Escriba `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **URI redirigir** . Reemplace **{inquilino}** por nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com).

    ![Cuenta de Microsoft - redirigir URL](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

7. En la sección de **Información confidencial de la aplicación** , haga clic en **Generar nueva contraseña** . Copiar la nueva contraseña que se muestra en pantalla. Necesitará para configurar la cuenta de Microsoft como proveedor de identidad de su inquilino. Esta contraseña es una credencial de seguridad importantes.

    ![Microsoft cuenta - generar nueva contraseña](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)

    ![Cuenta de Microsoft - contraseña nueva](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)

8. Active la casilla que dice **Live SDK de soporte técnico** en la sección **Opciones avanzadas** . Haga clic en **Guardar**.

    ![Cuenta de Microsoft - soporte técnico de Live SDK](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Configurar la cuenta de Microsoft como proveedor de identidad de su inquilino

1. Siga estos pasos para [navegar hasta el módulo de características B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) en el portal de Azure.
2. En el módulo de características B2C, haga clic en **proveedores de identidades**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. Proporcionar un descriptivo **nombre** para la configuración del proveedor de identidades. Por ejemplo, escriba "MSA".
5. Haga clic en **tipo de proveedor de identidades**, seleccione la **cuenta de Microsoft**y haga clic en **Aceptar**.
6. Haga clic en **configurar este proveedor de servicios de identidad** y escriba el identificador de la aplicación y la contraseña de la aplicación de la cuenta de Microsoft que creó anteriormente.
7. Haga clic en **Aceptar** y, a continuación, haga clic en **crear** para guardar la configuración de cuenta de Microsoft.
