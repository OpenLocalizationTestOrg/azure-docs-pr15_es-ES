<properties 
    pageTitle="Bus de servicio de Azure | Microsoft Azure" 
    description="Introducción al uso de Bus de servicio para conectarse a otro software de aplicaciones de Azure." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/31/2016" 
    ms.author="sethm"/>

# <a name="azure-service-bus"></a>Bus de servicio de Azure

Si una aplicación o servicio se ejecuta en la nube o de forma local, con qué frecuencia se necesita interactuar con otras aplicaciones o servicios. Para proporcionar una forma muy útil para hacer esto, Microsoft Azure ofrece Bus de servicio. En este artículo le lleva a un vistazo a esta tecnología, que describe qué es y por qué desea usarlo.

## <a name="service-bus-fundamentals"></a>Conceptos básicos de Bus de servicio

Llamar situaciones diferentes para diferentes estilos de comunicación. A veces, permitiendo que las aplicaciones enviar y recibir mensajes a través de una cola simple es la mejor solución. En otras situaciones, una cola ordinaria no es suficiente. una cola con un mecanismo de publicación y suscripción es mejor. En algunos casos, todo lo que realmente se necesita es una conexión entre aplicaciones. colas no son obligatorios. Bus de servicio proporciona las tres opciones, habilitar las aplicaciones interactuar de varias formas diferentes.

Bus de servicio es un servicio de nube de varios inquilinos, lo que significa que el servicio está compartido por varios usuarios. Cada usuario, como programador de aplicaciones, crea un *espacio de nombres*, a continuación, define los mecanismos de comunicación que necesita dentro de ese espacio de nombres. Figura 1 muestra el aspecto.

![][1]
 
**Figura 1: Bus de servicio proporciona un servicio de varios inquilino para conectar aplicaciones a través de la nube.**

Dentro de un espacio de nombres, puede utilizar una o varias instancias de cuatro mecanismos de comunicación diferentes, cada una de las cuales conecta aplicaciones de manera diferente. Las opciones son:

- *Colas*, que permiten la comunicación unidireccional. Cada cola actúa como intermediario (a veces denominado un *agente*) que almacena los mensajes enviados hasta que se reciben. Un único destinatario recibe cada mensaje.
- *Temas*, que proporcionan comunicación unidireccional con *suscripciones*- un tema puede tener varias suscripciones. Como una cola, un tema actúa como un agente, pero cada suscripción puede utilizar opcionalmente un filtro para recibir sólo los mensajes que cumplen criterios específicos.
- *Transmisiones*, que proporcionan comunicación bidireccional. A diferencia de colas y temas, una retransmisión no almacena mensajes en curso; no es un agente. En su lugar, simplemente pasa la aplicación de destino.

Cuando se crea una cola, el tema o la retransmisión, darle un nombre. Combinar con la que llama el espacio de nombres, este nombre crea un identificador único para el objeto. Pueden proporcionar este nombre a Bus de servicios de aplicaciones y luego usar esa cola, tema o retransmisión para comunicarse con otros. 

Para usar cualquiera de estos objetos en el escenario de retransmisión, aplicaciones de Windows pueden usar Windows Communication Foundation (WCF). Para colas y temas, aplicaciones de Windows pueden usar las API de mensajería definidos por el Bus de servicio. Para facilitar el uso de aplicaciones de Windows no de estos objetos, Microsoft proporciona SDK para Java, Node.js y otros idiomas. También puede acceder a colas y temas con la API de REST sobre HTTP (s). 

Es importante entender que aunque servicio propio Bus se ejecuta en la nube (es decir, en centros de datos de Microsoft Azure), pueden ejecutar aplicaciones que usa cualquier lugar. Puede usar Bus de servicio para conectar aplicaciones que se ejecutan en Azure, por ejemplo, o aplicaciones que se ejecutan dentro de su propio centro de datos. También puede usar para conectarse a una aplicación que se ejecuta en Azure u otra plataforma de nube con una aplicación local o tabletas y teléfonos. Es posible conectar electrodomésticos, sensores y otros dispositivos con una aplicación central o entre sí. Bus de servicio es un mecanismo de comunicación en la nube que se puede acceder desde prácticamente cualquier lugar. Cómo se usa depende de lo que deben hacer las aplicaciones.

## <a name="queues"></a>Colas

Suponga que decide conectar con una cola de Bus de servicio de dos aplicaciones. Figura 2 ilustra esta situación.

![][2]
 
**Figura 2: Colas de Bus de servicio proporcionan cola asincrónica unidireccional.**

El proceso es sencillo: un remitente envía un mensaje a una cola de Bus de servicio y un receptor recoge dicho mensaje en algún momento posterior. Una cola puede tener solo un receptor único, como se muestra en la figura 2. O bien, pueden leer varias aplicaciones de la misma cola. En la última situación, cada mensaje se lee por un solo receptor. Para un servicio de conversión múltiple, debe usar un tema en su lugar.

