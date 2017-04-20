<properties
   pageTitle="Información general sobre funciones de Azure | Microsoft Azure"
   description="Entender cómo utilizar las funciones de Azure para optimizar las cargas de trabajo asincrónicas en minutos."
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure funciones, funciones, procesamiento de eventos, webhooks, cálculo dinámica, sin servidor arquitectura"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/29/2016"
   ms.author="cfowler;mahender;glenga"/>
   
   
# <a name="azure-functions-overview"></a>Información general de las funciones de Azure

Funciones de Azure es una solución para ejecutar fácilmente pequeños fragmentos de código o "acciones", en la nube. Puede escribir simplemente el código que necesario para el problema a mano, sin preocuparse por una aplicación completa o la infraestructura para ejecutarlo. Esto puede hacer que el desarrollo aún más productiva y puede usar el lenguaje de programación de la elección, como C#, F #, Node.js, Python o PHP. Pago solo para el tiempo que se ejecuta el código y la confianza Azure escalar según sea necesario.

Este tema proporciona una descripción general de las funciones de Azure. Si desea directamente a empezar a trabajar con funciones de Azure, empiece por [crear su primera función de Azure](functions-create-first-azure-function.md). Si desea obtener más información técnica sobre funciones, vea la [referencia del programador](functions-reference.md).

## <a name="features"></a>Características

Estas son algunas características clave de las funciones de Azure:
    
