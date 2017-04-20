<properties
    pageTitle="registro de la aplicación de versión 2.0 | Microsoft Azure"
    description="Cómo registrar una aplicación con Microsoft para habilitar inicio de sesión y obtener acceso a servicios de Microsoft con el extremo de la versión 2.0"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Cómo registrar una aplicación con el extremo de la versión 2.0

Para crear una aplicación que acepta MSA & Azure AD iniciar sesión, primero debe registrar una aplicación con Microsoft.  En este momento, no podrá usar ninguna aplicación existente, es posible que tenga con Azure AD o MSA - tendrá que crear uno nuevo.

> [AZURE.NOTE]
    No todas las características y escenarios de Azure Active Directory son compatibles con el extremo de la versión 2.0.  Para determinar si debe usar el extremo de la versión 2.0, lea acerca de [las limitaciones de la versión 2.0](active-directory-v2-limitations.md).

## <a name="visit-the-microsoft-app-registration-portal"></a>Visite el portal de registro de la aplicación de Microsoft
Lo primero es lo primero: vaya a [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Se trata de un nuevo portal de registro de aplicación desde donde puede administrar las aplicaciones de Microsoft.

Sesión ya sea un personal o trabajo o escuela cuenta de Microsoft.  Si no tiene uno, registrarse para obtener una cuenta personal. Continuar, no tardar: Esperamos va aquí.

¿Ha terminado? Ahora piense en la lista de aplicaciones de Microsoft, que es probable vacío.  Vamos a cambiar.

Haga clic en **Agregar una aplicación**y a continuación, asígnele un nombre.  El portal de asignará un identificador exclusivo global de aplicación que usará más adelante en el código de la aplicación.  Si su aplicación incluye un componente de servidor que necesita tokens de acceso para llamar a las API (pensar: Office, Azure o su propia web API), que desee crear una **Aplicación secreto** aquí también.
<!-- TODO: Link for app secrets -->

A continuación, agregue las plataformas que vayan a utilizar la aplicación.

- Para las aplicaciones basadas en la web, proporcione una **Redirección URI** donde se pueden enviar mensajes de inicio de sesión.
- Para las aplicaciones móviles, copiar hacia abajo el uri de redirección predeterminada creado automáticamente.

Si lo desea, puede personalizar la apariencia de la página de inicio de sesión en la sección de perfil.  Asegúrese de hacer clic en **Guardar** antes de continuar.

> [AZURE.NOTE] Cuando se crea una aplicación con [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), la aplicación se registrará en el inquilino principal de la cuenta que utiliza para iniciar sesión en el portal.  Esto significa que no se puede registrar una aplicación de su inquilino de Azure AD mediante una cuenta personal de Microsoft.  Si desea registrar una aplicación en un determinado inquilino explícitamente, inicie sesión con una cuenta que se creó originalmente en ese inquilino.

## <a name="build-a-quick-start-app"></a>Crear una aplicación de inicio rápido
Ahora que ya tiene una aplicación de Microsoft, puede realizar uno de los tutoriales de inicio rápido de la versión 2.0.  Estas son algunas recomendaciones:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]
