<properties
   pageTitle="Aplicaciones de lógica bucles, los ámbitos y Debatching | Microsoft Azure"
   description="Bucle de aplicación lógica, ámbito y debatching conceptos"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# <a name="logic-apps-loops-scopes-and-debatching"></a>Aplicaciones de lógica bucles, los ámbitos y Debatching
  
>[AZURE.NOTE] Esta versión de este artículo se aplica a esquema lógica aplicaciones 2016 04-01-vista previa y versiones posteriores.  Conceptos son similares para esquemas más antiguos, pero ámbitos solo están disponibles para este esquema y posterior.
  
## <a name="foreach-loop-and-arrays"></a>Bucle y matrices
  
Lógica de aplicaciones le permite recorrer un conjunto de datos y realizar una acción para cada elemento.  Esto es posible a través de la `foreach` acción.  En el diseñador, puede especificar para agregar un bucle for each.  Después de seleccionar la matriz que desea iteración, puede empezar a agregar acciones.  Actualmente están limitados a solo una acción por bucle, pero esta limitación se eliminarán en las próximas semanas.  Una vez en el bucle puede empezar a especificar qué debe ocurrir en cada valor de la matriz.

Si usa la vista código, puede especificar un bucle for each como debajo.  Este es un ejemplo de un bucle for each que envía un correo electrónico para cada dirección de correo electrónico que contiene 'microsoft.com':

```
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  A `foreach` acción puede repetir sobre matrices hasta 5.000 filas.  Cada iteración puede ejecutar en paralelo, por lo que puede ser necesario agregar los mensajes a una cola si se requiere control de flujo.
  
## <a name="until-loop"></a>Hasta el bucle
  
  Puede realizar una acción o serie de acciones hasta que se cumpla una condición.  El escenario más común para este llama a un extremo hasta que llegue la respuesta que busca.  En el diseñador, puede especificar para agregar un hasta el bucle.  Después de agregar acciones dentro del bucle, puede establecer la condición de salida, así como el bucle límites.  Hay un retraso de 1 minuto entre ciclos de bucle.
  
  Si usa la vista código, puede especificar un hasta bucle como las siguientes.  Esto es un ejemplo de llamar a un extremo HTTP hasta el cuerpo de la respuesta tiene el valor 'Completada'.  Finalizará cuando alguno 
  
  * Respuesta HTTP tiene el estado de 'Completada'
  * Ha intentado para 1 hora
  * Ha repetido 100 veces
  
  ```
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed'),
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn y Debatching

A veces un desencadenador puede aparecer una matriz de elementos que desea debatch e iniciar un flujo de trabajo por elemento.  Esto se puede conseguir a través de la `spliton` comando.  De forma predeterminada, si su swagger desencadenador especifica una carga que es una matriz, un `spliton` se agregará e iniciar una ejecución por elemento.  SplitOn sólo pueden agregarse a un desencadenador.  Puede ser manualmente configurado o se reemplaza en la vista código de definición.  Actualmente puede debatch SplitOn matrices hasta 5.000 elementos.  No puede tener un `spliton` e implementar también el modelo de respuesta sincrónica.  Los flujos de trabajo que tiene un `response` acción además `spliton` ejecutará de forma asincrónica y enviar de forma inmediata `202 Accepted` respuesta.  

SplitOn puede especificarse en la vista código como el siguiente ejemplo.  Este recibe una matriz de elementos y debatches en cada fila.

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequency": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>Ámbitos

Es posible agrupar una serie de manera conjunta con un ámbito de acciones.  Esto es especialmente útil para implementar el control de excepciones.  En el diseñador puede agregar un nuevo ámbito y empezar a agregar las acciones dentro de ella.  Puede definir ámbitos en la vista código como el siguiente:


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
