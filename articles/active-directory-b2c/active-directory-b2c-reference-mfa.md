<properties
    pageTitle="Azure Active Directory B2C: Autenticación multifactor | Microsoft Azure"
    description="Cómo habilitar la autenticación multifactor en aplicaciones para consumidores protegidas por B2C de Azure Active Directory"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure Active B2C de directorio: Habilitar la autenticación multifactor en las aplicaciones de consumidor orientación

B2C de Azure Active Directory (AD Azure) se integra directamente con [Autenticación multifactor de Azure](../multi-factor-authentication/multi-factor-authentication.md) para que puede agregar una segunda capa de seguridad a experiencias de suscripción e inicio de sesión en las aplicaciones de consumidor orientación. Y puede hacerlo sin escribir una sola línea de código. Actualmente se admite comprobación de mensaje de llamada de teléfono y el texto. Si ya ha creado directivas de suscripción e inicio de sesión, aún puede habilitar la autenticación multifactor.

> [AZURE.NOTE]
Al crear directivas de suscripción e inicio de sesión, no solo editando directivas existentes, también puede habilitarse la autenticación multifactor.

Esta característica ayuda a las aplicaciones administrar escenarios como los siguientes:

- No requiere autenticación multifactor tener acceso a una aplicación, pero necesita tener acceso a otro. Por ejemplo, el cliente puede iniciar sesión en una aplicación de seguros automáticamente con una cuenta local o social, pero debe comprobar el número de teléfono antes de obtener acceso a la aplicación de seguros inicio registrado en el mismo directorio.
- No requiere autenticación multifactor tener acceso a una aplicación en general, pero lo necesita tener acceso a las partes confidenciales dentro de ella. Por ejemplo, los consumidores pueden iniciar sesión en una aplicación bancaria con una cuenta social o local y el saldo de la cuenta de verificación, pero deben comprobar el número de teléfono antes de intentar a una transferencia.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Modificar la directiva de suscripción para habilitar la autenticación multifactor

1. [Siga estos pasos para navegar hasta el módulo de características B2C en el portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **directivas de suscripción**.
3. Haga clic en la directiva de inicio de sesión (por ejemplo, "B2C_1_SiUp") para abrirlo.
4. Haga clic en **autenticación multifactor** y desactivar el **estado** de **activado**. Haga clic en **Aceptar**.
5. Haga clic en **Guardar** en la parte superior de la hoja.

Puede usar la característica "Ejecutar ahora" en la directiva y para comprobar la experiencia de consumidores. Confirme lo siguiente:

Una cuenta de consumidor se creará en su directorio antes de que se produce el paso de autenticación multifactor. Durante el paso consumidor se pide que proporcione su número de teléfono y compruebe. Si la comprobación es correcta, el número de teléfono está conectado a la cuenta de cliente para su uso posterior. Aunque los consumidores cancelan o abandona, pueda puede pedirá que compruebe un número de teléfono nuevo durante el inicio de sesión siguiente (con la autenticación multifactor habilitado).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Modificar la directiva de inicio de sesión para habilitar la autenticación multifactor

1. [Siga estos pasos para navegar hasta el módulo de características B2C en el portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **directivas de inicio de sesión**.
3. Haga clic en la directiva de inicio de sesión (por ejemplo, "B2C_1_SiIn") para abrirlo. Haga clic en **Editar** en la parte superior de la hoja.
4. Haga clic en **autenticación multifactor** y desactivar el **estado** de **activado**. Haga clic en **Aceptar**.
5. Haga clic en **Guardar** en la parte superior de la hoja.

Puede usar la característica "Ejecutar ahora" en la directiva y para comprobar la experiencia de consumidores. Confirme lo siguiente:

Cuando se inicia el consumidor (utilizando una cuenta social o local), si un número de teléfono comprobado está unido a la cuenta de cliente, que se pide al verificarlo. Si no está conectado a ningún número de teléfono, consumidor se pide al proporcionar uno y confírmela. En comprobación correcta, el número de teléfono está conectado a la cuenta de cliente para su uso posterior.

## <a name="multi-factor-authentication-on-other-policies"></a>Autenticación multifactor en otras directivas

Como se describe para las directivas de suscripción y sesión anteriores, también es posible habilitar la autenticación multifactor de suscripción o directivas restablecen la contraseña y directivas de inicio de sesión. Estará disponible próximamente en directivas de edición del perfil.
