<properties 
    pageTitle="Servicio Bus con .NET y AMQP 1.0 | Microsoft Azure"
    description="Uso de Bus de servicio de .NET con AMQP"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="using-service-bus-from-net-with-amqp-10"></a>Uso de Bus de servicio de .NET con AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## <a name="downloading-the-service-bus-sdk"></a>Descargar el SDK de Bus de servicio

Compatibilidad con AMQP 1.0 está disponible en el SDK de Bus de servicio versión 2.1 o posterior. Puede asegurarse de que tiene la versión más reciente descargando los bits de Bus de servicio de [NuGet][].

## <a name="configuring-net-applications-to-use-amqp-10"></a>Configurar aplicaciones .NET usar AMQP 1.0

De forma predeterminada, la biblioteca de cliente .NET de Bus de servicio se comunica con el servicio de Bus de servicio con un protocolo basado en SOAP dedicado. Para utilizar AMQP 1.0 en lugar del predeterminado protocolo requiere configuración explícita en la cadena de conexión de Bus de servicio, como se describe en la siguiente sección. Distinto de este cambio, el código de la aplicación permanece sin modificar al usar AMQP 1.0.

En la versión actual, hay algunas características de la API que no son compatibles al usar AMQP. Estas características no compatibles se muestran más adelante en la sección [características no compatibles, las restricciones y diferencias de comportamiento](#unsupported-features-restrictions-and-behavioral-differences). Algunas de las opciones de configuración avanzada también tienen un significado diferente al usar AMQP.

### <a name="configuration-using-appconfig"></a>Configuración mediante App.config

Es recomendable para las aplicaciones usar el archivo de configuración App.config para almacenar la configuración. Para las aplicaciones de Bus de servicio, puede usar App.config para almacenar la configuración para el valor de **cadena de conexión** de Bus de servicio. Un ejemplo de archivo App.config es la siguiente:

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
        </appSettings>
    </configuration>

El valor de la `Microsoft.ServiceBus.ConnectionString` configuración es la cadena de conexión de Bus de servicio que se usa para configurar la conexión de Bus de servicio. El formato es como sigue:

    Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

Donde `[namespace]` y `SharedAccessKey` se obtienen desde el [portal de Azure][]. Para obtener más información, vea [Introducción a las colas de Bus de servicio][].

Al utilizar AMQP, anexar la cadena de conexión con `;TransportType=Amqp`. Esta notación informa a la biblioteca de cliente para hacer que su conexión a Bus de servicio mediante AMQP 1.0.

## <a name="message-serialization"></a>Serialización de mensajes

Cuando se usa el protocolo de forma predeterminada, el comportamiento de serialización predeterminado de la biblioteca de cliente .NET es utilizar el tipo de [DataContractSerializer][] para serializar una instancia de [BrokeredMessage][] transporte entre la biblioteca de cliente y el servicio de Bus de servicio. Cuando se utiliza el modo de transporte AMQP, la biblioteca de cliente utiliza el sistema de tipos AMQP para la serialización del [mensaje con][BrokeredMessage] en un mensaje AMQP. Esta serialización permite que el mensaje se ha recibido y interpreta una aplicación receptora que posiblemente se ejecuta en una plataforma diferente, por ejemplo, una aplicación de Java que utiliza la API de JMS para tener acceso a Bus de servicio.

Cuando se crea una instancia de [BrokeredMessage][] , puede proporcionar un objeto de .NET como un parámetro al constructor para servir el cuerpo del mensaje. Los objetos que se pueden asignar a tipos AMQP simples, el cuerpo es serie en tipos de datos AMQP. Si el objeto no se puede asignar directamente en un tipo simple AMQP; es decir, un tipo personalizado definido por la aplicación, el objeto es serie mediante [DataContractSerializer][]y el número de serie bytes se envían en un mensaje de datos AMQP.

Para facilitar la interoperabilidad con los clientes no .NET, use tipos de .NET que pueden serie directamente en tipos AMQP para el cuerpo del mensaje. En la tabla siguiente detalla los tipos y la asignación correspondiente al sistema de tipo AMQP.

| Tipo de objeto de cuerpo de .NET          | Tipo AMQP asignado                          | Tipo de sección de cuerpo AMQP                                                                                                                                    |
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| BOOL                           | valor booleano                                   | Valor AMQP                                                                                                                                                |
| Byte                           | ubyte                                     | Valor AMQP                                                                                                                                                |
| ushort                         | ushort                                    | Valor AMQP                                                                                                                                                |
| uint                           | uint                                      | Valor AMQP                                                                                                                                                |
| ulong                          | ulong                                     | Valor AMQP                                                                                                                                                |
| SByte                          | Byte                                      | Valor AMQP                                                                                                                                                |
| breve                          | breve                                     | Valor AMQP                                                                                                                                                |
| int                            | int                                       | Valor AMQP                                                                                                                                                |
| largo                           | largo                                      | Valor AMQP                                                                                                                                                |
| flotante                          | flotante                                     | Valor AMQP                                                                                                                                                |
| doble                         | doble                                    | Valor AMQP                                                                                                                                                |
| decimal                        | decimal128                                | Valor AMQP                                                                                                                                                |
| Char                           | Char                                      | Valor AMQP                                                                                                                                                |
| Fecha y hora                       | marca de tiempo                                 | Valor AMQP                                                                                                                                                |
| GUID                           | UUID                                      | Valor AMQP                                                                                                                                                |
| byte]                         | binario.                                    | Valor AMQP                                                                                                                                                |
| cadena                         | cadena                                    | Valor AMQP                                                                                                                                                |
| System.Collections.IList       | lista                                      | Valor AMQP: elementos contenidos en la colección sólo pueden ser los que se definen en esta tabla.                                                             |
| System.Array                   | matriz                                     | Valor AMQP: elementos contenidos en la colección sólo pueden ser los que se definen en esta tabla.                                                             |
| System.Collections.IDictionary | mapa                                       | Valor AMQP: elementos contenidos en la colección sólo pueden ser los que se definen en esta tabla. Nota: claves de cadena solo son compatibles.                        |
| URI                            | Se describe la cadena (consulte la tabla siguiente) | Valor AMQP                                                                                                                                                |
| DateTimeOffset                 | Describe cuánto tiempo (consulte la tabla siguiente)   | Valor AMQP                                                                                                                                                |
| Intervalo de tiempo                       | Describe cuánto tiempo (vea la siguiente)         | Valor AMQP                                                                                                                                                |
| Secuencia                         | binario.                                    | Datos de AMQP (pueden ser varios). Las secciones de datos contienen la lectura desde el objeto de secuencia de bytes sin formato.                                                           |
| Otro objeto                   | binario.                                    | Datos de AMQP (pueden ser varios). Contiene el número de serie binario del objeto que usa DataContractSerializer o un serializador proporcionada por la aplicación. |

