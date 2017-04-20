<properties
    pageTitle="Proteger la API con la administración de la API de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo proteger su API con cuotas y límite de directivas (limitación de velocidad)."
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

# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>Proteger la API con los límites de tasa utilizando la administración de la API de Azure

Esta guía muestra lo fácil que es agregar protección a la API de back-end mediante la configuración de directivas de límite y cuota de tasa con la administración de la API de Azure.

En este tutorial, creará un producto "Prueba gratuita" API que permite a los desarrolladores realizar llamadas de hasta 10 por minuto y hasta un máximo de 200 llamadas por semana a la API con las directivas de [cuota de uso conjunto por suscripción](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) y la [tasa de llamada límite por suscripción](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) . A continuación, se la API de publicación y probar la directiva de límite de tasa.

Para escenarios más avanzados de limitación con las directivas de [tasa límite por clave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) y [cuota por clave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) , vea [solicitud avanzada límite con la administración de la API de Azure](api-management-sample-flexible-throttling.md).

## <a name="create-product"> </a>Para crear un producto

En este paso, creará un producto de prueba gratuita que no requiere aprobación de suscripción.

>[AZURE.NOTE] Si ya tiene un producto configurado y desea utilizar para este tutorial, puede pasar a [Configurar llamada tasa límite y cuota directivas][] y siga el tutorial desde allí con el producto en lugar de producto de prueba gratuita.

Para empezar, haga clic en **Administrar** en Azure clásico de su servicio de administración de la API. Se abrirá el portal de administración de la API de publisher.

![Portal de Publisher][api-management-management-console]

>Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia de servicio de administración de la API][] en el tutorial de [administrar su primera API de administración de la API de Azure][] .

Haga clic en **productos** en el menú de **Administración de la API** de la izquierda para mostrar la página de **productos** .

![Agregar producto][api-management-add-product]

Haga clic en **Agregar producto** para mostrar el cuadro de diálogo **Agregar nuevo producto** .

![Agregar nuevo producto][api-management-new-product-window]

En el cuadro **título** , escriba la **Versión de prueba gratuita**.

En el cuadro **Descripción** , escriba el siguiente texto:  **los suscriptores podrán ejecutar 10 llamadas por minuto hasta un máximo de 200 llamadas por semana después de que acceso denegado.**

Los productos de administración de la API pueden estar protegidos o abrirán. Productos protegidos deben estar suscrito a antes de que se pueden usar. Abrir productos pueden utilizarse sin una suscripción. Asegúrese de que está seleccionada la **suscripción se requiere** para crear un producto protegido que requiere una suscripción. Esta es la configuración predeterminada.

Si desea que un administrador para revisar y Aceptar o rechazar intentos de suscripción a este producto, seleccione **Requerir aprobación de suscripción**. Si no está seleccionada la casilla de verificación, los intentos de suscripción será aprueba automáticamente. En este ejemplo, las suscripciones se aprueban automáticamente, por lo que no active la casilla.

Para permitir que las cuentas de desarrollador suscribirse varias veces para el nuevo producto, seleccione la casilla de verificación **Permitir varias suscripciones simultáneas** . Este tutorial no utilizan varias suscripciones simultáneas, así que deja desactivada.

Después de que se especifican todos los valores, haga clic en **Guardar** para crear el producto.

![Producto agregado][api-management-product-added]

De forma predeterminada, son visibles para los usuarios del grupo de **administradores de** nuevos productos. Vamos a agregar el grupo de **programadores** . Haga clic en la **Versión de prueba gratuita**y, a continuación, haga clic en la ficha **visibilidad** .

>En administración de la API, los grupos se utilizan para administrar la visibilidad de productos para los desarrolladores. Productos conceden visibilidad a grupos y los desarrolladores pueden ver y suscribirse a los productos que son visibles para los grupos en la que pertenecen. Para obtener más información, vea [cómo crear y usar grupos de administración de la API de Azure][].

![Agregar grupo programadores][api-management-add-developers-group]

Seleccione la casilla de verificación de **los programadores** y, a continuación, haga clic en **Guardar**.

## <a name="add-api"> </a>Para agregar una API con el producto

En este paso del tutorial, agregaremos la API de eco al nuevo producto de prueba gratuita.

