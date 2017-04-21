<properties
    pageTitle="Actualizar a la versión 2 de la API de análisis de texto | Microsoft Azure"
    description="Máquina Azure texto análisis - actualización a la versión 2 de aprendizaje"
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

# <a name="upgrading-to-version-2-of-the-text-analytics-api"></a>Actualizar a la versión 2 de la API de análisis de texto #

Esta guía le guiará por el proceso de actualizar el código de la [primera versión de la API](../machine-learning/machine-learning-apps-text-analytics.md) al uso de la segunda versión. 

Si no ha utilizado la API y para obtener más información, puede **[obtener más información acerca de la API aquí](//go.microsoft.com/fwlink/?LinkID=759711)** o **[siga la Guía de inicio rápido](//go.microsoft.com/fwlink/?LinkID=760860)**. Para referencia técnica, consulte la **[Definición de la API](//go.microsoft.com/fwlink/?LinkID=759346)**.

### <a name="part-1-get-a-new-key"></a>Parte 1. Obtener una nueva clave ###

En primer lugar, debe obtener una nueva clave de API desde el **Portal de Azure**:

1. Vaya al servicio de análisis de texto a través de la [Galería de inteligencia de Cortana](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2). Aquí, también encontrará vínculos a la documentación y ejemplos de código.

1. Haga clic en **registrarse**. Este vínculo le llevará el portal de administración de Azure, donde puede registrarse para el servicio.

1. Seleccione un plan. Puede seleccionar la **capa de libre de 5.000 transacciones por mes**. Como un plan libre, no se cobrará para utilizar el servicio. Debe iniciar sesión en su suscripción de Azure. 

1. Después de registrarse el análisis de texto, se le dará una **Clave API**. Copie esta clave, ya que tendrá al usar los servicios de la API.

### <a name="part-2-update-the-headers"></a>Parte 2. Actualizar los encabezados ###

Actualizar los valores de encabezado enviado tal como se muestra a continuación. Tenga en cuenta que ya no está codificada la clave de cuenta.

**Versión 1**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**Versión 2**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### <a name="part-3-update-the-base-url"></a>Parte 3. Actualizar la dirección URL base ###

**Versión 1**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**Versión 2**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### <a name="part-4a-update-the-formats-for-sentiment-key-phrases-and-languages"></a>Parte 4. Actualizar los formatos de opinión, frases clave e idiomas ###

#### <a name="endpoints"></a>Extremos ####

OBTENER extremos ahora se han descartado, por lo que todas las entradas deben enviarse como una solicitud de publicación. Actualizar los extremos a las que se muestra a continuación.

| |Extremo único de versión 1|Extremo del lote de versión 1|Punto final de la versión 2|
|---|---|---|---|
|Tipo de llamada|Obtener|Exponer|Exponer|
|Opinión|```GetSentiment```|```GetSentimentBatch```|```sentiment```|
|Frases clave|```GetKeyPhrases```|```GetKeyPhrasesBatch```|```keyPhrases```|
|Idiomas|```GetLanguage```|```GetLanguageBatch```|```languages```|

#### <a name="input-formats"></a>Formatos de entrada ####

Nota ahora se acepta ese formato de entrada única, por lo que debe cambiar el formato de cualquier entrada que usaba anteriormente los extremos único documento según corresponda. Entradas no distinguen mayúsculas de minúsculas.

**Versión 1 (lote)**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Versión 2**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="output-from-sentiment"></a>Resultado de opinión ####

**Versión 1**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versión 2**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-key-phrases"></a>Resultado de frases clave ####

**Versión 1**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versión 2**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-languages"></a>Resultado de idiomas ####


**Versión 1**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versión 2**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### <a name="part-4b-update-the-formats-for-topics"></a>Elemento 4b. Actualizar los formatos de temas ###

#### <a name="endpoints"></a>Extremos ####

| |Punto final de la versión 1 | Punto final de la versión 2|
|---|---|---|
|Enviar para la detección de tema (publicación)|```StartTopicDetection```|```topics```|
|Obtener resultados de tema (GET)|```GetTopicDetectionResult?JobId=<jobId>```|```operations/<operationId>```|

#### <a name="input-formats"></a>Formatos de entrada ####

**Versión 1**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Versión 2**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="submission-results"></a>Resultados de envío ####

**Versión 1 (publicación)**

Anteriormente, cuando haya terminado el trabajo, recibirá el siguiente resultado JSON, donde el Id. ¿se anexa a una dirección URL para capturar el resultado.

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**Versión 2 (publicación)**

La respuesta ahora incluye un valor de encabezado siguiente, donde `operation-location` se usa como el punto final para sondear los resultados:

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### <a name="operation-results"></a>Resultados de la operación ####

**Versión 1 (GET)**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versión 2 (GET)**

Como antes, se devuelve **sondear periódicamente el resultado** (del período sugerido es cada minuto) hasta que el resultado. 

Cuando haya terminado la API de temas, una lectura de estado `succeeded` se devolverán. Los resultados se incluirá en el formato que se muestra a continuación:

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### <a name="part-5-test-it"></a>Parte 5. Pruébelo. ###

¡Ahora debería listo! Probar el código con un pequeño ejemplo para asegurarse de que puede procesar correctamente los datos.