* **Elección del idioma** - funciones de escritura con C#, F #, Node.js, Python, PHP, por lotes, fiesta, Java o cualquier archivo ejecutable.
* **Modelo de precios de pago por uso** - pago solo para el tiempo dedicado a ejecutar el código. Ver la opción Plan de servicio de aplicación dinámico en la [sección de precios](#pricing) debajo.  
* **Traer sus propia dependencias** - funciones es compatible con NuGet y NPM, por lo que puede usar las bibliotecas de favoritas.  
* **Seguridad integrada** : proteger HTTP desencadenadas funciones con proveedores de OAuth como Azure Active Directory, Facebook, Google, Twitter y Account de Microsoft.  
* **Integración simplificado** : aprovechar fácilmente servicios de Azure y ofertas de software como-servicio (SaaS). Vea la [sección integraciones](#integrations) debajo para ver algunos ejemplos.  
* **Desarrollo flexible** - su derecho de funciones en el portal de código o configurar la integración continua e implementar el código a través de GitHub, Visual Studio Team Services y otras [Herramientas de desarrollo compatibles](../app-service-web/web-sites-deploy.md#deploy-using-an-ide).  
* **Abrir origen** - tiempo de ejecución de las funciones es Abrir origen y [están disponibles en GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>¿Qué puedo hacer con funciones?

Funciones de Azure es una magnífica solución para procesamiento de datos, integración de sistemas, trabajar con la internet de la cosas (IoT) y la creación de la API simples y microservices. Considere la posibilidad de funciones para tareas como imagen o el orden de procesamiento, mantenimiento de archivos, tareas de larga duración que desea ejecutar en un subproceso en segundo plano, o para las tareas que desea ejecutar una programación. 

Funciones proporciona plantillas para ayudarle a comenzar con situaciones clave, incluidas las siguientes:

* **BlobTrigger** - blobs de Azure almacenamiento de proceso cuando se agregan a los contenedores. Se pueden utilizar para cambiar el tamaño de la imagen.
* **EventHubTrigger** : responder a eventos se ha entregado a un concentrador de evento de Azure. Especialmente útil en instrumentación de aplicaciones, procesamiento de flujo de trabajo o de la experiencia del usuario y escenarios de Internet de cosas (IoT).
* **Genérico webhook** - proceso webhook HTTP solicitudes de cualquier servicio compatible con webhooks.
* **GitHub webhook** - responder a los eventos que se producen en los repositorios GitHub. Para obtener un ejemplo, vea [crear un webhook o función de la API](functions-create-a-web-hook-or-api-function.md).
* **HTTPTrigger** - desencadenador la ejecución del código mediante una solicitud HTTP.
* **QueueTrigger** - responder a mensajes como llegan a una cola de almacenamiento de Azure. Para obtener un ejemplo, vea [crear una función de Azure que enlaza a un servicio de Azure](functions-create-an-azure-connected-function.md).
* **ServiceBusQueueTrigger** - conectar su código a otros servicios de Azure o local servicios escuchando colas de mensajes. 
* **ServiceBusTopicTrigger** - conectar su código a otros servicios de Azure o local servicios al suscribirse a temas. 
* **TimerTrigger** - Ejecutar limpieza u otras tareas de lote en una programación predefinida. Para obtener un ejemplo, vea [crear un evento en función de procesamiento](functions-create-an-event-processing-function.md).

Funciones de Azure es compatible con *desencadenadores*, que son modos de comenzar la ejecución del código y *enlaces*, que son métodos para simplificar la codificación para los datos de entrada y salidos. Para obtener una descripción detallada de los desencadenadores y enlaces que proporciona funciones de Azure, consulte [referencia del programador de enlaces y desencadenadores de funciones de Azure](functions-triggers-bindings.md).


## <a name="integrations"></a>Integraciones

Funciones de Azure se integra con una variedad de Azure y servicios de terceros 3 º. Puede usar estas para desencadenar la función e iniciar la ejecución o para servir de entrada y salida para el código. Las siguientes integraciones de servicio son compatibles con las funciones de Azure. 

* DocumentDB de Azure
* Evento Azure Hubs 
* Aplicaciones de Azure Mobile (tablas)
* Notificación de Azure Hubs
* Azure Bus de servicio (colas y temas)
* Almacenamiento de Azure (blob, colas y tablas) 
* GitHub (webhooks)
* Local (con Bus de servicio)

## <a name="pricing"></a>¿Cuánto cuesta funciones costo?

Funciones de Azure tiene dos tipos de planes de precios, elija el que mejor se adapte a sus necesidades: 

* **Plan de hospedaje dinámico** - cuando se ejecuta la función, Azure proporciona todos los recursos de cálculo necesarios. No tiene que preocuparse por la administración de recursos y paga sólo durante el tiempo que se ejecuta el código. Los detalles de precios completos están disponibles en la [página de precios de funciones](/pricing/details/functions). 

* **Plan de servicios de aplicación** - ejecutar la funciones como la web, móvil y aplicaciones de la API Cuando ya está usando el servicio de aplicación para las demás aplicaciones, puede ejecutar las funciones en el mismo plan sin costo adicional. Detalles completos pueden encontrarse en la [página de precios de servicio de aplicación](/pricing/details/app-service/).

Para obtener más información sobre las funciones de ajuste de escala, vea [cómo ampliar las funciones de Azure](functions-scale.md).

##<a name="next-steps"></a>Pasos siguientes

+ [Crear su primera función de Azure](functions-create-first-azure-function.md)  
Directamente a crear su primera función con el tutorial de funciones de Azure. 
+ [Referencia del programador de Azure funciones](functions-reference.md)  
Proporciona información técnica sobre el tiempo de ejecución de funciones de Azure y una referencia de funciones de codificación y definir desencadenadores y enlaces.
+ [Probar funciones de Azure](functions-test-a-function.md)  
Describe diversas herramientas y técnicas para probar sus funciones.
+ [Cómo ampliar las funciones de Azure](functions-scale.md)  
Se tratan los planes de servicio disponibles con funciones de Azure, incluidos el plan de servicio dinámicos y cómo elegir el plan de la derecha. 
+ [Obtenga más información sobre la aplicación de servicio de Azure](../app-service/app-service-value-prop-what-is.md)  
Funciones de Azure aprovecha la plataforma de servicio de la aplicación de Azure funcionalidad principales como implementaciones, variables de entorno y diagnósticos. 