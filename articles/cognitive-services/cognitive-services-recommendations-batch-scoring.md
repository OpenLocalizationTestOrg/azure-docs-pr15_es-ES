
<properties
    pageTitle="Obtener recomendaciones en lotes: API de recomendaciones de aprendizaje de máquina | Microsoft Azure"
    description="Máquina Azure recomendaciones--obtener recomendaciones en lotes de aprendizaje"
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
    ms.date="08/17/2016"
    ms.author="luisca"/>

# <a name="get-recommendations-in-batches"></a>Obtener recomendaciones por lotes

>[AZURE.NOTE] Obtener recomendaciones en lotes es más complejo que obtener recomendaciones de uno en uno. Compruebe las API para obtener información sobre cómo obtener recomendaciones para una única petición:

> [Recomendaciones de elemento a otro](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4)<br>
> [Recomendaciones de elemento de usuario](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)
>
> Puntuación lote solo funciona en las versiones que se crearon después 21 de julio de 2016.


Hay situaciones en que necesita obtener recomendaciones para más de un elemento a la vez. Por ejemplo, es posible que interesado en crear una caché de recomendaciones o incluso analizar los tipos de recomendaciones que está recibiendo.

Lote puntuación operaciones, como llamamos, son asincrónica. Debe enviar la solicitud, espere a que termine la operación y, a continuación, recopilar los resultados.  

Para ser más precisos, estos son los pasos a seguir:

1.  Crear un contenedor de almacenamiento de Azure si aún no tiene una.
2.  Cargar un archivo de entrada que describe cada uno de sus solicitudes de recomendación con el almacenamiento de blobs de Windows Azure.
3.  Iniciar el proceso de puntuación.
4.  Espere a que la operación asincrónica Finalizar.
5.  Cuando haya terminado la operación, recopilar los resultados de almacenamiento de blobs.

Vamos a través de cada uno de estos pasos.

## <a name="create-a-storage-container-if-you-dont-have-one-already"></a>Crear un contenedor de almacenamiento si aún no tiene una

Vaya al [portal de Azure](https://portal.azure.com) y cree una nueva cuenta de almacenamiento si aún no tiene una. Para ello, vaya a **nuevo** > **datos** + **almacenamiento** > **Cuenta de almacenamiento**.

Después de que tiene una cuenta de almacenamiento, debe crear los contenedores de blobs de Windows donde se guardará la entrada y salida de la ejecución del lote.

Cargar un archivo de entrada que describe cada uno de su recomendación solicitudes de almacenamiento de blobs, vamos a llame a input.json de archivo.
Una vez un contenedor, debe cargar un archivo que describe cada una de las solicitudes que debe llevar a cabo desde el servicio de recomendaciones.

Un lote puede realizar un único tipo de solicitud de una compilación concreta. Explicaremos cómo definir esta información en la siguiente sección. Por ahora, supongamos que se va a realizar recomendaciones de elemento de una compilación concreta. El archivo de entrada, a continuación, contiene la información de entrada (en este caso, los elementos de valor de inicialización) para cada una de las solicitudes.

Este es un ejemplo del aspecto del archivo input.json:

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }

Como puede ver, el archivo es un archivo JSON, donde cada una de las solicitudes tiene la información necesaria enviar una solicitud de recomendaciones. Crear un archivo JSON similar para las solicitudes que debe cumplir y copiarlo en el contenedor que acaba de crear en el almacenamiento de blobs.

## <a name="kick-start-the-batch-job"></a>Iniciar el proceso

El siguiente paso es enviar una nueva tarea por lotes. Para obtener más información, consulte la [referencia de la API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/).

El cuerpo de la solicitud de la API debe definir las ubicaciones donde necesitan almacenarse la entrada y salida archivos de error. También debe definir las credenciales necesarias tener acceso a las ubicaciones. Además, debe especificar algunos parámetros que se aplican a todo el lote (el tipo de recomendaciones para solicitar la compilación de modelo o para usar el número de resultados por llamada y así sucesivamente).

Este es un ejemplo del aspecto de cuerpo de la solicitud:

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

Aquí algunos aspectos importantes que tenga en cuenta:

-   Actualmente, **authenticationType** siempre debe establecerse a **PublicOrSas**.

-   Debe obtener un token de firma de acceso compartido (SA) para permitir que la API de recomendaciones leer y escribir su cuenta de almacenamiento de blobs de/a. Encontrará más información sobre cómo generar tokens SA en [la página de la API de recomendaciones](../storage/storage-dotnet-shared-access-signature-part-1.md).

-   La única **apiName** que es compatible actualmente es **ItemRecommend**, que se usa para obtener recomendaciones de elemento a otro. Procesamiento por lotes no admite actualmente recomendaciones de elemento de usuario.

## <a name="wait-for-the-asynchronous-operation-to-finish"></a>Espere finalizar la operación asincrónica

Al iniciar la operación por lotes, la respuesta devuelve el encabezado de la ubicación de la operación que le ofrece la información necesaria realizar un seguimiento de la operación.
Realizar un seguimiento de la operación con la [API de estado operación recuperar]( https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da), igual que para realizar el seguimiento de la operación de una operación de compilación.

## <a name="get-the-results"></a>Obtener los resultados

Cuando haya terminado la operación, suponiendo que no ha habido errores, puede recopilar los resultados de la salida de almacenamiento de blobs.

El ejemplo siguiente muestra la apariencia que podría tener el resultado. En este ejemplo, le mostraremos los resultados de un lote con solo dos solicitudes (por razones de brevedad).

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## <a name="learn-about-the-limitations"></a>Obtenga más información sobre las limitaciones

-   Proceso solo se puede llamar por suscripción a la vez.
-   Un archivo de entrada de trabajo por lotes no puede ser más de 2 MB.
