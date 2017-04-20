<properties
    pageTitle="Temas de Ayuda de registro de la aplicación Portal | Microsoft Azure"
    description="Descripción de las diferentes funciones en el portal de registro de la aplicación de Microsoft."
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

# <a name="app-registration-reference"></a>Referencia del registro de aplicación
Este documento proporciona contexto y las descripciones de diversas características que se encuentran en el Portal de registro de aplicación de Microsoft [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).

## <a name="my-applications"></a>Mis aplicaciones
Esta lista contiene todas las aplicaciones registradas para su uso con el extremo de la versión 2.0 de Azure AD.  Estas aplicaciones tienen la capacidad de los usuarios con dos cuentas personales de la cuenta de Microsoft y cuentas de trabajo o la escuela de Azure Active Directory de inicio de sesión.  Para obtener más información sobre el extremo de la versión 2.0 de Azure AD, consulte nuestra [Descripción general de la versión 2.0](active-directory-appmodel-v2-overview.md).  Estas aplicaciones también pueden utilizarse para integrar con el extremo de autenticación de cuentas de Microsoft, `https://login.live.com`.

## <a name="live-sdk-applications"></a>Live aplicaciones SDK
Esta lista contiene todas las aplicaciones registradas para su uso únicamente con la cuenta de Microsoft.  No están habilitadas para su uso con Azure Active Directory alguna.  Esto es donde encontrará todas las aplicaciones que habían sido registradas previamente con el portal de programadores MSA en `https://account.live.com/developers/applications`.  Todas las funciones que realizó en `https://account.live.com/developers/applications` ahora se puede realizar en este nuevo portal, `https://apps.dev.microsoft.com`.  Si tiene alguna pregunta sobre las aplicaciones de la cuenta de Microsoft, póngase en contacto con nosotros.

## <a name="application-secrets"></a>Información confidencial de la aplicación
Información confidencial de la aplicación es credenciales que permiten a su aplicación realizar la [autenticación del cliente](http://tools.ietf.org/html/rfc6749#section-2.3) de fiable con Azure AD.  En OAuth & OpenID conectarse, una información confidencial de la aplicación es comúnmente como un `client_secret`.  En el protocolo de la versión 2.0, cualquier aplicación que recibe un token de seguridad en una ubicación de web accesible (con un `https` combinación) debe usar un secreto de aplicación para identificar en Azure AD tras la amortización de ese token de seguridad.  Además, cualquier cliente nativo que recibe tokens en un dispositivo se prohibido desde mediante un secreto de aplicación para realizar la autenticación de cliente, para evitar el almacenamiento de información confidencial en entornos inseguros.

Cada aplicación puede contener dos información confidencial de aplicación válida en un momento dado en el tiempo.  Al mantener dos de información confidencial, tiene la ablilty para realizar la sustitución de la clave periódico a través de entorno de la aplicación.  Una vez que haya migrado la totalidad de la aplicación a un nuevo secreto, puede eliminar el antiguo secreto y aprovisionar uno nuevo.

En este momento, se admiten sólo dos tipos de información confidencial de la aplicación en el portal de registro de la aplicación.  Elegir **Generar nueva contraseña** generará y almacenar un secreto compartido en el almacén de datos correspondientes, que puede usar en la aplicación.  Si elige **Generar nuevo par de clave** , se creará un par de claves públicas y privadas nuevo que puede descargar y usar para la autenticación de cliente a Azure AD.

## <a name="profile"></a>Perfil
La sección de perfil del portal de registro de aplicación de puede usarse para personalizar la página de la aplicación de inicio de sesión.  En este momento, se puede modificar el inicio de sesión logotipo de la aplicación de la página, las condiciones de la dirección URL del servicio y la declaración de privacidad.  El logotipo debe ser un transparente 48 x 48 o 50 x 50 píxeles una imagen en un archivo GIF, PNG o JPEG 15 KB o más pequeña.  Intente cambiar los valores y ver el inicio de sesión resultante en la página!

## <a name="live-sdk-support"></a>Soporte de SDK
Al habilitar "Live SDK soporte", se aprovisionará ningún secreto de aplicación que se crea en la Azure AD y almacena datos de Microsoft Account.  Esto le permitirá la aplicación integrar directamente con el servicio de Microsoft Account (login.live.com).  Si desea crear una aplicación con Microsoft Account directamente (en lugar de usar el extremo de la versión 2.0 de Azure AD), asegúrese de que está habilitada la compatibilidad del SDK de Live.

Deshabilitar la compatibilidad con Live SDK se asegurará de que el secreto de aplicación solo se escribe en los datos de Azure AD almacenar.  Los datos de Azure AD almacén incorpora normas de nivel empresarial que le permiten cumplir determinados estándares, como el cumplimiento de FISMA.  Si habilita la compatibilidad de Live SDK, la aplicación que no obtenga algunos de estos estándares de cumplimiento.

Si planea usar el extremo de la versión 2.0 de Azure AD nunca, puede deshabilitar con seguridad soporte técnico de Live SDK.

