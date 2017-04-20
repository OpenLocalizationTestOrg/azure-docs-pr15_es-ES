<properties
    pageTitle="Azure Active B2C de directorio: Registro de aplicación | Microsoft Azure"
    description="Cómo registrar la aplicación con Azure Active Directory B2C"
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
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="swkrish"/>


# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active B2C de directorio: Registrar la aplicación

## <a name="prerequisite"></a>Requisito previo

Para crear una aplicación que acepta consumidor suscripción e inicio de sesión, debe registrar la aplicación con un inquilino de Azure Active Directory B2C. Obtener a su propio inquilino mediante los pasos descritos en [crear un inquilino de Azure AD B2C](active-directory-b2c-get-started.md). Después de seguir los pasos de este artículo, tendrá el módulo de características B2C anclado a la Startboard.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Desplácese hasta el módulo de características B2C

Si tiene el módulo de características B2C anclado a la Startboard, verá el módulo tan pronto como inicie sesión el [portal de Azure](https://portal.azure.com/) como administrador Global del inquilino B2C.

También puede acceder a la hoja haciendo clic en **Examinar** y, a continuación, en **Azure AD B2C** en el panel de navegación izquierdo en el [portal de Azure](https://portal.azure.com/).

> [AZURE.IMPORTANT] Debe ser administrador Global del inquilino B2C puedan tener acceso a los módulos de características B2C. Un administrador Global de cualquier otro inquilino o de cualquier inquilino un usuario no puede acceder a él.  Puede cambiar su inquilino B2C usando el conmutador de inquilinos de la esquina superior derecha del Portal de Azure.

## <a name="register-an-application"></a>Registrar una aplicación

1. En el módulo de características B2C en el portal de Azure, haga clic en **aplicaciones**.
2. Haga clic en **+ Agregar** en la parte superior de la hoja.
3. Escriba un **nombre** para la aplicación que describa la aplicación a los consumidores. Por ejemplo, podría introducir "Contoso B2C aplicación".
4. Si está escribiendo una aplicación basada en web, activar o desactivar el modificador **incluir Online / web API** en **Sí**. Las **Direcciones URL de respuesta** son extremos donde Azure AD B2C devolverá los tokens que solicita la aplicación. Por ejemplo, escriba `https://localhost:44321/`. Si la aplicación web también se llama algunos web API protegido por Azure AD B2C, desea crear una **Aplicación secreto** también haciendo clic en el botón **Generar clave** .

    > [AZURE.NOTE] Una **Aplicación secreto** es una credencial de seguridad importantes y deben ser protegidos adecuadamente.

5. Si está escribiendo una aplicación móvil, activar o desactivar el modificador de **cliente nativo de incluir** en **Sí**. Copiar el valor predeterminado de **Redirección URI** que se creará automáticamente.
6. Haga clic en **crear** para registrar la aplicación.
7. Haga clic en la aplicación que acaba de crear y copiar hacia abajo el único global **Identificador de cliente de la aplicación** que usará más adelante en el código.

> [AZURE.IMPORTANT] Las aplicaciones creadas en el módulo de características B2C deben administrado en la misma ubicación. Si modifica B2C aplicaciones con PowerShell o con otro portal, que se convierten en no admitidas y es probable que no funcionará con Azure AD B2C.

## <a name="build-a-quick-start-application"></a>Crear una aplicación de inicio rápido

Ahora que ya tiene una aplicación registrada con Azure AD B2C, puede realizar uno de nuestros tutoriales de inicio rápido para ponerse en marcha. Estas son algunas recomendaciones:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]
