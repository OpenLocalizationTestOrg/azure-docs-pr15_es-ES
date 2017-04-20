<properties
   pageTitle="Usar funciones de Azure con aplicaciones de lógica | Microsoft Azure"
   description="Vea cómo usar funciones de Azure con aplicaciones de lógica"
   services="logic-apps,functions"
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
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="using-azure-functions-with-logic-apps"></a>Usar funciones de Azure con aplicaciones de lógica

Puede ejecutar fragmentos personalizados de C# o node.js mediante funciones de Azure desde dentro de una aplicación de lógica.  [Funciones de Azure](../azure-functions/functions-overview.md) ofrece informática sin por el servidor de Microsoft Azure. Esto es útil para realizar las siguientes tareas:

* Formato avanzado o cálculo de los campos dentro de una aplicación de lógica
* Realizar cálculos dentro de un flujo de trabajo
* Extender la funcionalidad de aplicaciones de lógica con funciones que son compatibles con C# o node.js

## <a name="create-a-function-for-logic-apps"></a>Crear una función para aplicaciones de lógica

Le recomendamos que cree una nueva función en el portal de funciones de Azure con las plantillas **Genérico Webhook - nodo** o **Genérico Webhook - C#** . Esto llena automáticamente una plantilla que acepta `application/json` desde una aplicación de lógica.  Si selecciona la ficha **integrar** en las funciones de Azure debería tener **modo** **Webhook** y **Webhook tipo** de **JSON genérico**.  Funciones que utilizan estas plantillas se detectan automáticamente y que aparece en el Diseñador de aplicaciones de lógica en **funciones de Azure en mi país.**

Aceptar una solicitud de funciones de Webhook y pase al método a través de un `data` variable. Puede acceder a las propiedades de la carga utilizando la notación de puntos como `data.foo`.  Por ejemplo, una función de JavaScript simple que convierte un valor de fecha en una cadena de fecha es similar en el siguiente ejemplo:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-a-logic-app"></a>Llamar a funciones de Azure desde una aplicación de lógica

En el diseñador, si hace clic en el menú **acciones** , puede seleccionar las **Funciones de Azure en mi país**.  Enumera los contenedores en la suscripción y le permite elegir la función que desea llamar.  

Después de seleccionar la función, se le pide que especifique un objeto de entrada de carga. Este es el mensaje que la aplicación de la lógica se envía a la función y debe ser un objeto JSON. Por ejemplo, si desea pasar de la fecha de **Última modificación** de un desencadenador de Salesforce, la carga de la función podría ser similar a esta:

![Última fecha de modificarse][1]

## <a name="trigger-logic-apps-from-a-function"></a>Aplicaciones de lógica de desencadenador de una función

También es posible activar una aplicación de lógica desde dentro de una función.  Para ello, simplemente cree una aplicación de lógica con un desencadenador manual. Para obtener más información, consulte [aplicaciones de lógica como extremos puede llamadas](app-service-logic-http-endpoint.md).  A continuación, dentro de la función generar un HTTP POST a la dirección URL de desencadenador manual con la carga que desea enviar a la aplicación de la lógica.

### <a name="create-a-function-from-the-designer"></a>Crear una función en el diseñador

También puede crear una función webhook node.js desde el diseñador. En primer lugar, seleccione **Las funciones de Azure en mi país** y, a continuación, elija un contenedor de la función.  Si todavía no tiene un contenedor, debe crearla desde el [portal de funciones de Azure](https://functions.azure.com/signin). A continuación, seleccione **Crear nuevo**.  

Para generar una plantilla a partir de los datos que desea calcular, especifique el objeto de contexto que se va a pasar a una función. Debe ser un objeto JSON. Por ejemplo, si se pasa el contenido del archivo de una acción de FTP, la carga de contexto tendrá este aspecto:

![Carga de contexto][2]

>[AZURE.NOTE] Dado que este objeto no se ha convertido en una cadena, se agregará el contenido directamente a la carga JSON. Sin embargo, lo hará error si no es un símbolo JSON (es decir, una cadena o un objeto o matriz JSON). Para convertirlo en una cadena, basta con agregar comillas tal como se muestra en la primera ilustración en este artículo.

El diseñador genera una plantilla de función que se puede crear en línea. Las variables previamente se crean según el contexto en el que se va a pasar a la función.




<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png
