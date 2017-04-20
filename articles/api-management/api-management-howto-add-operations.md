<properties 
    pageTitle="Cómo agregar acciones a una API de administración de la API de Azure | Microsoft Azure" 
    description="Obtenga información sobre cómo agregar acciones a una API de administración de la API de Azure." 
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

# <a name="how-to-add-operations-to-an-api-in-azure-api-management"></a>Cómo agregar acciones a una API de administración de la API de Azure

Antes de poder usar una API de administración de la API, deben agregarse operaciones. Esta guía muestra cómo agregar y configurar diferentes tipos de operaciones a una API de administración de la API.

## <a name="add-operation"> </a>Agregue una operación

Las operaciones se agregan y configuradas para una API en el portal de publisher. Para acceder al portal de publisher, haga clic en **Administrar** en el Portal de Azure clásico de su servicio de administración de la API.

![Portal de Publisher][api-management-management-console]

>Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia de servicio de administración de la API][] en el tutorial de [Introducción a la administración de la API de Azure][] .

Seleccione la API deseada en el portal de publisher y, a continuación, seleccione la ficha **operaciones** . 

![Operaciones][api-management-operations]

Haga clic en **Agregar operación** para agregar una nueva operación. Se mostrará la **nueva operación** y se selecciona la ficha **firma** predeterminada.

![Agregar operación][api-management-add-operation]

Especificar el **verbo HTTP** , elija en la lista desplegable.

![Método HTTP][api-management-http-method]

<a name="url-template"></a>

Definir la plantilla de dirección URL escribiendo en un fragmento de dirección URL formada por uno o más segmentos de ruta de acceso de la dirección URL y cero o más parámetros de cadena de consulta. La plantilla de dirección URL, que se anexa a la dirección URL base de la API identifica una única operación de HTTP. Puede contener uno o más denominado partes variables identificados llaves. Estas partes variables se denominan parámetros de plantilla y se asignan dinámicamente valores extraídos de la dirección URL de la solicitud cuando se está procesando la solicitud de la plataforma de administración de la API.

![Plantilla de dirección URL][api-management-url-template]

<a name="rewrite-url-template"></a>

Si lo desea, especifique la **dirección URL de reescritura de plantilla**. Esto le permite usar la plantilla estándar de la dirección URL para el procesamiento de las solicitudes entrantes en el front-end, al que llama el back-end mediante una dirección URL convertida según la plantilla de reescritura. Parámetros de plantilla desde la plantilla de dirección URL se deben utilizar en la plantilla de reescritura. En el ejemplo siguiente se muestra cómo contenido tipo codificado como segmento de ruta de acceso en el servicio web del ejemplo anterior se puede ofrecer como un parámetro de consulta en la API publicada a través de la plataforma de administración de la API con las plantillas de dirección URL.

![Reescritura de URL plantilla][api-management-url-template-rewrite]

Las personas que llaman a la operación usará el formato `/customers?customerid=ALFKI` y esto se asignará a `/Customers('ALFKI')` cuando se invoca el servicio back-end.


Nombre para **Mostrar** y la **Descripción** proporcionan una descripción de la operación y se utilizan para proporcionar la documentación a los desarrolladores que usan esta API en el portal de programadores.

![Descripción][api-management-description]

La descripción de la operación puede especificarse como texto sin formato o HTML en el cuadro de texto **Descripción** .

## <a name="operation-caching"> </a>Operación almacenamiento en caché

Almacenamiento en caché de respuesta reduce la latencia observar que los consumidores de API, disminuye el consumo de ancho de banda y disminuye la carga en la web HTTP implementación de la API del servicio. 

Para rápida y fácilmente Habilitar almacenamiento en caché para la operación, seleccione la ficha **caché** y Active la casilla de verificación **Habilitar** .

![Almacenamiento en caché][api-management-caching-tab]

**Duración** especifica el período de tiempo durante el cual la respuesta de la operación permanece en la caché. El valor predeterminado es 3.600 segundos o 1 hora.

