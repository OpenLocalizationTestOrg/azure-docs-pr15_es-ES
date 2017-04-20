<properties
   pageTitle="Escenario de aplicación lógica: crear un desencadenador de Bus de servicio de funciones de Azure | Microsoft Azure"
   description="Usar funciones de Azure para crear un desencadenador de Bus de servicio para una aplicación de lógica"
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
   ms.date="05/23/2016"
   ms.author="jehollan"/>

# <a name="logic-app-scenario-create-an-azure-service-bus-trigger-by-using-azure-functions"></a>Escenario de aplicación lógica: crear un desencadenador de Bus de servicio de Azure mediante funciones de Azure

Puede usar funciones de Azure para crear un desencadenador para una aplicación de lógica cuando necesite implementar una tarea o escucha de ejecución larga. Por ejemplo, puede crear una función que escuchar de una cola y, a continuación, aplique inmediatamente una aplicación lógica como un desencadenador de inserción.

## <a name="build-the-logic-app"></a>Crear la aplicación de lógica

En este ejemplo, tiene una función que se ejecuta para cada aplicación de lógica que debe realizarse. Primero, cree una aplicación de lógica que tiene un desencadenador de solicitud HTTP. La función llama a ese extremo siempre que se recibe un mensaje de cola.  

1. Crear una nueva aplicación lógica; Seleccione el desencadenador **Manual: cuando solicitud HTTP un se recibe** .  
   Si lo desea, puede especificar un esquema JSON para usar con el mensaje de cola con una herramienta como [jsonschema.net](http://jsonschema.net). Pegue el esquema en el desencadenador. Esto ayuda del Diseñador de comprender la forma de los datos y más fácilmente fluyen propiedades mediante el flujo de trabajo.
1. Agregue los pasos adicionales que desee que se produzca después de recibir un mensaje de cola. Por ejemplo, enviar un correo electrónico a través de Office 365.  
1. Guardar la aplicación lógica para generar la dirección URL de devolución de llamada para esta aplicación lógica el desencadenador. La dirección URL aparece en la tarjeta de desencadenador.

![Dirección URL de la devolución de llamada aparece en la tarjeta de desencadenador][1]

## <a name="build-the-function"></a>Crear la función

A continuación, deberá crear una función que actúe como el desencadenador y escuchar la cola.

1. En el [portal de funciones de Azure](https://functions.azure.com/signin), seleccione **Nueva función**y, a continuación, seleccione la plantilla de **ServiceBusQueueTrigger - C#** .

    ![Portal de funciones de Azure][2]

2. Configurar la conexión a la cola de Bus de servicio (que usará el SDK de Bus de servicio de Azure `OnMessageReceive()` escucha).
3. Escribir una función simple para llamar al extremo de la aplicación de lógica (creado anteriormente) mediante el mensaje de cola como un desencadenador. Este es un ejemplo completo de una función. En el ejemplo se usa un `application/json` tipo de contenido del mensaje, pero puede cambiar esta opción si es necesario.

   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";

   public static void Run(string myQueueItem, TraceWriter log)
   {

       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

Para probar, agregar un mensaje de cola a través de una herramienta como el [Explorador de Bus de servicio](https://github.com/paolosalvatori/ServiceBusExplorer). Consulte la aplicación lógica fire inmediatamente después de la función recibe el mensaje.

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG
