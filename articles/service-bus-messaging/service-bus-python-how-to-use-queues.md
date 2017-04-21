<properties 
    pageTitle="Cómo usar colas de Bus de servicio con Python | Microsoft Azure" 
    description="Obtenga información sobre cómo usar colas de Bus de servicio de Azure de Python." 
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
    ms.date="09/21/2016" 
    ms.author="sethm;lmazuel"/>


# <a name="how-to-use-service-bus-queues"></a>Cómo usar colas de Bus de servicio

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artículo describe cómo usar colas de Bus de servicio. Los ejemplos se escriben en Python y usar el [paquete de Bus de servicio de Azure Python][]. Los escenarios cubiertos incluyen **crear colas, enviar y recibir mensajes**y **eliminar colas**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

> [AZURE.NOTE] Para instalar el [paquete de Bus de servicio de Azure Python][]o Python, consulte la [Guía de instalación de Python](../python-how-to-install.md).

## <a name="create-a-queue"></a>Crear una cola

El objeto **ServiceBusService** le permite trabajar con colas. Agregue el siguiente código cerca de la parte superior de cualquier archivo Python en la que desea tener acceso mediante programación Bus de servicio:

```
from azure.servicebus import ServiceBusService, Message, Queue
```

El código siguiente crea un objeto **ServiceBusService** . Reemplazar `mynamespace`, `sharedaccesskeyname`, y `sharedaccesskey` con el espacio de nombres, el nombre de la clave de acceso compartido firma (SA) y el valor.

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Los valores para el nombre de la clave de SA y el valor pueden encontrarse en la información de conexión de [Azure portal clásico][] o en el panel **Propiedades** de Visual Studio al seleccionar el espacio de nombres de Bus de servicio en el Explorador de servidores (como se muestra en la sección anterior).

```
bus_service.create_queue('taskqueue')
```

**create_queue** también es compatible con más opciones, que le permite invalidar la configuración cola como período de vida (TTL) o el tamaño máximo de cola. En el ejemplo siguiente se establece el tamaño máximo de cola a 5GB y el valor TTL en 1 minuto:

```
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>Enviar mensajes a una cola

Para enviar un mensaje de una cola de Bus de servicio, las llamadas de aplicación la **Enviar\_cola\_mensaje** método en el objeto **ServiceBusService** .

En el ejemplo siguiente se muestra cómo enviar un mensaje de prueba a la cola denominada *taskqueue con* **Enviar\_cola\_mensaje**:

```
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

Colas de Bus de servicio compatible con el tamaño máximo de mensaje de 256 KB en el [nivel estándar](service-bus-premium-messaging.md) y 1 MB en el [nivel de Premium](service-bus-premium-messaging.md). El encabezado, que incluye las propiedades de la aplicación estándar y personalizados, puede tener un tamaño máximo de 64 KB. No hay ningún límite del número de mensajes de una cola, pero hay un límite en el tamaño total de los mensajes que llevan a cabo una cola. Tamaño de la cola se define en la hora de creación, con un límite máximo de 5 GB. Para obtener más información acerca de las cuotas, vea [las cuotas de Bus de servicio][].

## <a name="receive-messages-from-a-queue"></a>Recibir mensajes de una cola

Reciben los mensajes de una cola utilizando la **recibir\_cola\_mensaje** método en el objeto **ServiceBusService** :

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

Se eliminan los mensajes de la cola de medida que se leen cuando el parámetro **peek\_bloqueo** está establecida en **False**. Puede leer (peek) y bloquear el mensaje sin eliminarlo de la cola estableciendo el parámetro **peek\_bloqueo** en **True**.

El comportamiento de lectura y eliminar el mensaje como parte de la operación de recepción es el modelo más sencillo y funciona mejor para escenarios en los que puede tolerar una aplicación no procesar un mensaje si se produce un error. Para comprender esto, considere la posibilidad de un escenario en el que el consumidor envía la solicitud de recepción y, a continuación, se bloquea antes de procesamiento. Dado que Bus de servicio habrá marcar el mensaje como consumida, cuando la aplicación se reinicia y empieza a utilizar de nuevo, los mensajes habrá perdido el mensaje que se ha utilizado antes el bloqueo.

Si la **información\_bloqueo** parámetro se establece en **True**, la recepción se convierte en una operación de dos fases, que permite a las aplicaciones de soporte técnico que no pueden tolerar mensajes que falta. Cuando Bus de servicio recibe una solicitud, encuentra el mensaje siguiente consumir, bloquea para evitar que otros consumidores recibirlo y, a continuación, se devuelve a la aplicación. Después de la aplicación termine de procesar el mensaje (o almacena confiable para procesamiento futuro), complete la segunda fase del proceso de recepción llamando al método **Eliminar** en el objeto de **mensaje** . El método **Eliminar** marcar el mensaje como consumida y quitar de la cola.

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Cómo controlar los mensajes no se puede leer y bloqueos de la aplicación

Bus de servicio proporciona funcionalidad para ayudarle a recuperarse correctamente de errores en la aplicación o dificultades para procesar un mensaje. Si una aplicación de receptor no puede procesar el mensaje por algún motivo, puede llamar al método **desbloquear** en el objeto de **mensaje** . Esto provocará Bus de servicio desbloquear el mensaje en la cola y que esté disponible para recibir nuevamente, por la misma aplicación consumir o por otra aplicación consume.

También es un tiempo de espera asociado a un mensaje bloqueado en la cola y si se produce un error en la aplicación procesar el mensaje antes el tiempo de espera de bloqueo caduca (por ejemplo, si se bloquea la aplicación), se desbloquear el mensaje automáticamente y que esté disponible para recibir nuevo Bus de servicio.

En caso de que la aplicación se bloquea después de procesar el mensaje, pero antes de que se llama al método de **Eliminar** , a continuación, el mensaje se volverá a entregar a la aplicación cuando se reinicia. A menudo se denomina **Al menos una vez procesamiento**, es decir, cada mensaje se procesará al menos una vez pero en algunos casos es posible volverá a entregar el mismo mensaje. Si el escenario no puede tolerar procesamiento duplicado, los desarrolladores de aplicaciones deben agregar lógica adicional a su aplicación para controlar la entrega de mensajes duplicados. A menudo, esto se logra mediante la propiedad de **Id. de mensaje** de los mensajes, que permanecerán constante entre intentos de entrega.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de colas de Bus de servicio, siga estos vínculos para obtener más información.

-   Vea [colas, temas y las suscripciones][].

[Portal de clásico de Azure]: https://manage.windowsazure.com
[Paquete de Python Bus de servicio de Azure]: https://pypi.python.org/pypi/azure-servicebus  
[Colas, temas y suscripciones]: service-bus-queues-topics-subscriptions.md
[Cuotas de Bus de servicio]: service-bus-quotas.md
 
