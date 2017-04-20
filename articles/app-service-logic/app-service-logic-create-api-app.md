<properties 
    pageTitle="Crear una API para aplicaciones de lógica" 
    description="Crear una API personalizada para usarlos con aplicaciones de lógica" 
    authors="jeffhollan" 
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
    ms.date="10/18/2016"
    ms.author="jehollan"/>
    
# <a name="creating-a-custom-api-to-use-with-logic-apps"></a>Crear una API personalizada para usarlos con aplicaciones de lógica

Si desea ampliar la plataforma de aplicaciones de lógica, hay muchas formas, que puede llamar a la API o sistemas que no están disponibles como uno de nuestros muchos conectores de cuadro.  Una de las formas de crear un App API puede llamar desde dentro de un flujo de trabajo de la aplicación de la lógica.

## <a name="helpful-tools"></a>Herramientas útiles

Para que las API trabajar mejor con lógica de aplicaciones, se recomienda generar a un documento [swagger](http://swagger.io) que detalla las operaciones compatibles y parámetros de la API.  Hay muchas bibliotecas (como [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)) que se generarán automáticamente el swagger para usted.  También puede usar [TRex](https://github.com/nihaue/TRex) para ayudar a realizar anotaciones en la swagger para que funcione correctamente con aplicaciones de lógica (Mostrar nombres, tipos de propiedades, etcetera.).  Para algunos ejemplos de aplicaciones de la API integrado para lógica de aplicaciones, asegúrese de desproteger nuestro [repositorio de GitHub](http://github.com/logicappsio) o un [blog](http://aka.ms/logicappsblog).

## <a name="actions"></a>Acciones

La acción básica para una aplicación de lógica es un controlador que aceptar una solicitud de HTTP y devolver una respuesta (normalmente 200).  Sin embargo hay tramas diferentes que puede seguir para ampliar acciones según sus necesidades.

De forma predeterminada, el motor de aplicación lógica tendrá tiempo de espera de una solicitud después de 1 minuto.  Sin embargo, puede que la API ejecutar acciones que tarden y tiene el motor esperar a que finalice, siguiendo un asincrónico o webhook patrón descrito a continuación.

Para acciones estándares, simplemente escribir un método de solicitud HTTP en la API que se expone a través de swagger.  Puede ver ejemplos de aplicaciones de la API que funcionan con aplicaciones de lógica en nuestro [repositorio de GitHub](https://github.com/logicappsio).  A continuación se muestran maneras para llevar a cabo patrones comunes con un conector personalizado.

### <a name="long-running-actions---async-pattern"></a>Acciones de ejecución larga - patrón asincrónico

Cuando se ejecuta un paso larga o una tarea, lo primero que debe hacer es asegurarse de que el motor sabe que no ha agotado el tiempo. También debe comunicarse con el motor cómo sabrá cuando haya terminado con la tarea y, por último, necesita devolver datos pertinentes para el motor de continuar con el flujo de trabajo. Puede completar a través de una API siguiendo el flujo siguiente. Estos pasos son desde el punto de vista de la API personalizada:

1. Cuando se recibe una solicitud, devolver una respuesta inmediatamente (antes de realizar trabajo). Esta respuesta será un `202 ACCEPTED` respuesta, saber el motor obtuvo los datos, acepte la carga y ahora está procesando. La respuesta 202 debe contener los encabezados siguientes: 
 * `location`encabezado (requerido): este es absoluta puede usar ruta de acceso a las aplicaciones de lógica de la dirección URL para comprobar el estado del trabajo.
 * `retry-after`(opcional, le 20 acciones de forma predeterminada). Este es el número de segundos que debe esperar el motor antes de la dirección URL de encabezado para comprobar el estado de sondeo.

2. Cuando se activa el estado del trabajo, realice las siguientes comprobaciones: 
 * Si se realiza el trabajo: devolver un `200 OK` respuesta, con la carga de respuesta.
 * Si el trabajo aún se está procesando: devolver otro `202 ACCEPTED` respuesta, con los mismos encabezados como la respuesta inicial

Este modelo le permite realizar tareas muy largas en un subproceso de la API personalizado, pero mantenimiento de una conexión activa con el motor de aplicaciones de lógica para que no espera o continúe antes de que se haya completado el trabajo. Al agregar esto en la aplicación lógica, es importante que tenga en cuenta que no es necesario hacer nada en la definición de la aplicación de lógica continuar sondeos y comprobar el estado. Tan pronto como el motor detecta una respuesta aceptado 202 con un encabezado de ubicación válido, se respetan el patrón asincrónico y continuar sondear la cabecera de ubicación hasta que se devuelve un usuario que no sean 202.

Puede ver un ejemplo de este patrón en GitHub [aquí](https://github.com/jeffhollan/LogicAppsAsyncResponseSample)

### <a name="webhook-actions"></a>Acciones de Webhook

Durante el flujo de trabajo, puede tener la aplicación de lógica detenga y espere una "devolución de llamada" continuar.  Esta devolución de llamada se suministra en forma de HTTP POST.  Para implementar este patrón, debe proporcionar dos extremos en el controlador: suscribirse y anular la suscripción.

En 'suscribirse', la aplicación de lógica creará y registrar una dirección URL de devolución de llamada que puede almacenar su API y devolución de llamada con listo como HTTP POST.  Los encabezados de contenido se pasarán a la aplicación de lógica y se pueden usar en el resto del flujo de trabajo.  El motor de aplicación lógica llamará el punto de suscribirse en ejecución tan pronto como llegue a ese paso.

Si se ha cancelado la ejecución, el motor de aplicación lógica llamará al extremo 'Cancelar la suscripción'.  La API, a continuación, puede anular la dirección URL de devolución de llamada según sea necesario.

El Diseñador de lógica de aplicación no se admite actualmente descubrir un extremo webhook a través de swagger, por lo que para usar este tipo de acción debe agregar la acción "Webhook" y especifique la dirección URL, encabezados y cuerpo de la convocatoria.  Puede usar el `@listCallbackUrl()` función de flujo de trabajo en cualquiera de los campos según sea necesario para pasar la dirección URL de devolución de llamada.

Puede ver un ejemplo de un modelo de webhook en GitHub [aquí](https://github.com/jeffhollan/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)

## <a name="triggers"></a>Desencadenadores

Además de acciones, puede tener la ley API personalizada como un desencadenador para una aplicación de lógica.  Hay dos patrones que puede seguir a continuación para activar una aplicación de lógica:

### <a name="polling-triggers"></a>Desencadenadores de sondeo

Desencadenadores de sondeo actúan muy similar a las acciones de larga asincrónico ejecuta anteriores.  El motor de aplicación lógica llamará el extremo de desencadenador después de un período de tiempo transcurrido (dependiendo del SKU, 15 segundos para Premium, 1 minuto estándar y 1 hora gratis).

Si hay ningún dato, el desencadenador devuelve un `202 ACCEPTED` respuesta, con un `location` y `retry-after` encabezado.  Sin embargo, para desencadenadores se recomienda la `location` encabezado contiene un parámetro de consulta de `triggerState`.  Se trata de algún identificador de la API saber cuándo la última vez que la aplicación de la lógica se activa.  Si no hay datos disponibles, el desencadenador devuelve un `200 OK` respuesta con la carga de contenido.  Esto aplicará la aplicación lógica.

Por ejemplo, si me sondeo para ver si un archivo estaba disponible, puede crear un desencadenador de sondeo que hacer lo siguiente:

* Si ha recibido una solicitud con ningún triggerState la API devolverá un `202 ACCEPTED` con una `location` encabezado que tiene un triggerState de la hora actual y un `retry-after` de 15.
* Si ha recibido una solicitud con un triggerState:
 * Compruebe si los archivos se agregaron después de la fecha y hora de triggerState. 
  * Si hay 1 archivo, devolver un `200 OK` respuesta con la carga de contenido, incrementar la triggerState a la fecha y hora del archivo que devuelve y establecer la `retry-after` a 15.
  * Si hay varios archivos, ¿devuelven 1 simultáneamente con una `200 OK`, incrementar mi triggerState en la `location` encabezado y establecer `retry-after` en 0.  Esto le permitirá el motor saber hay más datos e inmediatamente, solicitar en el `location` encabezado especificado.
  * Si no hay ningún archivo disponible, devolver un `202 ACCEPTED` respuesta y deja la `location` triggerState la misma.  Establecer `retry-after` a 15.

Puede ver un ejemplo de un desencadenador de sondeo en GitHub [aquí](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)

### <a name="webhook-triggers"></a>Webhook desencadenadores

Desencadenadores Webhook actúan muy similar Webhook acciones anteriores.  El motor de aplicación lógica llamará el extremo 'suscribirse' siempre que se agrega un desencadenador webhook y se guardan.  La API puede registrar la dirección URL de webhook y llamar a través de HTTP POST cada vez que los datos están disponibles.  Los encabezados y la carga de contenido se pasarán a la aplicación de lógica ejecutar.

Si nunca se elimina un desencadenador webhook (la aplicación de lógica por completo o solo el desencadenador webhook), el motor de realizar una llamada a la dirección URL 'Cancelar la suscripción' donde puede anular el registro de la API de la dirección URL de devolución de llamada y detener los procesos según sea necesario.

El Diseñador de lógica de aplicación no se admite actualmente descubrir un desencadenador webhook a través de swagger, por lo que para usar este tipo de acción debe agregar el desencadenador "Webhook" y especifique la dirección URL, encabezados y cuerpo de la convocatoria.  Puede usar el `@listCallbackUrl()` función de flujo de trabajo en cualquiera de los campos según sea necesario para pasar la dirección URL de devolución de llamada.

Puede ver un ejemplo de un desencadenador webhook en GitHub [aquí](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)