>Cada instancia del servicio de administración de la API viene preconfigurado con una API de eco que se pueden usar para probar y obtener información sobre la administración de la API. Para obtener más información, consulte [administrar su primera API de administración de la API de Azure][].

Haga clic en **productos** en el menú de **Administración de la API** de la izquierda y, a continuación, haga clic en la **Versión de prueba gratuita** para configurar el producto.

![Configurar el producto][api-management-configure-product]

Haga clic en **Agregar API al producto**.

![Agregar API a producto][api-management-add-api]

Seleccione **Eco API**y, a continuación, haga clic en **Guardar**.

![Agregar API de eco][api-management-add-echo-api]

## <a name="policies"> </a>Para configurar las directivas de límite y cuota de tasa de llamada

Límites de velocidad y las cuotas están configuradas en el editor de directivas. En el menú de **Administración de la API** de la izquierda, haga clic en **directivas** . En la lista de **productos** , haga clic en **Prueba gratuita**.

![Directiva de producto][api-management-product-policy]

Haga clic en **Agregar directiva** para importar la plantilla de directiva y empezar a crear directivas de límite y de cuota de la tasa.

![Agregar directiva][api-management-add-policy]

Para insertar directivas, coloque el cursor en la sección de la **entrada** o **salida** de la plantilla de directiva. Las directivas de límite y cuota de tasa son las entrantes, así que coloque el cursor en el elemento de entrada.

![Editor de directivas][api-management-policy-editor-inbound]

Las dos directivas que agregamos en este tutorial son las directivas de [tasa de llamada límite por suscripción](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) y [establecer la cuota de uso por suscripción](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) .

![Instrucciones de directiva][api-management-limit-policies]

Después de que el cursor se coloca en el elemento de la directiva de **entrada** , haga clic en la flecha situada junto a **velocidad de llamada límite por suscripción** para insertar su plantilla de directiva.

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**Tasa de llamada límite por suscripción** puede usarse en el nivel de producto y también puede usarse en la API en el nivel de nombre de operación individual. En este tutorial, se usan solamente las directivas de nivel de producto, de modo que elimina los elementos de la **api** y **operación** desde el elemento de **límite de velocidad** , por lo que solo exterior **límite de velocidad** elemento permanece, tal como se muestra en el ejemplo siguiente.

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

En el producto de prueba gratuita, la tasa de llamada permitido máximo 10 llamadas por minuto, así que escriba **10** como el valor del atributo de **llamadas** y **60** para el atributo de **período de renovación** .

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

Para configurar la directiva de **establecer la cuota de uso por suscripción** , coloque el cursor inmediatamente debajo del elemento de recién agregado **tasa límite** dentro del elemento de **entrada** y, a continuación, haga clic en la flecha situada a la izquierda del **conjunto de cuota de uso por suscripción**.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

Como esta directiva también está diseñada para ser en el nivel de producto, eliminar los elementos de nombre **api** y **operación** , tal como se muestra en el ejemplo siguiente.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

Las cuotas pueden estar en función del número de llamadas por intervalo, ancho de banda o ambos. En este tutorial, nos estamos limitación no dependen del ancho de banda, de modo que elimina el atributo de **ancho de banda** .

    <quota calls="number" renewal-period="seconds">
    </quota>

En el producto de prueba gratuita, la cuota es 200 llamadas por semana. Especificar **200** como el valor del atributo de **llamadas** y luego especifique **604800** como el valor del atributo **período de renovación** .

    <quota calls="200" renewal-period="604800">
    </quota>

>Intervalos de directiva se especifican en segundos. Para calcular el intervalo de una semana, puede multiplicar el número de días (7) por el número de horas en un día (24) por el número de minutos en una hora (60) por el número de segundos en cuestión de minutos (60): 7 *24* 60 * 60 = 604800.

Cuando haya terminado de configurar la directiva, debe coincidir con el siguiente ejemplo.

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />

    </inbound>
    <outbound>

        <base />

        </outbound>
    </policies>

Después de configuran las directivas, haga clic en **Guardar**.

![Guardar la directiva][api-management-policy-save]

## <a name="publish-product"></a> Para publicar el producto

Ahora que el se agregan las API y las directivas se configuran, se debe publicar el producto para que se puede usar los desarrolladores. Haga clic en **productos** en el menú de **Administración de la API** de la izquierda y, a continuación, haga clic en la **Versión de prueba gratuita** para configurar el producto.

