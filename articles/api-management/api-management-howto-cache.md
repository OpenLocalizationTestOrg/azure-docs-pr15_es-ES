<properties
    pageTitle="Agregar almacenamiento en caché para mejorar el rendimiento de la administración de la API de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo mejorar la latencia, consumo de ancho de banda y el servicio web de carga para las llamadas de servicio de administración de la API."
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
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Agregar almacenamiento en caché para mejorar el rendimiento de la administración de la API de Azure

Operaciones de administración de la API de se pueden configurar para la caché de respuesta. Almacenamiento en caché de respuesta significativamente puede reducir la latencia de la API, consumo de ancho de banda y web carga del servicio de datos que no cambian a menudo.

Esta guía le muestra cómo agregar almacenamiento en caché de respuesta de la API y configurar las directivas para las operaciones de eco API de ejemplo. A continuación, puede llamar a la operación desde el portal de programadores para comprobar el almacenamiento en caché en acción.

>[AZURE.NOTE] Para obtener información sobre el almacenamiento de los elementos clave con las expresiones de directiva, vea [almacenamiento en caché personalizado en administración de la API de Azure](api-management-sample-cache-by-key.md).

## <a name="prerequisites"></a>Requisitos previos

Antes de seguir los pasos descritos en esta guía, debe tener una instancia de servicio de administración de la API con una API y configurado un producto. Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia de servicio de administración de la API][] en el tutorial de [Introducción a la administración de la API de Azure][] .

## <a name="configure-caching"> </a>Configurar una operación de almacenamiento en caché

En este paso, revisará la configuración de caché de la operación de **Obtener recursos (caché)** de la API de eco de muestra.

>[AZURE.NOTE] Cada instancia del servicio de administración de la API viene preconfigurado con una API de eco que se pueden usar para probar y obtener información sobre la administración de la API. Para obtener más información, vea [Introducción a la administración de la API de Azure][].

Para empezar, haga clic en **Administrar** en el Portal de Azure clásico de su servicio de administración de la API. Se abrirá el portal de administración de la API de publisher.

![Portal de Publisher][api-management-management-console]

Haga clic en **las API de** desde el menú de **Administración de la API** de la izquierda y, a continuación, haga clic en **Las API de eco**.

![API de eco][api-management-echo-api]

Haga clic en la ficha **operaciones** y, a continuación, haga clic en la operación de **Obtener recursos (caché)** de la lista de **operaciones** .

![Operaciones de API de eco][api-management-echo-api-operations]

Haga clic en la ficha de **caché** para ver la configuración de almacenamiento en caché para realizar esta operación.

![Ficha almacenamiento en caché][api-management-caching-tab]

Para habilitar almacenamiento en caché para una operación, seleccione la casilla de verificación **Habilitar** . En este ejemplo, se habilita el almacenamiento en caché.

Se introduce cada respuesta de la operación, en función de los valores de los campos **variar por parámetros de cadena de consulta** y **variar por encabezados** . Si desea varias respuestas en función de los parámetros de cadena de consulta o encabezados de caché, puede configurarlos en estos dos campos.

**Duración** especifica el intervalo de caducidad de las respuestas en caché. En este ejemplo, el intervalo es **3600** segundos, que es equivalente a una hora.

Mediante la configuración de almacenamiento en caché en este ejemplo, la primera solicitud para la operación de **Obtener recursos (en caché)** devuelve una respuesta desde el servicio de back-end. Esta respuesta se almacenarán en caché, ordenadas por los encabezados especificados y los parámetros de cadena de consulta. Las llamadas posteriores a la operación, con parámetros coincidentes, tendrá la respuesta en caché devuelva hasta que el intervalo de duración de la memoria caché ha expirado.

## <a name="caching-policies"> </a>Revisar las directivas de almacenamiento en caché

En este paso, revise la configuración de caché para la operación de **Obtener recursos (caché)** de la API de eco de muestra.

Cuando la configuración de almacenamiento en caché para una operación en la pestaña de **almacenamiento en caché** , se agregan las directivas de almacenamiento en caché para la operación. Estas directivas se pueden ver y editar en el editor de directivas.

Haga clic en **directivas** desde el menú de **Administración de la API** de la izquierda y, a continuación, seleccione **API de eco / obtener recursos (en caché)** de la lista desplegable de **operación** .

![Operación de ámbito de la directiva][api-management-operation-dropdown]

Esto muestra las directivas para realizar esta operación en el editor de directivas.

![Editor de directivas de administración de la API][api-management-policy-editor]

La definición de directivas para realizar esta operación incluye las directivas que definen la configuración del almacenamiento en caché que se han revisado con la ficha **caché** en el paso anterior.

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

>[AZURE.NOTE] Los cambios realizados en las directivas de almacenamiento en caché en el editor de directivas se reflejarán en la pestaña de **almacenamiento en caché** de una operación y viceversa.

## <a name="test-operation"> </a>Una operación de llamadas y probar el almacenamiento en caché

Para ver el almacenamiento en caché en acción, podemos llamamos la operación desde el portal de programadores. En el menú superior derecha, haga clic en **portal de programadores** .

![Portal de programadores][api-management-developer-portal-menu]

Haga clic en **las API de** en el menú superior y, a continuación, seleccione **Las API de eco**.

![API de eco][api-management-apis-echo-api]

>Si tiene solo una API configurada o visible a su cuenta, haga clic en las API de lleva directamente a las operaciones para la API.

Seleccione la operación de **Obtener recursos (en caché)** y, a continuación, haga clic en **Abrir la consola**.

![Abra la consola][api-management-open-console]

La consola permite invocar operaciones directamente desde el portal de programadores.

![Consola][api-management-console]

Mantenga los valores predeterminados para **param1** y **parám2**.

Seleccione la clave que desee en la lista desplegable de **clave de suscripción** . Si su cuenta sólo tenga una suscripción, ya se seleccionará.

Escriba **sampleheader:value1** en el cuadro de texto de **encabezados de solicitud** .

Haga clic en **HTTP Get** y tome nota de los encabezados de respuesta.

Escriba **sampleheader:value2** en el cuadro de texto de **encabezados de solicitud** y, a continuación, haga clic en **HTTP Get**.

Tenga en cuenta que el valor de **sampleheader** sigue siendo **valor1** en la respuesta. Pruebe algunos valores diferentes y observe que se devuelve la respuesta en caché de la primera llamada.

Escriba **25** en el campo **param2** y, a continuación, haga clic en **HTTP Get**.

Observe que el valor de **sampleheader** en la respuesta ahora es **valor2**. Dado que los resultados de la operación se con clave de cadena de consulta, no se devolvió la respuesta en caché anterior.

## <a name="next-steps"> </a>Pasos siguientes

-   Para obtener más información acerca de las directivas de almacenamiento en caché, vea [almacenamiento en caché de directivas][] en la [referencia de la directiva de administración de la API][].
-   Para obtener información sobre el almacenamiento de los elementos clave con las expresiones de directiva, vea [almacenamiento en caché personalizado en administración de la API de Azure](api-management-sample-cache-by-key.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introducción a la administración de la API de Azure]: api-management-get-started.md

[Referencia de la directiva de administración de la API]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Directivas de almacenamiento en caché]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Crear una instancia de servicio de administración de la API]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
