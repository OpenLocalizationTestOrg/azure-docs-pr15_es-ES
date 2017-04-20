<properties 
    pageTitle="Cómo crear las API de administración de la API de Azure" 
    description="Obtenga información sobre cómo crear y configurar las API de administración de la API de Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-apis-in-azure-api-management"></a>Cómo crear las API de administración de la API de Azure

Una API de administración de la API representa un conjunto de operaciones que se pueden invocar aplicaciones cliente. Nuevas API se crean en el portal de publisher y, a continuación, se agregan las operaciones que desee. Una vez que haya agregado a las operaciones de la API se agrega a un producto y se puede publicar. Una vez que se publica una API, puede suscribirse a y utilizan los desarrolladores.

Esta guía muestra el primer paso del proceso: cómo crear y configurar una nueva API de administración de la API. Para obtener más información sobre cómo agregar operaciones y publicar un producto, vea [cómo agregar acciones a una API][] y [cómo crear y publicar un producto][].

## <a name="create-new-api"> </a>Crear una nueva API

API se crean y se configuran en el portal de publisher. Para acceder al portal de publisher, haga clic en **Administrar** en el Portal de Azure clásico de su servicio de administración de la API.

![Portal de Publisher][api-management-management-console]

>Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia de servicio de administración de la API][] en el tutorial de [Introducción a la administración de la API de Azure][] .

Haga clic en **las API de** desde el menú de **Administración de la API** de la izquierda y, a continuación, haga clic en **Agregar API**.

![Crear API][api-management-create-api]

Utilice la ventana **Agregar nueva API** para configurar la nueva API.

![Agregar nueva API][api-management-add-new-api]

Los siguientes campos se usan para configurar la nueva API.

-   **Nombre de la API de Web** proporciona un nombre descriptivo y único para la API. Se muestra en los portales de desarrollador y publisher.
-   **Dirección URL del servicio Web** hace referencia a los servicios HTTP implementación de la API. Administración de la API reenvía las solicitudes a esta dirección.
-   **Dirección URL de Web API sufijo** se anexa a la dirección URL base para el servicio de administración de la API. La base de la dirección URL es común para todas las API hospedadas por una instancia de servicio de administración de la API. Administración de la API distingue API por su sufijo y, por tanto, el sufijo debe ser único para cada API para un editor determinado.
-   **Esquema de dirección URL de Web API** determina los protocolos que pueden utilizarse para tener acceso a la API. HTTPs se especifica de forma predeterminada.
-   Para agregar opcionalmente esta nueva API a un producto, haga clic en el menú desplegable **productos (opcionales)** y elija un producto. Este paso se puede repetir varias veces para agregar la API a varios productos.

Una vez configurados los valores que desee, haga clic en **Guardar**. Una vez creada la nueva API, se muestra la página de resumen de la API en el portal de publisher.

![Resumen de la API][api-management-api-summary]

## <a name="configure-api-settings"> </a>Configuración configurar API

Puede usar la pestaña **configuración** comprobar y editar la configuración para una API. **Nombre de Web API**, la **dirección URL del servicio Web**y **sufijo de dirección URL de Web API** se establecen inicialmente cuando se crea la API y se puede modificar aquí. **Descripción** proporciona una descripción opcional y **esquema de dirección URL de Web API** determina los protocolos que pueden utilizarse para tener acceso a la API.

![Configuración de la API][api-management-api-settings]

Para configurar la autenticación de puerta de enlace para el servicio de back-end implementación de la API, seleccione la pestaña **seguridad** . **Con credenciales** desplegable puede usarse para configurar la autenticación **básica HTTP** o **certificados de cliente** . Para usar autenticación básica HTTP, simplemente escriba las credenciales que desee. Para obtener información sobre el uso de autenticación de certificado de cliente, vea [cómo proteger los servicios de back-end mediante la autenticación de certificado de cliente en la administración de la API de Azure][].

La pestaña **seguridad** también puede utilizarse para configurar la **autorización del usuario** mediante OAuth 2.0. Para obtener más información, vea [cómo autorizar cuentas programador mediante OAuth 2.0 en administración de la API de Azure][].

![Configuración de autenticación básica][api-management-api-settings-credentials]

Haga clic en **Guardar** para guardar los cambios que realice en la configuración de la API.

## <a name="next-steps"> </a>Pasos siguientes

Una vez que se crea una API y la configuración, los pasos siguientes son para agregar las operaciones a la API, agregue la API a un producto y publicarlo para que esté disponible para los programadores. Para obtener más información, consulte los artículos siguientes.

-   [Cómo agregar acciones a una API][]
-   [Cómo crear y publicar un producto][]





[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[Cómo agregar acciones a una API]: api-management-howto-add-operations.md
[Cómo crear y publicar un producto]: api-management-howto-add-products.md

[Introducción a la administración de la API de Azure]: api-management-get-started.md
[Crear una instancia de servicio de administración de la API]: api-management-get-started.md#create-service-instance
[Cómo proteger los servicios de back-end mediante el cliente de autenticación de certificado de la administración de la API de Azure]: api-management-howto-mutual-certificates.md
[Cómo autorizar cuentas de programador mediante OAuth 2.0 en administración de la API de Azure]: api-management-howto-oauth2.md