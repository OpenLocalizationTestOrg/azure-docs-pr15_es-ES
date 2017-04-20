<properties
    pageTitle="Azure Active B2C de directorio: Atributos personalizados | Microsoft Azure"
    description="Cómo utilizar atributos personalizados en Azure Active Directory B2C para recopilar información acerca de los consumidores"
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

#  <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure Active B2C de directorio: Utilizar atributos personalizados para recopilar información acerca de los consumidores

El directorio de Azure Active Directory (AD Azure) B2C se suministra con un conjunto integrado de información (atributos): nombre, apellidos, ciudad, código Postal y otros atributos. No obstante, todas las aplicaciones para consumidores tiene requisitos únicos en qué atributos de recopilación de consumidores. Con Azure AD B2C, puede ampliar el conjunto de atributos almacenados en cada cuenta de consumidores. Puede crear atributos personalizados en el [portal de Azure](https://portal.azure.com/) y usarla en las directivas de la suscripción, tal como se muestra a continuación. También puede leer y escribir estos atributos mediante la [API de Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [AZURE.NOTE]
Atributos personalizados usan [las extensiones de esquema de directorio de Azure AD Graph API](https://msdn.microsoft.com/library/azure/dn720459.aspx).

## <a name="create-a-custom-attribute"></a>Crear un atributo personalizado

1. [Siga estos pasos para navegar hasta el módulo de características B2C en el portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **atributos de usuario**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. Proporcione un **nombre** para el atributo personalizado (por ejemplo, "ShoeSize") y, opcionalmente, una **Descripción**. Haga clic en **crear**.

    > [AZURE.NOTE]
    Solo "Cadena" **Tipo de datos** está disponible actualmente.

El atributo personalizado ahora está disponible en la lista de **atributos**de usuario y para su uso en las directivas de suscripción.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Utilizar un atributo personalizado en la directiva de suscripción

1. [Siga estos pasos para navegar hasta el módulo de características B2C en el portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **directivas de suscripción**.
3. Haga clic en la directiva de inicio de sesión (por ejemplo, "B2C_1_SiUp") para abrirlo. Haga clic en **Editar** en la parte superior de la hoja.
4. Haga clic en **atributos de suscripción** y seleccione el atributo personalizado (por ejemplo, "ShoeSize"). Haga clic en **Aceptar**.
5. Haga clic en **notificaciones de la aplicación** y seleccione el atributo personalizado. Haga clic en **Aceptar**.
6. Haga clic en **Guardar** en la parte superior de la hoja.

Puede usar la característica "Ejecutar ahora" en la directiva y para comprobar la experiencia de consumidores. Ahora debe ver "ShoeSize" en la lista de atributos recopilados durante la suscripción del consumidor y verlo en el token enviado a su aplicación.

## <a name="notes"></a>Notas

- Junto con las directivas de la suscripción, atributos personalizados también pueden utilizarse en directivas de inicio de sesión o inicio de sesión y directivas de edición del perfil.
- Hay una limitación conocida de atributos personalizados. Solo se crea la primera vez que se utiliza en cualquier directiva y no al agregarlo a la lista de **atributos**de usuario.
