<properties
    pageTitle="Registro de información y control de errores en las aplicaciones de lógica | Microsoft Azure"
    description="Ver un caso de uso de reales de error avanzada con aplicaciones de la lógica de registro y tratamiento"
    keywords=""
    services="logic-apps"
    authors="hedidin"
    manager="anneta"
    editor=""
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="b-hoedid"/>

# <a name="logging-and-error-handling-in-logic-apps"></a>Registro de información y control de errores en las aplicaciones de lógica

Este artículo describe cómo puede ampliar una aplicación lógica para mejorar la compatibilidad con control de excepciones. Es un caso de uso reales y nuestra respuesta a la pregunta de "Lógica aplicaciones admite excepción y control de errores?"

>[AZURE.NOTE]La versión actual de la característica de aplicaciones de la lógica de aplicación de servicio de Microsoft Azure proporciona una plantilla estándar para las respuestas de acción.
>Esto incluye validación interna y respuestas de error devueltos desde una aplicación de API.

## <a name="overview-of-the-use-case-and-scenario"></a>Información general sobre el caso de uso y escenario

El artículo siguiente es el caso de uso de este artículo.
Una organización de salud conocida contrató nosotros para desarrollar una solución de Azure que crearía un portal paciente usando Microsoft Dynamics CRM Online. Necesita enviar registros de cita entre el portal de pacientes Dynamics CRM Online y Salesforce.  Se nos pide que use el estándar [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) para todos los registros de pacientes.

El proyecto tuviera dos requisitos principales:  

 -  Un método para registrar registros enviados desde el portal de Dynamics CRM Online
 -  Una forma de ver los errores que se han producido en el flujo de trabajo


## <a name="how-we-solved-the-problem"></a>¿Cómo se soluciona el problema

