<properties
    pageTitle="Azure Active B2C de directorio: Configuración LinkedIn | Microsoft Azure"
    description="Proporcionar suscripción e inicio de sesión a los usuarios con cuentas de LinkedIn en las aplicaciones que están protegidas por B2C de Azure Active Directory"
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure Active B2C de directorio: Proporcionar suscripción e inicio de sesión a los usuarios con cuentas de LinkedIn

## <a name="create-a-linkedin-application"></a>Crear una aplicación de LinkedIn

Para usar LinkedIn como un proveedor de identidades en B2C de Azure Active Directory (AD Azure), debe crear una aplicación de LinkedIn y proporcione con los parámetros de la derecha. Se necesita una cuenta de LinkedIn para realizar esta acción. Si no tiene una, se puede obtener en [https://www.linkedin.com/](https://www.linkedin.com/).

1. Ir al [sitio Web de desarrolladores de LinkedIn](https://www.developer.linkedin.com/) e inicie sesión con sus credenciales de cuenta de LinkedIn.
2. Haga clic en **Mis aplicaciones** en la barra de menús superior y, a continuación, haga clic en **Crear una aplicación**.

    ![LinkedIn - nueva aplicación](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. En el formulario de **crear una nueva aplicación** , rellene la información relevante (**Nombre de la compañía**, **nombre**, **Descripción**, **Dirección URL del logotipo de la aplicación**, **Uso de la aplicación**, **Dirección URL de sitio Web**, **Correo electrónico del trabajo** y **Teléfono del trabajo**).
4. Aceptar las **Condiciones de uso del API de LinkedIn** y haga clic en **Enviar**.

    ![LinkedIn - aplicación de registro](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. Copie los valores del **Identificador de cliente** y el **Secreto de cliente**. (Podrá buscarlas en **Claves de autenticación**.) Necesitará dos configurar LinkedIn como un proveedor de identidades de su inquilino.

    >[AZURE.NOTE] **Secreto a cliente** es una credencial de seguridad importantes.

6. Escriba `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **Autorizados redirigir las direcciones URL** (en **OAuth 2.0**). Reemplace **{inquilino}** por nombre de su inquilino (por ejemplo, contoso.onmicrosoft.com). Haga clic en **Agregar**y, a continuación, haga clic en **Actualizar**. El valor de **{inquilino}** distingue mayúsculas de minúsculas.

    ![LinkedIn - aplicación de configuración](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>Configurar LinkedIn como un proveedor de identidades de su inquilino

1. Siga estos pasos para [navegar hasta el módulo de características B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) en el portal de Azure.
2. En el módulo de características B2C, haga clic en **proveedores de identidades**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. Proporcionar un descriptivo **nombre** para la configuración del proveedor de identidades. Por ejemplo, escriba "LI".
5. Haga clic en **tipo de proveedor de identidades**, seleccione **LinkedIn**y haga clic en **Aceptar**.
6. Haga clic en **configurar este proveedor de servicios de identidad** y escriba el identificador de cliente y el secreto de cliente de la aplicación LinkedIn que creó anteriormente.
7. Haga clic en **Aceptar** y, a continuación, haga clic en **crear** para guardar la configuración de LinkedIn.
