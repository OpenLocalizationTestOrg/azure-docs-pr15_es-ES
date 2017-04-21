<properties
    pageTitle="Cómo usar temas de Bus de servicio (Ruby) | Microsoft Azure"
    description="Obtenga información sobre cómo usar temas de Bus de servicios y suscripciones en Azure. Ejemplos de código se escriben para aplicaciones Ruby."
    services="service-bus"
    documentationCenter="ruby"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topicssubscriptions"></a>Cómo usar temas y suscripciones de Bus de servicio

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artículo describe cómo usar temas de Bus de servicios y suscripciones desde aplicaciones Ruby. Los escenarios cubiertos incluyen la **creación de temas y suscripciones, crear filtros de suscripción, enviar mensajes** a un tema, **recibir mensajes de una suscripción**y **eliminar temas y suscripciones**. Para obtener más información sobre los temas y las suscripciones, vea la sección [Pasos siguientes](#next-steps) .

## <a name="service-bus-topics-and-subscriptions"></a>Las suscripciones y temas de Bus de servicio

Las suscripciones y temas de Bus de servicio de soporte técnico una *publicación o suscripción* mensajería modelo de comunicación. Al usar temas y suscripciones, componentes de una aplicación distribuida no comunican directamente con otros; intercambiar mensajes a través de un tema, que actúa como intermediario en su lugar.

![TopicConcepts](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

A diferencia de colas de Bus de servicio, donde cada mensaje es procesado por un único consumidor, temas y suscripciones proporcionan un formulario de **uno a varios** de comunicación, utilizando un modelo de publicación y suscripción. Es posible registrar varias suscripciones a un tema. Cuando se envía un mensaje a un tema, a continuación, estará disponible para cada suscripción para procesar de forma independiente.

Una suscripción de tema se asemeja a una cola virtual que recibe copia de los mensajes que se han enviado al tema. Si lo desea, puede registrar reglas de filtro para un tema en una base de cada suscripción, lo que le permite filtrar o restringir las suscripciones de tema reciben los mensajes en un tema.

Temas de Bus de servicios y suscripciones le permiten escalar para procesar una gran cantidad de mensajes a través de un gran número de usuarios y aplicaciones.

## <a name="create-a-namespace"></a>Crear un espacio de nombres

Para empezar a usar colas de Bus de servicio en Azure, primero debe crear un espacio de nombres. Un espacio de nombres proporciona un contenedor de ámbito para el direccionamiento de recursos de Bus de servicio de la aplicación. Debe crear el espacio de nombres mediante la interfaz de línea de comandos porque el [portal de Azure][] no crea el espacio de nombres con una conexión de ACS.

Para crear un espacio de nombres:

1. Abra una ventana de la consola de Powershell de Azure.

2. Escriba el comando siguiente para crear un espacio de nombres. Proporcionar su propio valor de espacio de nombres y especificar la misma región como la aplicación.

    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true
    ```

    ![Crear Namespace](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## <a name="obtain-default-management-credentials-for-the-namespace"></a>Obtener predeterminado credenciales de administración para el espacio de nombres

Para poder realizar operaciones de administración, como la creación de una cola en el espacio de nombres, debe obtener las credenciales de administración para el espacio de nombres.

El cmdlet de PowerShell que ejecutó para crear el espacio de nombres de Bus de servicio muestra la clave que se puede usar para administrar el espacio de nombres. Copie el valor de **DefaultKey** . Más adelante en este tutorial usará este valor en el código.

![Copiar clave](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

> [AZURE.NOTE]
> También puede encontrar esta clave si iniciar sesión en el [portal de Azure][] y vaya a la información de conexión para el espacio de nombres.

## <a name="create-a-ruby-application"></a>Crear una aplicación de ideogramas y transcripción fonética

Para obtener instrucciones, vea [crear una aplicación de rubí en Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Configurar la aplicación para usar Bus de servicio

Para usar Bus de servicio, descargar y usar el paquete de Azure Ruby, que incluye un conjunto de bibliotecas de comodidad que comunicarse con los servicios del resto de almacenamiento.

### <a name="use-rubygems-to-obtain-the-package"></a>Usar RubyGems para obtener el paquete

1. Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).

2. Escriba "azure de instalación de indicador" en la ventana de comandos para instalar el indicador y dependencias.

### <a name="import-the-package"></a>Importar el paquete

Usar el editor de texto, agregue lo siguiente a la parte superior del archivo ideogramas y transcripción fonética en la que va a utilizar almacenamiento:

```
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Configurar una conexión de Bus de servicio

El módulo Azure lee las variables de entorno **AZURE\_SERVICEBUS\_espacio de nombres** y **AZURE\_SERVICEBUS\_ACCESS\_clave** información necesaria para conectarse a su espacio de nombres. Si no se establecen estas variables de entorno, debe especificar la información de espacio de nombres antes de utilizar **Azure::ServiceBusService** con el siguiente código:

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Establezca el valor de espacio de nombres en el valor que creó en lugar de la dirección URL completa. Por ejemplo, use **"yourexamplenamespace"**, no "yourexamplenamespace.servicebus.windows.net".

## <a name="create-a-topic"></a>Crear un tema

El objeto **Azure::ServiceBusService** le permite trabajar con los temas. El código siguiente crea un objeto **Azure::ServiceBusService** . Para crear un tema, utilice la **crear\_topic()** método. En el ejemplo siguiente se crea un tema o imprime los errores si hay alguno.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

También puede pasar un objeto de **Azure::ServiceBus::Topic** con opciones adicionales que le permiten invalidar la configuración predeterminada de tema, como la hora del mensaje Live o el tamaño máximo de cola. En el ejemplo siguiente se muestra al establecer el tamaño máximo de cola en 5GB y la hora a live a 1 minuto:

```
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Crear suscripciones

Suscripciones de tema también se crean con el objeto **Azure::ServiceBusService** . Las suscripciones se denominan y pueden tener un filtro opcional que restringe el conjunto de mensajes entregados a cola virtual de la suscripción.

Suscripciones persistentes y seguirán existe hasta alguno o el tema que están asociadas, se eliminan. Si su aplicación contiene lógica para crear una suscripción, debe comprobar primero si la suscripción ya existe a través del método getSubscription.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Crear una suscripción con el filtro predeterminado (MatchAll)

El filtro de **MatchAll** es el filtro predeterminado que se usa si no se especifica ningún filtro cuando se crea una nueva suscripción. Cuando se utiliza el filtro **MatchAll** , todos los mensajes publicados en el tema se colocan en la cola virtual de la suscripción. En el ejemplo siguiente se crea una suscripción con el nombre "todos los mensajes" y se usa el filtro de **MatchAll** predeterminado.

```
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Crear suscripciones con los filtros

También puede definir los filtros que le permite especificar qué mensajes enviados a un tema deben mostrarse dentro de una suscripción específica.

El tipo de filtro admitido por suscripciones más flexible es **Azure::ServiceBus::SqlFilter**, que implementa un subconjunto de SQL92. Filtros SQL funcionan en las propiedades de los mensajes que se publican en el tema. Para obtener más detalles acerca de las expresiones que se pueden usar con un filtro SQL, revise la sintaxis de la [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx) .

Puede agregar filtros a una suscripción mediante la **crear\_rule()** método del objeto **Azure::ServiceBusService** . Este método permite agregar nuevos filtros a una suscripción existente.

Dado que el filtro predeterminado se aplica automáticamente a todas las suscripciones nuevas, primero debe quitar el filtro predeterminado o el **MatchAll** invalidará cualquier otro filtro que puede especificar. Puede quitar la regla predeterminada mediante la **Eliminar\_rule()** método en el objeto **Azure::ServiceBusService** .

En el ejemplo siguiente se crea una suscripción denominada "alta-mensajes" con un **Azure::ServiceBus::SqlFilter** que solo selecciona mensajes que tengan una personalizada **mensaje\_número** propiedad mayor que 3:

```
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Asimismo, en el ejemplo siguiente se crea una suscripción denominada "bajo-mensajes" con un **Azure::ServiceBus::SqlFilter** que solo selecciona mensajes que tienen una propiedad **message_number** o igual a 3:

```
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Cuando un mensaje se envía ahora al "tema de prueba", siempre se entregarán a los receptores suscrito a la suscripción de tema "todos los mensajes" y entregar selectivamente a receptores suscritos a las suscripciones de tema "mensajes de alta" y "mensajes de baja" (según el contenido del mensaje).

## <a name="send-messages-to-a-topic"></a>Enviar mensajes a un tema

Para enviar un mensaje a un tema de Bus de servicio, la aplicación debe utilizar el **Enviar\_tema\_message()** método en el objeto **Azure::ServiceBusService** . Los mensajes enviados a temas de Bus de servicio son instancias de los objetos **Azure::ServiceBus::BrokeredMessage** . Objetos de **Azure::ServiceBus::BrokeredMessage** tienen un conjunto de propiedades estándar (como **etiqueta** y **tiempo\_a\_live**), un diccionario que se utiliza para contener propiedades específicas de aplicación personalizada y un cuerpo de datos de cadena. Una aplicación puede establecer el cuerpo del mensaje pasando un valor de cadena a la **Enviar\_tema\_message()** método y cualquier obligatorio propiedades estándar se rellenará con valores predeterminados.

En el ejemplo siguiente se muestra cómo enviar mensajes al "tema de prueba" de cinco prueba. Tenga en cuenta que el valor de propiedad personalizada **message_number** de cada mensaje varía en la iteración del bucle (determina qué suscripción lo reciba):

```
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Temas de Bus de servicio compatible con el tamaño máximo de mensaje de 256 KB en el [nivel estándar](service-bus-premium-messaging.md) y 1 MB en el [nivel de Premium](service-bus-premium-messaging.md). El encabezado, que incluye las propiedades de la aplicación estándar y personalizados, puede tener un tamaño máximo de 64 KB. No hay ningún límite del número de mensajes en un tema, pero hay un límite en el tamaño total de los mensajes que llevan a cabo por un tema. El tamaño de este tema se define en la hora de creación, con un límite máximo de 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Recibir mensajes de una suscripción

Reciben los mensajes de una suscripción usando la **recibir\_suscripción\_message()** método en el objeto **Azure::ServiceBusService** . De forma predeterminada, mensajes read(peak) y bloqueado sin eliminarlo de la suscripción. Puede leer y eliminar el mensaje de la suscripción estableciendo la **peek\_bloqueo** opción como **Falso**.

El comportamiento predeterminado hace que la lectura y la eliminación de una operación de dos fases, que posibilita también compatibilidad con aplicaciones que no pueden tolerar mensajes que falta. Cuando Bus de servicio recibe una solicitud, encuentra el mensaje siguiente consumir, bloquea para evitar que otros consumidores recibirlo y, a continuación, se devuelve a la aplicación. Después de la aplicación termine de procesar el mensaje (o almacena confiable para procesamiento futuro), complete la segunda fase del proceso de recepción llamando **Eliminar\_suscripción\_message()** método y proporcionar el mensaje que se va a eliminar como parámetro. El **Eliminar\_suscripción\_message()** método marcar el mensaje como consumida y quitar de la suscripción.

Si el **: información\_bloqueo** parámetro se establece en **false**, leer y eliminar el mensaje se convierte en el modelo más sencillo y funciona mejor para escenarios en los que puede tolerar una aplicación no procesar un mensaje si se produce un error. Para comprender esto, considere la posibilidad de un escenario en el que el consumidor envía la solicitud de recepción y, a continuación, se bloquea antes de procesamiento. Dado que Bus de servicio habrá marcar el mensaje como consumida, cuando la aplicación se reinicia y empieza a utilizar de nuevo, los mensajes habrá perdido el mensaje que se ha utilizado antes el bloqueo.

En el ejemplo siguiente se muestra cómo se pueden recibir mensajes y el uso de procesado **recibir\_suscripción\_message()**. En el ejemplo primero recibe y elimina un mensaje de la suscripción "mensajes de baja" mediante **: información\_bloqueo** establecida en **false**, recibe otro mensaje de "alto de mensajes" y luego elimina el mensaje utilizando **Eliminar\_suscripción\_message()**:

```
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Controlador de aplicación se bloquea y no se puede leer mensajes

Bus de servicio proporciona funcionalidad para ayudarle a recuperarse correctamente de errores en la aplicación o dificultades para procesar un mensaje. Si una aplicación de receptor no se puede procesar el mensaje por algún motivo, puede llamar a la **desbloquear\_suscripción\_message()** método en el objeto **Azure::ServiceBusService** . Esto hace Bus de servicio desbloquear el mensaje dentro de la suscripción y que esté disponible para recibir nuevamente, por la misma aplicación consumir o por otra aplicación consume.

También es un tiempo de espera asociado a un mensaje bloqueado dentro de la suscripción, y si se produce un error en la aplicación procesar el mensaje antes el tiempo de espera de bloqueo caduca (por ejemplo, si se bloquea la aplicación), se desbloquear el mensaje automáticamente y que esté disponible para recibir nuevo Bus de servicio.

En caso de que la aplicación se bloquea después de procesar el mensaje pero antes del **Eliminar\_suscripción\_message()** se denomina método, a continuación, se vuelve a entregar el mensaje a la aplicación cuando se reinicia. A menudo se denomina **Procesamiento de al menos una vez**; es decir, cada mensaje se procesará al menos una vez, pero en algunos casos es posible volverá a entregar el mismo mensaje. Si el escenario no puede tolerar procesamiento duplicado, los desarrolladores de aplicaciones deben agregar lógica adicional a su aplicación para controlar la entrega de mensajes duplicados. Esta lógica se suele usar el **mensaje\_id** propiedad del mensaje, que permanecerá constante entre intentos de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar temas y suscripciones

Temas de suscripciones son persistentes y deben eliminarse explícitamente a través del [portal de Azure][] o mediante programación. El ejemplo siguiente muestra cómo eliminar el tema denominado "tema de prueba".

```
azure_service_bus_service.delete_topic("test-topic")
```

Eliminación de un tema, también elimina cualquier suscripciones que están registradas con el tema. Suscripciones también se pueden eliminar de forma independiente. El código siguiente muestra cómo eliminar la suscripción con el nombre "mensajes de alta" del tema "tema de prueba":

```
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de temas de Bus de servicio, siga estos vínculos para obtener más información.

- Vea [colas, temas y las suscripciones](service-bus-queues-topics-subscriptions.md).
- Referencia de la API para [SqlFilter](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx).
- Visite el repositorio de [Azure SDK de rubí](https://github.com/Azure/azure-sdk-for-ruby) en GitHub.
 
[Portal de Azure]: https://portal.azure.com
