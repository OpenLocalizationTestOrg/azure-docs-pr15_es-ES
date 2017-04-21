<properties 
    pageTitle="Cómo usar temas de Bus de servicio con Python | Microsoft Azure" 
    description="Obtenga información sobre cómo usar temas de Bus de servicio de Azure y suscripciones de Python." 
    services="service-bus" 
    documentationCenter="python" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Cómo utilizar las suscripciones y temas de Bus de servicio

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artículo describe cómo utilizar las suscripciones y temas de Bus de servicio. Los ejemplos se escriben en Python y usar el [paquete de Azure Python][]. Las situaciones de ejemplo incluyen la **creación de temas y suscripciones**, **crear filtros de suscripción**, **enviar mensajes a un tema**, **recibir mensajes de una suscripción**y **eliminar temas y suscripciones**. Para obtener más información sobre temas y suscripciones, vea la sección [Pasos siguientes](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

**Nota:** Si necesita instalar Python o el [paquete de Azure Python][], vea la [Guía de instalación de Python](../python-how-to-install.md).

## <a name="create-a-topic"></a>Crear un tema

El objeto **ServiceBusService** le permite trabajar con los temas. Agregue lo siguiente en la parte superior de cualquier archivo Python en la que desea acceder mediante programación Bus de servicio:

```
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

El código siguiente crea un objeto **ServiceBusService** . Reemplazar `mynamespace`, `sharedaccesskeyname`, y `sharedaccesskey` con el espacio de nombres real, valor de nombre y clave de clave de firma de acceso compartido (SA).

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Puede obtener los valores para el nombre de la clave de SA y el valor desde el [portal de Azure][].

```
bus_service.create_topic('mytopic')
```

**crear\_tema** también es compatible con más opciones, que le permite invalidar la configuración de tema predeterminada como período de vida o el tamaño máximo de temas. En el ejemplo siguiente se establece el tamaño máximo del tema a 5 GB y una hora para live valor (TTL) de 1 minuto:

```
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Crear suscripciones

Suscripciones a temas también se crean con el objeto **ServiceBusService** . Las suscripciones se denominan y pueden tener un filtro opcional que restringe el conjunto de mensajes entregados a cola virtual de la suscripción.

> [AZURE.NOTE] Las suscripciones son persistentes y seguirán existe hasta, tanto el tema al que está suscrito, se eliminan.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Crear una suscripción con el filtro predeterminado (MatchAll)

El filtro de **MatchAll** es el filtro predeterminado que se usa si no se especifica ningún filtro cuando se crea una nueva suscripción. Cuando se utiliza el filtro **MatchAll** , todos los mensajes publicados en el tema se colocan en la cola virtual de la suscripción. En el ejemplo siguiente se crea una suscripción con nombre 'AllMessages' y usa el filtro de **MatchAll** predeterminado.

```
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Crear suscripciones con los filtros

También puede definir los filtros que le permite especificar qué mensajes enviados a un tema deben mostrarse dentro de una suscripción de un tema específico.

El tipo de filtro admitido por suscripciones más flexible es un **SqlFilter**, que implementa un subconjunto de SQL92. Filtros SQL funcionan en las propiedades de los mensajes que se publican en el tema. Para obtener más información sobre las expresiones que se pueden usar con un filtro SQL, vea la sintaxis de la [SqlFilter.SqlExpression][] .

Puede agregar filtros a una suscripción mediante la **crear\_regla** método del objeto **ServiceBusService** . Este método le permite agregar nuevos filtros a una suscripción existente.

> [AZURE.NOTE] Porque el filtro predeterminado se aplica automáticamente a todas las suscripciones nuevas, primero debe quitar el filtro predeterminado o el **MatchAll** invalidará cualquier otro filtro que puede especificar. Puede quitar la regla predeterminada mediante la **Eliminar\_regla** método del objeto **ServiceBusService** .

En el ejemplo siguiente se crea una suscripción denominada `HighMessages` con **SqlFilter** que solo selecciona mensajes que tienen una propiedad personalizada **messagenumber** superior a 3:

```
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Asimismo, en el ejemplo siguiente se crea una suscripción denominada `LowMessages` con **SqlFilter** que solo selecciona mensajes que tienen una propiedad **messagenumber** o igual a 3:

```
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Ahora, cuando se envía un mensaje a `mytopic` siempre se entrega a receptores suscrito a la suscripción de tema **AllMessages** y entregar selectivamente a receptores suscritos a las suscripciones de tema **HighMessages** y **LowMessages** (según el contenido del mensaje).

## <a name="send-messages-to-a-topic"></a>Enviar mensajes a un tema

Para enviar un mensaje a un tema de Bus de servicio, la aplicación debe utilizar el **Enviar\_tema\_mensaje** método del objeto **ServiceBusService** .

En el ejemplo siguiente se muestra cómo enviar cinco probar mensajes a `mytopic`. Tenga en cuenta que el valor de propiedad **messagenumber** de cada mensaje varía en la iteración del bucle (determina qué suscripciones reciban):

```
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Temas de Bus de servicio compatible con el tamaño máximo de mensaje de 256 KB en el [nivel estándar](service-bus-premium-messaging.md) y 1 MB en el [nivel de Premium](service-bus-premium-messaging.md). El encabezado, que incluye las propiedades de la aplicación estándar y personalizados, puede tener un tamaño máximo de 64 KB. No hay ningún límite del número de mensajes en un tema, pero hay un límite en el tamaño total de los mensajes que llevan a cabo por un tema. El tamaño de este tema se define en la hora de creación, con un límite máximo de 5 GB. Para obtener más información acerca de las cuotas, vea [las cuotas de Bus de servicio][].

## <a name="receive-messages-from-a-subscription"></a>Recibir mensajes de una suscripción

Reciben los mensajes de una suscripción usando la **recibir\_suscripción\_mensaje** método en el objeto **ServiceBusService** :

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Se eliminan los mensajes de la suscripción a medida que se leen cuando el parámetro **peek\_bloqueo** está establecida en **False**. Puede leer (peek) y bloquear el mensaje sin eliminarlo de la cola estableciendo el parámetro **peek\_bloqueo** en **True**.

El comportamiento de lectura y eliminar el mensaje como parte de la operación de recepción es el modelo más sencillo y funciona mejor para escenarios en los que puede tolerar una aplicación no procesar un mensaje si se produce un error. Para comprender esto, considere la posibilidad de un escenario en el que el consumidor envía la solicitud de recepción y, a continuación, se bloquea antes de procesamiento. Dado que Bus de servicio habrá marcar el mensaje como consumida, cuando la aplicación se reinicia y empieza a utilizar de nuevo, los mensajes habrá perdido el mensaje que se ha utilizado antes el bloqueo.

Si la **información\_bloqueo** parámetro se establece en **True**, la recepción se convierte en una operación de dos fases, que permite a las aplicaciones de soporte técnico que no pueden tolerar mensajes que falta. Cuando Bus de servicio recibe una solicitud, encuentra el mensaje siguiente consumir, bloquea para evitar que otros consumidores recibirlo y, a continuación, se devuelve a la aplicación. Después de la aplicación termine de procesar el mensaje (o almacena confiable para procesamiento futuro), complete la segunda fase del proceso de recepción llamando al método **delete** en el objeto de **mensaje** . El método **delete** marca el mensaje como consumida y lo quita de la suscripción.

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Cómo controlar los mensajes no se puede leer y bloqueos de la aplicación

Bus de servicio proporciona funcionalidad para ayudarle a recuperarse correctamente de errores en la aplicación o dificultades para procesar un mensaje. Si una aplicación de receptor no puede procesar el mensaje por algún motivo, puede llamar al método **desbloquear** en el objeto de **mensaje** . Esto provocará Bus de servicio desbloquear el mensaje dentro de la suscripción y que esté disponible para recibir nuevamente, por la misma aplicación consumir o por otra aplicación consume.

También es un tiempo de espera asociado a un mensaje bloqueado dentro de la suscripción, y si se produce un error en la aplicación procesar el mensaje antes el tiempo de espera de bloqueo caduca (por ejemplo, si se bloquea la aplicación), entonces Bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para volver a recibir.

En caso de que la aplicación se bloquea después de procesar el mensaje, pero antes de que se llama al método de **Eliminar** , a continuación, el mensaje se volverá a entregar a la aplicación cuando se reinicia. A menudo se denomina **Al menos una vez procesamiento**, es decir, cada mensaje se procesará al menos una vez pero en algunos casos es posible volverá a entregar el mismo mensaje. Si el escenario no puede tolerar procesamiento duplicado, los desarrolladores de aplicaciones deben agregar lógica adicional a su aplicación para controlar la entrega de mensajes duplicados. A menudo, esto se logra mediante la propiedad de **Id. de mensaje** de los mensajes, que permanecerán constante entre intentos de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar temas y suscripciones

Temas de suscripciones son persistentes y deben eliminarse explícitamente a través del [portal de Azure][] o mediante programación. En el ejemplo siguiente se muestra cómo eliminar el tema denominado `mytopic`:

```
bus_service.delete_topic('mytopic')
```

Eliminación de un tema, también elimina cualquier suscripciones que están registradas con el tema. Suscripciones también se pueden eliminar de forma independiente. El código siguiente muestra cómo eliminar una suscripción denominada `HighMessages` desde el `mytopic` tema:

```
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de temas de Bus de servicio, siga estos vínculos para obtener más información.

-   Vea [colas, temas y las suscripciones][].
-   Referencia de [SqlFilter.SqlExpression][].

[Portal de Azure]: https://portal.azure.com
[Paquete de Azure Python]: https://pypi.python.org/pypi/azure  
[Colas, temas y suscripciones]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Cuotas de Bus de servicio]: service-bus-quotas.md 