![Configurar el producto][api-management-configure-product]

Haga clic en **Publicar**y, a continuación, haga clic en **Sí, publicarlo** para confirmar.

![Publicar el producto][api-management-publish-product]

## <a name="subscribe-account"> </a>Para suscribirse a una cuenta de desarrollador con el producto

Ahora que se publica el producto, está disponible para ser suscrito a y utilizar los desarrolladores.

>Los administradores de una instancia de administración de la API se suscribe automáticamente a cada producto. En este paso del tutorial, se suscribe una de las cuentas de administrador no programador con el producto de prueba gratuita. Si su cuenta de desarrollador es parte de la función Administradores, a continuación, puede seguir junto con este paso, incluso si ya se ha suscrito.

Haga clic en **usuarios** en el menú de **Administración de la API** de la izquierda y, a continuación, haga clic en el nombre de su cuenta de desarrollador. En este ejemplo, estamos usando la cuenta del programador de **Benito Gragg** .

![Configurar developer][api-management-configure-developer]

Haga clic en **Agregar suscripción**.

![Agregar la suscripción][api-management-add-subscription-menu]

Seleccione la **Versión de prueba gratuita**y, a continuación, haga clic en **suscribirse**.

![Agregar la suscripción][api-management-add-subscription]

>[AZURE.NOTE] En este tutorial, varias suscripciones simultáneas no están habilitadas para el producto de prueba gratuita. Si fueran, ¿se le un nombre de la suscripción, tal como se muestra en el ejemplo siguiente.

![Agregar la suscripción][api-management-add-subscription-multiple]

Después de hacer clic en **suscribirse**, el producto aparece en la lista de **suscripción** para el usuario.

![Suscripción agregada][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Para llamar a una operación y probar el límite

Ahora que el producto de prueba gratuita configurado y publicado, podemos algunas operaciones de llamadas y pruebe la directiva de límite de velocidad.
Cambiar al portal de programadores haciendo clic en el **portal de programadores** en el menú de la esquina superior derecha.

![Portal de programadores][api-management-developer-portal-menu]

Haga clic en **las API de** en el menú superior y, a continuación, haga clic en **Las API de eco**.

![Portal de programadores][api-management-developer-portal-api-menu]

Haga clic en **Obtener recursos**y, a continuación, haga clic en **probar la aplicación**.

![Abra la consola][api-management-open-console]

Mantener el valor predeterminado de los valores de parámetro y, a continuación, seleccione la clave de suscripción para el producto de prueba gratuita.

![Clave de suscripción][api-management-select-key]

>[AZURE.NOTE] Si tiene varias suscripciones, asegúrese de seleccionar la clave de **Versión de prueba gratuita**, o bien las directivas que se han configurado en los pasos anteriores no estarán activo.

Haga clic en **Enviar**y, a continuación, ver la respuesta. Tenga en cuenta el **estado de respuesta** de **200 Aceptar**.

![Resultados de la operación][api-management-http-get-results]

Haga clic en **Enviar** a una velocidad mayor que la directiva de límite de velocidad de 10 llamadas por minuto. Después de que se ha superado la directiva de límite de tasa, se devuelve un estado de respuesta de **429 demasiado muchas solicitudes** .

![Resultados de la operación][api-management-http-get-429]

El **contenido de la respuesta** indica el intervalo restante antes de reintentos se realizará correctamente.

Cuando la directiva de límite de velocidad de 10 llamadas por minuto es en efecto, se producirá un error llamadas posteriores hasta 60 segundos transcurridos desde la primera de las 10 llamadas correctamente con el producto antes de que se ha superado el límite. En este ejemplo, el intervalo restante es 54 segundos.

## <a name="next-steps"> </a>Pasos siguientes

-   Ver una demostración de establecer cuotas y límites de velocidad en el siguiente vídeo.

> [AZURE.VIDEO rate-limits-and-quotas]


[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Administrar su primera API de administración de la API de Azure]: api-management-get-started.md
[Cómo crear y usar grupos de administración de la API de Azure]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Crear una instancia de servicio de administración de la API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configurar directivas de límite y cuota de tasa de llamada]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota
