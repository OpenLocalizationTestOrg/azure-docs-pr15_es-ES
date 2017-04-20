<properties
    pageTitle="Azure Active B2C de directorio: Marco de directivas Extensible | Microsoft Azure"
    description="Un tema en el marco de directivas extensible de Azure Active Directory B2C y sobre cómo crear distintos tipos de directiva"
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

# <a name="azure-active-directory-b2c-extensible-policy-framework"></a>Azure Active B2C de directorio: Marco de directivas Extensible

## <a name="the-basics"></a>Conceptos básicos

El marco de directivas extensible de Azure Active Directory (AD Azure) B2C es la potencia de núcleo del servicio. Directivas de describen completamente experiencias de identidad de consumidor como suscripción, inicio de sesión o perfil de edición. Por ejemplo, una directiva de inicio de sesión le permite controlar comportamientos mediante la configuración de las siguientes opciones:

- Tipos de cuenta (cuentas sociales como Facebook o cuentas locales como dirección de correo electrónico) que pueden usar los consumidores de inicio de sesión en la aplicación.
- Atributos (por ejemplo, nombre, código postal y tamaño de anclaje) que se recopilen del consumidor durante la suscripción.
- Uso de la autenticación multifactor.
- La apariencia y funcionamiento de todas las páginas de suscripción.
- Información (que manifiestos de notificaciones en un token) que la aplicación cuando recibe la directiva termina la ejecución.

Puede crear directivas de varios de los diferentes tipos de su inquilino y usarlos en las aplicaciones según sea necesario. Pueden reutilizar directivas en aplicaciones. Esto permite a los desarrolladores definir y modificar consumidor experiencias de identidad con mínimas o sin cambios en su código.

Las directivas están disponibles para su uso mediante una interfaz de desarrollador simple. La aplicación activa una directiva mediante una solicitud de autenticación HTTP estándar (pasando un parámetro de directiva en la solicitud) y recibe un token personalizado como respuesta. Por ejemplo, la única diferencia entre solicitudes invocar una directiva de suscripción y los invocar una directiva de inicio de sesión es el nombre de directiva utilizado en el parámetro de cadena de consulta "p":

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

