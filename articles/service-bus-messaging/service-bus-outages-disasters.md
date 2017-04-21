<properties 
    pageTitle="Aplicaciones de servicio Bus contra interrupciones y problemas de aislamiento | Microsoft Azure"
    description="Describe las técnicas que puede usar para proteger aplicaciones de una interrupción de Bus de servicio posible."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/02/2016"
    ms.author="sethm" />

# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Prácticas recomendadas para aislar aplicaciones contra interrupciones de Bus de servicio y problemas

Aplicaciones críticas deben funcionar continuamente, incluso en presencia planeados o desastres. Este tema describe técnicas que puede utilizar para proteger aplicaciones de Bus de servicio contra un desastre o una interrupción del servicio posibles.

Una interrupción se define como la disponibilidad de Bus de servicio de Azure. La interrupción puede afectar a algunos componentes de Bus de servicio, como un almacén de mensajería, o incluso en todo el centro de datos. Después de que se ha corregido el problema, vuelva a estar disponible Bus de servicio. Normalmente, una interrupción no perder mensajes u otros datos. Un ejemplo de un error de componente es la disponibilidad de un determinado almacén de mensajería. Un ejemplo de una interrupción de todo el centro de datos es un error de alimentación de centro de datos o un conmutador de red defectuoso del centro de datos. Una interrupción puede durar desde unos minutos en unos días.

Un desastre se define como la pérdida permanente de una unidad de escala de Bus de servicio o el centro de datos. El centro de datos posible o puede no estar disponible. Normalmente un desastre provoca la pérdida de algunos o todos los mensajes u otros datos. Ejemplos de desastres son fire, saturación o terremoto.

## <a name="current-architecture"></a>Arquitectura actual

Bus de servicio usa varios stores mensajería para almacenar los mensajes que se envían a colas o temas. Se asigna una cola sin particiones o un tema a un almacén de mensajería. Si este almacén mensajería no está disponible, se producirá un error en todas las operaciones en esa cola o tema.

Todas las entidades de mensajería de Bus de servicio (colas, temas, transmisiones) residen en un espacio de nombres de servicio, que está relacionado con un centro de datos. Bus de servicio no permite la replicación de geo automática de datos, ni tampoco un espacio de nombres abarcar varios centros de datos.

## <a name="protecting-against-acs-outages"></a>Protección contra interrupciones de ACS

Si está usando credenciales de ACS y ACS no está disponible, los clientes ya no pueden obtener tokens. Los clientes que tengan un token en el momento en que cae ACS pueden seguir usando Bus de servicio hasta que expiren el tokens. La duración del token predeterminado es 3 horas.

Para proteger contra interrupciones de ACS, use tokens de firma de acceso compartido (SA). En este caso, el cliente autentica directamente con Bus de servicio iniciando un token minted automáticamente con una clave secreta. Llamadas a ACS ya no son necesarias. Para obtener más información sobre los tokens SA, vea [Bus de servicios de autenticación][].

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>Proteger colas y temas contra errores en el almacén de mensajería

Se asigna una cola sin particiones o un tema a un almacén de mensajería. Si este almacén mensajería no está disponible, se producirá un error en todas las operaciones en esa cola o tema. Una cola dividida, por otro lado, consta de varios fragmentos. Cada fragmento se almacena en un almacén de mensajería diferente. Cuando se envía un mensaje a una cola dividida o el tema, Bus de servicio asigna el mensaje a uno de los fragmentos. Si el almacén de mensajes correspondiente no está disponible, Bus de servicio escribe el mensaje a un fragmento diferente, si es posible. Para obtener más información sobre las entidades divididas, consulte [particiones entidades mensajería][].

## <a name="protecting-against-datacenter-outages-or-disasters"></a>Protección contra interrupciones del centro de datos o desastres

