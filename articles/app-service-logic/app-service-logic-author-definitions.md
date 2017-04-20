<properties 
    pageTitle="Crear definiciones de aplicación lógica | Microsoft Azure" 
    description="Obtenga información sobre cómo escribir la definición de JSON para las aplicaciones de lógica" 
    authors="jeffhollan" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jehollan"/>
    
# <a name="author-logic-app-definitions"></a>Definiciones de aplicación de la lógica de autor
Este tema muestra cómo usar las definiciones de [Aplicaciones de lógica de Azure](app-service-logic-what-are-logic-apps.md) , que es un lenguaje JSON descriptiva simple. Si todavía no lo ha hecho todavía, consulte [cómo crear una nueva aplicación lógica](app-service-logic-create-a-logic-app.md) en primer lugar. También puede leer el [material de referencia completa del lenguaje de definición en MSDN](http://aka.ms/logicappsdocs).

## <a name="several-steps-that-repeat-over-a-list"></a>Varios pasos que se repiten en una lista

Puede aprovechar el [tipo de foreach](app-service-logic-loops-and-scopes.md) para repetir los elementos en una matriz de hasta 10 k y realizar una acción para cada uno.

## <a name="a-failure-handling-step-if-something-goes-wrong"></a>Un paso de control de errores si algo va mal

Frecuencia desea poder escribir un *paso de corrección* : algunos lógica que se ejecuta, si **y solo si**, error uno o más de sus llamadas. En este ejemplo, obtenemos datos desde una gran variedad de ubicaciones, pero si se produce un error en la llamada, deseo publicar un mensaje en algún lugar para que puedo rastrear ese error más adelante:  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "postToErrorMessageQueue": {
            "type": "ApiConnection",
            "inputs": "...",
            "runAfter": {
                "readData": ["Failed"]
            }
        }
    },
    "outputs": {}
}
```

Puede hacer que el uso de la `runAfter` propiedad para especificar el `postToErrorMessageQueue` debe ejecutar sólo después de `readData` es **incorrecto**.  También puede ser una lista de posibles valores, así que `runAfter` podría ser `["Succeeded", "Failed"]`.

Por último, porque ahora ha controlado el error, nos ya no marcar la ejecución como **error**. Como puede ver aquí, la ejecución es **correcta** aunque uno error, en el paso debido a escribió en el paso para controlar este error.

## <a name="two-or-more-steps-that-execute-in-parallel"></a>Dos (o más) de los pasos que se ejecutan en paralelo

Tener varios ejecución de acciones en paralelo, el `runAfter` propiedad debe ser equivalente en tiempo de ejecución. 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "branch1": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        },
        "branch2": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        }
    },
    "outputs": {}
}
```

Como puede ver en el ejemplo anterior, ambos `branch1` y `branch2` está establecido para ejecutarse después de `readData`. Como resultado, ambas de estas ramas se ejecutarán en paralelo:

![Paralelo](./media/app-service-logic-author-definitions/parallel.png)

Puede ver que la marca de tiempo para ambas bifurcaciones es idéntico. 

## <a name="join-two-parallel-branches"></a>Unir dos ramas paralelas

Puede combinar dos acciones que se establecieron para ejecutar en paralelo agregando elementos a la `runAfter` propiedad similar a encima.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
    "actions": {
        "readData": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        },
        "branch1": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "branch2": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "join": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "branch1": [
                    "Succeeded"
                ],
                "branch2": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {},
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {}
            },
            "kind": "Http",
            "type": "Request"
        }
    }
}
```

![Paralelo](./media/app-service-logic-author-definitions/join.png)

## <a name="mapping-items-in-a-list-to-some-different-configuration"></a>Asignación de elementos de una lista a una configuración diferente

A continuación, supongamos que queremos obtener contenido completamente diferente según un valor de una propiedad. Podemos crear una asignación de valores a destinos como parámetro:  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
            "type": "Array"
        },
        "destinationMap": {
            "defaultValue": {
                "science": "http://www.nasa.gov",
                "microsoft": "https://www.microsoft.com/en-us/default.aspx",
                "google": "https://www.google.com",
                "robots": "https://en.wikipedia.org/wiki/Robot",
                "NSA": "https://www.nsa.gov/"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            },
            "conditions": []
        },
        "getSpecialPage": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
            },
            "conditions": [{
                "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
            }],
            "forEach": "@body('getArticles').responseData.feed.entries"
        }
    }
}
```