Cada mensaje tiene dos partes: un conjunto de propiedades, cada un par de clave/valor y el cuerpo de un mensaje binario. ¿Cómo se usan depende de lo que una aplicación está intentando hacer. Por ejemplo, una aplicación de enviar un mensaje sobre una venta reciente puede incluir las propiedades *vendedor = "Ava"* y *cantidad = 10000*. El cuerpo del mensaje puede incluir una imagen digitalizada del contrato firmado de venta o, si no hay una, simplemente permanecer vacío.

Un receptor puede leer un mensaje de una cola de Bus de servicio de dos maneras diferentes. La primera opción, denominada *ReceiveAndDelete*, elimina un mensaje de la cola y lo elimina inmediatamente. Esto es sencillo, pero si el destinatario se bloquea antes de que finalice el procesamiento del mensaje, el mensaje se perderán. Debido a se haya eliminado de la cola, ningún otro receptor puede obtener acceso a él. 

La segunda opción, *PeekLock*, está pensada para ayudarle con este problema. Como **ReceiveAndDelete**, una lectura **PeekLock** quita un mensaje de la cola. Sin embargo no elimina el mensaje. En su lugar, se bloquea el mensaje, lo que invisibles a otros receptores y que espera para uno de los tres eventos:

- Si el receptor procesa el mensaje correctamente, llama **completado**y la cola elimina el mensaje. 
- Si el receptor decide que no puede procesar el mensaje correctamente, llamadas **abandonar**. A continuación, la cola quita el bloqueo del mensaje y hace que esté disponible para otros receptores.
- Si el receptor llama a ninguno de estos dentro de un período de tiempo configurable (60 segundos de forma predeterminada), la cola supone que el receptor ha fallado. En este caso, se comporta como si hubiera llamado el receptor **abandonar**, poner el mensaje a disposición de otros destinatarios.

Observe lo que puede ocurrir aquí: el mismo mensaje podría entregarán dos veces, tal vez a dos receptores diferentes. Aplicaciones mediante colas de Bus de servicio deben estar preparadas para esto. Para facilitar la detección de duplicados, que cada mensaje tiene una propiedad de **Id. de mensaje** única que predeterminada varía, independientemente de cómo muchas veces se lee el mensaje de una cola. 

Colas son útiles para una serie de situaciones. Permiten a las aplicaciones comunicarse aun cuando ambos no se ejecutan al mismo tiempo, algo que es especialmente útil con lote y aplicaciones móviles. Una cola con varios receptores también proporciona equilibrio de carga automático, ya que se extienden los mensajes enviados a través de estos receptores.

## <a name="topics"></a>Temas

Útil que aparecen, colas no siempre son la solución correcta. A veces, los temas de Bus de servicio son mejores. Figura 3 ilustra esta idea.

![][3]
 
**Figura 3: Se basa en el filtro que se especifica una aplicación de la suscripción, puede recibir todos o algunos de los mensajes enviados a un tema de Bus de servicio.**

Un *tema* es similar en muchas formas de una cola. Mensajes de remitentes enviar a un tema de la misma manera que envían mensajes de una cola y los mensajes tienen el mismo aspecto que con colas. La gran diferencia es que temas habilitar cada aplicación receptora crear su propia *suscripción* si define un *filtro*. Un suscriptor, a continuación, podrá ver sólo los mensajes que coinciden con el filtro. Por ejemplo, la figura 3 muestra un remitente y un tema con los tres suscriptores, cada una con su propio filtro:

- Suscriptor 1 recibe únicamente los mensajes que contienen la propiedad *vendedor = "Ava"*.
- Suscriptor 2 recibe mensajes que contienen la propiedad *vendedor = "Ruby"* o contener una propiedad *importe* cuyo valor es mayor que 100,000. Tal vez Ruby es el Administrador de venta, por lo que desea ver sus propia ventas y todas las ventas grandes con independencia de que crea.
- Suscriptor 3 ha establecido su filtro como *True*, lo que significa que recibe todos los mensajes. Por ejemplo, esta aplicación podría ser responsable de mantener un seguimiento de auditoría y por tanto, necesita ver todos los mensajes.

Al igual que con colas, los suscriptores de un tema pueden leer mensajes con **ReceiveAndDelete** o **PeekLock**. A diferencia de colas, sin embargo, un único mensaje enviado a un tema puede recibir varias suscripciones. Este método, que suele denominada *publicar y suscribirse* (o *pub/sub*), es útil cuando varias aplicaciones están interesadas en los mismos mensajes. Al definir el filtro derecha, cada suscriptor puede puntear en la parte de la secuencia de mensajes que necesita ver.

