<properties
    pageTitle="Autenticación y la autorización en las aplicaciones móviles Azure | Microsoft Azure"
    description="Referencia conceptual e información general sobre la autenticación / autorización de características para las aplicaciones móviles de Azure"
    services="app-service\mobile"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="authentication-and-authorization-in-azure-mobile-apps"></a>Autenticación y la autorización en las aplicaciones móviles de Azure

## <a name="what-is-app-service-authentication--authorization"></a>¿Qué es la aplicación de servicio de autenticación o autorización?

> [AZURE.NOTE] En este tema se migrarán a una consolidación [aplicación de servicio de autenticación o autorización](../app-service/app-service-authentication-overview.md) tema, que abarca la Web, móvil y aplicaciones de la API.

Aplicación servicio de autenticación / autorización es una característica que permite a su aplicación iniciar sesión en los usuarios sin cambios de código necesarios en la aplicación de back-end. Proporciona una forma sencilla de proteger la aplicación y trabajar con datos de cada usuario.

Aplicación de servicio usa identidad federada, en la que un 3 º **proveedor de identidades** ("IDP") almacena las cuentas y autentica a los usuarios y la aplicación usa esta identidad en lugar de su propio. Aplicación de servicio compatible con cinco proveedores de identidades fuera de la caja: _Azure Active Directory_, _Facebook_, _Google_, _Cuenta de Microsoft_y _Twitter_. También puede expandir esta compatibilidad para las aplicaciones mediante la integración de otro proveedor de identidades o su propia solución identidad personalizada.

La aplicación puede aprovechar cualquier número de estos proveedores de identidades, para que puedan proporcionar los usuarios finales con opciones para cómo iniciar sesión.

Si desea comenzar inmediatamente, vea uno de los siguientes tutoriales:

- [Agregar autenticación para su aplicación iOS]
- [Agregar autenticación para su aplicación Xamarin.iOS]
- [Agregar autenticación para su aplicación Xamarin.Android]
- [Agregar autenticación para su aplicación de Windows]

## <a name="how-authentication-works"></a>Cómo funciona la autenticación

Para autenticar mediante uno de los proveedores de identidades, debe configurar el proveedor de identidades saber sobre la aplicación. El proveedor de identidades, a continuación, le proporcionará identificadores y la información confidencial que proporciona a la aplicación. Esto completa la relación de confianza y permite la aplicación de servicio validar las identidades que se le proporcionadas.

Estos pasos se detallan en los temas siguientes:

- [Cómo configurar la aplicación para usar el inicio de sesión de Azure Active Directory]
- [Cómo configurar la aplicación para usar el inicio de sesión de Facebook]
- [Cómo configurar la aplicación para usar el inicio de sesión de Google]
- [Cómo configurar la aplicación para usar el inicio de sesión de Microsoft Account]
- [Cómo configurar la aplicación para usar el inicio de sesión de Twitter]

Una vez que todo está configurado en el servidor, puede modificar el cliente para iniciar sesión. Existen dos métodos aquí:

- Con una sola línea de código, permitir que las aplicaciones móviles SDK de cliente de inicio de sesión los usuarios.
- Aproveche un SDK publicado por un proveedor de identidad especificada para establecer identidades y obtener acceso a la aplicación de servicio.

>[AZURE.TIP] La mayoría de las aplicaciones deben usar un proveedor de SDK para obtener una experiencia de inicio de sesión de más de nativo sentimiento y aprovechar la compatibilidad de actualización y otras ventajas de específicas del proveedor.

### <a name="how-authentication-without-a-provider-sdk-works"></a>Cómo funciona la autenticación sin un proveedor de SDK

Si no desea configurar un proveedor de SDK, puede permitir aplicaciones móviles realizar el inicio de sesión. El cliente de aplicaciones Mobile SDK abrirá una vista web del proveedor de su elección y completar el inicio de sesión. En ocasiones en blogs y foros, verá esto conoce como "servidor flujo" o "flujo dirigen servidor" desde el servidor administra el inicio de sesión y el cliente de SDK nunca recibe el token de proveedor.

El código necesario para iniciar este flujo se trata en el tutorial de autenticación para cada plataforma. Al final del flujo de cliente SDK tiene un token de servicio de aplicaciones y el token se adjunta automáticamente a todas las solicitudes del back-end.

