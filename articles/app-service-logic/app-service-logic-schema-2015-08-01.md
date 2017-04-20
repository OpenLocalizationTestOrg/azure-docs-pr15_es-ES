<properties 
    pageTitle="Nuevo esquema versión 2015-08-01-preview" 
    description="Obtenga información sobre cómo escribir la definición de JSON para la versión más reciente de aplicaciones de lógica" 
    authors="stepsic-microsoft-com" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="stepsic"/>
    
# <a name="new-schema-version-2015-08-01-preview"></a>Nuevo esquema versión 2015-08-01-preview

El nuevo esquema y API versión para las aplicaciones de lógica tiene una serie de mejoras que mejorar la confiabilidad y facilidad de uso de aplicaciones de lógica. Hay diferencias clave 4:

1. El tipo de acción **APIApp** se ha actualizado a un nuevo tipo de acción de **APIConnection** .
2. **Repita** ha cambiado a **Foreach**.
3. La aplicación de la API de **Escucha HTTP** ya no es necesaria.
4. Llamar a los flujos de trabajo secundarios, usa un esquema de nuevo.

## <a name="1-moving-to-api-connections"></a>1. mover conexiones API

El cambio más importante es que ya no necesita implementar aplicaciones de la API en la suscripción de Azure usar API. Existen 2 formas que puede usar las API de:
* API administrada
* La API de Web personalizado

Cada uno de estos se administra ligeramente diferente porque su administración y modelos de hospedaje son diferentes. Una ventaja de este modelo es que ya no está limitado a los recursos que se implementan en el grupo de recursos. 

### <a name="managed-apis"></a>API administradas

Hay una serie de API administradas por Microsoft en su nombre, como Office 365, Salesforce, Twitter, etcetera FTP... Algunas de estas API administrada pueden usarse como-es, como Bing traducir, mientras que otras necesitan configuración. Esta configuración se denomina una *conexión*.

Por ejemplo, cuando utiliza Office 365, debe crear una conexión que contiene el token de inicio de sesión de Office 365. Este token se guardarán segura y se actualizará para que su aplicación lógica siempre puede llamar a la API de Office 365. Como alternativa, si desea conectarse al servidor SQL o FTP, debe crear una conexión que tiene la cadena de conexión. 

Dentro de la definición de estas acciones se denominan `APIConnection`. Aquí tiene un ejemplo de una conexión que llama a Office 365 para enviar un correo electrónico:

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

La parte de las entradas que sea única para conexiones de API es el `host` objeto. Esta página contiene dos partes: `api` y `connection`.

La `api` tiene la dirección URL de donde que API administrada se hospeda el tiempo de ejecución. Puede ver todas las API administradas disponibles para usted llamando `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Cuando se utiliza una API, puede o no tener los **parámetros de conexión** definidos. Si no lo está sin **conexión** es necesario. Si es así, tendrá que crear una conexión. Al crear la conexión tendrá el nombre que elija, y luego hacer referencia en el `connection` objeto dentro de la `host` objeto. Para crear una conexión en un grupo de recursos, llame al:

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Con el siguiente cuerpo:


```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
    "parameterValues" : {
        "accountName" : "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location" : "{Logic app's location}"
}
```

### <a name="deploying-managed-apis-in-an-azure-resource-manager-template"></a>Implementar API administradas en una plantilla de administrador de recursos de Azure

Puede crear una aplicación completa en una plantilla de ARM siempre y cuando no requiere un inicio de sesión interactivo. Si requiere inicio de sesión, puede configurar todo el contenido con la plantilla ARM, pero todavía tendrá que visitar el portal para autorizar las conexiones. 

```
    "resources": [{
        "apiVersion": "2015-08-01-preview",
        "name": "azureblob",
        "type": "Microsoft.Web/connections",
        "location": "[resourceGroup().location]",
        "properties": {
            "api": {
                "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
            },
            "parameterValues": {
                "accountName": "[parameters('storageAccountName')]",
                "accessKey": "[parameters('storageAccountKey')]"
            }
        }
    }, {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-08-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[resourceId('Microsoft.Web/connections', 'azureblob')]"
        ],
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
                "actions": {
                    "Create_file": {
                        "type": "apiconnection",
                        "inputs": {
                            "host": {
                                "api": {
                                    "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                                },
                                "connection": {
                                    "name": "@parameters('$connections')['azureblob']['connectionId']"
                                }
                            },
                            "method": "post",
                            "queries": {
                                "folderPath": "[concat('/',parameters('containerName'))]",
                                "name": "helloworld.txt"
                            },
                            "body": "@decodeDataUri('data:,Hello+world!')",
                            "path": "/datasets/default/files"
                        },
                        "conditions": []
                    }
                },
                "contentVersion": "1.0.0.0",
                "outputs": {},
                "parameters": {
                    "$connections": {
                        "defaultValue": {},
                        "type": "Object"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "Recurrence",
                        "recurrence": {
                            "frequency": "Day",
                            "interval": 1
                        }
                    }
                }
            },
            "parameters": {
                "$connections": {
                    "value": {
                        "azureblob": {
                            "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                            "connectionName": "azureblob",
                            "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                        }

                    }
                }
            }
        }
    }]