En este caso, obtenemos primero una lista de artículos y, a continuación, el segundo paso busca en un mapa, basado en la categoría que se definió como un parámetro, la dirección URL que desea obtener el contenido de. 

Preste atención aquí dos elementos: el [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) función se usa para comprobar si la categoría coincide con una de las categorías conocidas definidas. En segundo término, una vez que se obtiene la categoría, nos podemos extraer el elemento del mapa con corchetes: `parameters[...]`. 

## <a name="working-with-strings"></a>Trabajar con cadenas

Hay una gran variedad de funciones que puede utilizar para manipular la cadena. Veamos un ejemplo donde tenemos una cadena que se va a pasar a un sistema, pero no estamos seguros de que la codificación de caracteres se tratará correctamente. Una opción es base 64 codificar esta cadena. Sin embargo, para evitar escape en una dirección URL, vamos a reemplazar algunos caracteres. 

También queremos una subcadena de la el orden nombre porque no se utilizan los 5 primeros caracteres.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1",
                "orderer": "NAME=Stèphén__Šīçiłianö"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
            }
        }
    },
    "outputs": {}
}
```

Trabajar al revés:

1. Obtener la [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) de nombre de orderer, volver devuelve el número total de caracteres

2. Restar 5 (ya que le interesará una cadena más corta)

3. Tomar el [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring) . Empezaremos por índice `5` y vaya el resto de la cadena.

4. Convertir esta subcadena a un [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) cadena

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)todos los `+` caracteres con`-`

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)todos los `/` caracteres con`_`

## <a name="working-with-date-times"></a>Trabajar con las fechas

Fecha veces puede ser útil, especialmente cuando intenta extraer datos de un origen de datos que no admite naturalmente **desencadenadores**.  También puede usar las fechas para averiguar cuánto diversos pasos están realizando. 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{parameters('order').id}"
            }
        },
        "timingWarning": {
            "actions" {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
                },
                "runAfter": {}
            }
            "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
        }
    },
    "outputs": {}
}
```

En este ejemplo, hemos estamos extraer la `startTime` del paso anterior. Después de que estamos obteniendo la hora actual y restar un segundo:[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) (podría usar otras unidades de tiempo como `minutes` o `hours`). Por último, podemos comparar estos dos valores. Si la primera es menor que coloca la segunda, a continuación, esto significa que más de un segundo transcurrido desde el orden fue la primero. 

Observe también que podemos utilizar formateadores de cadena para aplicar formato a fechas: en la cadena de consulta se usa [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) para obtener la RFC1123. Todo fecha formato [documentado en MSDN](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow). 

## <a name="using-deployment-time-parameters-for-different-environments"></a>Uso de los parámetros de tiempo de implementación para distintos entornos

Es común tener un ciclo de vida de implementación que tenga un entorno de desarrollo, un entorno de ensayo y, a continuación, en un entorno de producción. En todos estos pueden desee la definición del misma, pero usar bases de datos diferentes, por ejemplo. Del mismo modo, que desea usar la misma definición en muchas áreas diferentes para alta disponibilidad, pero quiere que cada instancia de aplicación lógica para comunicarse con la base de datos de la región. 

Tenga en cuenta que esto es diferente de tomar distintos parámetros en *tiempo de ejecución*, que se debe usar la `trigger()` como resaltadas encima de la función. 

Puede empezar con una definición muy sencilla como este:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

A continuación, en los datos reales `PUT` solicitud para la aplicación de lógica puede proporcionar el parámetro `uri`. Tenga en cuenta que ya no es un valor predeterminado, este parámetro es necesario de la carga de la aplicación de lógica:

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

En cada entorno, a continuación, puede proporcionar un valor diferente para la `connection` parámetro. 

Consulte la [documentación de la API de REST](https://msdn.microsoft.com/library/azure/mt643787.aspx) de todas las opciones disponibles para crear y administrar aplicaciones de lógica. 