## <a name="relays"></a>Transmisiones

Colas y temas proporcionan comunicación asincrónica unidireccional a través de un agente. Tráfico fluye en una dirección y no hay ninguna conexión directa entre los remitentes y destinatarios. Pero, ¿qué sucede si no desea esto? Supongamos que las aplicaciones que necesita para enviar y recibir mensajes, o tal vez desee crear un vínculo directo entre ellas y no es necesario un agente para almacenar los mensajes. Para resolver escenarios como esta, Bus de servicio proporciona *transmite*, como se muestra en la figura 4.

![][4]
 
**Figura 4: Retransmisión de Bus de servicio ofrece sincrónica comunicación bidireccional entre aplicaciones.**

La pregunta evidente sobre transmisiones es esto: ¿por qué debería usar uno? Incluso si no tengo colas, ¿por qué realizar aplicaciones comunicarse a través de un servicio de nube en lugar de simplemente interactuar directamente? La respuesta es que hablar directamente puede ser más difícil de lo que parece.

Suponga que desea conectar dos aplicaciones locales, ambos ejecutándose dentro de los centros de datos corporativos. Cada una de estas aplicaciones se encuentra detrás de un firewall, y cada centro de datos probablemente utiliza traducción de direcciones de red (NAT). El firewall bloquea datos entrantes en todos excepto unos cuantos puertos y NAT implica que el equipo que se está quedando cada aplicación no dispone de una dirección IP fija que puede ponerse en contacto directamente desde fuera del centro de datos. Sin la ayuda adicional, supone un problema al conectar estas aplicaciones sobre internet público.

Puede ayudar una retransmisión de Bus de servicio. Para la comunicación bidireccional a través de un relé, cada aplicación establece una conexión saliente de TCP con Bus de servicio y luego mantiene abierta. Todas las comunicaciones entre las dos aplicaciones viajan a través de estas conexiones. Dado que se estableció cada conexión desde dentro del centro de datos, el firewall permite el tráfico entrante a cada aplicación sin tener que abrir nuevos puertos. Este método también se obtiene solucionar el problema NAT, porque cada aplicación tiene un extremo coherente en la nube en toda la comunicación. Mediante el intercambio de datos a través de la retransmisión de las aplicaciones pueden evitar los problemas que de lo contrario haga comunicación difícil. 

Para usar las transmisiones de Bus de servicio, aplicaciones se basan en Windows Communication Foundation (WCF). Bus de servicio proporciona enlaces de WCF que hacen que sea sencillo para las aplicaciones de Windows interactuar a través de transmisiones. Aplicaciones que ya utilizan WCF pueden normalmente solo especificar uno de estos enlaces y hablar con ellos a través de un relé. A diferencia de colas y temas, sin embargo, usa transmisiones de aplicaciones de Windows no, mientras posible, requiere algún esfuerzo de programación; no se proporcionan bibliotecas estándar.

A diferencia de colas y temas, aplicaciones no crean explícitamente transmisiones. En su lugar, cuando una aplicación que desea recibir mensajes establece una conexión de TCP con Bus de servicio, se crea automáticamente una retransmisión. Cuando la conexión se interrumpe, se elimina la retransmisión. Para habilitar una aplicación encontrar la retransmisión creada por una escucha específica, Bus de servicio proporciona un registro que permite a las aplicaciones localizar una retransmisión específica por su nombre.

Transmisiones son la solución correcta cuando necesite comunicación directa entre aplicaciones. Por ejemplo, considere la posibilidad de un sistema de reserva de líneas aéreas ejecuta en un centro de datos local que debe tener acceso desde otros equipos, quioscos de verificación y dispositivos móviles. Aplicaciones que se ejecutan en todos estos sistemas podrían dependen de transmisiones de Bus de servicio en la nube para comunicarse, donde puede ejecutar.

## <a name="summary"></a>Resumen

Conexión de aplicaciones siempre ha sido parte de la creación de soluciones completas y se establece el intervalo de escenarios que requieren aplicaciones y servicios para comunicarse con ellos para aumentar como más aplicaciones y dispositivos están conectados a Internet. Proporcionando tecnologías basadas en la nube para lograr esto a través de colas, temas y transmisiones de Bus de servicio pretende simplificar esta función esencial implementar y disponible con más amplitud.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los aspectos básicos de Azure Bus de servicio, siga estos vínculos para obtener más información.

- Cómo usar [colas de Bus de servicio](service-bus-dotnet-get-started-with-queues.md)
- Cómo usar [temas de Bus de servicio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- Cómo usar [retransmisión de Bus de servicio](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md)
- [Ejemplos de Bus de servicio](service-bus-samples.md)

[1]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_01_architecture.png
[2]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_02_queues.png
[3]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[4]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_04_relay.png
