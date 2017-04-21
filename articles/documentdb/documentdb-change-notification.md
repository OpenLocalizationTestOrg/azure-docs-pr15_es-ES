<properties 
    pageTitle="Usar aplicaciones de lógica de notificaciones de cambio de DocumentDB | Microsoft Azure" 
    description="." 
    keywords="notificación de cambios"
    services="documentdb" 
    authors="hedidin" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="rest-api" 
    ms.topic="article" 
    ms.date="09/23/2016" 
    ms.author="b-hoedid"/>

# <a name="notifications-for-new-or-changed-documentdb-resources-using-logic-apps"></a>Notificaciones de nuevas o modificadas de los recursos de DocumentDB mediante aplicaciones de lógica

En este artículo procede una pregunta que VI publicó uno de los foros de la Comunidad DocumentDB de Azure. ¿La pregunta era **hace DocumentDB admite las notificaciones de recursos modificados**?

Que he trabajado con BizTalk Server para muchos años y este es un escenario muy común cuando se usa el [Adaptador de WCF LOB](https://msdn.microsoft.com/library/bb798128.aspx). Así que decidido ver si podría duplicar esta funcionalidad en DocumentDB para documentos nuevos o modificados.

Este artículo proporciona una descripción general de los componentes de la solución de notificación de cambios, que incluye un [desencadenador](documentdb-programming.md#trigger) y la [Lógica de aplicación](../app-service-logic/app-service-logic-what-are-logic-apps.md). Importante los fragmentos de código se proporcionan en línea y la solución completa está disponible en [GitHub](https://github.com/HEDIDIN/DocDbNotifications).

## <a name="use-case"></a>Caso de uso

El artículo siguiente es el caso de uso de este artículo.

DocumentDB es el repositorio de documentos de estado del nivel siete internacional (HL7) rápido salud interoperabilidad recursos (FHIR). Supongamos que su base de datos de DocumentDB combina con la API y aplicación de lógica forman un servidor de FHIR HL7.  Un servicio de atención médico es almacenar los datos de pacientes en el DocumentDB base de datos de "Pacientes". Hay varias colecciones dentro de la base de datos de paciente; Clínicos, identificación, etcetera. Información de pacientes entra en identificación.  Tiene una colección denominada a "Paciencia".

El departamento de cardiología es el control de datos de estado del y ejercicio personales. Buscar los registros de pacientes nuevos o modificados lleva mucho tiempo. Solicitará que el departamento de TI si se ha producido una manera que podría recibir una notificación para los registros de pacientes nuevas o modificadas.  

El departamento de TI dice que podría fácilmente proporcionan esto. También dice que podrían insertar los documentos en [El almacenamiento de blobs de Windows Azure](https://azure.microsoft.com/services/storage/) para el departamento de cardiología fácilmente puede acceder a ellos.

## <a name="how-the-it-department-solved-the-problem"></a>Cómo el departamento de TI resuelve el problema

Para crear esta aplicación, el departamento de TI decidido primero model.  Lo bueno sobre el uso de modelo de proceso empresarial y Notation (BPMN) es que los usuarios técnicos y fácilmente entienda. Este proceso de notificación todo se considera un proceso empresarial. 

## <a name="high-level-view-of-notification-process"></a>Vista de alto nivel del proceso de notificación

1. Empezar con una aplicación de lógica que tiene un desencadenador de temporizador. De forma predeterminada, el desencadenador ejecuta cada hora.
2. A continuación siga HTTP POST a la aplicación de la lógica.
3. La aplicación de lógica hace todo el trabajo.

![Vista de nivel alto](./media/documentdb-change-notification/high-level-view.png)

### <a name="lets-take-a-look-at-what-this-logic-app-does"></a>Echemos un vistazo a lo que hace esta aplicación lógica
Si mira la siguiente ilustración hay varios pasos en el flujo de trabajo LogicApp.

![Proceso de lógica principal](./media/documentdb-change-notification/main-logic-app-process.png)

Los pasos son los siguientes:

1. Debe obtener la fecha y hora UTC actual de un App API.  El valor predeterminado es una hora anterior.

2. El valor de DateTime de UTC se convierte en un formato de marca de tiempo de Unix. Este es el formato predeterminado para las marcas de tiempo en DocumentDB.

3. REGISTRAR el valor a un App API, que realiza una DocumentDB consulta. El valor se usa en una consulta.

    ```SQL
        SELECT * FROM Patients p WHERE (p._ts >= @unixTimeStamp)
    ```

    > [AZURE.NOTE] La _ts representa los metadatos de marca de tiempo para todos los recursos de DocumentDB.

4. Si hay documentos que se encuentran, se envía el cuerpo de la respuesta a su almacenamiento de blobs de Windows Azure.

    > [AZURE.NOTE] Almacenamiento de blobs requiere una cuenta de almacenamiento de Azure. Debe aprovisionar una cuenta de almacenamiento de blobs de Windows Azure y agregue un nuevo Blob denominado a pacientes. Para obtener más información, consulte [acerca de Azure cuentas de almacenamiento](../storage/storage-create-storage-account.md) y [empezar a trabajar con el almacenamiento de blobs de Windows Azure](../storage/storage-dotnet-how-to-use-blobs.md).

5. Por último, se envía un correo electrónico que notifica a los destinatarios del número de documentos encontrados. Si no se han encontrado documentos, el cuerpo del correo electrónico sería "0 documentos encontrados". 

Ahora que ya tiene una idea de lo que hace el flujo de trabajo, echemos un vistazo a cómo implementar.

### <a name="lets-start-with-the-main-logic-app"></a>Comencemos con la aplicación de la lógica principal

Si no está familiarizado con lógica de aplicaciones, están disponibles en el [Catálogo de soluciones de Azure](https://portal.azure.com/)y puede obtener más información acerca de ellos en [¿qué aplicaciones de lógica?](../app-service-logic/app-service-logic-what-are-logic-apps.md)

Cuando se crea una nueva aplicación lógica, se le pregunte **cómo le gustaría iniciar?**

Al hacer clic dentro del cuadro de texto, tiene una opción de eventos. Para esta aplicación lógica, seleccione **Manual - solicitud HTTP un cuando se reciba** tal como se muestra a continuación.

![Comenzaremos](./media/documentdb-change-notification/starting-off.png)

### <a name="design-view-of-your-completed-logic-app"></a>Vista de diseño de la aplicación de lógica completada
Vamos a avanzar y observe la vista de diseño completo para la aplicación de la lógica, que se denomina DocDB.

![Flujo de trabajo de aplicación de lógica](./media/documentdb-change-notification/workflow-expanded.png)

Al editar las acciones en el Diseñador de lógica de aplicación, tiene la opción de seleccionar los **resultados** de la solicitud HTTP o de la acción anterior como se muestra en la acción de sendMail a continuación.

![Elija salidas](./media/documentdb-change-notification/choose-outputs.png)

Antes de cada acción del flujo de trabajo, puede tomar una decisión; **Agregar una acción** o **Agregar una condición** tal como se muestra en la siguiente ilustración.

![Tomar una decisión](./media/documentdb-change-notification/add-action-or-condition.png)

Si selecciona **Agregar una condición**, se presentan con un formulario, tal como se muestra en la siguiente ilustración, para introducir la lógica.  Básicamente, esta es una regla de negocio.  Si hace clic dentro de un campo, tiene una opción de selección de parámetros de la acción anterior. También puede introducir los valores directamente.

![Agregar una condición](./media/documentdb-change-notification/condition1.png)

> [AZURE.NOTE] También tiene la capacidad de todo lo que escriba en la vista código.

Echemos un vistazo a la aplicación de lógica completada en la vista código.  

```JSON
   
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
    "actions": {
        "Conversion": {
            "conditions": [
                {
                    "dependsOn": "GetUtcDate"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "currentdateTime": "@{body('GetUtcDate')}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "Createfile": {
            "conditions": [
                {
                    "expression": "@greater(length(body('GetDocuments')), 0)"
                },
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "@body('GetDocuments')",
                "host": {
                    "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['azureblob']['connectionId']"
                    }
                },
                "method": "post",
                "path": "/datasets/default/files",
                "queries": {
                    "folderPath": "/patients",
                    "name": "Patient_@{guid()}.json"
                }
            },
            "type": "ApiConnection"
        },
        "GetDocuments": {
            "conditions": [
                {
                    "dependsOn": "Conversion"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "unixTimeStamp": "@body('Conversion')"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "GetUtcDate": {
            "conditions": [],
            "inputs": {
                "method": "get",
                "queries": {
                    "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "sendMail": {
            "conditions": [
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
                "headers": {
                    "Content-type": "application/x-www-form-urlencoded"
                },
                "method": "POST",
                "uri": "https://api.sendgrid.com/api/mail.send.json"
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {
        "Results": {
            "type": "String",
            "value": "@{int(length(body('GetDocuments')))} Records Found"
        }
    },
    "parameters": {
        "$connections": {
            "defaultValue": {},
            "type": "Object"
        },
        "fromAddress": {
            "defaultValue": "user@msn.com",
            "type": "String"
        },
        "toAddress": {
            "defaultValue": "XXXXX@XXXXXXX.net",
            "type": "String"
        }
    },
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {
                    "properties": {},
                    "required": [],
                    "type": "object"
                }
            },
            "type": "Manual"
        }
    
```

Si no está familiarizado con lo que representa las distintas secciones en el código, puede ver la documentación del [Lenguaje de definición de flujo de trabajo de lógica de aplicación](http://aka.ms/logicappsdocs) .

Para este flujo de trabajo se con un [Desencadenador de HTTP Webhook](https://sendgrid.com/blog/whats-webhook/). Si mira el código anterior, verá parámetros como el siguiente ejemplo.

```C#

    =@{triggerBody()['Subject']}

```

La `triggerBody()` representa los parámetros que se incluyen en el cuerpo de una entrada del resto a la API de REST de lógica de aplicación. La `()['Subject']` representa el campo. Todos estos parámetros conforman el JSON formato cuerpo. 

> [AZURE.NOTE] Mediante un enlace de Web, puede tener acceso completo a encabezado y el cuerpo de la solicitud del desencadenador. En esta aplicación quiere el cuerpo.

Como se mencionó anteriormente, puede usar el diseñador para asignar parámetros o hacerlo en la vista código.
Si hace en la vista código, se definen las propiedades que requieren un valor como se muestra en el ejemplo siguiente. 

```JSON

    "triggers": {
        "manual": {
            "inputs": {
            "schema": {
                "properties": {
            "Subject": {
                "type" : "String"   

            }
            },
                "required": [
            "Subject"
                 ],
                "type": "object"
            }
            },
            "type": "Manual"
        }
        }
```

Lo que está haciendo está creando un esquema JSON que se pasa en el cuerpo de HTTP POST.
Para activar el desencadenador, necesita una dirección URL de devolución de llamada.  Aprenderá a generar más adelante en el tutorial.  

## <a name="actions"></a>Acciones
Vamos a ver lo que hace cada acción en nuestra aplicación lógica.

### <a name="getutcdate"></a>GetUTCDate

**Vista del diseñador**

![](./media/documentdb-change-notification/getutcdate.png)

**Vista código**

```JSON

    "GetUtcDate": {
            "conditions": [],
            "inputs": {
            "method": "get",
            "queries": {
                "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
            },
            "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
            },
            "metadata": {
            "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
            },
            "type": "Http"
        },

```

Esta acción de HTTP realiza una operación GET.  Llama al método API aplicación GetUtcDate. La Uri utiliza la propiedad de 'GetUtcDate_HoursBack' pasada el cuerpo del desencadenador.  El valor de 'GetUtcDate_HoursBack' se establece en la primera aplicación lógica. Obtendrá más información sobre la aplicación de la lógica de desencadenador más adelante en el tutorial.

Esta acción llama su App API para devolver el valor de cadena de fecha UTC.

#### <a name="operations"></a>Operaciones

**Solicitar**

```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization",
        "method": "get",
        "queries": {
          "hoursBack": "24"
        }
    }

```

**Respuesta**

```JSON

    {
        "statusCode": 200,
        "headers": {
          "pragma": "no-cache",
          "cache-Control": "no-cache",
          "date": "Fri, 26 Feb 2016 15:47:33 GMT",
          "server": "Microsoft-IIS/8.0",
          "x-AspNet-Version": "4.0.30319",
          "x-Powered-By": "ASP.NET"
        },
        "body": "Fri, 15 Jan 2016 23:47:33 GMT"
    }

```

El siguiente paso es convertir el valor de DateTime de UTC para la marca de tiempo de Unix, que es un tipo de .NET doble.

### <a name="conversion"></a>Conversión

##### <a name="designer-view"></a>Vista del diseñador

![Conversión](./media/documentdb-change-notification/conversion.png)

##### <a name="code-view"></a>Vista código

```JSON

    "Conversion": {
        "conditions": [
        {
            "dependsOn": "GetUtcDate"
        }
        ],
        "inputs": {
        "method": "post",
        "queries": {
            "currentDateTime": "@{body('GetUtcDate')}"
        },
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
        },
        "metadata": {
        "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
        },
        "type": "Http"
    },

```

En este paso se pasar el valor devuelto por la GetUTCDate.  Hay una condición dependsOn, lo que significa que la acción GetUTCDate debe completarse correctamente. Si no es así, a continuación, se omite esta acción. 

Esta acción llama su App API para controlar la conversión.

#### <a name="operations"></a>Operaciones

##### <a name="request"></a>Solicitar

```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion",
        "method": "post",
        "queries": {
        "currentDateTime": "Fri, 15 Jan 2016 23:47:33 GMT"
        }
    }   
```

##### <a name="response"></a>Respuesta

```JSON

    {
        "statusCode": 200,
        "headers": {
          "pragma": "no-cache",
          "cache-Control": "no-cache",
          "date": "Fri, 26 Feb 2016 15:47:33 GMT",
          "server": "Microsoft-IIS/8.0",
          "x-AspNet-Version": "4.0.30319",
          "x-Powered-By": "ASP.NET"
        },
        "body": 1452901653
    }
```

En la siguiente acción, realizará una operación de publicación a nuestra App API.

### <a name="getdocuments"></a>GetDocuments 

##### <a name="designer-view"></a>Vista del diseñador

![Obtener los documentos](./media/documentdb-change-notification/getdocuments.png)

##### <a name="code-view"></a>Vista código

```JSON

    "GetDocuments": {
        "conditions": [
        {
            "dependsOn": "Conversion"
        }
        ],
        "inputs": {
        "method": "post",
        "queries": {
            "unixTimeStamp": "@{body('Conversion')}"
        },
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
        },
        "metadata": {
        "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
        },
        "type": "Http"
    },

```

Para la acción GetDocuments va a pasar en el cuerpo de la respuesta de la acción de la conversión. Se trata de un parámetro en el Uri:

 
```C#

    unixTimeStamp=@{body('Conversion')}

```

La acción QueryDocuments realiza una operación de HTTP POST a la API de App. 

El método llamado es **QueryForNewPatientDocuments**.

#### <a name="operations"></a>Operaciones

##### <a name="request"></a>Solicitar

```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient",
        "method": "post",
        "queries": {
        "unixTimeStamp": "1452901653"
        }
    }
```

##### <a name="response"></a>Respuesta

```JSON

    {
        "statusCode": 200,
        "headers": {
        "pragma": "no-cache",
        "cache-Control": "no-cache",
        "date": "Fri, 26 Feb 2016 15:47:35 GMT",
        "server": "Microsoft-IIS/8.0",
        "x-AspNet-Version": "4.0.30319",
        "x-Powered-By": "ASP.NET"
        },
        "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
            "status": "generated",
            "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
            {
                "use": "usual",
                "type": {
                "coding": [
                    {
                    "system": "http://hl7.org/fhir/v2/0203",
                    "code": "MR"
                    }
                ]
                },
                "system": "urn:oid:2.16.840.1.113883.19.5",
                "value": "12345"
            }
            ],
            "active": true,
            "name": [
            {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

La siguiente acción es guardar los documentos en el [Blog de Azure almacenamiento](https://azure.microsoft.com/services/storage/). 

> [AZURE.NOTE] Almacenamiento de blobs requiere una cuenta de almacenamiento de Azure. Debe aprovisionar una cuenta de almacenamiento de blobs de Windows Azure y agregue un nuevo Blob denominado a pacientes. Para obtener más información, vea [Introducción a almacenamiento de blobs de Windows Azure](../storage/storage-dotnet-how-to-use-blobs.md).

### <a name="create-file"></a>Crear archivo

##### <a name="designer-view"></a>Vista del diseñador

![Crear archivo](./media/documentdb-change-notification/createfile.png)

##### <a name="code-view"></a>Vista código

```JSON

    {
    "host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

Se genera el código de acción en el diseñador. No tiene que modificar el código.

Si no está familiarizado con el uso de la API de blobs de Windows Azure, consulte [Introducción a la API de almacenamiento de blobs de Windows Azure](../connectors/connectors-create-api-azureblobstorage.md).

#### <a name="operations"></a>Operaciones

##### <a name="request"></a>Solicitar

```JSON

    "host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },….


```

##### <a name="response"></a>Respuesta

```JSON

    {
        "statusCode": 200,
        "headers": {
        "pragma": "no-cache",
        "x-ms-request-id": "2b2f7c57-2623-4d71-8e53-45c26b30ea9d",
        "cache-Control": "no-cache",
        "date": "Fri, 26 Feb 2016 15:47:36 GMT",
        "set-Cookie": "ARRAffinity=29e552cea7db23196f7ffa644003eaaf39bc8eb6dd555511f669d13ab7424faf;Path=/;Domain=127.0.0.1",
        "server": "Microsoft-HTTPAPI/2.0",
        "x-AspNet-Version": "4.0.30319",
        "x-Powered-By": "ASP.NET"
        },
        "body": {
        "Id": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE",
        "Name": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "DisplayName": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "Path": "/Patient/Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "LastModified": "2016-02-26T15:47:36.215Z",
        "Size": 65647,
        "MediaType": "application/octet-stream",
        "IsFolder": false,
        "ETag": "\"c-g_a-1OtaH-kNQ4WBoXLp3Zv9s/MTQ1NjUwMTY1NjIxNQ\"",
        "FileLocator": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE"
        }
    }
```

El último paso es enviar una notificación de correo electrónico

### <a name="sendemail"></a>sendEmail

##### <a name="designer-view"></a>Vista del diseñador

![Enviar correo electrónico](./media/documentdb-change-notification/sendemail.png)

##### <a name="code-view"></a>Vista código

```JSON


    "sendMail": {
        "conditions": [
        {
            "dependsOn": "GetDocuments"
        }
        ],
        "inputs": {
        "body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
        "headers": {
            "Content-type": "application/x-www-form-urlencoded"
        },
        "method": "POST",
        "uri": "https://api.sendgrid.com/api/mail.send.json"
        },
        "type": "Http"
    }
```

En esta acción de enviar una notificación de correo electrónico.  Usa [SendGrid](https://sendgrid.com/marketing/sendgrid-services?cvosrc=PPC.Bing.sendgrib&cvo_cid=SendGrid%20-%20US%20-%20Brand%20-%20&mc=Paid%20Search&mcd=BingAds&keyword=sendgrib&network=o&matchtype=e&mobile=&content=&search=1&utm_source=bing&utm_medium=cpc&utm_term=%5Bsendgrib%5D&utm_content=%21acq%21v2%2134335083397-8303227637-1649139544&utm_campaign=SendGrid+-+US+-+Brand+-+%28English%29).   

El código para esto se generó mediante una plantilla de aplicación lógica y SendGrid que se encuentra en el [repositorio de Github sendgrid-101-lógica de aplicación](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sendgrid).
 
La operación de HTTP es una entrada de blog. 

Los parámetros de autorización están en las propiedades del desencadenador

```JSON

    },
        "sendgridPassword": {
             "type": "SecureString"
         },
         "sendgridUsername": {
            "type": "String"
         }

        In addition, other parameters are static values set in the Parameters section of the Logic App. These are:
        },
        "toAddress": {
            "defaultValue": "XXXX@XXXX.com",
            "type": "String"
        },
        "fromAddress": {
            "defaultValue": "XXX@msn.com",
            "type": "String"
        },
        "emailBody": {
            "defaultValue": "@{string(concat(int(length(actions('QueryDocuments').outputs.body)) Records Found),'/n', actions('QueryDocuments').outputs.body)}",
            "type": "String"
        },

```

El emailBody es concatenar el número de documentos devuelto por la consulta, que puede ser "0" o más, junto con, "Registros encuentra". El resto de los parámetros se establecen los parámetros de desencadenador.

Esta acción depende de la acción **GetDocuments** .

#### <a name="operations"></a>Operaciones

##### <a name="request"></a>Solicitar
```JSON

    {
        "uri": "https://api.sendgrid.com/api/mail.send.json",
        "method": "POST",
        "headers": {
        "Content-type": "application/x-www-form-urlencoded"
        },
        "body": "api_user=azureuser@azure.com&api_key=Biz@Talk&from=user@msn.com&to=XXXX@XXXX.com&subject=New Patients&text=37 Documents Found"
    }

```

##### <a name="response"></a>Respuesta

```JSON

    {
        "statusCode": 200,
        "headers": {
        "connection": "keep-alive",
        "x-Frame-Options": "DENY,DENY",
        "access-Control-Allow-Origin": "https://sendgrid.com",
        "date": "Fri, 26 Feb 2016 15:47:35 GMT",
        "server": "nginx"
        },
        "body": {
        "message": "success"
        }
    }
```

Por último que desea que vean los resultados de la aplicación de la lógica en el Portal de Azure. Para ello, agregue un parámetro a la sección de resultados.


```JSON

    "outputs": {
        "Results": {
            "type": "String",
            "value": "@{int(length(actions('QueryDocuments').outputs.body))} Records Found"
        }

```

Devuelve el mismo valor que se envía en el cuerpo del correo electrónico. La siguiente ilustración muestra un ejemplo donde "29 registros encontraron".

![Resultados](./media/documentdb-change-notification/logic-app-run.png)

## <a name="metrics"></a>Métricas
Puede configurar la supervisión de la aplicación de lógica principal en el portal. Esto le permite ver la latencia ejecutar y otros eventos, como se muestra en la siguiente ilustración.

![](./media/documentdb-change-notification/metrics.png)

## <a name="docdb-trigger"></a>Desencadenador DocDb

Esta aplicación lógica es el desencadenador que inicia el flujo de trabajo en la aplicación de la lógica principal.

La figura siguiente muestra la vista de diseñador.

![](./media/documentdb-change-notification/trigger-recurrence.png)

```JSON

    {
        "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
        "actions": {
        "Http": {
            "conditions": [],
            "inputs": {
            "body": {
                "EmailTo": "XXXXXX@XXXXX.net",
                "GetUtcDate_HoursBack": "24",
                "Subject": "New Patients",
                "sendgridPassword": "********",
                "sendgridUsername": "azureuser@azure.com"
            },
            "method": "POST",
            "uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c"
            },
            "type": "Http"
        }
        },
        "contentVersion": "1.0.0.0",
        "outputs": {
        "Results": {
            "type": "String",
            "value": "@{body('Http')['status']}"
        }
        },
        "parameters": {},
        "triggers": {
        "recurrence": {
            "recurrence": {
            "frequency": "Hour",
            "interval": 24
            },
            "type": "Recurrence"
        }
        }
    }

```

El desencadenador se establece para una periodicidad de 24 horas. La acción es un HTTP POST que usa la dirección URL de devolución de llamada para la aplicación de la lógica principal. El cuerpo contiene los parámetros que se especifican en el esquema de JSON. 

#### <a name="operations"></a>Operaciones

##### <a name="request"></a>Solicitar

```JSON

    {
        "uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c",
        "method": "POST",
        "body": {
        "EmailTo": "XXXXXX@XXXXX.net",
        "GetUtcDate_HoursBack": "24",
        "Subject": "New Patients",
        "sendgridPassword": "********",
        "sendgridUsername": "azureuser@azure.com"
        }
    }

```

##### <a name="response"></a>Respuesta

```JSON

    {
        "statusCode": 202,
        "headers": {
        "pragma": "no-cache",
        "x-ms-ratelimit-remaining-workflow-writes": "7486",
        "x-ms-ratelimit-burst-remaining-workflow-writes": "1248",
        "x-ms-request-id": "westus:2d440a39-8ba5-4a9c-92a6-f959b8d2357f",
        "cache-Control": "no-cache",
        "date": "Thu, 25 Feb 2016 21:01:06 GMT"
        }
    }
```

Ahora vamos a analizar el App API.

## <a name="docdbnotificationapi"></a>DocDBNotificationApi

Aunque hay varias operaciones en la aplicación, sólo va a utilizar tres.

* GetUtcDate
* ConvertToTimeStamp
* QueryForNewPatientDocuments

### <a name="docdbnotificationapi-operations"></a>Operaciones de DocDBNotificationApi
Echemos un vistazo a la documentación de Swagger

> [AZURE.NOTE] Para permitir que llamar a las operaciones externamente, debe agregar una CORS valor de origen de permitido "*" (sin comillas) en la configuración de su App API tal como se muestra en la siguiente ilustración.

![Configuración de Cors](./media/documentdb-change-notification/cors.png)

#### <a name="getutcdate"></a>GetUtcDate

![G](./media/documentdb-change-notification/getutcdateswagger.png)

#### <a name="converttotimestamp"></a>ConvertToTimeStamp

![Obtener fecha UTC](./media/documentdb-change-notification/converion-swagger.png)

#### <a name="queryfornewpatientdocuments"></a>QueryForNewPatientDocuments

![Consulta](./media/documentdb-change-notification/patientswagger.png)

Echemos un vistazo al código subyacente de esta operación.

#### <a name="getutcdate"></a>GetUtcDate

```C#

    /// <summary>
    /// Gets the current UTC Date value
    /// </summary>
    /// <returns></returns>
    [H ttpGet]
    [Metadata("GetUtcDate", "Gets the current UTC Date value minus the Hours Back")]
    [SwaggerOperation("GetUtcDate")]
    [SwaggerResponse(HttpStatusCode.OK, type: typeof (string))]
    [SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
    public string GetUtcDate(
       [Metadata("Hours Back", "How many hours back from the current Date Time")] int hoursBack)
    {


        return DateTime.UtcNow.AddHours(-hoursBack).ToString("r");
    }
```

Esta operación devuelve simplemente la devuelve DateTime UTC actual menos el valor de HoursBack.

#### <a name="converttotimestamp"></a>ConvertToTimeStamp

``` C#

        /// <summary>
        ///     Converts DateTime to double
        /// </summary>
        /// <param name="currentdateTime"></param>
        /// <returns></returns>
        [Metadata("Converts Universal DateTime to number")]
        [SwaggerResponse(HttpStatusCode.OK, null, typeof (double))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "DateTime is invalid")]
        [SwaggerResponse(HttpStatusCode.InternalServerError)]
        [SwaggerOperation(nameof(ConvertToTimestamp))]
        public double ConvertToTimestamp(
            [Metadata("currentdateTime", "DateTime value to convert")] string currentdateTime)
        {
            double result;

            try
            {
                var uncoded = HttpContext.Current.Server.UrlDecode(currentdateTime);

                var newDateTime = DateTime.Parse(uncoded);
                //create Timespan by subtracting the value provided from the Unix Epoch
                var span = newDateTime - new DateTime(1970, 1, 1, 0, 0, 0, 0).ToLocalTime();

                //return the total seconds (which is a UNIX timestamp)
                result = span.TotalSeconds;
            }
            catch (Exception e)
            {
                throw new Exception("unable to convert to Timestamp", e.InnerException);
            }

            return result;
        }

```

Esta operación convierte la respuesta de la operación GetUtcDate a un valor double.

#### <a name="queryfornewpatientdocuments"></a>QueryForNewPatientDocuments

```C#

        /// <summary>
        ///     Query for new Patient Documents
        /// </summary>
        /// <param name="unixTimeStamp"></param>
        /// <returns>IList</returns>
        [Metadata("QueryForNewDocuments",
            "Query for new Documents where the Timestamp is greater than or equal to the DateTime value in the query parameters."
            )]
        [SwaggerOperation("QueryForNewDocuments")]
        [SwaggerResponse(HttpStatusCode.OK, type: typeof (Task<IList<Document>>))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "The syntax of the SQL Statement is incorrect")]
        [SwaggerResponse(HttpStatusCode.NotFound, "No Documents were found")]
        [SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
        // ReSharper disable once ConsiderUsingAsyncSuffix
        public IList<Document> QueryForNewPatientDocuments(
            [Metadata("UnixTimeStamp", "The DateTime value used to search from")] double unixTimeStamp)
        {
            var context = new DocumentDbContext();
            var filterQuery = string.Format(InvariantCulture, "SELECT * FROM Patient p WHERE p._ts >=  {0}",
                unixTimeStamp);
            var options = new FeedOptions {MaxItemCount = -1};


            var collectionLink = UriFactory.CreateDocumentCollectionUri(DocumentDbContext.DatabaseId,
                DocumentDbContext.CollectionId);

            var response =
                context.Client.CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();

            return response.ToList();
    }

```

Esta operación usa el [SDK de .NET DocumentDB](documentdb-sdk-dotnet.md) para crear una consulta de documento. 

```C#
     CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();
```

Se pasa la respuesta de la operación de ConvertToTimeStamp (unixTimeStamp). La operación devuelve una lista de documentos, `IList<Document>`.

Anteriormente hablamos sobre la CallbackURL. Para iniciar el flujo de trabajo en la aplicación de la lógica principal, debe llamar mediante el CallbackURL.

## <a name="callbackurl"></a>CallbackURL

Para empezar, debe el Token de Azure AD.  Puede ser difícil obtener este token. Que estaba buscando un método fácil y Jeff Hollan, que es un administrador de programa de aplicación de la lógica de Azure, se recomienda utilizando la [armclient](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) en PowerShell.  Puede instalarlo siguiendo las instrucciones proporcionadas.

Las operaciones que desea usar son inicio de sesión y llamar a la API de ARM.
 
Inicio de sesión: Usar las mismas credenciales para iniciar sesión en el Portal de Azure. 

La operación de llamar a la Api de ARM es la que genere su CallBackURL.

En PowerShell, llame a lo siguiente:  

```powershell

    ArmClient.exe post https://management.azure.com/subscriptions/[YOUR SUBSCRIPTION ID/resourcegroups/[YOUR RESOURCE GROUP]/providers/Microsoft.Logic/workflows/[YOUR LOGIC APP NAME/triggers/manual/listcallbackurl?api-version=2015-08-01-preview

```

El resultado debería tener este aspecto:

```powershell

    https://prod-02.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-prevaiew&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=XXXXXXXXXXXXXXXXXXX

```

Puede usar una herramienta como [postman](http://www.getpostman.com/) para probar la aplicación de lógica principal tal como se muestra en la siguiente ilustración.

![Postman](./media/documentdb-change-notification/newpostman.png)

La siguiente tabla enumera los parámetros de desencadenador que constituyen el cuerpo de la aplicación de lógica de desencadenador DocDB.

Parámetro | Descripción 
--- | --- 
GetUtcDate_HoursBack | Se usa para establecer el número de horas de la fecha de inicio de la búsqueda
sendgridUsername | Se usa para establecer el número de horas de la fecha de inicio de la búsqueda
sendgridPassword | El nombre de usuario para el correo electrónico de la cuadrícula de enviar
Correo electrónicopara | La dirección de correo electrónico que va a recibir la notificación de correo electrónico
Asunto | El asunto para el correo electrónico

## <a name="viewing-the-patient-data-in-the-azure-blob-service"></a>Ver los datos de pacientes en el servicio de blobs de Windows Azure

Vaya a su cuenta de almacenamiento de Azure y seleccione BLOB en servicios como se muestra en la siguiente ilustración.

![Cuenta de almacenamiento](./media/documentdb-change-notification/docdbstorageaccount.png) 

Podrá ver la información del archivo blob pacientes tal como se muestra a continuación.

![Servicio de blobs](./media/documentdb-change-notification/blobservice.png)


## <a name="summary"></a>Resumen

En este tutorial, ha aprendido lo siguiente:

* Es posible implementar notificaciones en DocumentDB.
* Mediante el uso de lógica de aplicaciones, puede automatizar el proceso.
* Mediante el uso de lógica de aplicaciones, puede reducir el tiempo que se tarda en entregar una aplicación.
* Mediante el protocolo HTTP fácil puede consumir un App API dentro de una aplicación de lógica.
* Puede crear fácilmente una CallBackURL que reemplaza la escucha HTTP.
* Puede crear fácilmente los flujos de trabajo personalizados con el Diseñador de lógica de aplicaciones.

La clave es planificar con antelación y del modelo de flujo de trabajo.

## <a name="next-steps"></a>Pasos siguientes
Descargue y use el código de aplicación lógica proporcionado en [Github](https://github.com/HEDIDIN/DocDbNotifications). Puedo invitar a generar en la aplicación y enviar cambios a la repo. 

Para obtener más información sobre DocumentDB, visite la [Ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/documentdb/).
