<properties 
    pageTitle="Conceptos básicos de administración de la API" 
    description="Obtenga información sobre las API, productos, roles, grupos y otros conceptos clave de administración de la API." 
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
    ms.topic="hero-article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

#<a name="what-is-api-management"></a>¿Qué es la administración de la API?

Administración de la API de ayuda a las organizaciones a publicar API externos, socios y desarrolladores internos para desbloquear el potencial de sus datos y servicios. Empresas en todas partes están buscando para ampliar sus operaciones como una plataforma digital, crear nuevos canales, encontrar a nuevos clientes y conducción más profundo compromiso con las existentes. Administración de la API proporciona las capacidades principales para garantizar que un programa de API a través de compromiso de desarrollador, perspectivas empresariales, análisis, seguridad y protección.

Vea el siguiente vídeo para obtener información general de administración de la API de Azure y aprender a usar la administración de API para agregar características a la API, incluido el control de acceso, limitación de velocidad, supervisión, registro de eventos y almacenamiento en caché de respuesta, con el mínimo esfuerzo por su parte.

> [AZURE.VIDEO azure-api-management-overview]

Para usar la API de administración, los administradores crear API. Cada API consta de una o más operaciones y cada API puede agregarse a uno o varios productos. Para usar una API, los desarrolladores suscripción a un producto que contiene dicha API y, a continuación, puede llamar a la operación de la API, sujeto a las directivas de uso que puedan estar vigentes.

Este tema proporciona una descripción general de los conceptos básicos de administración de la API.

