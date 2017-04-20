<properties
    pageTitle="Autenticación y la autorización de servicio de la aplicación de Azure | Microsoft Azure"
    description="Referencia conceptual e información general sobre la autenticación / autorización de características de servicio de la aplicación de Azure"
    services="app-service"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="mahender"/>

# <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticación y la autorización de servicio de la aplicación de Azure

## <a name="what-is-app-service-authentication--authorization"></a>¿Qué es la aplicación de servicio de autenticación o autorización?

Aplicación servicio de autenticación / autorización es una característica que proporciona una manera de su aplicación iniciar sesión en los usuarios para que no tiene que cambiar el código en la aplicación de back-end. Proporciona una forma sencilla de proteger la aplicación y trabajar con datos de cada usuario.

Aplicación de servicio usa identidad federada, en la que un proveedor de identidades de terceros almacena las cuentas y autentica a los usuarios. La aplicación se basa en información de la identidad del proveedor para que no tiene la aplicación para almacenar esa información en Sí. Aplicación de servicio compatible con cinco proveedores de identidades fuera de la caja: Twitter, Facebook, Google, Account de Microsoft y Azure Active Directory. La aplicación puede usar cualquier número de estos proveedores de identidades para proporcionar a los usuarios con opciones para cómo iniciar sesión en. Para expandir la compatibilidad integrada, puede integrar otro proveedor de identidades o [su propia solución identidad personalizada][custom-auth].

Si desea comenzar inmediatamente, vea uno de los siguientes tutoriales:

- [Agregar autenticación para su aplicación iOS] [ iOS] (o [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]o [Cordova])
- [Autenticación de usuario para las aplicaciones de la API de servicio de la aplicación de Azure][apia-user]
- [Introducción a la aplicación de servicio de Azure - parte 2][web-getstarted]

## <a name="how-authentication-works-in-app-service"></a>Cómo funciona la autenticación en la aplicación de servicio

Para autenticar mediante uno de los proveedores de identidades, debe configurar el proveedor de identidades saber sobre la aplicación. A continuación, le proporcionará el proveedor de identidades identificadores y la información confidencial que proporciona a la aplicación de servicio. Esto completa la relación de confianza para que el servicio de aplicación puede validar aserciones de usuario, como símbolos de autenticación, desde el proveedor de identidades.

Para cerrar la sesión de un usuario mediante uno de estos proveedores, el usuario debe redirigirá a un extremo que inicia sesión los usuarios para ese proveedor. Si clientes están utilizando un explorador web, puede hacer que el servicio de aplicación dirigir automáticamente a todos los usuarios autenticados al extremo que inicia sesión los usuarios. En caso contrario, tendrá que dirija a sus clientes a `{your App Service base URL}/.auth/login/<provider>`, donde `<provider>` es uno de los siguientes valores: aad, facebook, google, microsoft o twitter. En secciones más adelante en este artículo se explican los escenarios móviles y API.

