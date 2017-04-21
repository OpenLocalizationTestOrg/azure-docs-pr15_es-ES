<properties 
    pageTitle="Cómo configurar los extremos de aprendizaje del equipo de Azure en análisis de secuencia | Microsoft Azure" 
    description="Funciones definidas por el usuario de idioma de la máquina en el análisis de secuencia"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"
/>

# <a name="machine-learning-integration-in-stream-analytics"></a>Integración de aprendizaje en el análisis de secuencia de máquina

Análisis de secuencia es compatible con las funciones definidas por el usuario que llamar a los extremos de aprendizaje del equipo de Azure. Compatibilidad con la API de REST de esta característica se detalla en la [biblioteca de la API de REST de análisis de secuencia](https://msdn.microsoft.com/library/azure/dn835031.aspx). Este artículo proporciona información complementaria necesaria para la implementación correcta esta función de análisis de la secuencia. Un tutorial también se ha registrado y está disponible [aquí](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Información general: Terminología de Azure el aprendizaje

Aprendizaje de Microsoft Azure máquina proporciona una herramienta de colaboración, arrastrar y colocar que puede usar para crear, probar e implementar soluciones de análisis predictiva en los datos. Esta herramienta se denomina el *Studio de aprendizaje del equipo de Azure*. El studio se usa para interactuar con los recursos de aprendizaje de equipo y fácilmente generar, probar y trabajemos en el diseño. Estos recursos y sus definiciones se encuentran por debajo.

- **Área de trabajo**: el *área de trabajo* es un contenedor que contiene todos los otros recursos de aprendizaje de máquina juntos en un contenedor de administración y control.
- **Ensayo**: *experimentación* creados por científicos datos utilizar conjuntos de datos e instruir a un modelo de aprendizaje del equipo.
- **Extremo**: *extremos* son el objeto de aprendizaje de Azure equipo usado para tomar características como entrada, aplicar un modelo de aprendizaje de máquina especificada y devolver resultados puntuado.
- **Puntuación servicioWeb**: una *puntuación servicioWeb* es una colección de extremos como se mencionó anteriormente.

Cada extremo tiene API de ejecución por lotes y ejecución sincrónica. Análisis de secuencia usa la ejecución sincrónica. El servicio específico con el nombre de un [Servicio de convocatoria y respuesta](../machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs) en AzureML studio.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Máquina necesarios para trabajos de análisis de secuencia de recursos de aprendizaje

Para los fines de análisis de secuencia de procesamiento de trabajo, un punto final de la solicitud y respuesta, un [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md#get-an-azure-machine-learning-authorization-key)y una definición de swagger son todas las necesarias para ejecución correcta. Análisis de secuencia tiene un extremo adicional que crea la dirección url de extremo de swagger, busca la interfaz y devuelve una definición de UDF predeterminado al usuario.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Configurar una secuencia analíticas y aprendizaje UDF a través de la API de REST

Mediante las API de REST puede configurar su trabajo para llamar a las funciones de idioma de la máquina de Azure. Los pasos son los siguientes:

1. Crear un análisis de flujo de trabajo
2. Definir una entrada
3. Definir un resultado
4. Crear una función definida por el usuario (UDF)
5. Escribir una transformación de análisis de secuencia que llama al UDF
6. Iniciar el trabajo

## <a name="creating-a-udf-with-basic-properties"></a>Crear un archivo UDF con propiedades básicas

Por ejemplo, el siguiente código de ejemplo crea una UDF escalar denominada *newudf* que se enlaza un extremo de aprendizaje del equipo de Azure. Nota que se encuentra el *punto final* (URI del servicio) en la página de Ayuda de la API para el servicio seleccionado y la *apiKey* se encuentra en la página principal de servicios.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

Cuerpo de la solicitud de ejemplo:  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
````

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Llamar RetrieveDefaultDefinition extremo predeterminado UDF

Una vez que la estructura que se crea UDF es necesaria la definición completa del archivo UDF. El extremo de RetreiveDefaultDefinition le ayuda a obtener la definición predeterminada para una función escalar enlazado a un extremo de aprendizaje del equipo de Azure. La carga a continuación, debe obtener la definición de UDF predeterminada para una función escalar que está enlazada a un extremo de aprendizaje del equipo de Azure. Como ya se ha proporcionado durante SUPERPONER solicitud no especifica el extremo real. Análisis de secuencia llama el extremo proporcionado en la solicitud si se proporciona explícitamente. En caso contrario, utiliza el originalmente al que hace referencia. Aquí la toma UDF una única cadena de parámetro (una oración) y devuelve un único resultado de tipo cadena que indica la etiqueta "opinión" para esa oración.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Cuerpo de la solicitud de ejemplo:  

````
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
````

Un ejemplo de salida de esta busque algo gustaría debajo.  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="patch-udf-with-the-response"></a>Revisión de UDF con la respuesta 

Ahora debe revisarse UDF con la respuesta anterior, como se muestra a continuación.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Cuerpo de la solicitud (salida de RetrieveDefaultDefinition):

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementar transformación de análisis de secuencia para llamar a UDF

Ahora la consulta UDF (aquí denominado scoreTweet) para cada evento de entrada y escribir una respuesta para ese evento en un resultado.  

````
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
````


## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)
