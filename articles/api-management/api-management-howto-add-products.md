<properties 
    pageTitle="Cómo crear y publicar un producto de administración de la API de Azure" 
    description="Aprenda a crear y publicar los productos de administración de la API de Azure." 
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

# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>Cómo crear y publicar un producto de administración de la API de Azure

En administración de la API de Azure, un producto contiene las API de uno o más, así como una cuota de uso y los términos de uso. Una vez que se publica un producto, los desarrolladores pueden suscribirse al producto y empezar a usar las API de productos. El tema proporciona a una guía para crear un producto, agregar una API y publicar para desarrolladores.

## <a name="create-product"> </a>Crear un producto

Las operaciones se agregan y configuradas para una API en el portal de publisher. Para acceder al portal de publisher, haga clic en **Administrar** en el Portal de Azure clásico de su servicio de administración de la API.

![Portal de Publisher][api-management-management-console]

>Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia de servicio de administración de la API][] en el tutorial de [Introducción a la administración de la API de Azure][] .

Haga clic en **productos** en el menú de la izquierda para mostrar la página de **productos** y haga clic en **Agregar producto**.

![Productos][api-management-products]

![Nuevo producto][api-management-add-new-product]

En el campo **nombre** y una descripción del producto en el campo **Descripción** , escriba un nombre descriptivo para el producto.

Los productos de administración de la API pueden estar **abierto** o **protegida**. Productos protegidos deben estar suscrito a antes de que se pueden usar, mientras esté abierto productos pueden utilizarse sin una suscripción. Marque la opción **Requerir suscripción** para crear un producto protegido que requiere una suscripción. Esta es la configuración predeterminada.

Si desea que un administrador para revisar y Aceptar o rechazar intentos de suscripción a este producto, consulte **requerir la aprobación de suscripción** . Si la casilla está desactivada, los intentos de suscripción será aprueba automáticamente. Para obtener más información acerca de las suscripciones, consulte [suscriptores de vista a un producto][].

Para permitir que cuentas de desarrollador suscribirse varias veces para el producto, active la casilla de verificación **Permitir varias suscripciones** . Si esta casilla no está activada, cada cuenta de desarrollador puede suscribirse solo una sola vez con el producto.

![Ilimitado varias suscripciones][api-management-unlimited-multiple-subscriptions]

Para limitar el número de varias suscripciones simultáneas, active la casilla de verificación **limitar el número de suscripciones simultáneas a** y escriba el límite de suscripción. En el ejemplo siguiente, suscripciones simultáneas están limitadas de cuatro por cuenta del programador.

![Cuatro varias suscripciones][api-management-four-multiple-subscriptions]

Una vez que se haya configurado todas las nuevas opciones de producto, haga clic en **Guardar** para crear el nuevo producto.

![Productos][api-management-products-page]

>De forma predeterminada nuevos productos están publicados y son visibles solo para el grupo de **administradores** .

Para configurar un producto, haga clic en el nombre de producto en la pestaña **productos** .

## <a name="add-apis"> </a>Las API de agregar a un producto

La página de **productos** contiene cuatro vínculos de configuración: **Resumen**, **configuración**, **visibilidad**y **suscriptores**. La ficha **Resumen** es donde puede agregar las API y publicar o anular la publicación de un producto.

![Resumen][api-management-new-product-summary]

Antes de publicar su producto debe agregar las API de uno o más. Para ello, haga clic en **Agregar API al producto**.

![Agregar API][api-management-add-apis-to-product]

Seleccione las API que desee y haga clic en **Guardar**.

## <a name="add-description"> </a>Agregue información descriptiva a un producto

La pestaña **configuración** permite proporcionar información detallada sobre el producto, como su propósito, las API que proporciona acceso a y otra información de utilidad. El contenido está dirigido a los desarrolladores que se pueden llamar a la API y se pueden escribir en texto sin formato o en formato HTML.

![Configuración del producto][api-management-product-settings]

Marque la opción **Requerir suscripción** para crear un producto protegido que requiere una suscripción que se utilizarán o desactive la casilla de verificación para crear un producto abierto que se puede llamar sin una suscripción.

Si desea aprobar manualmente todas las solicitudes de suscripción de producto, seleccione **requerir la aprobación de suscripción** . De forma predeterminada todas las suscripciones de producto se le concede automáticamente.

Para permitir que cuentas de desarrollador suscribirse varias veces para el producto, active la casilla de verificación **Permitir varias suscripciones** y, opcionalmente, especificar un límite. Si esta casilla no está activada, cada cuenta de desarrollador puede suscribirse solo una sola vez con el producto.

Opcionalmente, rellenar el campo de **condiciones de uso** que describe los términos de uso del producto que suscriptores deben Aceptar para poder usar el producto.

## <a name="publish-product"> </a>Publicar un producto

Para pueden llamar a las API de un producto, se debe publicar el producto. En la pestaña **Resumen** del producto, haga clic en **Publicar**y, a continuación, haga clic en **Sí, publicarlo** para confirmar. Para hacer privado un producto publicado anteriormente, haga clic en **Quitar**.

![Publicar el producto][api-management-publish-product]

## <a name="make-visible"> </a>Que un producto esté visible para los desarrolladores

La ficha **visibilidad** le permite elegir qué funciones se puede ver el producto en el portal de programadores y suscribirse al producto.

![Visibilidad del producto][api-management-product-visiblity]

Para activar o desactivar la visibilidad de un producto para los desarrolladores de un grupo, active o desactive la casilla de verificación junto al grupo y, a continuación, haga clic en **Guardar**.

>Para obtener más información, vea [cómo crear y usar grupos para administrar las cuentas de programador en la administración de la API de Azure][].

## <a name="view-subscribers"> </a>Ver los suscriptores a un producto

La ficha **suscriptores** enumera los desarrolladores que se han suscrito al producto. Los detalles y configuración para todos los desarrolladores se pueden visualizar haciendo clic en el nombre del desarrollador. En este ejemplo no programadores aún suscrito con el producto.

![Desarrolladores][api-management-developer-list]

## <a name="next-steps"> </a>Pasos siguientes

Una vez que se agregan las API deseadas y el producto publicado, los desarrolladores pueden suscribirse al producto y empiece a llamar a la API. Para obtener un tutorial que muestra estos elementos, así como la configuración avanzada de producto, vea [cómo crear y configurar las opciones avanzadas del producto en la administración de la API de Azure][].

Para obtener más información sobre cómo trabajar con productos, vea el siguiente vídeo.

> [AZURE.VIDEO using-products]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[Suscriptores de la vista a un producto]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Introducción a la administración de la API de Azure]: api-management-get-started.md
[Crear una instancia de servicio de administración de la API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[Cómo crear y usar grupos para administrar cuentas de programador en la administración de la API de Azure]: api-management-howto-create-groups.md
[Cómo crear y configurar las opciones de avanzadas del producto de administración de la API de Azure]: api-management-howto-product-with-rules.md 