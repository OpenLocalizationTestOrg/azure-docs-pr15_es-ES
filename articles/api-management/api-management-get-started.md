<properties
    pageTitle="Administrar su primera API de administración de la API de Azure | Microsoft Azure"
    description="Aprenda a crear API, agregue operaciones y empezar a trabajar con la API de administración."
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

# <a name="manage-your-first-api-in-azure-api-management"></a>Administrar su primera API de administración de la API de Azure

## <a name="overview"> </a>Información general

Esta guía le muestra cómo empezar a usar la administración de la API de Azure rápidamente y realizar su primera llamada API.

## <a name="concepts"> </a>¿Qué es la administración de la API de Azure?

Puede usar la administración de la API de Azure para tomar cualquier back-end e iniciar un programa de API completo basado en ella.

Escenarios comunes son:

* **Proteger la infraestructura móvil** mediante el control de acceso con teclas de API, impide DOS ataques mediante la limitación o mediante las directivas de seguridad avanzada como la validación de token JWT.
* **Habilitar ISV ecosistemas de partner** por ofreciendo integrado rápido asociado a través del portal de programadores y la creación de una fachada API desacoplar de implementaciones internas que no son de madurez consumo asociado.
* **Ejecutar un programa de API interno** , ofreciendo una ubicación centralizada para la organización para comunicar información sobre la disponibilidad y los cambios más recientes a las API, el control de acceso basado en cuentas de la organización, todo basado en un canal seguro entre la puerta de enlace API y el back-end.


El sistema se compone de los siguientes componentes:

* La **puerta de enlace API** es el extremo que:
  * Acepta API llama y les dirige a su back-ends.
  * Comprueba claves API, tokens JWT, certificados y otras credenciales.
  * Impone cuotas de uso y los límites de índice.
  * Las transformaciones de la API sobre la marcha sin modificaciones de código.
  * Almacena las respuestas de back-end en configurar.
  * Registros de llamadas metadatos con fines de análisis.

* El **portal de publisher** es la interfaz administrativa donde configura el programa de la API. Utilícelo para:
    * Definir o importar esquema de API.
    * Paquete API en productos.
    * Configurar directivas como cuotas o transformaciones en las API.
    * Obtener recomendaciones de análisis.
    * Administrar los usuarios.

* El **portal de programadores** sirve como la presencia web principal para los desarrolladores, donde podrá:
    * Documentación de la API de lectura.
    * Pruebe una API a través de la consola interactiva.
    * Crear una cuenta y suscribirse para obtener claves API.
    * Análisis de Access en su propio uso.


## <a name="create-service-instance"> </a>Crea una instancia de administración de la API

>[AZURE.NOTE] Para completar este tutorial, necesita una cuenta de Azure. Si no tiene una cuenta, puede crear una cuenta gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure][].

El primer paso para trabajar con la API de administración es crear una instancia de servicio. Inicie sesión en el [Portal de clásico de Azure][] y haga clic en **nuevo**, **Servicios de aplicación**, **Administración de la API**, **crear**.

![Nueva instancia de la administración de la API][api-management-create-instance-menu]

Para la **dirección URL**, especifique un nombre de subdominio único para la dirección URL del servicio.

Elija la **suscripción** y **región** que desee para la instancia de servicio. Después de realizar las selecciones, haga clic en el botón **siguiente** .

![Servicio de administración de la API de nuevo][api-management-create-instance-step1]

Escriba **Contoso Ltd.** para el **Nombre de la organización**y escriba su dirección de correo electrónico en el campo de **Correo electrónico del administrador** .

>[AZURE.NOTE] Esta dirección de correo electrónico se usa para las notificaciones desde el sistema de administración de la API. Para obtener más información, consulte [cómo configurar las notificaciones y plantillas de correo electrónico en administración de la API de Azure][].

![Servicio de administración de la API de nuevo][api-management-create-instance-step2]

Instancias del servicio de administración de la API están disponibles en tres niveles: programador, estándar y Premium. De forma predeterminada, se crean nuevas instancias del servicio de administración de la API en el nivel de desarrollador. Para seleccionar el nivel estándar o Premium, active la casilla de verificación de **Configuración avanzada** y seleccione el nivel que desee en la pantalla siguiente.

