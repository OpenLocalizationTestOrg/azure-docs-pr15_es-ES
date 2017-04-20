<properties 
    pageTitle="¿Qué aplicaciones de lógica?" 
    description="Obtenga más información sobre la aplicación de servicio de lógica de aplicaciones" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article" 
    ms.date="10/12/2016"
    ms.author="klam"/>

# <a name="what-are-logic-apps"></a>¿Qué aplicaciones de lógica?

Aplicaciones de lógica proporcionan una manera de simplificar e implementar integraciones scalable y flujos de trabajo en la nube. Proporciona un diseñador visual para representar y automatizar el proceso de una serie de pasos que se conoce como un flujo de trabajo.  Hay [muchos conectores](../connectors/apis-list.md) a través de la nube y local integrar rápidamente a través de los servicios y protocolos.  Una aplicación lógica comienza con un desencadenador (como 'cuando se agrega una cuenta a Dynamics CRM') y después de activación puede empezar muchas acciones de combinaciones, conversiones y lógica de condición.

Las ventajas del uso de las aplicaciones de lógica incluyen lo siguiente:  

- Ahorre tiempo al diseño de procesos complejos mediante fácil de entender las herramientas de diseño
- Implementación de patrones y flujos de trabajo sin problemas, en caso contrario, sería difícil de implementar código
- Introducción a rápidamente a partir de plantillas
- Personalizar la aplicación lógica con su propia API, código y acciones personalizadas
- Conectar y sincronizar sistemas distintos a través de la nube y local
- Generar quitarlas BizTalk server, administración de la API, funciones de Azure y Bus de servicio de Azure con soporte de integración de primera clase

Lógica de aplicaciones es una iPaaS totalmente administrado (integración de plataforma como servicio) que permite a los desarrolladores que no tiene que preocuparse de creación de hospedaje, escalabilidad, disponibilidad y administración.  Lógica de aplicaciones escalar automáticamente a una petición de reunirse.

![Diseñador de flujos de aplicación](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Como se mencionó con lógica de aplicaciones, puede automatizar procesos empresariales. Estos son algunos ejemplos:  
 
* Mover los archivos cargados en un servidor FTP en el almacenamiento de Azure
* Proceso y ruta pedidos en local y sistemas de nube
* Supervisar todos los tweets sobre un tema determinado, analizar la opinión y crear alertas y las tareas para los elementos que necesitan realizar su seguimiento.

Escenarios como estos pueden configurarse desde el diseñador visual y sin escribir una sola línea de código. Ir a la [creación de la aplicación de lógica ahora][create].  Una vez escritos - una aplicación de lógica puede ser [implementado rápidamente y volver a configurar](app-service-logic-create-deploy-template.md) en múltiples entornos y regiones.

## <a name="why-logic-apps"></a>¿Por qué aplicaciones de lógica?

Lógica de aplicaciones ofrece velocidad y escalabilidad en el espacio de integración empresarial.  Facilidad de uso del diseñador, asegúrese de gran variedad de desencadenadores, acciones y potentes herramientas de administración centralizar la API más sencillas que nunca.  Como empresas mover hacia digitalización, aplicaciones de lógica permite conectar sistemas heredados y avanzados.

Además, con nuestra [Cuenta de integración de Enterprise] [ biztalk] puede escalar para adultos escenarios de integración con la potencia de un [mensajería XML][xml], [administración de socios comerciales][tpm]y mucho más.

- **Herramientas de diseño fáciles de usar** - aplicaciones de lógica puede ser diseñada llevar a cabo en el explorador o con Visual Studio tools. Empiece con un desencadenador, desde una programación simple para cuando se crea un problema de GitHub. A continuación, organizar cualquier número de acciones con la Galería de conectores enriquecida.

- **API conectar fácilmente** -tareas de composición par que sean fáciles de describir son difíciles de implementar en el código. Lógica de aplicaciones facilita la conectar sistemas separados. ¿Desea conectar su solución a sus instalaciones de marketing de nube facturación sistema? ¿Desea centralizar mensajería a través de las API de sistemas y con un Bus de servicio de la empresa? Aplicaciones de lógica son la forma más rápida y más confiable ofrecer soluciones para estos problemas.

- **Introducción rápida a partir de plantillas** : para ayudarle a empezar a proporcionamos una [Galería de plantillas de] [ templates] que le permiten crear rápidamente algunas soluciones comunes. Desde soluciones avanzadas de B2B conectividad SaaS simple e incluso unos que sean 'para la diversión': la galería es la manera más rápida de introducción a la potencia de lógica de aplicaciones.

- **Preparado de capacidad de ampliación** - no ve el conector que necesita? Lógica de aplicaciones está diseñado para funcionar con su propia API y código; puede crear fácilmente su propia aplicación API para usar como un conector personalizado o llamar a una [Función de Azure](https://functions.azure.com) para ejecutar fragmentos de código a petición. 

- **Caballo de integración real** - iniciar fácil y aumentar como necesite. Lógica de aplicaciones fácilmente puede aprovechar la potencia de BizTalk, mejor integración solución del sector de Microsoft que permiten crear las soluciones que necesitan los profesionales de integración. Obtenga más información sobre el [Paquete de integración de Enterprise](./app-service-logic-enterprise-integration-overview.md).

## <a name="logic-app-concepts"></a>Conceptos de aplicación de lógica

Las siguientes son algunas de las partes claves que forman parte de la experiencia de aplicaciones de lógica. 

- **Flujo de trabajo** - lógica de aplicaciones le ofrece una forma gráfica para modelar los procesos empresariales como una serie de pasos o un flujo de trabajo.
- **Conectores administra** - sus aplicaciones de lógica necesitan tener acceso a datos y servicios. Conectores administrados se crean específicamente para ayudarle a conectarse a y trabajar con los datos. Vea la lista de conectores ahora disponibles en [administrada conectores][managedapis].
- **Desencadenadores** : algunos conectores administra también pueden actuar como un desencadenador. Un desencadenador inicia una nueva instancia de un flujo de trabajo basado en un evento específico, como la llegada de un mensaje de correo o un cambio en la cuenta de almacenamiento de Azure.
-  **Acciones** - cada paso después de que el desencadenador en un flujo de trabajo se denomina una acción. Cada acción normalmente se asigna a una operación en su conector administrado o aplicaciones personalizadas de API.
- **Paquete de integración de Enterprise** - escenarios de integración más avanzados, aplicaciones de lógica incluye capacidades de BizTalk. BizTalk es la plataforma de integración de Microsoft sector inicial. Los conectores de paquete de integración de Enterprise le permiten incluir fácilmente validación, transformación y mucho más en los flujos de trabajo de aplicación lógica.

## <a name="getting-started"></a>Introducción  

- Para empezar con la lógica de aplicaciones, siga el [crear una aplicación de lógica] [ create] tutorial.  
- [Ver ejemplos y escenarios comunes](app-service-logic-examples-and-scenarios.md)
- [Puede automatizar procesos empresariales con aplicaciones de lógica](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Obtenga información sobre cómo integrar sus sistemas con aplicaciones de lógica](http://channel9.msdn.com/Events/Build/2016/P462)

[biztalk]: app-service-logic-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-enterprise-integration-accounts.md
[xml]: app-service-logic-enterprise-integration-b2b.md
[templates]: app-service-logic-use-logic-app-templates.md
