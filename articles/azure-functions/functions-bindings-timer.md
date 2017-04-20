<properties
    pageTitle="Desencadenador de temporizador de funciones Azure | Microsoft Azure"
    description="Aprenda a usar desencadenadores de temporizador en funciones de Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="funciones de Azure, funciones, el procesamiento de eventos, cálculo dinámica, sin servidor arquitectura"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-timer-trigger"></a>Azure desencadenador de temporizador de funciones

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar desencadenadores de temporizador en las funciones de Azure. Temporizador activa las funciones de llamada según una programación, una vez o periódica.  

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a name="functionjson-for-timer-trigger"></a>Function.JSON desencadenador de temporizador

El archivo *function.json* proporciona una expresión de programación. Por ejemplo, la siguiente programación ejecuta la función cada minuto:

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

El desencadenador temporizador controla instancias múltiples escalado automáticamente: una única instancia de una función de temporizador determinado se ejecutará en todas las instancias.

## <a name="format-of-schedule-expression"></a>Formato de expresión de programación

La expresión de programación es una [expresión CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) que incluye 6 campos: `{second} {minute} {hour} {day} {month} {day of the week}`. 

Tenga en cuenta que muchas de las expresiones de cron que se encuentre en línea omiten el campo {segundo}, por lo que si copia desde una de ellas tendrá que ajustar el campo adicional. 

Estas es algunas otra programación ejemplos de expresiones:

Para desencadenar una vez cada 5 minutos:

```json
"schedule": "0 */5 * * * *"
```

Para activar una vez en la parte superior de cada hora:

```json
"schedule": "0 0 * * * *",
```

Para desencadenar una vez cada dos horas:

```json
"schedule": "0 0 */2 * * *",
```

Para desencadenar una vez cada hora de 9 A.M. a 5 P.M.:

```json
"schedule": "0 0 9-17 * * *",
```

Para desencadenar a 9:30 A.M. todos los días:

```json
"schedule": "0 30 9 * * *",
```

Para desencadenar a 9:30 A.M. todos los días laborables:

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="timer-trigger-c-code-example"></a>Ejemplo de código de desencadenador C# temporizador

Este ejemplo de código C# escribe un único registro cada vez que se activa la función.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