```

Puede ver en este ejemplo que las conexiones son recursos simplemente normales que se encuentran en el grupo de recursos. Referencia a la managedAPIs disponibles en su suscripción.

### <a name="your-custom-web-apis"></a>La API de Web personalizado

Si utiliza su propia API (específicamente, que no administrado de Microsoft), debe usar la acción **HTTP** integrada para llamar a ellos. Para tener una experiencia ideal, debería exponer un extremo de swagger de la API. Esto le permitirá el Diseñador de la aplicación de lógica representar las entradas y los resultados de la API. Sin un swagger, el diseñador solo podrá mostrar las entradas y salidas como opacos objetos JSON.

Aquí tenemos un ejemplo que muestra la nueva `metadata.apiDefinitionUrl` propiedad:
```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata" : {
              "apiDefinitionUrl" : "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method" : "GET"
            }
        }
    }
}
```

Si hospeda la API de Web en la **Aplicación de servicio** , a continuación, muestra automáticamente hacia arriba en la lista de acciones disponibles en el Diseñador de. Si no es así, tendrá que pegar directamente en la dirección URL. Debe ser no autenticado el extremo swagger para ser utilizable dentro del Diseñador de aplicaciones de lógica (aunque puede proteger la API con los métodos que son compatibles con el Swagger).

### <a name="using-your-already-deployed-api-apps-with-2015-08-01-preview"></a>Usar las aplicaciones de la API ya implementadas con 2015-08-01-vista previa

Si ha implementado una aplicación API previamente, puede llamar a través de la acción de **HTTP** .

Por ejemplo, si utiliza Dropbox a los archivos de la lista, es posible que algo parecido a esto en la definición de versión del esquema de **2014 12-01-vista previa** :

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Puede crear la acción equivalente de HTTP como debajo (la sección de parámetros de la permanece de definición de aplicación lógica sin cambios):

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata" : {
              "apiDefinitionUrl" : "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method" : "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Recorrer las propiedades de uno a uno:

| Propiedad de acción |  Descripción |
| --------------- | -----------  |
| `type` | `Http`En lugar de`APIapp` |
| `metadata.apiDefinitionUrl` | Si desea usar esta acción en el Diseñador de aplicaciones de lógica, desea incluir el extremo de metadatos. Esto se construye desde:`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Esto se construye desde:`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Siempre`POST` |
| `inputs.body` | Idénticos a los parámetros de aplicación api | 
| `inputs.authentication` | Idéntica a la autenticación de aplicación api |

Este método debería funcionar para todas las acciones de aplicación de la API. Sin embargo, tenga en cuenta que estas aplicaciones API anterior ya no son compatibles y, a continuación, debe mover a una de las otras dos opciones anteriores (una API administrada o la API de Web personalizados de hospedaje).

## <a name="2-repeat-renamed-to-foreach"></a>2. Repetir cambia a Foreach

Para la versión anterior de esquema hemos recibido una gran cantidad de comentarios de los clientes que **Repetir** era confusa y no capturar correctamente que realmente era un bucle for each. Por tanto, nos hemos cambiado a **Foreach**. Por ejemplo:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

Ahora debería escribirse como:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

Anteriormente, la función `@repeatItem()` se ha usado para hacer referencia al elemento actual que se repite sobre. Esto se ha simplificado justo `@item()`. 

### <a name="referencing-the-outputs-of-the-foreach"></a>Hacer referencia a los resultados de Foreach
Para simplificar aún más, no se ajustará los resultados de **Foreach** acciones en un objeto denominado **repeatItems**. Esto significa que, mientras que los resultados de la repetición anterior eran:

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Ahora será:

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Al hacer referencia a estos resultados, para llegar al cuerpo de la acción tendrá que hacer:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "repeat" : "@outputs('pingBing').repeatItems",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@repeatItem().outputs.body"
            }
        }
    }
}
```

