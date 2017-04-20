<properties
  pageTitle="Cliente y servidor SDK control de versiones en aplicaciones móviles y servicios móviles | Servicio de aplicaciones de Azure"
  description="Lista de cliente SDK y compatibilidad con versiones SDK de servidor de servicios móviles y aplicaciones móviles de Azure"
  services="app-service\mobile"
  documentationCenter=""
  authors="adrianhall"
  manager="erikre"
  editor=""/>

<tags
  ms.service="app-service-mobile"
  ms.workload="mobile"
  ms.tgt_pltfrm="mobile-multiple"
  ms.devlang="dotnet"
  ms.topic="article"
  ms.date="10/01/2016"
  ms.author="adrianha"/>

# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Control de versiones de cliente y de servidor en aplicaciones móviles y servicios para dispositivos móviles

La última versión de servicios móviles de Azure es la característica de **Aplicaciones móviles** de servicio de aplicaciones de Azure.

El SDK de cliente y el servidor de aplicaciones móviles originalmente se basa en los de servicios móviles, pero son *no* es compatible con ellos.
Es decir, debe usar a un cliente *Móvil aplicaciones* SDK con un servidor de *Aplicaciones de Mobile* SDK y del mismo modo para *Servicios móviles*. Este contrato se aplica a través de un valor de encabezado especial utilizado por el cliente y el servidor SDK, `ZUMO-API-VERSION`.

Nota: cada vez que este documento hace referencia a un servidor de *Servicios móviles* , no es necesariamente necesario para insertarse en servicios para dispositivos móviles. Ahora es posible migrar un servicio móvil para que se ejecute en la aplicación de servicio sin realizar cambios de código, pero el servicio aún haría con versiones SDK de *Servicios móviles* .

Para obtener más información sobre cómo migrar a la aplicación de servicio sin realizar cambios de código, vea el artículo [migrar un servicio móvil al servicio de la aplicación de Azure].

## <a name="header-specification"></a>Especificación de encabezado

La clave `ZUMO-API-VERSION` pueden especificar en el encabezado HTTP o la cadena de consulta. El valor es una cadena de versión en el formulario **x.y.z**.

Por ejemplo:

OBTENER https://service.azurewebsites.net/tables/TodoItem

ENCABEZADOS: VERSIÓN DE API ZUMO: 2.0.0

PUBLICAR https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Optar por no comprobación de la versión

Puede optar por versión comprobación estableciendo un valor de **true** para la aplicación de configuración **MS_SkipVersionCheck**. Especificar esto en el archivo web.config o en la sección Configuración de la aplicación del portal de Azure.

> [AZURE.NOTE] Hay una serie de cambios de comportamiento entre servicios móviles y aplicaciones móviles, especialmente en las áreas de sincronización sin conexión, la autenticación y las notificaciones de inserción. Solo debe optar por versión comprobación después de probar completo para garantizar que estos cambios de comportamiento no romper la funcionalidad de la aplicación.

## <a name="summary-of-compatibility-for-all-versions"></a>Resumen de compatibilidad para todas las versiones

La siguiente tabla muestra la compatibilidad entre todos los tipos de cliente y el servidor. Back-end se clasifica según los **Servicios** móviles o móviles **aplicaciones** en el servidor de SDK que utiliza.

|                           | **Servicios de móvil** Node.js o .NET | **Aplicaciones móviles** Node.js o .NET |
| ----------                | -----------------------             |   ----------------              |
| [Clientes de servicios móviles] | Vale                                  | Error\*                         |
| [Clientes de aplicaciones móviles]     | Error\*                             | Vale                              |

\*Esto se puede controlar especificando **MS_SkipVersionCheck**.


<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Servidor y cliente de servicios móviles

El SDK de cliente en la tabla siguiente son compatibles con **Servicios móviles**.

Nota: el cliente de servicios móviles SDK *no* enviar un valor de encabezado `ZUMO-API-VERSION`. Si el servicio recibe este encabezado o el valor de cadena de consulta, se devuelve un error a menos que ha optado explícitamente alejar como se describió anteriormente.

### <a name="MobileServicesClients"></a>Cliente de *Servicios* móvil SDK

| Plataforma de cliente                   | Versión                                                                   | Valor de encabezado de versión |
| -------------------               | ------------------------                                                  | -------------------  |
| Cliente administrado (Windows, Xamarin) | [1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) | n/a.                  |
| iOS                               | [2.2.2](http://aka.ms/gc6fex)                                             | n/a.                  |
| Android                           | [2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126)                   | n/a.                  |
| HTML                              | [1.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) | n/a.     |

### <a name="mobile-services-server-sdks"></a>Servidor de *Servicios* móvil SDK

| Plataforma de servidor  | Versión                                                                                                        | Encabezado de versión aceptadas |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [WindowsAzure.MobileServices.Backend.* versión 1.0. x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) | **Sin encabezado de versión** |
| Node.js          | (próximamente)                        | **Sin encabezado de versión** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Comportamiento de servicios móviles back-ends

| VERSIÓN DE API DE ZUMO | Valor de MS_SkipVersionCheck | Respuesta |
| ---------------- | ---------------------------- | -------- |
| No especificado    | Cualquier                          | 200 - Aceptar |
| Cualquier valor.        | True                         | 200 - Aceptar |
| Cualquier valor.        | Falso y no especificado          | 400 - Solicitud incorrecta |

## <a name="2.0.0"></a>Azure Mobile aplicaciones cliente y del servidor

### <a name="MobileAppsClients"></a>Clientes de *aplicaciones* móviles SDK

Comprobación de la versión se introdujo empezando por las siguientes versiones del cliente SDK para **Aplicaciones de Azure Mobile**:

| Plataforma de cliente                   | Versión                   | Valor de encabezado de versión |
| -------------------               | ------------------------  | -----------------    |
| Cliente administrado (Windows, Xamarin) | [2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) | 2.0.0 |
| iOS                               | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) | 2.0.0  |
| Android                           | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) | 3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>Servidor de *aplicaciones* móvil SDK

Comprobación de la versión se incluye en los siguientes versiones SDK de servidor:

| Plataforma de servidor  | SDK                                                                                                        | Encabezado de versión aceptadas |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) | 2.0.0 |
| Node.js          | [Azure aplicaciones mobile)](https://www.npmjs.com/package/azure-mobile-apps)                         | 2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Comportamiento de back-ends de aplicaciones móviles

| VERSIÓN DE API DE ZUMO | Valor de MS_SkipVersionCheck | Respuesta |
| ---------------- | ---------------------------- | -------- |
| x.y.z o Null    | True                         | 200 - Aceptar |
| Null             | Falso y no especificado          | 400 - Solicitud incorrecta |
| 1.x.y            | Falso y no especificado          | 400 - Solicitud incorrecta |
| 2.0.0-2.x.y      | Falso y no especificado          | 200 - Aceptar |
| 3.0.0-3.x.y      | Falso y no especificado          | 400 - Solicitud incorrecta |


## <a name="next-steps"></a>Pasos siguientes

- [Migrar un servicio móvil al servicio de aplicación de Azure]


[Clientes de servicios móviles]: #MobileServicesClients
[Clientes de aplicaciones móviles]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Migrar un servicio móvil al servicio de aplicación de Azure]: app-service-mobile-migrating-from-mobile-services.md

