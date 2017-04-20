<properties
   pageTitle="Aplicaciones de lógica ejemplos y escenarios | Microsoft Azure"
   description="Vea ejemplos de aplicaciones comunes de lógica y aprenda a implementar escenarios comunes"
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

# <a name="logic-apps-examples-and-common-scenarios"></a>Escenarios comunes y ejemplos de aplicaciones de lógica

Este escenarios comunes de documento detalles y ejemplos para ayudarle a entender algunas de las formas pueden usar las aplicaciones de lógica para automatizar procesos empresariales. 

## <a name="custom-triggers-and-actions"></a>Acciones y desencadenadores personalizados

Hay varias maneras que pueden desencadenar una aplicación lógica desde otra aplicación. Hay algunos ejemplos comunes:

- [Crear un desencadenador personalizado o una acción](app-service-logic-create-api-app.md)
- [Acciones de larga duración](app-service-logic-create-api-app.md)
- [Desencadenador de solicitud HTTP (publicación)](app-service-logic-http-endpoint.md)
- [Acciones y desencadenadores Webhook](app-service-logic-create-api-app.md)
- [Desencadenadores de sondeo](app-service-logic-create-api-app.md)

### <a name="scenarios"></a>Escenarios

- [Solicitar respuesta sincrónico](app-service-logic-http-endpoint.md)
- [Solicitar respuesta con SMS](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="error-handling-and-logging"></a>Registro de errores y

- [Excepciones y control de errores](app-service-logic-exception-handling.md)
- [Configurar alertas de Azure y diagnósticos](app-service-logic-monitor-your-logic-apps.md)

### <a name="scenarios"></a>Escenarios

- [Caso de uso: Control de errores y excepciones](app-service-logic-scenario-error-and-exception-handling.md)

## <a name="deploying-and-managing"></a>Implementar y administrar

- [Crear una implementación automatizada](app-service-logic-create-deploy-template.md)
- [Crear e implementar aplicaciones de lógica de Visual Studio](app-service-logic-deploy-from-vs.md)
- [Supervisar aplicaciones de lógica](app-service-logic-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations"></a>Transformaciones, conversiones y tipos de contenido

El [lenguaje de definición de flujo de trabajo](http://aka.ms/logicappsdocs) de aplicaciones de lógica contiene muchas funciones para permitir que se va a convertir y trabajar con diferentes tipos de contenido.  Además el motor hará todo lo que para mantener los tipos de contenido como flujos de datos a través del flujo de trabajo.

- [Tratamiento de tipos de contenido](app-service-logic-content-type.md) como application/json, aplicación/xml y o texto sin formato
- [Crear definiciones de flujo de trabajo](app-service-logic-author-definitions.md)
- [Referencia del lenguaje de definición de flujo de trabajo](http://aka.ms/logicappsdocs)

## <a name="batches-and-looping"></a>Lotes y bucles

- [SplitOn](app-service-logic-loops-and-scopes.md)
- [ForEach](app-service-logic-loops-and-scopes.md)
- [Hasta](app-service-logic-loops-and-scopes.md)

## <a name="integrating-with-azure-functions"></a>Integración con funciones de Azure

- [Integración de funciones de Azure](app-service-logic-azure-functions.md)

### <a name="scenarios"></a>Escenarios

- [Función Azure como un desencadenador de Bus de servicio](app-service-logic-scenario-function-sb-trigger.md)

## <a name="http-rest-and-soap"></a>HTTP, resto y SOAP

 - [Llamar a SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)


Se le mantener agregando ejemplos y escenarios para este documento. Use la siguiente sección de comentarios para hacernos saber qué ejemplos o escenarios que le gustaría ver aquí.