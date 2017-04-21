<properties
    pageTitle="Máquina API de aprendizaje: Análisis texto | Microsoft Azure"
    description="API de análisis de Microsoft máquina aprendizaje texto puede usarse para analizar texto estructurado para análisis de opinión, la extracción de frase clave, la detección de idioma y detección de tema."
    services="machine-learning"
    documentationCenter=""
    authors="onewth"
    manager="jhubbard"
    editor="cgronlun"/> 

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="onewth"/>


# <a name="machine-learning-apis-text-analytics-for-sentiment-key-phrase-extraction-language-detection-and-topic-detection"></a>API de aprendizaje de máquina: Análisis de texto de opinión, clave extracción frase, la detección de idioma y la detección de tema

>[AZURE.NOTE] Esta guía es para la versión 1 de la API. Para la versión 2, [**consulte este documento**](../cognitive-services/cognitive-services-text-analytics-quick-start.md). Versión 2 ahora es la versión preferida de esta API.

## <a name="overview"></a>Información general

La API de análisis de texto es un conjunto de texto análisis [servicios web](https://datamarket.azure.com/dataset/amla/text-analytics) creados con el aprendizaje Azure. La API de puede usarse para analizar texto estructurado para tareas como análisis de opinión, la extracción de frase clave, la detección de idioma y detección de tema. No hay datos de formación es necesaria para usar esta API: simplemente pase los datos de texto. Esta API emplea técnicas de procesamiento de lenguaje de natural avanzada para ofrecer mejor en predicciones de clase.

Puede ver el análisis de texto en acción en nuestro [sitio de demostración](https://text-analytics-demo.azurewebsites.net/), donde también encontrará [ejemplos](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) sobre cómo implementar el análisis de texto en C# y Python.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 

---

## <a name="sentiment-analysis"></a>Análisis de opinión

La API devuelve una puntuación numérica entre 0 y 1. Puntuación cerca de 1 indica opinión positivo, mientras puntuaciones cerca de 0 indican opinión negativo. Calificación de opinión se genera utilizando técnicas de clasificación. Las características de entrada al clasificador incluyen n-g, características generadas a partir de las etiquetas de la parte de la oración e incrustaciones de word. Actualmente, inglés es el único lenguaje admitido.
 
## <a name="key-phrase-extraction"></a>Extracción de frase clave

La API devuelve una lista de cadenas que denotan los puntos clave de la conversación en el texto de entrada. Se emplean técnicas del Kit de herramientas procesamiento de lenguaje Natural sofisticado de Microsoft Office. Actualmente, inglés es el único lenguaje admitido.

## <a name="language-detection"></a>Detección de idioma

La API devuelve el idioma detectado y una puntuación numérica entre 0 y 1. Puntuación cerca 1 indica certeza 100% que el idioma identificado es true. Un total de 120 idiomas son compatibles.

## <a name="topic-detection"></a>Detección de tema

Esta es una API reciente que devuelve la parte superior detectado temas para obtener una lista de enviar registros de texto. Un tema se identifica con una frase clave, que puede ser una o más personas relacionadas palabras. Esta API requiere un mínimo de 100 registros de texto se envíen, pero está diseñada para detectar temas a través de cientos a miles de registros. Tenga en cuenta que esta API cargos 1 transacción por cada registro de texto que ha enviado. La API está diseñada para funcionar bien con texto escrito humano cortas como revisiones y comentarios de los usuarios.

---

## <a name="api-definition"></a>Definición de la API

### <a name="headers"></a>Encabezados

Asegúrese de que incluye los encabezados correctos en la convocatoria, que debe ser como sigue:

    Authorization: Basic <creds>
    Accept: application/json
               
    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Puede encontrar la clave de cuenta desde su cuenta en el [Mercado de datos de Azure](https://datamarket.azure.com/account/keys). Tenga en cuenta que JSON actualmente solo se acepta para formatos de entrada y salidos. XML no es compatible.

---

## <a name="single-response-apis"></a>API de respuesta único

### <a name="getsentiment"></a>GetSentiment

**DIRECCIÓN URL** 

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Solicitud de ejemplo**

En la llamada a continuación, se precisa análisis de opinión la frase "Hola a todos":

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Devolverá una respuesta como sigue:

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
        "Score":1.0
    }

---

### <a name="getkeyphrases"></a>GetKeyPhrases

**DIRECCIÓN URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Solicitud de ejemplo**

En la llamada a continuación, se precisa las frases clave que se encuentra en el texto "Era un hotel maravilloso permanecer en, con decoración único y descriptivo personal":

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
    Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Devolverá una respuesta como sigue:

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
      "KeyPhrases":[
        "wonderful hotel",
        "unique decor",
        "friendly staff"
      ]
    }
 
---

### <a name="getlanguage"></a>GetLanguage

**DIRECCIÓN URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**Solicitud de ejemplo**

En la llamada GET a continuación, se precisa para la opinión de las frases clave en el texto *Hola a todos*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
    Text=Hello+World

Devolverá una respuesta como sigue:

    {
      "UnknownLanguage": false,
      "DetectedLanguages": [{
        "Name": "English",
        "Iso6391Name": "en",
        "Score": 1.0
      }]
    }

**Parámetros opcionales**

`NumberOfLanguagesToDetect`es un parámetro opcional. El valor predeterminado es 1.

---

## <a name="batch-apis"></a>API de proceso por lotes

El servicio de análisis de texto le permite hacer opinión y extracción frase clave en modo por lotes. Tenga en cuenta que cada uno de los registros anotados recuentos como una transacción. Por ejemplo, si solicitar opinión 1000 registros en una sola llamada, 1000 transacciones se deduce.

Observe que los identificadores introducidos en el sistema son los identificadores devueltos por el sistema. El servicio web no comprueba que estos identificadores son únicos. Es responsabilidad del llamador para verificar exclusividad. 


### <a name="getsentimentbatch"></a>GetSentimentBatch

**DIRECCIÓN URL** 

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Solicitud de ejemplo**

En la llamada de entrada a continuación, se precisa para los mensajes de las frases "Hola a todos", "Foo Hola a todos" y "Hola Mi mundo" en el cuerpo de la solicitud:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Cuerpo de la solicitud:

    {"Inputs":
    [
        {"Id":"1","Text":"hello world"},
        {"Id":"2","Text":"hello foo world"},
        {"Id":"3","Text":"hello my world"},
    ]}

En la respuesta a continuación, obtener la lista de calificaciones asociado con los identificadores de texto:

    {
      "odata.metadata":"<url>", 
      "SentimentBatch":
      [
        {"Score":0.9549767,"Id":"1"},
        {"Score":0.7767222,"Id":"2"},
        {"Score":0.8988889,"Id":"3"}
      ],  
      "Errors":[]
    }


---

### <a name="getkeyphrasesbatch"></a>GetKeyPhrasesBatch

**DIRECCIÓN URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Solicitud de ejemplo**

En este ejemplo, se precisa para la lista de mensajes de las frases clave en los textos siguientes: 

* "Era un hotel maravilloso permanecer en, con decoración único y descriptivo personal"
* "Era una conferencia de compilación sorprendentes, con las conversaciones muy interesantes"
* "El tráfico terrible, pasó tres horas en el aeropuerto"

Esta solicitud se realiza como una llamada de entrada hasta el punto final:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Cuerpo de la solicitud:

    {"Inputs":
    [
        {"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
        {"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
        {"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
    ]}

En la respuesta a continuación, obtener la lista de frases clave asociado con los identificadores de texto:

    { "odata.metadata":"<url>",
        "KeyPhrasesBatch":
        [
           {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
           {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
           {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
        ],
        "Errors":[]
    }

---

### GetLanguageBatch

In the POST call below, we are requesting language detection for two text inputs:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

Request body:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

This returns the following response, where English is detected in the first input and French in the second input:

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "Inglés",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "Francés",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

---

## <a name="topic-detection-apis"></a>API de detección de tema

Esta es una API reciente que devuelve la parte superior detectado temas para obtener una lista de enviar registros de texto. Un tema se identifica con una frase clave, que puede ser una o más personas relacionadas palabras. Tenga en cuenta que esta API cargos 1 transacción por cada registro de texto que ha enviado.

Esta API requiere un mínimo de 100 registros de texto se envíen, pero está diseñada para detectar temas a través de cientos a miles de registros.


### <a name="topics--submit-job"></a>Temas: enviar trabajo

**DIRECCIÓN URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**Solicitud de ejemplo**


En la llamada de entrada a continuación, se precisa temas para un conjunto de 100 artículos, donde se muestran los artículos de entrada y el apellido y dos StopPhrases se incluyen.

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

Cuerpo de la solicitud:

    {"Inputs":[
        {"Id":"1","Text":"I loved the food at this restaurant"},
        ...,
        {"Id":"100","Text":"I hated the decor"}
    ],
    "StopPhrases":[
        "restaurant", “visitor"
    ]}

En la respuesta a continuación, obtendrá el ID para el trabajo enviado:

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

Una lista de palabra único o múltiples frases de word que no se deben devolver como temas. Puede utilizarse para filtrar los temas muy genéricos. Por ejemplo, en un conjunto de datos sobre hotel críticas, "hotel" y "hostel" puede ser razonable detener frases.  

### <a name="topics--poll-for-job-results"></a>Resultados de sondeo de trabajo de temas:

**DIRECCIÓN URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**Solicitud de ejemplo**

Pase el Id. devuelta en el paso 'Enviar trabajo' para capturar los resultados. Se recomienda que llame a este extremo cada minuto hasta que el estado = 'Completado' en la respuesta. Tardará unos 10 minutos para una tarea completada o más para trabajos con varios miles de registros.

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


Mientras se está procesando, la respuesta será como sigue:

    {
        "odata.metadata":"<url>",
        "Status":"Running",
        "TopicInfo":[],
        "TopicAssignment":[],
        "Errors":[]
    }


La API devuelve el resultado en formato JSON en el siguiente formato:

    {
        "odata.metadata":"<url>",
        "Status":"Finished",
        "TopicInfo":[
        {
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Score":8.0,
            "KeyPhrase":"food"
        },
        ...
        {
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Score":6.0,
            "KeyPhrase":"decor"
            }
        ],
        "TopicAssignment":[
        {
            "Id":"1",
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Distance":0.7809
        },
        ...
        {
            "Id":"100",
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Distance":0.8034
        }
        ],
        "Errors":[]


Las propiedades de cada parte de la respuesta son los siguientes:

**Propiedades de TopicInfo**

| Clave | Descripción |
|:-----|:----|
| TopicId | Un identificador único para cada tema. |
| Puntuación | Recuento de registros asignados al tema. |
| KeyPhrase | Una palabra o frase para el tema resumir. Puede ser 1 o varias palabras. |

**Propiedades de TopicAssignment**

| Clave | Descripción |
|:-----|:----|
| Id. | Identificador para el registro. Es igual al identificador de incluir en la entrada. |
| TopicId | El identificador del tema que se ha asignado el registro a. |
| Distancia | Confianza de que el registro pertenece al tema. Distancia más cerca de cero indica confianza superior. |
