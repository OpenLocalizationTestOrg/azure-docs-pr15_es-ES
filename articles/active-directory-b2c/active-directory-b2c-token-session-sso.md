<properties
    pageTitle="Azure Active B2C de directorio: Token, sesión y configuración de inicio de sesión único | Microsoft Azure"
    description="Token, la sesión y la configuración de inicio de sesión único en Azure Active Directory B2C"
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

# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure Active B2C de directorio: Token, sesión y configuración de inicio de sesión único

Esta característica le permite controlar, [por la directiva de base](active-directory-b2c-reference-policies.md), de:
 
1. Duración de token de seguridad emitido por B2C de Azure Active Directory (AD Azure).
2. Duración de sesiones de la aplicación web administradas por Azure AD B2C.
3. Comportamiento de inicio de sesión único (SSO) entre distintas aplicaciones y directivas de su inquilino B2C.

Puede usar esta característica en su inquilino B2C como sigue:

1. Siga estos pasos para [navegar hasta el módulo de características B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) en el portal de Azure.
2. Haga clic en **directivas de inicio de sesión**. *Nota: puede usar esta característica en cualquier tipo de directiva, no sólo en* *Directivas de inicio de sesión***.
3. Abra una directiva haciendo clic en ella. Por ejemplo, haga clic en **B2C_1_SiIn**.
4. Haga clic en **Editar** en la parte superior de la hoja.
5. Haga clic en **símbolo, sesión y configuración de inicio de sesión único**.
6. Realice los cambios que desee. Obtenga información sobre las propiedades disponibles en las secciones siguientes.
7. Haga clic en **Aceptar**.
8. En la parte superior de la hoja, haga clic en **Guardar** .

![Captura de pantalla de token, la sesión y la configuración de inicio de sesión único](./media/active-directory-b2c-token-session-sso/token-session-sso.png)

## <a name="token-lifetimes-configuration"></a>Configuración de token de duración

Azure AD B2C es compatible con el [Protocolo de autorización de OAuth 2.0](active-directory-b2c-reference-protocols.md) para habilitar el acceso seguro a los recursos protegidos. Para implementar esta compatibilidad, Azure AD B2C emite varios [tokens de seguridad](active-directory-b2c-reference-tokens.md). Estas son las propiedades que puede usar para administrar la duración de token de seguridad emitido por Azure AD B2C:

- **Duración token de acceso & ID (minutos)**: la duración del token portador de OAuth 2.0 se usa para obtener acceso a un recurso protegido. Azure AD B2C emite tokens de identificador solo en este momento. Este valor se aplicará a tokens de acceso, cuando se agrega compatibilidad para ellos.
   - Predeterminado = 60 minutos.
   - Mínimo (inclusive) = 5 minutos.
   - Máximo (inclusive) = 1440 minutos.
- **Duración de token de actualización (días)**: el período de tiempo máximo antes de que un símbolo de actualización puede usarse para adquirir un nuevo acceso o token de ID (y opcionalmente, una nueva actualización token, si su aplicación tenía concedida la `offline_access` ámbito).
   - Predeterminado = 14 días.
   - Mínimo (inclusive) = 1 día.
   - Máximo (inclusive) = 90 días.
- **Deslizar la duración de la ventana de token de actualización (días)**: después de este período de tiempo transcurrido el usuario se obliga a volver a autenticarse, independientemente del período de validez de la más reciente actualizar token adquirida por la aplicación. Solo puede proporcionarse si el modificador se establece en **delimitada**. Debe ser mayor o igual que el **duración de token de actualización (días)** valor. Si el modificador se establece en **Unbounded**, no puede proporcionar un valor específico.
   - Predeterminado = 90 días.
   - Mínimo (inclusive) = 1 día.
   - Máximo (inclusive) = 365 días.

Estos son un par de casos de uso que pueden habilitar el uso de estas propiedades:

- Permitir que un usuario permanece conectada mi sesión en una aplicación móvil indefinidamente, siempre y cuando está continuamente activo en la aplicación. Puede hacerlo estableciendo la **duración de ventana de deslizante token de actualización (días)** cambiar **Unbounded** en la directiva de inicio de sesión.
- Cumplir los requisitos de cumplimiento y seguridad de la industria estableciendo la duración del token de acceso adecuado.

## <a name="session-configuration"></a>Configuración de la sesión

Azure AD B2C es compatible con el [Protocolo de autenticación OpenID conectar](active-directory-b2c-reference-oidc.md) para habilitar el inicio de sesión seguro en aplicaciones web. Estas son las propiedades que puede usar para administrar las sesiones de la aplicación web:

- **Aplicación Web sesión duración (en minutos)**: la duración de cookie de sesión de Azure AD B2C almacenado en el explorador del usuario tras la autenticación.
   - Predeterminada = 1440 minutos.
   - Mínimo (inclusive) = 15 minutos.
   - Máximo (inclusive) = 1440 minutos.
- **Tiempo de espera de sesión de Web app**: si este parámetro se establece en **absoluto**, el usuario está forzado a volver a autenticarse tras el período de tiempo especificado por transcurre **Web app sesión duración (en minutos)** . Si este parámetro se establece como **rodante** (el valor predeterminado), el usuario permanece iniciado en mientras el usuario está continuamente activo en la aplicación web.

Estos son un par de casos de uso que pueden habilitar el uso de estas propiedades:

- Cumple los requisitos de seguridad y cumplimiento de la industria mediante la configuración de la sesión de la aplicación web correspondiente duraciones.
- Forzar nueva autenticación tras un periodo de tiempo establecido durante la interacción de un usuario con un elemento de alta seguridad de la aplicación web. 

## <a name="single-sign-on-sso-configuration"></a>Configuración de inicio de sesión único (SSO)

Si tiene varias aplicaciones y directivas de su inquilino B2C, puede administrar las interacciones del usuario entre ellos mediante la propiedad de **configuración de inicio de sesión único** . Puede establecer la propiedad a uno de los siguientes valores:

- **Inquilino**: este es el valor predeterminado. Utilizar esta configuración permite varias aplicaciones y directivas de su inquilino B2C para compartir la misma sesión de usuario. Por ejemplo, una vez que un usuario inicia sesión en una aplicación, compras de Contoso, él o ella puede también perfecta iniciar sesión en otra farmacia uno, de Contoso, cuando se tiene acceso a él.
- **Aplicación**: permite mantener una sesión de usuario exclusivamente para una aplicación, con independencia de otras aplicaciones. Por ejemplo, si desea que el usuario para iniciar sesión en Contoso farmacia (con las mismas credenciales), aunque pueda ya ha iniciado sesión en la compra de Contoso, otra aplicación en el mismo B2C de inquilinos. 
- **Directiva**: permite mantener una sesión de usuario exclusivamente para una directiva, independiente de las aplicaciones con él. Por ejemplo, si el usuario ya ha iniciado sesión y completado un paso de autenticación (AMF) entre varios factores, que puede tener acceso a partes de una mayor seguridad de varias aplicaciones mientras la sesión están vinculada a la directiva no expire.
- **Deshabilitado**: Esto obliga a los usuarios ejecuten a través de viaje de usuario completo en cada ejecución de la directiva. Por ejemplo, esto le permitirá varios usuarios para registrarse a la aplicación (en un escenario de escritorio compartido), incluso durante un único usuario permanece iniciada en durante el tiempo completo.