>[AZURE.TIP] Puede ver un vídeo de alto nivel del proyecto en el [Grupo de usuarios de integración de](http://www.integrationusergroup.com/do-logic-apps-support-error-handling/ "Grupo de usuarios de integración").

Elegimos [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/ "Azure DocumentDB") como un repositorio para los registros de error y registro (DocumentDB hace referencia a registros como documentos). Como lógica de aplicaciones tiene una plantilla estándar para todas las respuestas, no tendríamos que crear un esquema personalizado. Podríamos crear una aplicación de API para **Insertar** y **consultas** para los registros de error y el registro. También podemos definir un esquema para cada uno dentro de la aplicación de la API.  

Otro requisito era purgar los registros después de una fecha determinada. DocumentDB tiene una propiedad denominada [Time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Time to Live") (TTL), que permitía a establecer un valor de **período de vida** para cada registro o la colección. Esto elimina la necesidad de eliminar manualmente los registros en DocumentDB.

### <a name="creation-of-the-logic-app"></a>Creación de la aplicación de lógica

El primer paso es crear la aplicación de lógica y cargarlo en el diseñador. En este ejemplo, estamos usando aplicaciones de lógica de elementos primarios y secundarios. Supongamos que ya ha creado al elemento primario y va a crear una aplicación de la lógica de secundarios.

Vamos a iniciar porque vamos a ser el registro del registro procedentes de Dynamics CRM Online, en la parte superior. Es necesario utilizar un desencadenador de solicitud porque la aplicación de la lógica principal activa a este elemento secundario.

> [AZURE.IMPORTANT] Para completar este tutorial, debe crear una base de datos de DocumentDB y dos colecciones (registro y errores).

### <a name="logic-app-trigger"></a>Desencadenador de aplicación de lógica

Estamos usando un desencadenador de solicitud tal como se muestra en el ejemplo siguiente.

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


### <a name="steps"></a>Pasos

Es necesario iniciar el origen (solicitud) del registro paciente desde el portal de Dynamics CRM Online.

1. Es necesario obtener un nuevo registro de cita de Dynamics CRM Online.
    El desencadenador procedentes de CRM nos proporciona la **PatentId CRM**, **tipo de registro**, **nuevo o actualizado registro** (nuevo o actualizar el valor de tipo Boolean) y **SalesforceId**. La **SalesforceId** puede ser null porque solo se utiliza para una actualización.
    El registro CRM se obtendrá mediante la CRM **PatientID** y el **Tipo de registro**.
1. A continuación, es necesario agregar nuestra aplicación API DocumentDB **InsertLogEntry** operación tal como se muestra en las siguientes figuras.


#### <a name="insert-log-entry-designer-view"></a>Insertar vista Diseñador de la entrada de registro

![Insertar la entrada de registro](./media/app-service-logic-scenario-error-and-exception-handling/lognewpatient.png)

#### <a name="insert-error-entry-designer-view"></a>Insertar vista Diseñador de entrada de error
![Insertar la entrada de registro](./media/app-service-logic-scenario-error-and-exception-handling/insertlogentry.png)

#### <a name="check-for-create-record-failure"></a>Active para crear el error de registro

![Condición](./media/app-service-logic-scenario-error-and-exception-handling/condition.png)


## <a name="logic-app-source-code"></a>Código fuente de aplicación de lógica

>[AZURE.NOTE]  Los siguientes son ejemplos solo. Dado que este tutorial se basa en una implementación actualmente en producción, el valor de un **Nodo de origen** no puede mostrar propiedades relacionadas con la programación de una cita.

### <a name="logging"></a>Registro de información
El siguiente ejemplo de código de la aplicación de lógica muestra cómo controlar el registro.

#### <a name="log-entry"></a>Entrada de registro
Este es el código de la aplicación de lógica para insertar una entrada de registro.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Solicitud de registro

Este es el mensaje de solicitud de registro registrado la aplicación de la API.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{\"Pragma\":\"no-cache\",\"x-ms-request-id\":\"e750c9a9-bd48-44c4-bbba-1688b6f8a132\",\"OData-Version\":\"4.0\",\"Cache-Control\":\"no-cache\",\"Date\":\"Fri, 10 Jun 2016 22:31:56 GMT\",\"Set-Cookie\":\"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1\",\"Server\":\"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0\",\"X-AspNet-Version\":\"4.0.30319\",\"X-Powered-By\":\"ASP.NET\",\"Content-Length\":\"1935\",\"Content-Type\":\"application/json; odata.metadata=minimal; odata.streaming=true\",\"Expires\":\"-1\"}"
        }
    }

```


#### <a name="log-response"></a>Respuesta de registro

Este es el mensaje de respuesta de registro de la aplicación de la API.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "\"0400fc2f-0000-0000-0000-575b3ff00000\"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{\"Pragma\":\"no-cache\",\"x-ms-request-id\":\"e750c9a9-bd48-44c4-bbba-1688b6f8a132\",\"OData-Version\":\"4.0\",\"Cache-Control\":\"no-cache\",\"Date\":\"Fri, 10 Jun 2016 22:31:56 GMT\",\"Set-Cookie\":\"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1\",\"Server\":\"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0\",\"X-AspNet-Version\":\"4.0.30319\",\"X-Powered-By\":\"ASP.NET\",\"Content-Length\":\"1935\",\"Content-Type\":\"application/json; odata.metadata=minimal; odata.streaming=true\",\"Expires\":\"-1\"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Ahora vamos a ver los pasos de control de errores.


### <a name="error-handling"></a>Control de errores

El siguiente ejemplo de código de aplicaciones de lógica muestra cómo puede implementar el control de errores.

#### <a name="create-error-record"></a>Crear el registro de errores

Este es el código fuente de aplicaciones de lógica para crear un registro de errores.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}          
```

#### <a name="insert-error-into-documentdb--request"></a>Error al insertar en DocumentDB--solicitar

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{\"Account_Class_vod__c\":\"PRAC\",\"Account_Status_MED__c\":\"I\",\"CRM_HUB_ID__c\":\"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0\",\"Credentials_vod__c\",\"DTC_ID_MED__c\":\"\",\"Fax\":\"\",\"FirstName\":\"A\",\"Gender_vod__c\":\"\",\"IMS_ID__c\":\"\",\"LastName\":\"BAILEY\",\"MasterID_mp__c\":\"\",\"C_ID_MED__c\":\"851588\",\"Middle_vod__c\":\"\",\"NPI_vod__c\":\"\",\"PDRP_MED__c\":false,\"PersonDoNotCall\":false,\"PersonEmail\":\"\",\"PersonHasOptedOutOfEmail\":false,\"PersonHasOptedOutOfFax\":false,\"PersonMobilePhone\":\"\",\"Phone\":\"\",\"Practicing_Specialty__c\":\"FM - FAMILY MEDICINE\",\"Primary_City__c\":\"\",\"Primary_State__c\":\"\",\"Primary_Street_Line2__c\":\"\",\"Primary_Street__c\":\"\",\"Primary_Zip__c\":\"\",\"RecordTypeId\":\"012U0000000JaPWIA0\",\"Request_Date__c\":\"2016-06-10T22:31:55.9647467Z\",\"ONY_ID__c\":\"\",\"Specialty_1_vod__c\":\"\",\"Suffix_vod__c\":\"\",\"Website\":\"\"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-documentdb--response"></a>Error al insertar en DocumentDB--respuesta


``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "\"0c00eaac-0000-0000-0000-575b3fdc0000\"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{\"Account_Class_vod__c\":\"PRAC\",\"Account_Status_MED__c\":\"I\",\"CRM_HUB_ID__c\":\"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0\",\"Credentials_vod__c\":\"DO - Degree level is DO\",\"DTC_ID_MED__c\":\"\",\"Fax\":\"\",\"FirstName\":\"A\",\"Gender_vod__c\":\"\",\"IMS_ID__c\":\"\",\"LastName\":\"BAILEY\",\"MterID_mp__c\":\"\",\"Medicis_ID_MED__c\":\"851588\",\"Middle_vod__c\":\"\",\"NPI_vod__c\":\"\",\"PDRP_MED__c\":false,\"PersonDoNotCall\":false,\"PersonEmail\":\"\",\"PersonHasOptedOutOfEmail\":false,\"PersonHasOptedOutOfFax\":false,\"PersonMobilePhone\":\"\",\"Phone\":\"\",\"Practicing_Specialty__c\":\"FM - FAMILY MEDICINE\",\"Primary_City__c\":\"\",\"Primary_State__c\":\"\",\"Primary_Street_Line2__c\":\"\",\"Primary_Street__c\":\"\",\"Primary_Zip__c\":\"\",\"RecordTypeId\":\"012U0000000JaPWIA0\",\"Request_Date__c\":\"2016-06-10T22:31:55.9647467Z\",\"XXXXXXX\":\"\",\"Specialty_1_vod__c\":\"\",\"Suffix_vod__c\":\"\",\"Website\":\"\"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Respuesta de error de Salesforce

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="returning-the-response-back-to-the-parent-logic-app"></a>Devuelve la respuesta a la aplicación de la lógica principal

Una vez que la respuesta, puede pasar a la aplicación de la lógica principal.

#### <a name="return-success-response-to-the-parent-logic-app"></a>Devolver la respuesta de éxito a la aplicación de la lógica principal

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "   Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-the-parent-logic-app"></a>Respuesta de error de retorno a la aplicación de la lógica principal

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="documentdb-repository-and-portal"></a>Portal y DocumentDB repositorio

Nuestra solución agrega capacidades adicionales con [DocumentDB](https://azure.microsoft.com/services/documentdb).

### <a name="error-management-portal"></a>Portal de administración de error

Para ver los errores, puede crear una aplicación web de MVC para mostrar los registros de error de DocumentDB. Operaciones de **lista**, **Detalles**, **Editar**y **Eliminar** se incluyen en la versión actual.

> [AZURE.NOTE]Editar operación: DocumentDB hace un reemplazo de todo el documento.
> Los registros que se muestran en la **lista** y vistas de **detalle** son sólo ejemplos. No son registros de cita pacientes real.

Siguientes son ejemplos de nuestros detalles de la aplicación MVC creados con el enfoque descrito anteriormente.

#### <a name="error-management-list"></a>Lista de administración de errores

![Lista de errores](./media/app-service-logic-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Vista de detalles de administración de error

![Detalles del error](./media/app-service-logic-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portal de administración de registro

Para ver los registros, también se crea una aplicación web de MVC.  Siguientes son ejemplos de nuestros detalles de la aplicación MVC creados con el enfoque descrito anteriormente.

#### <a name="sample-log-detail-view"></a>Vista de detalles de registro de ejemplo

![Vista de detalles de registro](./media/app-service-logic-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Detalles de la aplicación API

#### <a name="logic-apps-exception-management-api"></a>Administración de excepciones de aplicaciones API de lógica

Nuestra aplicación de API de administración de código abierto lógica aplicaciones excepción proporciona las siguientes funciones.

Hay dos controladores:

- **ErrorController** inserta un registro de errores (documento) en una colección de DocumentDB.
- **LogController** Inserta un registro (documento) en una colección de DocumentDB.

> [AZURE.TIP] Utilizan ambos controladores `async Task<dynamic>` operaciones. Esto permite operaciones se resuelven en tiempo de ejecución, por lo que podemos crear el esquema de DocumentDB en el cuerpo de la operación.

Todos los documentos de DocumentDB deben tener un identificador único. Estamos usando `PatientId` y agregar una marca de tiempo que se convierte en un valor de marca de tiempo de Unix (dos). Se trunca para quitar el valor decimal.

Puede ver el código fuente de nuestro controlador error API [de GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs).

Llamamos la API desde una aplicación de lógica mediante la siguiente sintaxis.

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

La expresión en el código de ejemplo anterior está comprobando el estado de *Create_NewPatientRecord* de **error**.

## <a name="summary"></a>Resumen

- Puede implementar fácilmente de registro y control de errores en una aplicación de lógica.
- Puede usar DocumentDB como repositorio para los registros de error y registro (documentos).
- Puede usar MVC para crear un portal para mostrar los registros de error y registro.

### <a name="source-code"></a>Código fuente
El código fuente de la administración de excepciones de aplicaciones de lógica aplicación API está disponible en este [GitHub repositorio](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API de administración de excepción de lógica de aplicación").


## <a name="next-steps"></a>Pasos siguientes
- [Ver más ejemplos de aplicaciones de lógica y escenarios](app-service-logic-examples-and-scenarios.md)
- [Obtenga más información sobre herramientas de supervisión de aplicaciones de lógica](app-service-logic-monitor-your-logic-apps.md)
- [Crear una plantilla de la distribución automática de aplicación de lógica](app-service-logic-create-deploy-template.md)