### <a name="how-authentication-with-a-provider-sdk-works"></a>Cómo funciona la autenticación con un proveedor de SDK

Trabajar con un proveedor SDK permite la experiencia de registro interactuar más estrechamente con la plataforma de sistema operativo, en que la aplicación se está ejecutando. Esto también le da un token de proveedor y la información de usuario en el cliente, lo que facilita mucho consumir graph API y personalizar la experiencia del usuario. En ocasiones en foros y blogs verá esto se denomina "flujo de cliente" o "flujo cliente dirige" desde el código en el cliente está controlando el inicio de sesión y el código de cliente tiene acceso a un símbolo de proveedor.

Una vez que se obtiene un token del proveedor, debe enviarse a la aplicación de servicio de validación. Al final del flujo de cliente SDK tiene un token de servicio de aplicaciones y el token se adjunta automáticamente a todas las solicitudes del back-end. El desarrollador también puede mantener una referencia al token del proveedor, si así lo desean.

## <a name="how-authorization-works"></a>Cómo funciona la autorización

Aplicación servicio de autenticación / autorización expone varias opciones para la **acción que se ejecuta cuando no se autentica la solicitud**. Antes de que el código recibe una solicitud determinada, puede tener comprobación del servicio de aplicación para ver si se autentica la solicitud y si no es así, rechazarla e intentar para que el usuario inicie sesión antes de intentarlo.

Una opción es ha no autenticado redirigir solicitudes a uno de los proveedores de identidades. En un explorador web, tardarían realmente el usuario a una página nueva. Sin embargo, no se puede redirigir los clientes móviles de este modo y respuestas no autenticadas recibirán una respuesta HTTP _401 no autorizado_ . Dado esto, la primera solicitud que hace que el cliente debe ser siempre al extremo de inicio de sesión y, a continuación, puede realizar llamadas a cualquier otras API. Si intenta llamar a otra API antes de iniciar sesión, el cliente recibirá un error.

Si desea tener más granulares control sobre qué extremos requerir autenticación, también puede seleccionar "ninguna acción (Permitir solicitud)" para las solicitudes no autenticadas. En este caso, se aplazan todas las decisiones de autenticación para el código de la aplicación. Esto también permite permitir el acceso a usuarios específicos en función de las reglas de autorización personalizada.

## <a name="documentation"></a>Documentación

Los siguientes tutoriales muestran cómo agregar autenticación a los clientes móviles mediante la aplicación de servicio:

- [Agregar autenticación para su aplicación iOS]
- [Agregar autenticación para su aplicación Xamarin.iOS]
- [Agregar autenticación para su aplicación Xamarin.Android]
- [Agregar autenticación para su aplicación de Windows]

Los siguientes tutoriales muestran cómo configurar la aplicación de servicio para aprovechar los diferentes proveedores de autenticación:

- [Cómo configurar la aplicación para usar el inicio de sesión de Azure Active Directory]
- [Cómo configurar la aplicación para usar el inicio de sesión de Facebook]
- [Cómo configurar la aplicación para usar el inicio de sesión de Google]
- [Cómo configurar la aplicación para usar el inicio de sesión de Microsoft Account]
- [Cómo configurar la aplicación para usar el inicio de sesión de Twitter]

Si desea usar un sistema de identidades que no sean los proporcionados aquí, también puede aprovechar la [vista previa de soporte técnico de autenticación personalizada en el SDK de .NET server](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth).

[Agregar autenticación para su aplicación iOS]: app-service-mobile-ios-get-started-users.md
[Agregar autenticación para su aplicación Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started-users.md
[Agregar autenticación para su aplicación Xamarin.Android]: app-service-mobile-xamarin-android-get-started-users.md
[Agregar autenticación para su aplicación de Windows]: app-service-mobile-windows-store-dotnet-get-started-users.md

[Cómo configurar la aplicación para usar el inicio de sesión de Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Cómo configurar la aplicación para usar el inicio de sesión de Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Cómo configurar la aplicación para usar el inicio de sesión de Google]: app-service-mobile-how-to-configure-google-authentication.md
[Cómo configurar la aplicación para usar el inicio de sesión de Microsoft Account]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Cómo configurar la aplicación para usar el inicio de sesión de Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md
