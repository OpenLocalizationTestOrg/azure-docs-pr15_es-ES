<properties
    pageTitle="Elija entre flujo, aplicaciones de lógica, funciones y WebJobs | Microsoft Azure"
    description="Comparar y contrastar la nube integración de servicios de Microsoft y decidir cuáles son los servicios que debe usar."
    services="functions,app-service\logic"
    documentationCenter="na"
    authors="cephalin"
    manager="wpickett"
    tags=""
    keywords="Microsoft flujo, flujo, aplicaciones de lógica, funciones de azure, funciones, azure webjobs, webjobs, procesamiento, cálculo dinámica, sin servidor arquitectura de eventos"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="09/08/2016"
    ms.author="chrande; glenga"/>

# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Elija entre flujo, aplicaciones de lógica, funciones y WebJobs

En este artículo compara y compara los siguientes servicios en la nube de Microsoft, que puede todo solucionar problemas de integración y automatización de procesos empresariales:

- [Flujo de Microsoft](https://flow.microsoft.com/)
- [Aplicaciones de Azure lógica](https://azure.microsoft.com/services/logic-apps/)
- [Funciones de Azure](https://azure.microsoft.com/services/functions/)
- [WebJobs de Azure de aplicación de servicio](../app-service-web/web-sites-create-web-jobs.md)

Todos estos servicios son útiles cuando "pegando" juntas sistemas diferentes. Pueden todos Definir entrada, acciones, condiciones y salida. Puede ejecutar cada uno de ellos en un desencadenador o programación. Sin embargo, cada servicio agrega un conjunto único de valor y compararlas no es una pregunta de "servicio que es la mejor?" pero uno de "qué servicio se adapta mejor a esta situación?" A menudo, una combinación de estos servicios es la mejor manera de crear rápidamente una solución de integración destacado scalable, completa.

<a name="flow"></a>
## <a name="flow-vs-logic-apps"></a>Lógica de flujo frente a aplicaciones

Veremos Flow de Microsoft y aplicaciones de lógica de Azure juntos porque son ambos servicios de integración de *configuración en primer lugar* , lo que facilita la crear flujos de trabajo y procesos e intégrelo con varias aplicaciones SaaS y enterprise. 

- Flujo se basa en aplicaciones de lógica
- Tienen el mismo diseñador de flujo de trabajo
- [Conectores](../connectors/apis-list.md) que funcionan en una también puede trabajar en el otro

Flujos de permite a los trabajadores de office para realizar integraciones simples (por ejemplo, obtener SMS para correos electrónicos importantes) sin pasar por los desarrolladores o TI. Por otro lado, aplicaciones de lógica puede habilitar avanzadas o críticas integraciones (por ejemplo, los procesos de B2B) donde se requieren prácticas de seguridad y DevOps de nivel de empresa. Es típico para un flujo de trabajo empresarial ganar en horas extra complejidad. Por consiguiente, puede empezar con un flujo en primer lugar y luego convertirla en una aplicación de lógica según sea necesario.

La siguiente tabla le ayuda a determinar si es mejores para una determinada integración flujo o aplicaciones de lógica.

|               | Flujo                                                                             | Aplicaciones de lógica                                                                                          |
|---------------|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| Audiencia      | trabajadores de Office, los usuarios empresariales                                                   | Los profesionales de TI, los desarrolladores                                                                                 |
| Escenarios     | Autoservicio                                                                     | Críticas                                                                                    |
| Herramienta de diseño   | En el explorador, solo la interfaz de usuario                                                              | En el explorador y [Visual Studio](../app-service/logic/app-service-logic-deploy-from-vs.md), la [vista código](../app-service-logic/app-service-logic-author-definitions.md) disponibles |
| DevOps        | Ad-hoc, desarrollar en producción                                                    | control de código fuente, pruebas, soporte y automatización y capacidad de [Administración de recursos de Azure](../app-service-logic/app-service-logic-arm-provision.md)|
| Experiencia de administración| [https://Flow.Microsoft.com](https://flow.microsoft.com)                       | [https://portal.Azure.com](https://portal.azure.com)                                                |
| Seguridad      | Prácticas estándar: [soberanía de datos](https://wikipedia.org/wiki/Technological_Sovereignty), el [cifrado al resto](https://wikipedia.org/wiki/Data_at_rest#Encryption) de los datos confidenciales, etcetera. | Garantía de seguridad de Azure: [Seguridad de Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Centro de seguridad](https://azure.microsoft.com/services/security-center/), [registros de auditoría](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/)y más. |

<a name="function"></a>
## <a name="functions-vs-webjobs"></a>Funciones frente a WebJobs

Se pueden tratar las funciones de Azure y Azure aplicación de servicio WebJobs juntos porque son ambos servicios de integración de *código en primer lugar* y diseñado para programadores. Permiten ejecutar una secuencia de comandos o un fragmento de código en respuesta a distintos eventos, como [Nuevo almacenamiento de Blobs](functions-bindings-storage.md) o [una solicitud de WebHook](functions-bindings-http-webhook.md). Estas son sus similitudes: 

- Ambos se basan en el [Servicio de aplicación de Azure](../app-service/app-service-value-prop-what-is.md) y disfrutarán de funciones como el [control de código fuente](../app-service-web/app-service-continuous-deployment.md), la [autenticación](../app-service/app-service-authentication-overview.md)y la [supervisión](../app-service-web/web-sites-monitor.md).
- Ambas son servicios centrados en programador.
- Soporte estándar scripting tanto lenguajes de programación.
- Ambos tienen NuGet y NPM de soporte técnico.

Funciones es la evolución natural de WebJobs que toma las mejores cosas de WebJobs y mejora con ellos. Las mejoras incluyen: 

- Desarrollo de simplificada, probar y ejecutar de código directamente en el explorador.
- Integración con servicios más Azure y 3 º entidades como [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
- Pago por uso, sin tener que pagar por un [Plan de servicio de aplicación](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
- Automática, [escala dinámico](functions-scale.md).
- Para los clientes existentes de servicio de aplicación que se ejecuta en el plan de servicios de aplicación posible (para aprovechar las ventajas de los recursos utilizados de).
- Integración con aplicaciones de lógica.

La siguiente tabla resume las diferencias entre funciones y WebJobs:

|                        | Funciones                                                                                                                                                                | WebJobs                            |
|------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------|
| Ajuste de escala                | Escala configurationless                                                                                                                                                | cambiar la escala con el plan de servicios de aplicación        |
| Precios                | Pago por uso o parte del plan de servicios de aplicación                                                                                                                                  | Parte del plan de servicio de aplicaciones           |
| Tipo de ejecución               | activa, programadas (por desencadenador temporizador)                                                                                                                                  | desencadenadas, continua, programada   |
| Desencadenar eventos         | [temporizador](functions-bindings-timer.md), [DocumentDB de Azure](functions-bindings-documentdb.md), [Hubs de evento de Azure](functions-bindings-event-hubs), [HTTP/WebHook (GitHub, margen de demora)](functions-bindings-http-webhook.md), [Aplicaciones de Azure aplicación de servicio móvil](functions-bindings-mobile-apps.md), [Hubs de notificación de Azure](functions-bindings-notification-hubs.md), [Bus de servicio de Azure](functions-bindings-service-bus.md), [Almacenamiento Azure](articles/functions-bindings-storage.md) | [Almacenamiento de azure](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), [Bus de servicio de Azure](websites-dotnet-webjobs-sdk-service-bus.md)         |
| Desarrollo de en el explorador | x                                                                                                                                                                        |                                    |
| Ventana de secuencias de comandos       | experimentación                                                                                                                                                             | x                                  |
| PowerShell             | experimentación                                                                                                                                                             | x                                  |
| C#                     | x                                                                                                                                                                        | x                                  |
| F#                     | x                                                                                                                                                                        |                                    |
| Fiesta                   | experimentación                                                                                                                                                             | x                                  |
| PHP                    | experimentación                                                                                                                                                             | x                                  |
| Python                 | experimentación                                                                                                                                                             | x                                  |
| JavaScript             | x                                                                                                                                                                        | x                                  |
| Java                   | experimentación                                                                                                                                                             | x                                  |

Si va a usar funciones o WebJobs finalmente depende de lo que está haciendo ya con la aplicación de servicio. Si tiene una aplicación de servicio de aplicaciones para el que desea ejecutar fragmentos de código y desea administrar de forma conjunta en el mismo entorno DevOps, debe usar WebJobs. Si desea ejecutar fragmentos de código para otros servicios de Azure o incluso 3 º entidades aplicaciones, o si desea administrar sus fragmentos de código de integración independientemente de sus aplicaciones de servicio de la aplicación, o si desea llamar a los fragmentos de código desde una aplicación de lógica, debe sacar partido de todas las mejoras de funciones.  

<a name="together"></a>
## <a name="flow-logic-apps-and-functions-together"></a>Flujo, lógica de aplicaciones y funciones juntos

Como se mencionó anteriormente, el servicio que se adapta mejor a la depende de su situación. 

- Para la optimización empresarial sencilla, a continuación, use flujo.
- Si también se avanzada del escenario de integración para el flujo de o necesita capacidades de DevOps y directrices de seguridad, a continuación, usar las aplicaciones de lógica.
- Si un paso en el escenario de integración requiere transformación altamente personalizada o código especializado, a continuación, escribir una aplicación de la función y, a continuación, desencadenar una función como una acción en la aplicación de la lógica.

Llamar a una aplicación de la lógica de un flujo. También puede llamar a una función en una aplicación de lógica y una aplicación lógica en una función. Continuar la integración entre flujo, lógica de aplicaciones y funciones mejorar de horas extra. Puede crear algo en un servicio y usarla en otros servicios. Por lo tanto, las inversiones que realice en estas tres tecnologías vale la pena.

## <a name="next-steps"></a>Pasos siguientes

Introducción a cada uno de los servicios mediante la creación de su primer flujo, aplicación lógica, aplicación de la función o WebJob. Haga clic en cualquiera de los siguientes vínculos:

- [Introducción a Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
- [Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Crear su primera función de Azure](../azure-functions/functions-create-first-azure-function.md)
- [Implementar WebJobs mediante Visual Studio](../app-service-web/websites-dotnet-deploy-webjobs.md)

O bien, puede obtener más información acerca de los servicios de integración con los siguientes vínculos:

- [Sacar provecho de las funciones de Azure y Azure aplicación de servicio para escenarios de integración de Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
- [Integraciones realizados Simple por Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
- [Aplicaciones de lógica Live presentación Web](http://aka.ms/logicappslive)
- [Microsoft flujo con frecuencia preguntas más frecuentes](https://flow.microsoft.com/documentation/frequently-asked-questions/)
- [Recursos de documentación WebJobs Azure](../app-service-web/websites-webjobs-resources.md)
