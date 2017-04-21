
<properties
    pageTitle="Migrar a Azure servicios cognitivas recomendaciones API de las API de recomendaciones de DataMarket | Microsoft Azure"
    description="Máquina Azure recomendaciones: migrar al servicio de recomendaciones cognitivas de aprendizaje"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="luisca"/>


# <a name="migrate-to-azure-cognitive-services-recommendations-api-from-the-datamarket-recommendations-api"></a>Migrar a Azure servicios cognitivas recomendaciones API de las API de recomendaciones de DataMarket
Este artículo muestra cómo migrar de la [API de recomendaciones de DataMarket de Microsoft](https://datamarket.azure.com/dataset/amla/recommendations) a la [API de recomendaciones de Microsoft Azure cognitivas servicios](https://www.microsoft.com/cognitive-services/en-us/recommendations-api).

La API de recomendaciones de DataMarket dejará de aceptar nuevos clientes el 31 de diciembre de 2016 y quedará obsoleta 28 de febrero de 2017.

## <a name="how-do-i-start-using-the-azure-cognitive-services-recommendations-api"></a>¿Cómo se puede iniciar mediante la API de recomendaciones de Azure cognitivas Services?

Para migrar a la API de recomendaciones cognitivas de servicios, haga lo siguiente:

1.  Si todavía no tiene una suscripción de Azure [registrarse](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) para uno. 

1.  Obtener instrucciones paso a paso de la [Guía de inicio rápido](cognitive-services-recommendations-quick-start.md) para iniciar sesión en la API de recomendaciones cognitivas de servicios y usarlo mediante programación. 

1.  Vaya a la [página de inicio de la API de recomendaciones servicios cognitivas](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) para obtener información sobre el servicio y buscar documentación.

## <a name="i-used-the-recommendations-ui-to-build-my-models-is-there-a-similar-tool-for-the-cognitive-services-recommendations-api"></a>Utiliza la interfaz de usuario de recomendaciones para generar modelos de mi. ¿Hay una herramienta similar de la API de recomendaciones cognitivas de servicios?

Por supuesto. La dirección URL de la nueva [Interfaz de usuario de recomendaciones](http://recommendations-portal.azurewebsites.net/) es http://recommendations-portal.azurewebsites.net. 

>[AZURE.NOTE] Las credenciales de DataMarket no funcionan aquí. Registrarse para una suscripción en el Portal de Azure y obtener la clave de cuenta necesarios para usar la nueva [Interfaz de usuario de recomendaciones](http://recommendations-portal.azurewebsites.net/).
Si no tiene una clave de cuenta, vea tarea 1 de la [Guía de inicio rápido](cognitive-services-recommendations-quick-start.md).

##<a name="is-the-new-api-format-the-same-as-the-datamarket-recommendations-api"></a>¿Es el nuevo formato de API idéntica a la API de recomendaciones de DataMarket?

La API admite los mismos escenarios y flujos de procesos como esos escenarios compatibles con la versión de DataMarket, pero se ha actualizado la interfaz API para ajustarse a las [Directrices de API de REST de Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). Las API son más coherentes y trabajar mejor con las herramientas que admiten Swagger.

Para comprender cada una de las API, consulte el [Explorador de API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db).
Usar la *Pruebe* botón para probar una llamada API. El formato de archivos consumido por la API de recomendaciones (archivos de catálogo y el uso) no ha cambiado, por lo que puede seguir usando los mismos archivos o infraestructura creada para generar los archivos.

##<a name="what-are-some-new-features-in-the-cognitive-services-recommendations-api"></a>¿Cuáles son algunas características nuevas de la API de recomendaciones cognitivas Services?

En los dos últimos meses, hemos publicado nuevas capacidades para la API de recomendaciones cognitivas de servicios:
-   Interfaz de usuario de formación y prueba modelos sin tener que escribir una sola línea de código de recomendaciones
-   Lote de puntuación para proporcionar miles de recomendaciones a la vez
-   Crear métricas compatibilidad para consultar la calidad de los modelos de recomendaciones
-   Compatibilidad con las reglas de negocio
-   Capacidad para enumerar y descargar archivos de uso y catálogo
-   Clasificación de construcción de soporte para consultar la calidad de las funciones de elemento de un modelo de recomendaciones
-   Se ha agregado funcionalidad para buscar un producto en el catálogo

## <a name="when-does-microsoft-stop-supporting-the-datamarket-recommendations-api"></a>¿Al dejar Microsoft compatible con la API de recomendaciones de DataMarket?

[API de recomendaciones en DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) deja de aceptar nuevos clientes tras el 31 de diciembre de 2016 y el 28 de febrero de 2017 quedará obsoleta completamente. 

## <a name="what-if-i-dont-have-the-data-that-i-need-to-recreate-my-models-in-the-cognitive-services-recommendations-api"></a>¿Qué ocurre si no tengo los datos que tiene que volver a crear mi modelos en la API de recomendaciones cognitivas de servicios?

Queremos aprovechar esta transición tan fácil como sea posible. Podemos ayudarle a mover los modelos antiguos de su cuenta de DataMarket a la nueva suscripción de Azure cognitivas servicios recomendaciones API. Póngase en contacto con nosotros en [mlapi@microsoft.com](mailto://mlapi@microsoft.com). Se le pedirá que proporcione su DataMarket identificador de suscripción y el identificador de suscripción a servicios cognitivas, antes de que se asocie los modelos de la nueva cuenta.