>[AZURE.NOTE] Es el nivel de desarrollador de desarrollo, pruebas y programas de API piloto donde alta disponibilidad no es un problema. En las capas estándar y Premium, puede escalar el número de unidades reservado para controlar más tráfico. Los niveles estándar y Premium proporcionan el servicio de administración de la API con más eficacia de procesamiento y el rendimiento. Puede completar este tutorial mediante cualquier nivel. Para obtener más información sobre niveles de administración de la API, vea [administración de la API de precios][].

Haga clic en la casilla de verificación para crear su instancia de servicio.

![Servicio de administración de la API de nuevo][api-management-instance-created]

Una vez creada la instancia de servicio, el siguiente paso es crear o importar una API.

## <a name="create-api"> </a>Importar una API

Una API consta de un conjunto de operaciones que se pueden invocar desde una aplicación de cliente. Operaciones de API son proxy de servicios web existentes.

API pueden crearse (y se pueden agregar operaciones) manualmente, o se pueden importar. En este tutorial, se importará la API de un servicio de web de calculadora de muestra proporcionados por Microsoft y hospedada en Azure.

>[AZURE.NOTE] Para obtener instrucciones sobre cómo crear una API y agregar manualmente las operaciones, consulte [cómo crear API](api-management-howto-create-apis.md) y [cómo agregar acciones a una API](api-management-howto-add-operations.md).

API se configuran desde el portal de publisher, que se tiene acceso a través del Portal de clásico de Azure. Para alcanzar el portal de publisher, haga clic en **Administrar** en el Portal de Azure clásico de su servicio de administración de la API.

![Portal de Publisher][api-management-management-console]

Para importar la API de calculadora, haga clic en **las API de** desde el menú de **Administración de la API** de la izquierda y, a continuación, haga clic en **Importar API**.

![Botón Importar API][api-management-import-api]

Siga estos pasos para configurar la API de calculadora:

1. Haga clic en **Desde dirección URL**, escriba **http://calcapi.cloudapp.net/calcapi.json** en el cuadro de texto de la **especificación de la dirección URL del documento** y haga clic en el botón de opción **Swagger** .
2. En el cuadro de texto **dirección URL de Web API sufijo** , escriba **calc** .
3. Haga clic en el cuadro de **productos (opcionales)** y elija **Starter**.
4. Haga clic en **Guardar** para importar la API.

![Agregar nueva API][api-management-import-new-api]