Claves de caché se utilizan para diferenciar las respuestas para que obtenga su propio valor independiente en caché la respuesta correspondiente a cada clave de caché diferente. Si lo desea, escriba los parámetros de cadena de consulta específica o encabezados HTTP para usarlo con sistemas de valores de clave de caché en los cuadros de texto **variar por parámetros de cadena de consulta** y **variar por encabezados** respectivamente. Cuando ninguno son la dirección URL especificada, completa la solicitud y los valores de encabezado HTTP siguientes se usan en la generación de claves de caché: **Aceptar** y **Aceptar juego de caracteres**.

>Para obtener más información sobre el almacenamiento en caché y el almacenamiento en caché de directivas, vea [cómo resultados de la operación de caché en administración de la API de Azure][].


## <a name="request-parameters"> </a>Solicitud de parámetros

Parámetros de la operación se administran en la ficha parámetros. Parámetros especificados en la **Plantilla de dirección URL** en la pestaña **firma** se agregan automáticamente y se pueden cambiar mediante la edición de la plantilla de dirección URL. Pueden especificar parámetros adicionales manualmente.

Para agregar un nuevo parámetro de consulta, haga clic en **Agregar parámetro de consulta** y escriba la información siguiente:

-   **Nombre** : nombre del parámetro.
-   **Descripción** : una breve descripción del parámetro (opcional).
-   **Tipo** : tipo de parámetro, seleccionado en la lista hacia abajo.
-   **Valores** : valores que se pueden asignar a este parámetro. Uno de los valores puede marcarse como predeterminado (opcional).
-   **Requerido** : hacer que el parámetro obligatoria activando la casilla de verificación. 

![Solicitud de parámetros][api-management-request-parameters]

## <a name="request-body"> </a>Cuerpo de la solicitud

Si permite la operación (por ejemplo, SUPERPONER, POST) y requiere un cuerpo puede proporcionar un ejemplo de en todos los formatos de representación compatible (por ejemplo, json, XML). 

>El cuerpo de la solicitud se usa solo con fines de documentación y no se valida.

Para introducir un cuerpo de la solicitud, cambie a la pestaña de **cuerpo** .

Haga clic en **Agregar representación**, comience a escribir el nombre de tipo de contenido que desee (por ejemplo, aplicación/json), selecciónelo en la lista desplegable y pegue el ejemplo de cuerpo de la solicitud que desee en el formato seleccionado en el cuadro de texto. 

![Cuerpo de la solicitud][api-management-request-body]

En adicionales a representaciones, también puede especificar una descripción opcional de texto en el cuadro de texto **Descripción** .

## <a name="responses"> </a>Respuestas

Es una buena práctica proporcionan ejemplos de respuestas para todos los códigos de estado que puede producir la operación. Cada código de estado puede tener más de un ejemplo de cuerpo de respuesta, uno para cada uno de los tipos de contenido admitidos. 

Para agregar una respuesta, haga clic en **Agregar** y comience a escribir el código de estado que desee. En este ejemplo es el código de estado **200 Aceptar**. Una vez que se muestra el código en la lista desplegable, selecciónela y el código de respuesta y se agrega a la operación.

![Código de respuesta][api-management-response-code]

Haga clic en **Agregar representación**, comience a escribir el nombre de tipo de contenido que desee (por ejemplo, aplicación/json) y, a continuación, seleccionar en el menú desplegable.

![Tipo de contenido del cuerpo][api-management-response-body-content-type]

Pegue el ejemplo de cuerpo de respuesta en el formato seleccionado en el cuadro de texto. 

![Cuerpo de respuesta][api-management-response-body]

Si lo desea, agregue una descripción opcional en el cuadro de texto **Descripción** .

Una vez configurada la operación, haga clic en **Guardar**.


## <a name="next-steps"> </a>Pasos siguientes

Una vez que las operaciones se agregan a una API, el siguiente paso es asociar la API de un producto y publicarlo para que los desarrolladores pueden llamar sus operaciones.

-   [Cómo crear y publicar un producto][]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Introducción a la administración de la API de Azure]: api-management-get-started.md
[Crear una instancia de servicio de administración de la API]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[Cómo crear y publicar un producto]: api-management-howto-add-products.md
[Cómo resultados de la operación de caché en administración de la API de Azure]: api-management-howto-cache.md