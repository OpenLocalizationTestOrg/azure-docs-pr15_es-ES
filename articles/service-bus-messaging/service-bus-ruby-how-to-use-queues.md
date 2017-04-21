<properties
    pageTitle="Cómo usar colas de Bus de servicio con Ruby | Microsoft Azure"
    description="Obtenga información sobre cómo usar colas de Bus de servicio en Azure. Ejemplos de código escritos en Ruby."
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

# <a name="how-to-use-service-bus-queues"></a>Cómo usar colas de Bus de servicio

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Esta guía describe cómo usar colas de Bus de servicio. Los ejemplos se escriben en Ruby y use el indicador de Azure. Los escenarios cubiertos incluyen **crear colas, enviar y recibir mensajes**y **eliminar colas**. Para obtener más información acerca de las colas de Bus de servicio, vea la sección [Pasos siguientes](#next-steps) .

## <a name="what-are-service-bus-queues"></a>¿Cuáles son las colas de Bus de servicio?

Colas de Bus de servicio de soporte técnico un modelo de comunicación *con mensajería* . Con colas, componentes de una aplicación distribuida no comunican directamente con los otros; en su lugar intercambiar mensajes a través de una cola, que actúa como intermediario. Un productor de mensaje (remitente) entrega un mensaje a la cola y continúa su procesamiento.
De forma asincrónica, un consumidor de mensaje (receptor) extrae el mensaje de la cola y lo procesa. El productor no tiene que esperar una respuesta del consumidor para continuar para procesar y enviar más mensajes. Colas ofrecen **primero en ENTRAR, primera salida (FIFO)** de entrega de mensajes a uno o varios de los consumidores competencia. Es decir, mensajes normalmente se recibió y procesados por los receptores en el orden en que se agregaron a la cola y cada mensaje se ha recibido y procesado por los consumidores de un solo mensaje.

![QueueConcepts](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

Colas de Bus de servicio son una tecnología de propósito general que puede usarse para una amplia variedad de escenarios:

-   Comunicación entre los roles de web y trabajador en una [aplicación de Azure de varios niveles](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md).
-   Comunicación entre aplicaciones locales y Azure había hospeda aplicaciones en una [solución híbrida](service-bus-dotnet-hybrid-app-using-service-bus-relay.md).
-   Comunicación entre los componentes de una aplicación distribuida que se ejecuta en local en diferentes organizaciones o departamentos de una organización.

Mediante colas puede le permite ampliar sus aplicaciones mejor y habilitar más resistencia a la arquitectura.

## <a name="create-a-namespace"></a>Crear un espacio de nombres

Para empezar a usar colas de Bus de servicio en Azure, primero debe crear un espacio de nombres. Un espacio de nombres proporciona un contenedor de ámbito para el direccionamiento de recursos de Bus de servicio de la aplicación. Debe crear el espacio de nombres mediante la interfaz de línea de comandos porque el portal de Azure no crea el espacio de nombres con una conexión de ACS.

Para crear un espacio de nombres:

1. Abrir una consola de Powershell de Azure.

2. Escriba el comando siguiente para crear un espacio de nombres de Bus de servicio. Proporcionar su propio valor de espacio de nombres y especificar la misma región como la aplicación.

    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

    ![Create Namespace](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)
    ```

## <a name="obtain-management-credentials-for-the-namespace"></a>Obtener las credenciales de administración para el espacio de nombres

Para poder realizar operaciones de administración, como la creación de una cola en el espacio de nombres, debe obtener las credenciales de administración para el espacio de nombres.

El cmdlet de PowerShell que ejecutó para crear el espacio de nombres de bus de servicio de Azure muestra la clave que se puede usar para administrar el espacio de nombres. Copie el valor de **DefaultKey** . Más adelante en este tutorial usará este valor en el código.

![Copiar clave](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

> [AZURE.NOTE] También puede encontrar esta clave si iniciar sesión en el [portal de Azure](https://portal.azure.com/) y vaya a la información de conexión para el espacio de nombres de Bus de servicio.

## <a name="create-a-ruby-application"></a>Crear una aplicación de ideogramas y transcripción fonética

Crear una aplicación Ruby. Para obtener instrucciones, vea [crear una aplicación de rubí en Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a name="configure-your-application-to-use-service-bus"></a>Configurar la aplicación para usar Bus de servicio

Para usar Bus de servicio de Azure, descargar y usar el paquete de Azure Ruby, que incluye un conjunto de bibliotecas de comodidad que comunicarse con los servicios del resto de almacenamiento.

### <a name="use-rubygems-to-obtain-the-package"></a>Usar RubyGems para obtener el paquete

1. Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).

2. Escriba "azure de instalación de indicador" en la ventana de comandos para instalar el indicador y dependencias.

### <a name="import-the-package"></a>Importar el paquete

Usar el editor de texto, agregue lo siguiente a la parte superior del archivo Ruby donde vaya a usar el almacenamiento:

```
require "azure"
```

## <a name="set-up-an-azure-service-bus-connection"></a>Configurar una conexión de Bus de servicio de Azure

El módulo Azure lee las variables de entorno **AZURE\_SERVICEBUS\_espacio de nombres** y **AZURE\_SERVICEBUS\_ACCESS_KEY** para obtener información necesaria para conectarse a su espacio de nombres de Bus de servicio. Si no se establecen estas variables de entorno, debe especificar la información de espacio de nombres antes de utilizar **Azure::ServiceBusService** con el siguiente código:

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Establezca el valor de espacio de nombres en el valor que se ha creado, en lugar de la dirección URL completa. Por ejemplo, use **"yourexamplenamespace"**, no "yourexamplenamespace.servicebus.windows.net".

## <a name="how-to-create-a-queue"></a>Cómo crear una cola

El objeto **Azure::ServiceBusService** le permite trabajar con colas. Para crear una cola, use el método de **create_queue()** . En el ejemplo siguiente se crea una cola o imprime los errores.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

También puede pasar un objeto de **Azure::ServiceBus::Queue** opciones adicionales, que le permite invalidar la configuración de cola predeterminada, por ejemplo, período de vida o el tamaño máximo de cola. En el ejemplo siguiente se muestra cómo configurar el tamaño máximo de cola a 5GB y tiempo para live a 1 minuto:

```
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Cómo enviar mensajes a una cola

Para enviar un mensaje de una cola de Bus de servicio, llamadas de aplicación la **Enviar\_cola\_message()** método en el objeto **Azure::ServiceBusService** . Los mensajes enviadas a (y recibidas desde) colas de Bus de servicio son objetos **Azure::ServiceBus::BrokeredMessage** y tienen un conjunto de propiedades estándar (como **etiqueta** y **tiempo\_a\_live**), un diccionario que se utiliza para contener propiedades específicas de aplicación personalizada y un cuerpo de datos de aplicación arbitrario. Una aplicación puede establecer el cuerpo del mensaje pasando un valor de cadena que el mensaje y las propiedades necesarias estándares se rellena con valores predeterminados.

En el ejemplo siguiente se muestra cómo enviar un mensaje de prueba a la cola denominada "cola de prueba" usando **Enviar\_cola\_message()**:

```
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Colas de Bus de servicio compatible con el tamaño máximo de mensaje de 256 KB en el [nivel estándar](service-bus-premium-messaging.md) y 1 MB en el [nivel de Premium](service-bus-premium-messaging.md). El encabezado, que incluye las propiedades de la aplicación estándar y personalizados, puede tener un tamaño máximo de 64 KB. No hay ningún límite del número de mensajes de una cola, pero hay un límite en el tamaño total de los mensajes que llevan a cabo una cola. Tamaño de la cola se define en la hora de creación, con un límite máximo de 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Cómo recibir mensajes de una cola

Reciben los mensajes de una cola utilizando la **recibir\_cola\_message()** método en el objeto **Azure::ServiceBusService** . De forma predeterminada, los mensajes se lea y bloqueados sin que se eliminan de la cola. Sin embargo, puede eliminar mensajes de la cola de medida que se leen estableciendo la **: peek_lock** opción como **Falso**.

El comportamiento predeterminado hace que la lectura y la eliminación de una operación de dos fases, que posibilita también compatibilidad con aplicaciones que no pueden tolerar mensajes que falta. Cuando Bus de servicio recibe una solicitud, encuentra el mensaje siguiente consumir, bloquea para evitar que otros consumidores recibirlo y, a continuación, se devuelve a la aplicación. Después de la aplicación termine de procesar el mensaje (o almacena confiable para procesamiento futuro), complete la segunda fase del proceso de recepción llamando **Eliminar\_cola\_message()** método y proporcionar el mensaje que se va a eliminar como parámetro. El **Eliminar\_cola\_message()** método marcar el mensaje como consumida y quitar de la cola.

Si el **: información\_bloqueo** parámetro se establece en **false**, leer y eliminar el mensaje se convierte en el modelo más sencillo y funciona mejor para escenarios en los que puede tolerar una aplicación no procesar un mensaje si se produce un error. Para comprender esto, considere la posibilidad de un escenario en el que el consumidor envía la solicitud de recepción y, a continuación, se bloquea antes de procesamiento. Porque Bus de servicio habrá marcado como consumido, cuando la aplicación se reinicia y empieza a utilizar mensajes de nuevo el mensaje, habrá perdido el mensaje que se ha utilizado antes el bloqueo.

En el ejemplo siguiente se muestra cómo recibir y procesar mensajes con **recibir\_cola\_message()**. El ejemplo primero recibe y elimina un mensaje utilizando **: información\_bloqueo** establecida en **false**, recibe otro mensaje y luego elimina el mensaje utilizando **Eliminar\_cola\_message()**:

```
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Cómo controlar los mensajes no se puede leer y bloqueos de la aplicación

Bus de servicio proporciona funcionalidad para ayudarle a recuperarse correctamente de errores en la aplicación o dificultades para procesar un mensaje. Si una aplicación de receptor no se puede procesar el mensaje por algún motivo, puede llamar a la **desbloquear\_cola\_message()** método en el objeto **Azure::ServiceBusService** . Esto hace Bus de servicio desbloquear el mensaje en la cola y que esté disponible para recibir nuevamente, por la misma aplicación consumir o por otra aplicación consume.

También es un tiempo de espera asociado a un mensaje bloqueado en la cola y si se produce un error en la aplicación procesar el mensaje antes el tiempo de espera de bloqueo caduca (por ejemplo, si se bloquea la aplicación), entonces Bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para volver a recibir.

En caso de que la aplicación se bloquea después de procesar el mensaje pero antes del **Eliminar\_cola\_message()** se denomina método, a continuación, el mensaje se volverá a entregar a la aplicación cuando se reinicia. A menudo se denomina **Procesamiento de al menos una vez**; es decir, cada mensaje se procesa al menos una vez, pero en algunos casos es posible volverá a entregar el mismo mensaje. Si el escenario no puede tolerar procesamiento duplicado, los desarrolladores de aplicaciones deben agregar lógica adicional a su aplicación para controlar la entrega de mensajes duplicados. A menudo esto se logra utilizando la **mensaje\_id** propiedad del mensaje, que se mantiene constante en todos los intentos de entrega.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de colas de Bus de servicio, siga estos vínculos para obtener más información.

-   Descripción general de [colas, temas y las suscripciones](service-bus-queues-topics-subscriptions.md).
-   Visite el repositorio de [Azure SDK de rubí](https://github.com/Azure/azure-sdk-for-ruby) en GitHub.

Para ver una comparación entre las colas de Bus de servicio de Azure analizados en este artículo y colas de Azure analizados en el artículo [cómo usar el almacenamiento de la cola de rubí](../storage/storage-ruby-how-to-use-queue-storage.md) , vea [colas de Azure y colas de Bus de servicio de Azure - en comparación y Contrasted](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
 