| Tipo .NET      | Asignada AMQP descrito tipo                                                                                              | Notas                   |
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| URI            | `<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>`                       | Uri.AbsoluteUri         |
| DateTimeOffset | `<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` | DateTimeOffset.UtcTicks |
| Intervalo de tiempo       | `<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> `              | TimeSpan.Ticks          |

## <a name="unsupported-features-restrictions-and-behavioral-differences"></a>Características no compatibles, las restricciones y diferencias de comportamiento

Las siguientes características de la API de .NET de Bus de servicio no se admiten actualmente al usar AMQP:

-   Transacciones

-   Enviar a través de destino de transferencia

También hay algunas pequeñas diferencias en el comportamiento de la API del servicio Bus .NET al utilizar AMQP, en comparación con el protocolo predeterminado:

-   Se omite la propiedad [OperationTimeout][] .

-   `MessageReceiver.Receive(TimeSpan.Zero)`se implementa como `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.

-   Completar mensajes por tokens bloquear solo puede realizarse por los receptores de mensaje que inicialmente haya recibido los mensajes.

## <a name="controlling-amqp-protocol-settings"></a>Controlar la configuración de protocolo AMQP

Las API de .NET exponen varias opciones para controlar el comportamiento del protocolo AMQP:

-   **MessageReceiver.PrefetchCount**: controla el crédito inicial aplicado a un vínculo. El valor predeterminado es 0.

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**: el tamaño máximo de marco AMQP ofrece durante la negociación de la conexión de controles abran tiempo. El valor predeterminado es 65.536 bytes.

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**: si transferencias son batchable, este valor determina el retardo máximo para enviar disposiciones. Remitentes y destinatarios heredan de forma predeterminada. Remitente o destinatario individual pueden reemplazar la predeterminada, que es 20 milisegundos.

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**: controla si se establecen conexiones de AMQP con una conexión SSL. El valor predeterminado es **true**.

## <a name="next-steps"></a>Pasos siguientes

¿Listo para obtener más información? Visite los siguientes vínculos:

- [Información general de AMQP de Bus de servicio]
- [Compatibilidad con AMQP 1.0 Bus de servicio particiones colas y temas]
- [AMQP de Bus de servicio de Windows Server]

  [Introducción a colas de Bus de servicio]: service-bus-dotnet-get-started-with-queues.md
  [DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[Portal de Azure]: https://portal.azure.com
[Información general de AMQP de Bus de servicio]: service-bus-amqp-overview.md
[Compatibilidad con AMQP 1.0 Bus de servicio particiones colas y temas]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP de Bus de servicio de Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
