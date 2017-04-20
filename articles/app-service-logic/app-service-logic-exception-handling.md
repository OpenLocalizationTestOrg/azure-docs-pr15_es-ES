<properties
   pageTitle="Control de excepciones de aplicaciones de lógica | Microsoft Azure"
   description="Obtenga información sobre patrones de error y control de excepciones con aplicaciones de Azure lógica"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-error-and-exception-handling"></a>Error de aplicaciones de lógica y excepciones

Lógica de aplicaciones proporciona un amplio conjunto de herramientas y patrones para ayudar a garantizar su integraciones son sólidas y flexibles contra errores.  Uno de los desafíos con cualquier arquitectura de integración es asegurarse de que el tiempo de inactividad o problemas de sistemas dependientes traten adecuadamente.  Lógica de aplicaciones hace que el control de errores en una experiencia de primera clase, que le da las herramientas que necesita para actuar en excepciones y errores dentro de sus flujos de trabajo.

## <a name="retry-policies"></a>Vuelva a intentar directivas

El tipo de excepción y tratamiento de errores más básico es una directiva de reintento.  Esta directiva define si debe reintentar la acción si solicitud inicial agotado o no (cualquier solicitud que ha generado una 429 o respuesta 5xx).  De forma predeterminada, todas las acciones de reintentos 4 adicionales sobre intervalos de 20 segundos.  Por tanto, si la primera solicitud recibido un `500 Internal Server Error` respuesta, el motor de flujo de trabajo se detiene para 20 segundos y el intento de la solicitud de nuevo.  Si después de todos los reintentos la respuesta sigue siendo un error o excepción, el flujo de trabajo continuará y marcar el estado de acción como `Failed`.

Puede configurar directivas de reintento en las **entradas** de una acción determinada.  Una directiva de reintento se puede configurar para intentar intervalos de hasta 4 veces más de 1 hora.  Información detallada sobre las propiedades de entrada puede encontrarse [en MSDN][retryPolicyMSDN].

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

Si desea que la acción HTTP reintentos 4 y esperar 10 minutos entre cada intento tendría la siguiente definición:

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

Para obtener más detalles sobre la sintaxis admitida, ver la [sección de directiva de reintento en MSDN][retryPolicyMSDN].

## <a name="runafter-property-to-catch-failures"></a>Propiedad de RunAfter para detectar errores

Cada acción de aplicación lógica declara las acciones que deben llevar a cabo antes de que se inicia la acción.  Puede considerar que esto como el orden de los pasos del flujo de trabajo.  Este orden se conoce como la `runAfter` propiedad en la definición de acción.  Es un objeto que describe qué acciones y Estados de acción podrían ejecutar la acción.  De forma predeterminada, todas las acciones que se agregan a través del diseñador se establecen en `runAfter` el paso anterior si el paso anterior era `Succeeded`.  Sin embargo, puede personalizar este valor para desencadenar acciones cuando son acciones anteriores `Failed`, `Skipped`, o un conjunto de estos valores posibles.  Si desea agregar un elemento a un tema de Bus de servicio designado después de una acción específica `Insert_Row` falla, debería usar los siguientes `runAfter` configuración:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

Aviso de la `runAfter` propiedad está establecida en aplicará si la `Insert_Row` acción es `Failed`.  Para ejecutar la acción si el estado de la acción es `Succeeded`, `Failed`, o `Skipped` la sintaxis sería:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

>[AZURE.TIP] Acciones que se ejecutan después de que han fallado una acción anterior y finaliza correctamente, se marcará como `Succeeded`.  Este comportamiento significa si correctamente captura todos los errores en un flujo de trabajo la ejecución sí se marcó como `Succeeded`.

## <a name="scopes-and-results-to-evaluate-actions"></a>Ámbitos y resultados para evaluar acciones

Similar a cómo puede ejecutar después de acciones individuales, también puede agrupar acciones juntos dentro de un [ámbito](app-service-logic-loops-and-scopes.md) - que actúen como una agrupación lógica de acciones.  Ámbitos son útiles para organizar sus acciones de aplicación lógica y para realizar evaluaciones agregadas en el estado de un ámbito.  El ámbito de aplicación recibirá el estado una vez han finalizado todas las acciones dentro de un ámbito.  El estado de ámbito se determina con los mismos criterios como una ejecución--si la acción final de una rama ejecución es `Failed` o `Aborted` el estado es `Failed`.

Puede `runAfter` se ha marcado un ámbito `Failed` aplique acciones específicas para los errores que se han producido en el ámbito.  Ejecutar después de que se produce un error en un ámbito le permite crear una única acción para detectar errores si se producen errores de *las* acciones en el ámbito.

### <a name="getting-the-context-of-failures-with-results"></a>Obtener el contexto de errores con resultados

Detectar errores de un ámbito es muy útil, pero también puede ser conveniente el contexto a comprender exactamente qué acciones de error y los errores o los códigos de estado que se han devuelto.  La `@result()` función de flujo de trabajo proporciona el contexto en el resultado de todas las acciones dentro de un ámbito.

