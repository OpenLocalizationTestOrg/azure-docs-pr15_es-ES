<properties
   pageTitle="Aplicaciones de lógica como extremos puede llamadas"
   description="Cómo crear y configurar extremos de desencadenador y usarlos en una aplicación de la lógica de servicio de la aplicación de Azure"
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


# <a name="logic-apps-as-callable-endpoints"></a>Aplicaciones de lógica como extremos puede llamadas

Lógica de aplicaciones de forma nativa puede exponer un extremo HTTP sincrónico como desencadenador.  También puede usar el modelo de extremos que se puede llamar para invocar lógica de aplicaciones como un flujo de trabajo anidada a través de la acción "flujo de trabajo" en una aplicación de lógica.

Hay 3 tipos de desencadenadores que pueden recibir solicitudes:

* Solicitar
* ApiConnectionWebhook
* HttpWebhook

Para el resto de este artículo, usaremos la **solicitud** como en el ejemplo, pero todos los principios aplicarán idéntica a la 2 tipos de desencadenadores.

## <a name="adding-a-trigger-to-your-definition"></a>Agregar un desencadenador para la definición de
El primer paso es agregar un desencadenador para la definición de aplicación de lógica que pueda recibir las solicitudes entrantes.  Puede buscar en el Diseñador de "Solicitud de HTTP" agregar la tarjeta de desencadenador. Puede definir un esquema de JSON cuerpo de la solicitud y el diseñador generará tokens para ayudarle a analizar y pasar datos desde el desencadenador manual a través del flujo de trabajo.  Recomienda usar una herramienta como [jsonschema.net](http://jsonschema.net) para generar un esquema JSON desde una carga de cuerpo de ejemplo.

![Tarjeta de desencadenador de solicitud][2]

Después de guardar la definición de una aplicación lógica, se generará una dirección URL de devolución de llamada similar a esta:
 
``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
```

Esta dirección URL contiene una clave de SA en los parámetros de consulta que se utiliza para la autenticación.

También puede obtener este extremo en el portal de Azure:

![][1]

O bien, llamando:

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

### <a name="security-for-the-trigger-url"></a>Seguridad de la dirección URL de desencadenador

Direcciones URL de devolución de llamada de aplicación lógica se generan de forma segura con una firma de acceso compartido.  La firma se pasa como un parámetro de consulta y se debe validar antes de la aplicación de la lógica se aplicará.  Se genera a través de una combinación única de una clave secreta por aplicación lógica, el nombre del desencadenador y realizar la operación.  A menos que un usuario tiene acceso a la clave de aplicación lógica secreto, no podrán generar una firma válida.

## <a name="calling-the-logic-app-triggers-endpoint"></a>Llamar a extremo del desencadenador de aplicación lógica

Una vez haya creado el punto final para el desencadenador, se puede activar a través de un `POST` a la dirección URL completa. Puede incluir encabezados adicionales y cualquier contenido en el cuerpo.

Si el tipo de contenido es `application/json` podrá a las propiedades de referencia desde dentro de la solicitud. En caso contrario, se tratará como una sola unidad binaria que se puede pasar a otras API, pero no puede hacer referencia a dentro del flujo de trabajo sin convertir el contenido.  Por ejemplo, si se pasa `application/xml` contenido podría usar `@xpath()` hacer una extracción xpath, o `@json()` para convertir de XML a JSON.  Para obtener más información sobre cómo trabajar con contenido tipos [pueden encontrarse aquí](app-service-logic-content-type.md)

Además, puede especificar un esquema JSON en la definición. Hace que el diseñador generar tokens que puede pasar en pasos.  Por ejemplo siguiente hará un `title` y `name` disponibles en el Diseñador de token:

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## <a name="referencing-the-content-of-the-incoming-request"></a>Hacer referencia al contenido de la solicitud entrante

La `@triggerOutputs()` función mostrará el contenido de la solicitud entrante. Por ejemplo, sería como:

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

Puede usar el `@triggerBody()` acceso directo para tener acceso a la `body` propiedad específicamente. 

## <a name="responding-to-the-request"></a>Responder a la solicitud

Para algunos las solicitudes de iniciar una aplicación de lógica, desea responder con parte del contenido a la persona que llama. Hay un nuevo tipo de acción denominado **respuesta** que se pueden usar para crear el código de estado, cuerpo y los encabezados de la respuesta. Nota que, si no hay ninguna forma de **respuesta** , el extremo de aplicación lógica le *inmediatamente* responder con **202 aceptado**.

![Acción de respuesta HTTP][3]

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

Las respuestas tienen lo siguiente:

| (Propiedad) | Descripción |
| -------- | ----------- |
| statusCode | El código de estado HTTP para responder a la solicitud entrante. Puede ser cualquier código de estado válida que se inicia con 2xx, 4xx o 5xx. no se permiten 3xx códigos de estado. | 
| cuerpo | Un objeto de cuerpo que puede ser una cadena, un objeto JSON o incluso binario contenido que se hace referencia en un paso anterior. | 
| encabezados | Puede definir cualquier número de encabezados que se incluirán en la respuesta | 

Todos los pasos en la aplicación lógica necesarios para la respuesta deben completar dentro de *60 segundos* de la solicitud original recibir la respuesta **a menos que el flujo de trabajo se llama como una aplicación de lógica anidada**. Si no se alcanza ninguna acción de respuesta dentro de 60 segundos, a continuación, la solicitud entrante se tiempo de espera y reciba una respuesta de **408 tiempo de espera de cliente** HTTP.  Para las aplicaciones de lógica anidadas, la principal aplicación lógica seguirá espere una respuesta hasta que haya completado, independientemente de la cantidad de tiempo que se tarda.

## <a name="advanced-endpoint-configuration"></a>Configuración avanzada de extremo

Aplicaciones de lógica dispone de compatibilidad integrada para el extremo del acceso directo y usar siempre la `POST` método para iniciar una ejecución de la aplicación lógica. La aplicación de **Escucha HTTP** API también admitía cambiando los segmentos de dirección URL y el método HTTP. Puede incluso configurar seguridad adicional o un dominio personalizado agregando al host de aplicación API (la aplicación Web que hospeda la aplicación de la API). 

Esta funcionalidad está disponible a través de **administración de la API**:
* [Cambiar el método de la solicitud](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Cambiar los segmentos de dirección URL de la solicitud](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Configurar los dominios de administración de la API en la ficha **Configurar** en el portal de Azure clásica
* Configurar directivas para comprobar la autenticación básica (**vínculo es necesario**)

## <a name="summary-of-migration-from-2014-12-01-preview"></a>Resumen de migración de vista previa de 2014-12-01

|  vista previa de 2014-12-01 | 2016-06-01 |
|---------------------|--------------------|
| Haga clic en la aplicación de **Escucha HTTP** API | Haga clic en **desencadenador Manual** (ninguna aplicación API requerido) |
| Escucha de HTTP estableciendo "*envía respuesta automáticamente*" | Ya sea incluir una acción de **respuesta** o no en la definición del flujo de trabajo |
| Configurar la autenticación básica o OAuth | mediante la administración de la API |
| Configurar método HTTP | mediante la administración de la API |
| Configurar la ruta de acceso relativa | mediante la administración de la API |
| Referencia del cuerpo entrante a través de`@triggerOutputs().body.Content` | Referencia a través de`@triggerOutputs().body` |
| Acción de **respuesta HTTP enviar** en la escucha HTTP | Haga clic en **responder a la solicitud HTTP** (ninguna aplicación API requerido)


[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png