>[AZURE.NOTE] Para obtener más información, consulte la [administración de API basada en nube: aprovechar la potencia de las API de](http://j.mp/ms-apim-whitepaper) informe detallado PDF. En este artículo sobre la administración de la API por CITO Research introductorio cubre: 
>
> - Desafíos y requisitos API comunes
>     - API de disociación y presentar fachadas
>     - Obtener los programadores y ejecutar rápidamente
>     - Proteger el acceso
>     - Análisis y mediciones
> - Obtener control e información con una plataforma de administración de la API
> - Uso de soluciones de nube vs local
> - Administración de la API de Azure

## <a name="apis"> </a>API y operaciones

API son la base de una instancia de servicio de administración de la API. Cada API representa un conjunto de operaciones disponibles para los desarrolladores. Cada API contiene una referencia al servicio back-end que implementa la API y su mapa de operaciones a las operaciones de implementada por el servicio de back-end. Operaciones de administración de la API son altamente configurables, controlando asignación URL, consulta y los parámetros de ruta de acceso, solicitud y contenido de la respuesta y almacenamiento en caché de respuesta de operación. Límite de velocidad, cuotas y las directivas de restricción de IP también se implementa en el nivel de operación individual o API.

Para obtener más información, vea [cómo crear API][] y [cómo agregar acciones a una API][].


## <a name="products"></a> Productos

Los productos son cómo aparecen las API para los programadores. Productos de administración de la API de tienen uno o más API y estén configurados con un título, descripción y condiciones de uso. Productos pueden estar **abierto** o **protegida**. Productos protegidos deben estar suscrito a antes de que se pueden usar, mientras esté abierto productos pueden utilizarse sin una suscripción. Cuando esté listo para usar los desarrolladores de un producto que se pueda publicar. Una vez publicado, se puede ver (y en el caso de los productos protegidos suscrito a) por los desarrolladores. Aprobación de la suscripción está configurado en el nivel de producto y puede requerir aprobación del administrador o ser aprueba automáticamente.

Los grupos se utilizan para administrar la visibilidad de productos para los desarrolladores. Productos conceden visibilidad a grupos y los desarrolladores pueden ver y suscribirse a los productos que son visibles para los grupos en la que pertenecen. 

Para obtener más información, vea [cómo crear y publicar un producto][] y el siguiente vídeo.

> [AZURE.VIDEO using-products]

## <a name="groups"></a> Grupos

Los grupos se utilizan para administrar la visibilidad de productos para los desarrolladores. Administración de la API tiene los siguientes grupos de sistema inmutable.

-   **Administradores** : los administradores de Azure suscripción son los miembros de este grupo. Los administradores administrar instancias del servicio de administración de la API, creación de la API, operaciones y productos de programadores.
-   **Los desarrolladores** - portal de programadores autenticado de los usuarios se dividen en este grupo. Los desarrolladores son los clientes que generar aplicaciones con la API. Los desarrolladores con acceso al portal de programadores y generación aplicaciones que llamen a las operaciones de una API.
-   **Invitados** - usuarios de portal de desarrollador no autenticados, como visitar el portal de programadores de una instancia de administración de la API de clientes potenciales se incluyen en este grupo. Puede tener concedidos determinado acceso de solo lectura, como la capacidad de ver las API pero no llamar a ellos.

Además de los grupos de sistema, los administradores pueden crear grupos personalizados o [aprovechar grupos externos de inquilinos de Azure Active Directory asociados](api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group). Personalizar y grupos externos pueden utilizarse junto con los grupos de sistema en el que le da visibilidad de programadores y acceso a los productos de la API. Por ejemplo, podría crear un grupo personalizado para desarrolladores asociados con una organización socio específico y les permiten el acceso a las API de un producto que contiene solo las API relevantes. Un usuario puede ser un miembro de más de un grupo.

Para obtener más información, vea [cómo crear y usar grupos][].

## <a name="developers"></a> Los desarrolladores

Los desarrolladores representan las cuentas de usuario en una instancia de servicio de administración de la API. Los desarrolladores pueden crearse o invitados a unirse a los administradores, o puede registrarse desde el [portal de programadores][]. Cada desarrollador es miembro de uno o varios grupos y puede suscribirse a los productos que conceden visibilidad a dichos grupos.

Cuando los desarrolladores suscripción a un producto se les concede la clave principal y secundaria para el producto. Esta clave se utiliza para realizar llamadas a las API de productos.

Para obtener más información, vea [cómo crear o invitar a los desarrolladores][] y [cómo asociar los grupos con los desarrolladores][].

## <a name="policies"></a> Directivas

Las directivas son una excelente capacidad de administración de API que permiten la publisher cambiar el comportamiento de la API mediante la configuración. Las directivas son un conjunto de instrucciones que se ejecutan secuencialmente en la solicitud o respuesta de una API. Instrucciones populares incluyen conversiones del formato XML a JSON y limitación de velocidad de llamada para restringir la cantidad de llamadas entrantes de un desarrollador y muchas otras directivas están disponibles.

Las expresiones de directiva se pueden utilizar como valores de atributo o valores de texto en cualquiera de las directivas de administración de la API, a menos que la directiva especifica lo contrario. Algunas directivas, como las directivas de [flujo de Control](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose) y [establezca variable](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable) se basan en las expresiones de directiva. Para obtener más información, vea [Directivas avanzadas](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies), [las expresiones de directiva](https://msdn.microsoft.com/library/azure/dn910913.aspx)y vea el siguiente vídeo.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

Para obtener una lista completa de directivas de administración de la API, vea [referencia de la directiva][]. Para obtener más información sobre el uso y la configuración de directivas, consulte [directivas de administración de la API][]. Para obtener un tutorial sobre la creación de un producto con tasa límite y cuota de directivas, vea [cómo crear y configurar las opciones avanzadas del producto][]. Para una demostración, vea el siguiente vídeo.

> [AZURE.VIDEO rate-limits-and-quotas]

## <a name="developer-portal"></a> Portal para desarrolladores

El portal de programadores es donde pueden obtener información acerca de las operaciones de llamada, la vista y las API de los desarrolladores y suscribirse a productos. Los clientes potenciales pueden visitar el portal de programadores ver API y operaciones y registrarse. La dirección URL de su portal de programadores se encuentra en el panel en el Portal de Azure clásica de la instancia de servicio de administración de la API.

Puede personalizar la apariencia de su portal de programadores agregando contenido personalizado, personalización de estilos y agregar su marca.

## <a name="api-management-and-the-api-economy"></a>Administración de la API y la economía API

Para obtener más información acerca de la administración de la API, vea la siguiente presentación de la conferencia de Microsoft Ignite 2015.

> [AZURE.VIDEO microsoft-ignite-2015-azure-api-management-and-the-api-economy]

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Portal de programadores]: #developer-portal

[Cómo crear API]: api-management-howto-create-apis.md
[Cómo agregar acciones a una API]: api-management-howto-add-operations.md
[Cómo crear y publicar un producto]: api-management-howto-add-products.md
[Cómo crear y usar grupos]: api-management-howto-create-groups.md
[Cómo asociar los grupos a los desarrolladores]: api-management-howto-create-groups.md#associate-group-developer
[Cómo crear y configurar las opciones avanzadas del producto]: api-management-howto-product-with-rules.md
[Cómo crear o invitar a los desarrolladores]: api-management-howto-create-or-invite-developers.md
[Referencia de directiva]: api-management-policy-reference.md
[Directivas de administración de la API]: api-management-howto-policies.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance



 