`@result()`toma un único parámetro, el nombre del ámbito y devuelve una matriz de todos los resultados de acción desde dentro de ese ámbito.  Estos objetos de acción incluyen los mismos atributos que la `@actions()` envía el objeto, incluyendo la hora de inicio de la acción, hora de finalización de la acción, estado de la acción, entradas de acción, ID de acción y acción.  Puede emparejar fácilmente una `@result()` funciona con un `runAfter` para enviar el contexto de las acciones que no se pudo dentro de un ámbito.

Si desea ejecutar una acción *para cada* acción en un ámbito que `Failed`, puede emparejar `@result()` con una acción de **[Matriz de filtro](../connectors/connectors-native-query.md)** y **[un bucle](app-service-logic-loops-and-scopes.md)** .  Esto le permite filtrar la matriz de resultados de las acciones que no se pudo.  Puede tomar la matriz de resultados filtrados y realizar una acción para cada error con **el bucle** .  Aquí tenemos un ejemplo a continuación, seguido de una explicación detallada.  Este ejemplo enviará una solicitud de HTTP POST con el cuerpo de la respuesta de las acciones que no se pudo dentro del ámbito `My_Scope`.

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Esto es un tutorial detallado de lo que sucede:

1. Acción de **Matriz de filtro** para filtrar el `@result('My_Scope')` para obtener el resultado de todas las acciones en`My_Scope`
1. Condición de la **Matriz de filtro** es cualquier `@result()` elemento con el estado es igual a `Failed`.  Esto filtrará la matriz de todos los resultados de la acción de `My_Scope` a solo una matriz de resultados de la acción error.
1. Realizar una acción **para cada uno** de los resultados de la **Matriz de filtrado** .  Llevará a cabo un acción *para cada* error al resultado de la acción se filtra por encima.
    - Si se ha producido una acción única en el ámbito que no se pudo, las acciones en el `foreach` sólo se ejecutará una vez.  Muchas acciones erróneas haría que una acción por error.
1. Enviar un HTTP POST la `foreach` elemento de cuerpo de la respuesta, o `@item()['outputs']['body']`.  La `@result()` forma elemento es el mismo que el `@actions()` , darles forma y se puede analizar la misma manera.
1. También incluye dos encabezados personalizados con el nombre de la acción error `@item()['name']` y ejecute el error al cliente el identificador de seguimiento `@item()['clientTrackingId']`.

Para referencia, aquí tenemos un ejemplo de un solo `@result()` elemento.  Puede ver el `name`, `body`, y `clientTrackingId` propiedades analizan en el ejemplo anterior.  Debe tener en cuenta que fuera de un `foreach`, `@result()` devuelve una matriz de estos objetos.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/foo/bar does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

Puede utilizar las expresiones anteriores para realizar excepciones diferentes patrones.  Puede elegir ejecutar una sola excepciones acción fuera del ámbito que acepta toda la matriz filtrada de errores y quitar el `foreach`.  También puede incluir otras propiedades útiles de la `@result()` respuesta mostrado arriba.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnóstico de Azure y telemetría

Los modelos anteriores son una buena opción a controlar errores y excepciones dentro de una ejecución, pero también puede identificar y responder a los errores independientes de la ejecución de sí mismo.  [Diagnósticos de Azure](app-service-logic-monitor-your-logic-apps.md) proporciona una forma sencilla de enviar todos los eventos de flujo de trabajo (incluidos todos los Estados de ejecutar y acción) a una cuenta de almacenamiento de Azure o a un concentrador de evento de Azure.  Puede supervisar los registros y métricas o publicarlas en cualquier herramienta de supervisión que lo prefiere, para evaluar los Estados de ejecución.  Una opción posible es transmitir todos los eventos a través de concentrador de evento de Azure en [El análisis de secuencia](https://azure.microsoft.com/services/stream-analytics/).  En el análisis de secuencia puede escribir consultas directo quitarlas cualquier anomalías, medias o errores de los registros de diagnóstico.  Análisis de secuencia puede enviar fácilmente a otros orígenes de datos como colas, temas, SQL, DocumentDB y Power BI.

## <a name="next-steps"></a>Pasos siguientes
- [Vea cómo un cliente integrado sólido control de errores con aplicaciones de lógica](app-service-logic-scenario-error-and-exception-handling.md)
- [Buscar más ejemplos de aplicaciones de lógica y escenarios](app-service-logic-examples-and-scenarios.md)
- [Aprenda a crear implementaciones automatizadas de aplicaciones de lógica](app-service-logic-create-deploy-template.md)
- [Diseñar e implementar aplicaciones de lógica de Visual Studio](app-service-logic-deploy-from-vs.md)


<!-- References -->
[retryPolicyMSDN]: https://msdn.microsoft.com/library/azure/mt643939.aspx#Anchor_9