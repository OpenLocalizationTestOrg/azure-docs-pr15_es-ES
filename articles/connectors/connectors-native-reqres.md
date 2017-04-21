<properties
    pageTitle="Usar acciones de convocatoria y respuesta | Microsoft Azure"
    description="Información general sobre el desencadenador de convocatoria y respuesta y una acción en una aplicación de la lógica de Azure"
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-request-and-response-components"></a>Introducción a los componentes de convocatoria y respuesta

Con los componentes de convocatoria y respuesta en una aplicación de lógica, puede responder a eventos a en tiempo real.

Por ejemplo, puede:

- Responder a una solicitud HTTP con datos desde una base de datos local a través de una aplicación de lógica.
- Desencadenar una aplicación de la lógica de un evento webhook externos.
- Llamar a una aplicación de lógica con una acción convocatoria y respuesta desde otra aplicación de lógica.

Para empezar a usar las acciones de la solicitud y la respuesta en una aplicación de lógica, consulte [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-http-request-trigger"></a>Utilice el desencadenador de solicitud HTTP

Un desencadenador es un evento que se pueden usar para iniciar el flujo de trabajo que se define en una aplicación de lógica. [Obtenga más información acerca de desencadenadores](connectors-overview.md).

Aquí tiene una secuencia de ejemplo de cómo configurar una solicitud HTTP en el Diseñador de lógica de aplicación.

1. Agregar el desencadenador **- solicitud HTTP un cuando es recibida** en la aplicación de la lógica. Puede proporcionar un esquema JSON (mediante una herramienta como [JSONSchema.net](http://jsonschema.net)) para el cuerpo de la solicitud. Esto permite al diseñador generar tokens para las propiedades de la solicitud HTTP.
2. Agregar otra acción para que puede guardar la aplicación lógica.
3. Después de guardar la aplicación lógica, puede obtener la URL de la solicitud HTTP de la tarjeta de la solicitud.
4. HTTP POST (puede usar una herramienta como [Postman](https://www.getpostman.com/)) a la dirección URL se activa la aplicación lógica.

>[AZURE.NOTE] Si no define una acción de respuesta, un `202 ACCEPTED` respuesta inmediatamente se devuelve a la persona que llama. Puede usar la acción de respuesta para personalizar una respuesta.

![Desencadenador de respuesta](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Usar la acción de respuesta HTTP

La acción de respuesta HTTP solo es válida cuando se usa en un flujo de trabajo que se activa mediante una solicitud HTTP. Si no define una acción de respuesta, un `202 ACCEPTED` respuesta inmediatamente se devuelve a la persona que llama.  Puede agregar una acción de respuesta en cualquier paso del flujo de trabajo. La aplicación de lógica solo mantiene la solicitud entrante abierta durante un minuto para una respuesta.  Después de un minuto, si no hay respuesta enviada desde el flujo de trabajo (y existe una acción de respuesta en la definición), un `504 GATEWAY TIMEOUT` se devuelven a la persona que llama.

Aquí le mostramos cómo agregar una acción de respuesta HTTP:

1. Seleccione el botón **Nuevo paso** .
2. Elija **Agregar una acción**.
3. En el cuadro de búsqueda de la acción, escriba la **respuesta** para la acción de respuesta de la lista.

    ![Seleccione la acción de respuesta](./media/connectors-native-reqres/using-action-1.png)

4. Agregar todos los parámetros necesarios para el mensaje de respuesta HTTP.

    ![Completar la acción de respuesta](./media/connectors-native-reqres/using-action-2.png)

5. Haga clic en la esquina superior izquierda de la barra de herramientas para guardar, y su aplicación lógica guardar y publicar (activar).

## <a name="request-trigger"></a>Solicitud de desencadenador

Estos son los detalles del desencadenador que admita este conector. Hay un desencadenador de solicitud.

|Desencadenador|Descripción|
|---|---|
|Solicitar|Se produce cuando se recibe una solicitud HTTP|

## <a name="response-action"></a>Acción de respuesta

Estos son los detalles de la acción que admita este conector. Hay una acción de respuesta único que solo se puede usar cuando se indique un desencadenador de solicitud.

|Acción|Descripción|
|---|---|
|Respuesta|Devuelve una respuesta a la solicitud HTTP relacionada|

### <a name="trigger-and-action-details"></a>Detalles de desencadenador y acción

Las tablas siguientes describen los campos de entrada para el desencadenador y la acción y detalles de salida de la correspondiente.

#### <a name="request-trigger"></a>Solicitud de desencadenador
A continuación se muestra un campo de entrada para el desencadenador de una solicitud HTTP entrante.

|Nombre para mostrar|Nombre de propiedad|Descripción|
|---|---|---|
|Esquema JSON|esquema|El esquema JSON del cuerpo de la solicitud HTTP|
<br>

**Detalles de salida**

Los siguientes son los detalles de salida de la solicitud.

|Nombre de propiedad|Tipo de datos|Descripción|
|---|---|---|
|Encabezados|objeto|Encabezados de solicitud|
|Cuerpo|objeto|Objeto de solicitud|

#### <a name="response-action"></a>Acción de respuesta

Los siguientes son los campos de entrada de la acción de respuesta HTTP. A * significa que es un campo obligatorio.

|Nombre para mostrar|Nombre de propiedad|Descripción|
|---|---|---|
|Código de estado *|statusCode|El código de estado HTTP|
|Encabezados|encabezados|Objeto JSON de los encabezados de respuesta para incluir|
|Cuerpo|cuerpo|El cuerpo de respuesta|

## <a name="next-steps"></a>Pasos siguientes

Ahora, pruebe la plataforma y [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Puede explorar los otros conectores disponibles en las aplicaciones de lógica consultando nuestra [lista de API](apis-list.md).