>[AZURE.NOTE] **API de administración** actualmente es compatible con la versión 1.2 y 2.0 de documento de Swagger para la importación. Asegúrese de que, aunque la [especificación 2.0 Swagger](http://swagger.io/specification) declara que `host`, `basePath`, y `schemes` propiedades son opcionales, el documento Swagger 2.0 **debe** contener dichas propiedades; en caso contrario, no se importa. 

Una vez que se importa la API, se muestra la página de resumen de la API en el portal de publisher.

![Resumen de la API][api-management-imported-api-summary]

La sección API tiene varias fichas. La ficha **Resumen** muestra métricas básicas e información acerca de la API. La pestaña [configuración](api-management-howto-create-apis.md#configure-api-settings) se usa para ver y editar la configuración para una API. La ficha [operaciones](api-management-howto-add-operations.md) se usa para administrar las operaciones de la API. La pestaña **seguridad** puede utilizarse para configurar la autenticación de puerta de enlace para el servidor back-end mediante la autenticación básica o [autenticación de certificado mutuo](api-management-howto-mutual-certificates.md)y configurar la [autorización del usuario mediante OAuth 2.0](api-management-howto-oauth2.md).  La ficha de **problemas** se utiliza para ver los problemas notificados por los desarrolladores que usan la API. La pestaña **Products** se usa para configurar los productos que contienen esta API.

De forma predeterminada, cada instancia de administración de la API se suministra con dos productos de ejemplo:

-   **Starter**
-   **Ilimitado**

En este tutorial, la API de calculadora básica se agregó al producto Starter cuando se ha importado la API.

Para realizar llamadas a una API, los desarrolladores deben suscribirse a un producto que proporciona acceso a él. Pueden suscribirse a los desarrolladores a productos en el portal de programadores o los administradores pueden suscribir a los desarrolladores de productos en el portal de publisher. Es un administrador ya que creó la instancia de administración de la API en los pasos anteriores en el tutorial, por lo que se ha suscrito a cada producto de forma predeterminada.

## <a name="call-operation"> </a>Llamar a una operación en el portal de programadores

Las operaciones se pueden llamar directamente desde el portal de programadores, que proporciona un modo adecuado para ver y probar las operaciones de una API. En este tutorial paso, llamará la operación de **Agregar dos enteros** de la API de calculadora básicas. Haga clic en el **portal de programadores** en el menú en la parte superior derecha del portal de publisher.

![Portal de programadores][api-management-developer-portal-menu]

Haga clic en **las API de** en el menú superior y, a continuación, haga clic en **Calculadora básicas** para ver las operaciones disponibles.

![Portal de programadores][api-management-developer-portal-calc-api]

Tenga en cuenta las descripciones de ejemplo y los parámetros que se importaron junto con la API y operaciones, proporcionar la documentación para los desarrolladores que vayan a utilizar esta operación. También pueden agregarse estas descripciones cuando las operaciones se agregan manualmente.

Para llamar a la operación de **Agregar dos enteros** , haga clic en **probar la aplicación**.

![Pruébelo:][api-management-developer-portal-calc-api-console]

Puede introducir algunos valores para los parámetros o mantener los valores predeterminados y, a continuación, haga clic en **Enviar**.

![HTTP Get][api-management-invoke-get]

Cuando se invoca una operación, el portal de programadores muestra el **estado de respuesta**, los **encabezados de respuesta**y cualquier **contenido de la respuesta**.

![Respuesta][api-management-invoke-get-response]

## <a name="view-analytics"> </a>Ver análisis

Para ver el análisis de calculadora básica, cambiar volver al portal de publisher, seleccione **Administrar** desde el menú en la parte superior derecha del portal de programadores.

![Administrar][api-management-manage-menu]

La vista predeterminada para el portal de publisher es **paneles**, que ofrece una descripción general de la instancia de administración de la API.

![Panel][api-management-dashboard]

Mantenga el mouse sobre el gráfico de **Calculadora básicas** ver las medidas específicas para el uso de la API durante un período de tiempo determinado.

>[AZURE.NOTE] Si no ve las líneas en el gráfico, vuelva al portal de programadores y realizar algunas llamadas a la API, espere unos minutos y vuelva al panel.

Haga clic en **Ver detalles** para ver la página de resumen de la API, incluyendo una versión ampliada de la métrica de muestra.

![Análisis][api-management-mouse-over]

![Resumen][api-management-api-summary-metrics]

Métricas detalladas e informes, haga clic en **análisis** en el menú de **Administración de la API** de la izquierda.

![Información general][api-management-analytics-overview]

La sección de **análisis** tiene las siguientes cuatro pestañas:

-   **Un vistazo** proporciona indicadores generales de mantenimiento y uso, así como los desarrolladores superiores, productos principales, API superiores y operaciones superiores.
-   **Uso** proporciona detenimiento llamadas API y ancho de banda, incluyendo una representación geográfica.
-   Enfoques de **Mantenimiento** de códigos de estado, tasas de éxito, tiempos de respuesta y API de caché y tiempos de respuesta del servicio.
-   **Actividad** proporciona informes que explorar en profundidad en la actividad específica, programador, producto, API y operación.

## <a name="next-steps"> </a>Pasos siguientes

- Obtenga información sobre cómo [proteger su API con límites de velocidad](api-management-howto-product-with-rules.md).

[Prueba gratuita de Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Cómo configurar las notificaciones y plantillas de correo electrónico en administración de la API de Azure]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[Administración de la API de precios]: http://azure.microsoft.com/pricing/details/api-management/

[Portal de clásico de Azure]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png