Para permitir un error entre dos centros de datos, puede crear un espacio de nombres de servicio de Bus de servicio en cada centro de datos. Por ejemplo, el espacio de nombres de servicio de Bus de servicio **contosoPrimary.servicebus.windows.net** puedan estar situados en la región del Norte y Central de Estados Unidos y **contosoSecondary.servicebus.windows.net** puedan estar situados en la región sur y Central nosotros. Si un Bus de servicios de mensajería entidad debe permanecer accesible en presencia de una interrupción en el centro de datos, puede crear esa entidad en ambos espacios de nombres.

Para obtener más información, vea la sección "Error de Bus de servicio dentro de un centro de datos de Azure" en [los modelos de mensajería asincrónicos y alta disponibilidad][].

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Extremos de retransmisión protección contra interrupciones del centro de datos o desastres

Geo replicación de extremos de retransmisión permite a un servicio que expone un extremo de retransmisión para que sea accesible en presencia interrupciones de Bus de servicio. Para lograr la replicación de geo, el servicio debe crear dos extremos de retransmisión en diferentes espacios de nombres. Los espacios de nombres deben estar en centros de datos distintos y los dos extremos deben tener nombres distintos. Por ejemplo, un extremo principal puede ponerse en **contosoPrimary.servicebus.windows.net/myPrimaryService**, mientras que pueda encontrar su equivalente secundaria en **contosoSecondary.servicebus.windows.net/mySecondaryService**.

El servicio escucha, a continuación, en ambos extremos y, a continuación, un cliente puede invocar el servicio a través de los puntos finales. Una aplicación cliente aleatoriamente elige una de las transmisiones como extremo de principal y envía su solicitud al extremo activo. Si la operación se produce un error con código de error, este error indica que el extremo de retransmisión no está disponible. La aplicación abre un canal para el extremo de copia de seguridad y envía la solicitud. En ese momento el activo y los extremos de una copia de seguridad cambiar roles: la aplicación cliente considera del extremo activo antiguo como el nuevo extremo de copia de seguridad y el extremo de copia de seguridad antiguo como el nuevo extremo activo. Si ambas operaciones error de envío, se modifican las funciones de las dos entidades y se devuelve un error.

El ejemplo de [replicación Geo con mensajes de retransmisión de Bus de servicio][] muestra cómo replicar transmisiones.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>Proteger colas y temas de interrupciones del centro de datos o desastres

Para lograr resistencia contra interrupciones del centro de datos cuando se usa con mensajería, Bus de servicio compatible con dos métodos: replicación *activos* y *pasivos* . Para cada enfoque si una determinada cola o tema debe permanecer accesible en presencia de una interrupción de centro de datos, puede crearlo en ambos espacios de nombres. Ambas entidades pueden tener el mismo nombre. Por ejemplo, una cola principal puede ponerse en **contosoPrimary.servicebus.windows.net/myQueue**, mientras que pueda encontrar su equivalente secundaria en **contosoSecondary.servicebus.windows.net/myQueue**.

Si la aplicación no requiere comunicación permanente de receptor de remitente, la aplicación puede implementar una cola de cliente resistente para evitar la pérdida de mensaje y para proteger el remitente de los errores de Bus de servicio transitorios.

## <a name="active-replication"></a>Replicación activa

La replicación de Active usa entidades en ambos espacios de nombres para cada operación. Cualquier cliente que envía un mensaje envía dos copias del mismo mensaje. La primera copia se envía a la entidad principal (por ejemplo, **contosoPrimary.servicebus.windows.net/sales**) y la segunda copia del mensaje se envía a la entidad secundaria (por ejemplo, **contosoSecondary.servicebus.windows.net/sales**).

Un cliente recibe mensajes de dos colas. El receptor procesa la primera copia de un mensaje y, a continuación, se suprime la segunda copia. Para eliminar mensajes duplicados, el remitente debe etiquetar cada mensaje con un identificador único. Ambas copias del mensaje deben estar etiquetados con el mismo identificador. Puede usar las propiedades [BrokeredMessage.MessageId][] o [BrokeredMessage.Label][] o una propiedad personalizada para etiquetar el mensaje. El receptor debe mantener una lista de los mensajes que ya ha recibido.