Los usuarios que interactúan con la aplicación a través de un explorador web tendrá una cookie configurar para que puedan permanecer autenticados al examinar la aplicación. Para otros tipos de cliente, como mobile, un símbolo de web JSON (JWT), que se presentarán en la `X-ZUMO-AUTH` encabezado, será emitida al cliente. El cliente de aplicaciones Mobile SDK controlará por usted. Como alternativa, un token de identidad de Azure Active Directory o el token de acceso puede estar directamente incluido en el `Authorization` encabezado como [token portador](https://tools.ietf.org/html/rfc6750).

Aplicación de servicio valida cualquier cookie o el token de problemas de la aplicación para autenticar a los usuarios. Para restringir quién puede tener acceso a la aplicación, consulte la sección de [autorización](#authorization) más adelante en este artículo.

### <a name="mobile-authentication-with-a-provider-sdk"></a>Autenticación de dispositivos móvil con un proveedor de SDK

Después de que todo está configurado en el servidor, puede modificar los clientes móviles que inicie sesión con la aplicación de servicio. Existen dos métodos aquí:

- Use un SDK que publica un proveedor de identidad especificada para establecer la identidad y obtener acceso a la aplicación de servicio.
- Use una sola línea de código para que el cliente de aplicaciones Mobile SDK puede iniciar sesión en los usuarios.

>[AZURE.TIP] La mayoría de las aplicaciones deben usar un proveedor de SDK para obtener una experiencia más coherente cuando los usuarios inicie sesión, utilizar la compatibilidad de actualización y obtener otras ventajas que especifica el proveedor de servicios.

Cuando se utiliza un proveedor de SDK, los usuarios pueden iniciar sesión a una experiencia que se integra más estrechamente con el sistema operativo que se está ejecutando la aplicación. Esto también le da un token de proveedor y la información de usuario en el cliente, lo que facilita mucho consumir graph API y personalizar la experiencia del usuario. En ocasiones en foros y blogs, verá esto se denomina "flujo de cliente" o "cliente dirige flujo" porque el código en el cliente de firma en los usuarios y el código de cliente tiene acceso a un símbolo de proveedor.

Después de obtener un token del proveedor, debe enviarse a la aplicación de servicio de validación. Después de aplicación de servicio valida el token, servicio de aplicación crea un nuevo token de servicio de la aplicación que se devuelve al cliente. El cliente de aplicaciones Mobile SDK tiene métodos auxiliares para administrar este cambio y vincular automáticamente el token a todas las solicitudes de la aplicación de back-end. Los desarrolladores también pueden mantener una referencia al token del proveedor, si así lo desean.

### <a name="mobile-authentication-without-a-provider-sdk"></a>Autenticación móvil sin un proveedor de SDK

Si no desea configurar un proveedor de SDK, puede permitir la característica Mobile aplicaciones de servicio de la aplicación de Azure para registrarse. El cliente de aplicaciones Mobile SDK abrirá una vista web del proveedor de su elección y el usuario de inicio de sesión. En ocasiones en foros y blogs, verá esto denominados "servidor flujo" o "flujo dirigen servidor" porque el servidor administra el proceso de firma en los usuarios y el cliente de SDK nunca recibe el token de proveedor.

Código para iniciar este flujo se incluye en el tutorial de autenticación para cada plataforma. Al final del flujo de cliente SDK tiene un token de servicio de aplicaciones y el token se adjunta automáticamente a todas las solicitudes de la aplicación de back-end.

### <a name="service-to-service-authentication"></a>Servicio de servicio de autenticación

Aunque puede conceder acceso a los usuarios a la aplicación, también puede confiar en otra aplicación para llamar a su propia API. Por ejemplo, podría tener una aplicación web llamar a una API en otra aplicación web. En este escenario, use las credenciales para una cuenta de servicio en lugar de las credenciales de usuario para obtener un token. Una cuenta de servicio también conocido como es un *servicio principal* en la terminología de Azure Active Directory y autenticación que utiliza dicha cuenta también conocido como es un escenario de servicio para el servicio.

>[AZURE.IMPORTANT] Puesto que ejecutan aplicaciones móviles en dispositivos cliente, aplicaciones móviles hacer _no_ contar como aplicaciones de confianza y no se debe usar un flujo principal del servicio. En su lugar, deben utilizar un flujo de usuario que se detalla en una versión anterior.

Escenarios de servicio para el servicio, el servicio de aplicación puede proteger la aplicación mediante Azure Active Directory. La aplicación de llamada solo debe proporcionar un símbolo de autorización principal del servicio de Azure Active Directory que se obtiene al proporcionar al cliente de identificador y el cliente secreto de Azure Active Directory. Un ejemplo de este escenario que usa aplicaciones de la API de ASP.NET se explica por el tutorial, [autenticación principal del servicio para las aplicaciones de la API][apia-service].

Si desea usar autenticación de servicio de la aplicación para controlar un escenario de servicio de servicio, puede usar autenticación básica o los certificados de cliente. Para obtener información acerca de los certificados de cliente en Azure, consulte [Cómo configurar TLS mutuo autenticación para las aplicaciones Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Para obtener información acerca de la autenticación básica en ASP.NET, consulte [Filtros de autenticación en ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

Autenticación de cuentas de servicio desde una aplicación de la lógica de servicio de aplicación a una aplicación de API es un caso especial que se detalla en [usando la API personalizada hospedada en servicio de la aplicación con aplicaciones de lógica](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="authorization"></a>Cómo funciona la autorización en la aplicación de servicio

Tiene control total sobre las solicitudes que puede tener acceso a la aplicación. Aplicación servicio de autenticación / autorización puede configurarse con cualquiera de los comportamientos siguientes:

- Permitir solo las solicitudes autenticadas llegar a la aplicación.

    Si un explorador recibe una solicitud anónima, servicio de la aplicación le redirigirá a una página para el proveedor de identidades que elija para que los usuarios puedan iniciar sesión. Si la solicitud proviene de un dispositivo móvil, la respuesta devuelta es una respuesta HTTP _401 no autorizado_ .

    Con esta opción, no es necesario escribir cualquier código de autenticación en su aplicación. Si necesita más preciso autorización, información sobre el usuario está disponible para su código.

- Permitir que todas las solicitudes llegar a la aplicación, pero validar solicitudes autenticadas y pasar a lo largo de la información de autenticación en los encabezados HTTP.

    Esta opción difiere decisiones de autorización para el código de la aplicación. Proporciona más flexibilidad en la administración de solicitudes de anónimas, pero tiene que escribir código.

- Permitir que todas las solicitudes llegar a la aplicación y realizar ninguna acción en la información de autenticación de las solicitudes.

    En este caso, la autenticación / autorización característica está desactivada. Las tareas de autenticación y la autorización son totalmente hasta el código de la aplicación.

La opción de **acción que se ejecuta cuando no se autentica la solicitud** en el portal de Azure controla los comportamientos anterior. Si elige * *iniciar sesión en *el nombre del proveedor* **, todas las solicitudes deben autenticarse.** Permitir solicitud (ninguna acción) ** difiere la decisión de autorización de su código, pero proporciona información de autenticación. Si desea hacer su código controle todo, puede deshabilitar la autenticación o característica de autorización.

## <a name="working-with-user-identities-in-your-application"></a>Trabajar con las identidades de usuario en la aplicación

Aplicación de servicio pasa incluye información de usuario a la aplicación con encabezados de especiales. Las solicitudes externas prohíben estos encabezados y solo se if presentar establecerá por la aplicación de servicio de autenticación o autorización. Algunos encabezados de ejemplo se incluyen:

* X MS CLIENTE PRINCIPAL DE NOMBRE
* X MS CLIENTE CAPITAL ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

Código escrito en cualquier idioma o un marco de trabajo puede obtener la información que necesita de estos encabezados. Para las aplicaciones de ASP.NET 4.6, el **ClaimsPrincipal** se establece automáticamente con los valores correspondientes.

La aplicación también puede obtener los detalles de usuario adicionales a través de un HTTP GET en el `/.auth/me` punto final de la aplicación. Un token válido que se incluye con la solicitud devolverá una carga JSON con detalles sobre el proveedor que se utiliza, el token de proveedor subyacente y otra información de usuario. El servidor de aplicaciones de Mobile SDK proporciona métodos auxiliares para trabajar con estos datos. Para obtener más información, vea [cómo usar el SDK de Node.js aplicaciones de Azure Mobile](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)y [trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="documentation-and-additional-resources"></a>Documentación y recursos adicionales

### <a name="identity-providers"></a>Proveedores de identidades
Los siguientes tutoriales muestran cómo configurar la aplicación de servicio para usar diferentes proveedores de autenticación:

- [Cómo configurar la aplicación para usar el inicio de sesión de Azure Active Directory][AAD]
- [Cómo configurar la aplicación para usar el inicio de sesión de Facebook][Facebook]
- [Cómo configurar la aplicación para usar el inicio de sesión de Google][Google]
- [Cómo configurar la aplicación para usar el inicio de sesión de Microsoft Account][MSA]
- [Cómo configurar la aplicación para usar el inicio de sesión de Twitter][Twitter]

Si desea usar un sistema de identidades que no sean los proporcionados aquí, también puede usar la [vista previa de soporte técnico de autenticación personalizada en el servidor de Mobile aplicaciones .NET SDK][custom-auth], que puede usarse en aplicaciones web, aplicaciones móviles o aplicaciones de la API.

### <a name="web-applications"></a>Aplicaciones Web
Los siguientes tutoriales muestran cómo agregar autenticación para una aplicación web:

- [Introducción a la aplicación de servicio de Azure - parte 2][web-getstarted]

### <a name="mobile-applications"></a>Aplicaciones móviles
Los siguientes tutoriales muestran cómo agregar autenticación a los clientes móviles mediante el flujo indicado por el servidor:

- [Agregar autenticación para su aplicación iOS][iOS]
- [Agregar autenticación para su aplicación Android] [Android]
- [Agregar autenticación para su aplicación de Windows] [Windows]
- [Autenticación de agregar a su aplicación Xamarin.iOS] [Xamarin.iOS]
- [Autenticación de agregar a su aplicación Xamarin.Android] [Xamarin.Android]
- [Autenticación de agregar a su aplicación Xamarin.Forms] [Xamarin.Forms]
- [Agregar autenticación para su aplicación Cordova] [Cordova]

Si desea usar el flujo de cliente dirige de Azure Active Directory, use los siguientes recursos:

- [Usar la biblioteca de autenticación de Active Directory para iOS][ADAL-iOS]
- [Usar la biblioteca de autenticación de Active Directory para Android][ADAL-Android]
- [Usar la biblioteca de autenticación de Active Directory para Windows y Xamarin][ADAL-dotnet]

Si desea usar el flujo de cliente dirige de Facebook, use los siguientes recursos:

- [Usar el SDK de Facebook para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Si desea usar el flujo de cliente dirige para Twitter, use los siguientes recursos:

- [Usar a Twitter tela para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Si desea usar el flujo de cliente dirige para Google, use los siguientes recursos:

- [Usar el SDK de inicio de sesión de Google para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

### <a name="api-applications"></a>Aplicaciones de la API
Los siguientes tutoriales muestran cómo proteger sus aplicaciones de la API:

- [Autenticación de usuario para las aplicaciones de la API de servicio de la aplicación de Azure][apia-user]
- [Autenticación principal del servicio para las aplicaciones de la API de servicio de la aplicación de Azure][apia-service]









[apia-user]: ../app-service-api/app-service-api-dotnet-user-principal-auth.md
[apia-service]: ../app-service-api/app-service-api-dotnet-service-principal-auth.md

[web-getstarted]: ../app-service-web/app-service-web-get-started-2.md#authenticate-your-users

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: ../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: ../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md
[MSA]: ../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: ../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