Ahora puede realizar en su lugar:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "foreach" : "@outputs('pingBing')",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@item().outputs.body"
            }
        }
    }
}
```

Con estos cambios, las funciones `@repeatItem()`, `@repeatBody()` y `@repeatOutputs()` se quitan.

## <a name="3-native-http-listener"></a>3. nativo escucha HTTP 
Las capacidades de escucha HTTP están integradas, por lo que ya no es necesario implementar una aplicación de API de escucha HTTP. Obtenga información sobre [los detalles completos de cómo hacer que el extremo de aplicación de la lógica que se puede llamar a continuación](app-service-logic-http-endpoint.md). 

Con estos cambios, la función `@accessKeys()` se elimina y se ha sustituido por la `@listCallbackURL()` función los fines de empezar el extremo (cuando es necesario). Además, ahora debe definir al menos un desencadenador en la aplicación lógica ahora. Si desea `/run` el flujo de trabajo, deberá tener una de un `manual`, `apiConnectionWebhook` o `httpWebhook` desencadenadores. 

## <a name="4-calling-child-workflows"></a>4. los flujos de trabajo secundario de llamada

Llamar a los flujos de trabajo secundario obligatorio anteriormente, va a dicho flujo de trabajo, recibe el token de acceso y luego pegar en la definición de la aplicación de la lógica que desea llamar a que secundarios. Con la nueva versión de esquema, el motor de aplicaciones de lógica generará automáticamente una SA en tiempo de ejecución del flujo de trabajo secundario, lo que significa que no tiene que pegar cualquier información confidencial en la definición.  Aquí tenemos un ejemplo:

```
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName" : "myendpointtrigger"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "conditions" : []
}
```

Una segunda mejora es que se va a proporcionarle los flujos de trabajo secundarios acceso completo a la solicitud entrante. Esto significa que puede pasar parámetros en la sección de *consultas* y en el objeto de *encabezados* y que puede definir completamente todo el cuerpo.

Por último, hay cambios necesarios para el flujo de trabajo secundario. Mientras que antes podría simplemente llamar a un flujo de trabajo secundario directamente; Ahora, deberá definir un extremo del desencadenador en el flujo de trabajo principal llamar. Normalmente, esto significa que deberá agregar un desencadenador de tipo **manual** y usarlo en la definición primaria. Tenga en cuenta que el `host` propiedad específicamente tiene un `triggerName`, ya que siempre se debe especificar qué desencadenador se invoca.

## <a name="other-changes"></a>Otros cambios

### <a name="new-queries-property"></a>Nueva propiedad de consultas
Todos los tipos de acción admiten ahora una nueva entrada denominada **consultas**. Esto puede ser un objeto estructurado en lugar de tener que ensamblar la cadena a mano.

### <a name="parse-function-renamed"></a>nombre de función Parse()
Como pronto agregaremos más tipos de contenido, la `parse()` función ha cambiado a `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Próximamente: API de integración de Enterprise
En este momento, hemos todavía no ha logrado versiones de las API de integración de Enterprise disponibles (por ejemplo, AS2). Estos aparecerá próximamente como se explica en la [Guía básica](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). Mientras tanto, puede usar su APIs BizTalk implementado existentes mediante la acción HTTP, como se explica anteriormente en "usando sus aplicaciones API ya implementados."
