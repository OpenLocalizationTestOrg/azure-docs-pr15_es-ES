<properties
    pageTitle="Azure Active B2C de directorio: Configuración de Amazon | Microsoft Azure"
    description="Proporcionar suscripción e inicio de sesión a los usuarios con cuentas de Amazon en las aplicaciones que están protegidas por Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure Active B2C de directorio: Proporcionar suscripción e inicio de sesión a los usuarios con cuentas de Amazon

## <a name="create-an-amazon-application"></a>Crear una aplicación de Amazon

Para utilizar Amazon como un proveedor de identidades en B2C de Azure Active Directory (AD Azure), debe crear una aplicación de Amazon y proporcione con los parámetros de la derecha. Necesita una cuenta de Amazon para realizar esta acción. Si no tiene una, se puede obtener en [http://www.amazon.com/](http://www.amazon.com/).

1. Vaya al [Centro de desarrollo de Amazon](https://login.amazon.com/) e inicie sesión con sus credenciales de cuenta de Amazon.
2. Si no lo ha hecho ya, haga clic en **Registrarse**, siga los pasos de registro del programador y aceptar la directiva.
3. Haga clic en **Registrar nueva aplicación**.

    ![Registrar una nueva aplicación en el sitio Web de Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. Proporcionar información de la aplicación (**nombre**, **Descripción**y **Dirección URL de aviso de privacidad**) y haga clic en **Guardar**.

    ![Proporcionar información de la aplicación para registrar una nueva aplicación de Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. En la sección **Configuración de Web** , copie los valores del **Identificador de cliente** y el **Secreto de cliente**. (Debe hacer clic en el botón **Mostrar secreto** para ver esto). Tiene dos configurar Amazon como un proveedor de identidades de su inquilino. Haga clic en **Editar** en la parte inferior de la sección. **Secreto a cliente** es una credencial de seguridad importantes.

    ![Proporcionar el identificador de cliente y el secreto de cliente para la nueva aplicación de Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. Escriba `https://login.microsoftonline.com` en el campo **Permite orígenes de JavaScript** y `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **Permite devolver las direcciones URL** . Reemplace **{inquilino}** por nombre de su inquilino (por ejemplo, contoso.onmicrosoft.com). Haga clic en **Guardar**. El valor de **{inquilino}** distingue mayúsculas de minúsculas.

    ![Proporcionar orígenes de JavaScript y devolver URL para la nueva aplicación de Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Configurar Amazon como un proveedor de identidades de su inquilino

1. Siga estos pasos para [navegar hasta el módulo de características B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) en el portal de Azure.
2. En el módulo de características B2C, haga clic en **proveedores de identidades**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. Proporcionar un descriptivo **nombre** para la configuración del proveedor de identidades. Por ejemplo, escriba "Amzn".
5. Haga clic en **tipo de proveedor de identidades**, seleccione **Amazon**y haga clic en **Aceptar**.
6. Haga clic en **configurar este proveedor de servicios de identidad** y escriba el identificador de cliente y el secreto de cliente de la aplicación de Amazon que creó anteriormente.
7. Haga clic en **Aceptar** y, a continuación, haga clic en **crear** para guardar la configuración de Amazon.