Para obtener más detalles sobre el marco de directivas, consulte esta [entrada de blog](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-policy"></a>Crear una directiva de suscripción

Para habilitar la suscripción en la aplicación, debe crear una directiva de suscripción. Esta directiva, describe las experiencias que atraviesan los consumidores durante la suscripción y el contenido de tokens que recibirá la aplicación en suscripciones correcta.

1. [Siga estos pasos para navegar hasta el módulo de características B2C en el portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **directivas de suscripción**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. El **nombre** determina el nombre de suscripción directiva utilizado por la aplicación. Por ejemplo, escriba "SiUp".
5. Haga clic en **proveedores de identidades** y seleccione "Suscripción de correo electrónico". Si lo desea, también puede seleccionar proveedores de identidades social, si ya ha configurado. Haga clic en **Aceptar**.
6. Haga clic en **atributos de suscripción**. A continuación elija los atributos que se desean recopilar del consumidor durante la suscripción. Por ejemplo, seleccione "País o región", "Nombre para mostrar" y "Código Postal". Haga clic en **Aceptar**.
7. Haga clic en **notificaciones de la aplicación**. A continuación seleccione notificaciones que desee incluir en los símbolos que se envía a la aplicación después de una experiencia de suscripción correcta. Por ejemplo, seleccione "Nombre para mostrar", "Proveedor de identidades", "Código Postal", "Usuario es nuevo" y "ID de objeto de usuario".
8. Haga clic en **crear**. Observe que la directiva que acaba de crear aparece como "**B2C_1_SiUp**" (la **B2C\_1\_ ** fragmento se agrega automáticamente) en el módulo de **directivas de suscripción** .
9. Abra la directiva haciendo clic en "**B2C_1_SiUp**".
10. Seleccione "Contoso B2C aplicación" en la lista desplegable de **aplicaciones** y `https://localhost:44321/` en la **dirección URL de respuesta o redirección URI** desplegable.
11. Haga clic en **Ejecutar ahora**. Se abre una nueva pestaña del explorador y se puede ejecutar mediante la experiencia de usuario de inicio de sesión de la aplicación.

    > [AZURE.NOTE]
    Ocupa un minuto para la creación de la directiva y actualizaciones surta efecto.

## <a name="create-a-sign-in-policy"></a>Crear una directiva de inicio de sesión

Para habilitar el inicio de sesión en la aplicación, debe crear una directiva de inicio de sesión. Esta directiva, describe las experiencias que los consumidores atraviesa durante el inicio de sesión y el contenido de tokens que recibirá la aplicación en inicios de sesión correctamente.

1. [Siga estos pasos para navegar hasta el módulo de características B2C en el portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **directivas de inicio de sesión**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. El **nombre** determina el nombre de inicio de sesión de la directiva utilizado por la aplicación. Por ejemplo, escriba "SiIn".
5. Haga clic en **proveedores de identidades** y seleccione "Inicio de sesión de cuenta Local". Si lo desea, también puede seleccionar proveedores de identidades social, si ya ha configurado. Haga clic en **Aceptar**.
6. Haga clic en **notificaciones de la aplicación**. A continuación seleccione notificaciones que desee incluir en los símbolos que se envía a la aplicación después de una experiencia de inicio de sesión correctamente. Por ejemplo, seleccione "Nombre para mostrar", "Proveedor de identidades", "Código Postal" y "ID de objeto de usuario". Haga clic en **Aceptar**.
7. Haga clic en **crear**. Observe que la directiva que acaba de crear aparece como "**B2C_1_SiIn**" (la **B2C\_1\_ ** fragmento se agrega automáticamente) en el módulo de **directivas de inicio de sesión** .
8. Abra la directiva haciendo clic en "**B2C_1_SiIn**".
9. Seleccione "Contoso B2C aplicación" en la lista desplegable de **aplicaciones** y `https://localhost:44321/` en la **dirección URL de respuesta o redirección URI** desplegable.
10. Haga clic en **Ejecutar ahora**. Se abre una nueva pestaña del explorador y puede ejecutar a través de la experiencia del consumidor de iniciar sesión en la aplicación.

    > [AZURE.NOTE]
    Ocupa un minuto para la creación de la directiva y actualizaciones surta efecto.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Crear una directiva de inicio de sesión o inicio de sesión

Esta directiva controla dos experiencias de suscripción & inicio de sesión de consumidor con una sola configuración. Los consumidores son dirigidos por la ruta derecha (suscripción o inicio de sesión) según el contexto. También se describe el contenido de tokens que recibirá la aplicación correcta suscripciones o inicios de sesión.  Un ejemplo de código para la directiva de inicio de sesión o inicio de sesión está [disponible aquí](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

1. [Siga estos pasos para navegar hasta el módulo de características B2C en el portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **directivas de inicio de sesión o inicio de sesión**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. El **nombre** determina el nombre de suscripción directiva utilizado por la aplicación. Por ejemplo, escriba "SiUpIn".
5. Haga clic en **proveedores de identidades** y seleccione "Suscripción de correo electrónico". Si lo desea, también puede seleccionar proveedores de identidades social, si ya ha configurado. Haga clic en **Aceptar**.
6. Haga clic en **atributos de suscripción**. A continuación elija los atributos que se desean recopilar del consumidor durante la suscripción. Por ejemplo, seleccione "País o región", "Nombre para mostrar" y "Código Postal". Haga clic en **Aceptar**.
7. Haga clic en **notificaciones de la aplicación**. A continuación seleccione notificaciones que desee incluir en los símbolos que se envía a la aplicación después de una experiencia de inicio de sesión o inicio de sesión correctamente. Por ejemplo, seleccione "Nombre para mostrar", "Proveedor de identidades", "Código Postal", "Usuario es nuevo" y "ID de objeto de usuario".
8. Haga clic en **crear**. Observe que la directiva que acaba de crear aparece como "**B2C_1_SiUpIn**" (la **B2C\_1\_ ** fragmento se agrega automáticamente) en el módulo de **directivas de inscripción o inicio de sesión** .
9. Abra la directiva haciendo clic en "**B2C_1_SiUpIn**".
10. Seleccione "Contoso B2C aplicación" en la lista desplegable de **aplicaciones** y `https://localhost:44321/` en la **dirección URL de respuesta o redirección URI** desplegable.
11. Haga clic en **Ejecutar ahora**. Se abre una nueva pestaña del explorador y puede ejecutar a través de la experiencia del consumidor de suscripción o iniciar sesión como se ha configurado.

    > [AZURE.NOTE]
    Ocupa un minuto para la creación de la directiva y actualizaciones surta efecto.

## <a name="create-a-profile-editing-policy"></a>Crear un perfil de directiva de edición

Para habilitar la edición en la aplicación del perfil, debe crear un perfil de directiva de edición. Esta directiva, describe las experiencias que atraviesan los consumidores durante la edición de perfil y el contenido de tokens que recibirá la aplicación con éxito.

1. [Siga estos pasos para navegar hasta el módulo de características B2C en el portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **directivas de edición de perfiles**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. El **nombre** determina el perfil de nombre de la directiva utilizada por la aplicación de edición. Por ejemplo, escriba "SiPe".
5. Haga clic en **proveedores de identidades** y seleccione "Dirección de correo electrónico". Si lo desea, también puede seleccionar proveedores de identidades social, si ya ha configurado. Haga clic en **Aceptar**.
6. Haga clic en **atributos del perfil**. A continuación elija atributos que pueden ver y editar los consumidores. Por ejemplo, seleccione "País o región", "Nombre para mostrar" y "Código Postal". Haga clic en **Aceptar**.
7. Haga clic en **notificaciones de la aplicación**. A continuación seleccione notificaciones que desee incluir en los símbolos que se envía a la aplicación después de una experiencia de edición de perfil correcta. Por ejemplo, seleccione "Nombre para mostrar" y "Código Postal".
8. Haga clic en **crear**. Observe que la directiva que acaba de crear aparece como "**B2C_1_SiPe**" (la **B2C\_1\_ ** fragmento se agrega automáticamente) en el módulo de **directivas de edición de perfiles** .
9. Abra la directiva haciendo clic en "**B2C_1_SiPe**".
10. Seleccione "Contoso B2C aplicación" en la lista desplegable de **aplicaciones** y `https://localhost:44321/` en la **dirección URL de respuesta o redirección URI** desplegable.
11. Haga clic en **Ejecutar ahora**. Se abre una nueva pestaña del explorador y se puede ejecutar mediante la experiencia de usuario en la aplicación de edición del perfil.

    > [AZURE.NOTE]
    Ocupa un minuto para la creación de la directiva y actualizaciones surta efecto.
    
## <a name="create-a-password-reset-policy"></a>Crear una directiva de restablecimiento de contraseña

Para habilitar la contraseña poseen restablecer en la aplicación, debe crear una directiva de restablecimiento de contraseña. Nota que para restablecer la contraseña de todo el inquilino opción especificada [aquí](active-directory-b2c-reference-sspr.md) es aplicable para las directivas de inicio de sesión. Esta directiva, describe las experiencias que atraviesan los consumidores durante el restablecimiento de contraseña y el contenido de tokens que recibirá la aplicación con éxito.

1. [Siga estos pasos para navegar hasta el módulo de características B2C en el portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **directivas de restablecimiento de contraseña**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. El **nombre** determina el que nombre de la directiva utilizada por la aplicación de restablecer la contraseña. Por ejemplo, escriba "SSPR".
5. Haga clic en **proveedores de identidades** y seleccione "Restablecer la contraseña con la dirección de correo electrónico". Haga clic en **Aceptar**.
6. Haga clic en **notificaciones de la aplicación**. A continuación seleccione notificaciones que desee incluir en los símbolos que se envía a la aplicación después de restablece la contraseña correcta experiencia. Por ejemplo, seleccione "ID de objeto de usuario".
7. Haga clic en **crear**. Observe que la directiva que acaba de crear aparece como "**B2C_1_SSPR**" (la **B2C\_1\_ ** fragmento se agrega automáticamente) en el módulo de **directivas de restablecimiento de contraseña** .
8. Abra la directiva haciendo clic en "**B2C_1_SSPR**".
9. Seleccione "Contoso B2C aplicación" en la lista desplegable de **aplicaciones** y `https://localhost:44321/` en la **dirección URL de respuesta o redirección URI** desplegable.
10. Haga clic en **Ejecutar ahora**. Se abre una nueva pestaña del explorador y se puede ejecutar mediante la experiencia de consumidores de restablecimiento de contraseña en la aplicación.

    > [AZURE.NOTE]
    Ocupa un minuto para la creación de la directiva y actualizaciones surta efecto.

## <a name="additional-resources"></a>Recursos adicionales

- [Token, la sesión y la configuración de inicio de sesión único](active-directory-b2c-token-session-sso.md).
