<properties
    pageTitle="Guía de inicio rápido: API de análisis de máquina aprendizaje texto | Microsoft Azure"
    description="Máquina Azure texto análisis - guía de inicio rápido de aprendizaje"
    services="cognitive-services"
    documentationCenter=""
    authors="onewth"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="onewth"/>

# <a name="getting-started-with-the-text-analytics-apis-to-detect-sentiment-key-phrases-topics-and-language"></a>Introducción a las API de análisis de texto para detectar opinión, frases clave, temas e idioma

<a name="HOLTop"></a>

Este documento se describe cómo incorporados a su servicio o la aplicación para que utilice las [API de análisis de texto](//go.microsoft.com/fwlink/?LinkID=759711).
Puede usar estas API para detectar opinión, frases clave, temas e idioma del texto. [Haga clic aquí para ver una demostración interactiva de la experiencia.](//go.microsoft.com/fwlink/?LinkID=759712)

Consulte las [definiciones de la API](//go.microsoft.com/fwlink/?LinkID=759346) de documentación técnica para las API.

Esta guía es para la versión 2 de la API. Para obtener detalles sobre la versión 1 de la API, [consulte este documento](../machine-learning/machine-learning-apps-text-analytics.md).

Al final de este tutorial, podrá detectar mediante programación:

- **Opinión** - es texto positivo o negativo?

- **Frases de clave** - ¿qué personas tratando en un solo artículo?

- **Temas** - ¿qué personas tratando entre muchos artículos?

- **Idiomas** : ¿qué idioma es texto escrito en?

Tenga en cuenta que esta API cargos 1 transacción por documento enviado. Por ejemplo, si solicitar opinión 1000 documentos en una sola llamada, 1000 transacciones se deduce.



<a name="Overview"></a>
## <a name="general-overview"></a>Descripción general ##

Este documento es una guía paso a paso. Nuestro objetivo es que le guíe durante los pasos necesarios para formar un modelo y a recursos que le permitirá poner en producción. Este ejercicio tardará unos 30 minutos.

Para estas tareas, necesitará un editor y llamar a los extremos REST en su idioma preferido.

Comencemos.

## <a name="task-1---signing-up-for-the-text-analytics-apis"></a>Tarea 1: firma para las API de análisis de texto ####

En esta tarea, se suscribirse al servicio de análisis de texto.

1. Vaya a **Servicios cognitivas** en el [Portal de Azure](//go.microsoft.com/fwlink/?LinkId=761108) y asegúrese de que seleccionar **El análisis de texto** como el tipo de API' '.

1. Seleccione un plan. Puede seleccionar la **capa de libre de 5.000 transacciones por mes**. Como un plan libre, no se cobrará para utilizar el servicio. Debe iniciar sesión en su suscripción de Azure. 

1. Completar los demás campos y crear su cuenta.

1. Después de registrarse el análisis de texto, busque la **Clave API**. Copie la clave principal, ya que lo necesitará al usar los servicios de la API.


## <a name="task-2---detect-sentiment-key-phrases-and-languages"></a>Tarea 2 - detectar opinión, frases clave e idiomas ####

Es fácil detectar idiomas, frases clave y opinión en el texto. Mediante programación, obtendrá los mismos resultados que devuelve la [experiencia de demostración](//go.microsoft.com/fwlink/?LinkID=759712) .

>[AZURE.TIP] Para el análisis de opinión, se recomienda dividir texto en frases. Esto generalmente lleva a una mayor precisión en predicciones de opinión.

Tenga en cuenta que los idiomas admitidos son los siguientes:

| Característica | Códigos de idioma |
|:-----|:----|
| Opinión | `en`(En inglés), `es` (español), `fr` (francés), `pt` (portugués) |
| Frases clave | `en`(En inglés), `es` (español), `de` (alemán), `ja` (japonés) |


1. Debe configurar los encabezados a la siguiente. Tenga en cuenta que JSON actualmente es el único formato de entrada aceptado para las API. XML no es compatible.

        Ocp-Apim-Subscription-Key: <your API key>
        Content-Type: application/json
        Accept: application/json

1. A continuación, aplicar formato a las filas de entrada en JSON. De opinión, frases clave y de idioma, el formato es el mismo. Tenga en cuenta que cada identificador debe ser único y el identificador devolverá el sistema. El tamaño máximo de un único documento que se pueda enviar es 10KB y el tamaño máximo total de entrada enviado es 1MB. No más de 1.000 documentos pueden presentarse en una llamada. Limitación de velocidad existe a una velocidad de 100 llamadas por minuto, por lo tanto, se recomienda que envíe grandes cantidades de documentos en una sola llamada. Idioma es un parámetro opcional que se debe especificar si analizar texto distinto del inglés. Un ejemplo de entrada se muestra a continuación, donde el parámetro opcional `language` opinión análisis o tecla extracción frase se incluye:

        {
            "documents": [
                {
                    "language": "en",
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "language": "en",
                    "id": "100",
                    "text": "Final document"
                }
            ]
        }

1. Realizar una llamada de **entrada** en el sistema con la entrada de opinión, frases clave e idioma. Las direcciones URL tendrán el siguiente aspecto:

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages

1. Esta llamada devolverá un JSON con formato de respuesta con los identificadores y detectado propiedades. Continuación se muestra un ejemplo de la salida de opinión (con los detalles del error excluidos). En el caso de opinión, se devolverá una puntuación entre 0 y 1 para cada documento:

        // Sentiment response
        {
            "documents": [
                {
                    "id": "1",
                    "score": "0.934"
                },
                ...
                {
                    "id": "100",
                    "score": "0.002"
                },
            ]
        }

        // Key phrases response
        {
            "documents": [
                {
                    "id": "1",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
                ...
                {
                    "id": "100",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
            ]
        }

        // Languages response
        {
            "documents": [
                {
                    "id": "1",
                    "detectedLanguages": [
                        {
                            "name": "English",
                            "iso6391Name": "en",
                            "score": "1"
                        }
                    ]
                },
                ...
                {
                    "id": "100",
                    "detectedLanguages": [
                        {
                            "name": "French",
                            "iso6391Name": "fr",
                            "score": "0.985"
                        }
                    ]
                }
            ]
        }


## <a name="task-3---detect-topics-in-a-corpus-of-text"></a>Tarea 3: detectar temas en un cuerpo de texto ####

Esta es una API reciente que devuelve la parte superior detectado temas para obtener una lista de enviar registros de texto. Un tema se identifica con una frase clave, que puede ser una o más personas relacionadas palabras. La API está diseñada para funcionar bien con texto escrito humano cortas como revisiones y comentarios de los usuarios.

Esta API requiere **un mínimo de 100 registros de texto** se envíen, pero está diseñada para detectar temas a través de cientos a miles de registros. Los registros no está en inglés o con menos de 3 palabras se descartarán y, por tanto, no se asignará a temas. Para la detección de tema, el tamaño máximo de un único documento que se pueda enviar es 30KB y el tamaño máximo total de entrada enviado es 30MB. Detección de tema es tasa limitado a 5 envíos cada 5 minutos.

Existen dos parámetros de entrada adicionales **opcionales** que pueden ayudar a mejorar la calidad de los resultados:

- **Dejar de palabras.**  Estas palabras y sus formularios cerrados (por ejemplo, plurales) se se excluyen de la canalización de detección de tema. Use esta opción para palabras comunes (por ejemplo, "problema", "error" y "usuario" pueden ser opciones apropiadas para quejas de los clientes sobre software). Cada cadena debe ser una sola palabra.
- **Dejar de frases** : estas frases quedarán excluidas de la lista de temas devueltos. Use esta opción para excluir genéricos temas que no desea ver en los resultados. Por ejemplo, "Microsoft" y "Azure" sería opciones apropiadas para temas para excluir. Las cadenas pueden contener varias palabras.

Siga estos pasos para detectar temas en el texto.

1. Dar formato a la entrada en JSON. En este momento, puede definir palabras vacías y dejar de frases.

        {
            "documents": [
                {
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "id": "100",
                    "text": "Final document"
                }
            ],
            "stopWords": [
                "issue", "error", "user"
            ],
            "stopPhrases": [
                "Microsoft", "Azure"
            ]
        }

1. Usa los mismos encabezados según se define en la tarea 2, realizar una **publicación** llamar al extremo de temas:

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/topics

1. Devolverá un `operation-location` como encabezado en la respuesta, donde el valor es la dirección URL para consultar los temas resultantes:

        'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

1. El valor devuelto de la consulta `operation-location` periódicamente con una solicitud **GET** . Se recomienda una vez por minuto.

        GET https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>

1. El extremo devolverá una respuesta incluidas `{"status": "notstarted"}` antes de la transformación, `{"status": "running"}` al procesar y `{"status": "succeeded"}` con el resultado de una vez completado. A continuación, puede utilizar el resultado que se encuentre en el siguiente formato (Nota detalles como fechas y formato de error se han excluido de este ejemplo):

        {
            "status": "succeeded",
            "operationProcessingResult": {
                "topics": [
                    {
                        "id": "8b89dd7e-de2b-4a48-94c0-8e7844265196"
                        "score": "5"
                        "keyPhrase": "first topic name"
                    },
                    ...
                    {
                        "id": "359ed9cb-f793-4168-9cde-cd63d24e0d6d"
                        "score": "3"
                        "keyPhrase": "final topic name"
                    }
                ],
                "topicAssignments": [
                    {
                        "topicId": "8b89dd7e-de2b-4a48-94c0-8e7844265196",
                        "documentId": "1",
                        "distance": "0.354"
                    },
                    ...
                    {
                        "topicId": "359ed9cb-f793-4168-9cde-cd63d24e0d6d",
                        "documentId": "55",
                        "distance": "0.758"
                    },            
                ]
            }
        }

Tenga en cuenta que la respuesta correcta para temas de la `operations` punto final tendrá el siguiente esquema:

    {
            "topics" : [{
                "id" : "string",
                "score" : "number",
                "keyPhrase" : "string"
            }],
            "topicAssignments" : [{
                "documentId" : "string",
                "topicId" : "string",
                "distance" : "number"
            }],
            "errors" : [{
                "id" : "string",
                "message" : "string"
            }]
        }

Explicación de cada parte de esta respuesta es las siguientes:

**temas**

| Clave | Descripción |
|:-----|:----|
| Id. | Un identificador único para cada tema. |
| puntuación | Recuento de documentos asignados al tema. |
| keyPhrase | Una palabra o frase para el tema resumir. |

**topicAssignments**

| Clave | Descripción |
|:-----|:----|
| documentId | Identificador para el documento. Es igual al identificador de incluir en la entrada. |
| topicId | El identificador del tema que el documento se ha asignado a. |
| distancia | Calificación del tema del documento afiliación entre 0 y 1. Inferior a una distancia puntuación es mayor del laboral de tema. |

**errores**

| Clave | Descripción |
|:-----|:----|
| Id. | Identificador único de documento de entrada del error se refiere a. |
| Mensaje | Mensaje de error. |

## <a name="next-steps"></a>Pasos siguientes ##

¡Felicidades! Ha completado mediante el análisis de texto en los datos. Ahora puede buscar en usar una herramienta como [Power BI](//powerbi.microsoft.com) para visualizar datos, así como para automatizar sus ideas para dar a una vista en tiempo real de los datos de texto.

Para ver cómo pueden utilizarse capacidades de análisis de texto, como opinión, como parte de un componente, consulte el ejemplo [Emoción robot](http://docs.botframework.com/en-us/bot-intelligence/language/#example-emotional-bot) en el sitio de robot Framework.