El ejemplo de [replicación Geo con servicio Bus negociada mensajes][] muestra active replicación de mensajería entidades.

> [AZURE.NOTE] El enfoque de replicación activa duplica el número de operaciones, por lo tanto, este enfoque puede provocar un costo mayor.

## <a name="passive-replication"></a>Replicación pasiva

En el caso sin errores, replicación pasiva utiliza solo una de las dos entidades de mensajería. Un cliente envía el mensaje a la entidad activa. Si se produce un error en la operación de la entidad activa con código de error que indica que el centro de datos que hospeda la entidad activa podría no estar disponible, el cliente envía una copia del mensaje a la entidad de copia de seguridad. En ese momento la activa y las entidades de copia de seguridad cambie las funciones: el cliente remitente considera la entidad active antigua que la nueva entidad de copia de seguridad y, a continuación, la entidad de copia de seguridad antigua es la nueva entidad activa. Si ambas operaciones error de envío, se modifican las funciones de las dos entidades y se devuelve un error.

Un cliente recibe mensajes de dos colas. Dado que es probable que el destinatario recibe dos copias del mismo mensaje, el receptor debe suprimir mensajes duplicados. Puede suprimir duplicados de la misma manera como se describe de replicación activa.

En general, replicación pasiva es más asequible que la replicación de active porque en la mayoría de los casos se realiza una única operación. Latencia, el rendimiento y el costo monetario son idénticos al escenario no replicada.

Al utilizar la replicación pasiva, en las situaciones siguientes mensajes se pueden perder o recibidos dos veces:

-   **Pérdida o retraso del mensaje**: se supone que el remitente envía correctamente m1 de un mensaje a la cola principal y, a continuación, la cola deja de estar disponible antes de que el destinatario recibe m1. El remitente envía un m2 mensaje posteriores a la cola secundaria. Si la cola principal no está disponible temporalmente, el destinatario recibe m1 después vuelva a estar disponible la cola. En caso de desastre, el destinatario no puede recibir nunca m1.

-   **Duplicar recepción**: se supone que el remitente envía un mensaje m a la cola principal. Bus de servicio correctamente procesa m, pero no puede enviar una respuesta. Después de la operación de envío agota el tiempo de espera, el remitente envía una copia idéntica de m en la cola secundaria. Si el destinatario es capaz de recibir la primera copia de m antes de la cola principal no está disponible, el destinatario recibe ambas copias de m aproximadamente al mismo tiempo. Si el destinatario no es capaz de recibir la primera copia de m antes de la cola principal no está disponible, el receptor recibe inicialmente solo la segunda copia de m, pero a continuación, recibe una segunda copia de m cuando la cola principal esté disponible.

El ejemplo de [replicación Geo con servicio Bus negociada mensajes][] muestra pasiva replicación de mensajería entidades.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de recuperación, consulte estos artículos:

- [Continuidad empresarial de base de datos SQL Azure][]
- [Orientación técnica de la resistencia de Azure][]

  [Autenticación de Bus de servicio]: service-bus-authentication-and-authorization.md
  [Entidades de mensajería divididas]: service-bus-partitioning.md
  [Alta disponibilidad y los modelos de mensajería asincrónicos]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
  [Replicación geo con Bus de servicio de retransmisión de mensajes]: http://code.msdn.microsoft.com/Geo-replication-with-16dbfecd
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [BrokeredMessage.Label]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx
  [Replicación geo con Bus de servicio negociada mensajes]: http://code.msdn.microsoft.com/Geo-replication-with-f5688664
  [Continuidad empresarial de base de datos SQL Azure]: ../sql-database/sql-database-business-continuity.md
  [Orientación técnica de la resistencia de Azure]: ../resiliency/resiliency-technical-guidance.md
