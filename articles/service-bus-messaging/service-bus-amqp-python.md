<properties 
    pageTitle="Servicio Bus y Python con AMQP 1.0 | Microsoft Azure"
    description="Uso de Bus de servicio de Python con AMQP."
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
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="using-service-bus-from-python-with-amqp-10"></a>Uso de Bus de servicio de Python con AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Protones Python es un enlace de lenguaje Python a protones-C; es decir, Python protones se implementa como un contenedor alrededor de un motor ha implementado en C.

## <a name="download-the-proton-client-library"></a>Descargar la biblioteca de cliente protones

Puede descargar protones-C y sus enlaces asociados (incluyendo Python) desde [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). La descarga está en formato de código fuente. Para generar el código, siga las instrucciones incluidas en el paquete descargado.

Tenga en cuenta que en el momento de escribir este documento, la compatibilidad con SSL en C protones solo está disponible para sistemas operativos Linux. Porque Bus de servicio de Azure requiere el uso de SSL, solo se utiliza protones C (y los enlaces language) para acceder Bus de servicios de Linux en este momento. Trabajo para habilitar protones-C con SSL en Windows es sencillo así que atrás con frecuencia para las actualizaciones.

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-python"></a>Trabajar con colas, temas y las suscripciones de Python Bus de servicio

El código siguiente muestra cómo enviar y recibir mensajes de un Bus de servicios de mensajería entidad.

### <a name="send-messages-using-proton-python"></a>Enviar mensajes con Python protones

El código siguiente muestra cómo enviar un mensaje a un Bus de servicios de mensajería entidad.

```
messenger = Messenger()
message = Message()
message.address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
```

### <a name="receive-messages-using-proton-python"></a>Recibir mensajes con Python protones

El código siguiente muestra cómo recibir un mensaje de un Bus de servicios de mensajería entidad.

```
messenger = Messenger()
address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"
messenger.subscribe(address)

messenger.start()
messenger.recv(1)
message = Message()
if messenger.incoming:
      messenger.get(message)
messenger.stop()
```

## <a name="messaging-between-net-and-proton-python"></a>Mensajería entre .NET y Python protones

### <a name="application-properties"></a>Propiedades de la aplicación

#### <a name="proton-python-to-service-bus-net-apis"></a>Protones Python a las API de .NET de Bus de servicio

Propiedades de la aplicación de los siguientes tipos de soporte técnico de mensajes de Python protones: **int**, **largo**, **flotante**, **uuid**, **bool**, **string**. El siguiente código Python muestra cómo establecer propiedades en un mensaje con cada uno de estos tipos de propiedades.

```
message.properties[u"TestString"] = u"This is a string"    
message.properties[u"TestInt"] = 1
message.properties[u"TestLong"] = 1000L
message.properties[u"TestFloat"] = 1.5    
message.properties[u"TestGuid"] = uuid.uuid1()    
```

En la API de .NET Bus de servicio, propiedades de la aplicación de mensaje se ejecutan en la colección de **Propiedades** de [BrokeredMessage][]. El código siguiente muestra cómo leer las propiedades de la aplicación de un mensaje recibido de un cliente de Python.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

La siguiente tabla asigna los tipos de propiedad de Python a los tipos de propiedades de .NET.

| Tipo de propiedad de Python | Tipo de propiedad de .NET |
|----------------------|--------------------|
| int                  | int                |
| flotante                | doble             |
| largo                 | Int64              |
| UUID                 | GUID               |
| BOOL                 | BOOL               |
| cadena               | cadena             |

#### <a name="service-bus-net-apis-to-proton-python"></a>Bus de servicio de la API de .NET a Python protones

El tipo de [BrokeredMessage][] es compatible con propiedades de la aplicación de los siguientes tipos: **byte**, **sbyte**, **char**, **breve**, **ushort**, **int**, **uint**, **long**, **ulong**, **flotación**, **doble**, **decimal**, **bool**, **Guid**, **cadena**, **Uri**, **DateTime**, **DateTimeOffset**e **intervalo de tiempo**. El siguiente código .NET muestra cómo establecer propiedades en un objeto de [BrokeredMessage][] con cada uno de estos tipos de propiedades.

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

El siguiente código Python muestra cómo leer las propiedades de la aplicación de un mensaje recibido de un cliente .NET de Bus de servicio.

```
if message.properties != None:
   for k,v in message.properties.items():         
         print "--   %s : %s (%s)" % (k, str(v), type(v))         
```

La siguiente tabla asigna los tipos de propiedades de .NET a los tipos de propiedad de Python.

| Tipo de propiedad de .NET | Tipo de propiedad de Python | Notas                                                                                                                                                               |
|--------------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Byte               | int                  | -                                                                                                                                                                     |
| SByte              | int                  | -                                                                                                                                                                     |
| Char               | Char                 | Clase protones Python                                                                                                                                                 |
| breve              | int                  | -                                                                                                                                                                     |
| ushort             | int                  | -                                                                                                                                                                     |
| int                | int                  | -                                                                                                                                                                     |
| uint               | int                  | -                                                                                                                                                                     |
| largo               | int                  | -                                                                                                                                                                     |
| ulong              | largo                 | Clase protones Python                                                                                                                                                 |
| flotante              | flotante                | -                                                                                                                                                                     |
| doble             | flotante                | -                                                                                                                                                                     |
| decimal            | Cadena               | Decimal no es compatible actualmente con protones.                                                                                                                     |
| BOOL               | BOOL                 | -                                                                                                                                                                     |
| GUID               | UUID                 | Clase protones Python                                                                                                                                                 |
| cadena             | cadena               | -                                                                                                                                                                     |
| Fecha y hora           | marca de tiempo            | Clase protones Python                                                                                                                                                 |
| DateTimeOffset     | DescribedType        | DateTimeOffset.UtcTicks asignadas al tipo de AMQP:<type name=”datetime-offset” class=restricted source=”long”><descriptor name=”com.microsoft:datetime-offset” /></type> |
| Intervalo de tiempo           | DescribedType        | Timespan.Ticks asignadas al tipo de AMQP:<type name=”timespan” class=restricted source=”long”><descriptor name=”com.microsoft:timespan” /></type>                        |
| URI                | DescribedType        | Uri.AbsoluteUri asignadas al tipo de AMQP:<type name=”uri” class=restricted source=”string”><descriptor name=”com.microsoft:uri” /></type>                               |

### <a name="standard-properties"></a>Propiedades estándar

Las siguientes tablas muestran las asignaciones entre las propiedades de mensaje estándar de Python protones y las propiedades de mensaje estándar [BrokeredMessage][] .

#### <a name="proton-python-to-service-bus-net-apis"></a>Protones Python a las API de .NET de Bus de servicio

| Python protones        | Bus de servicio .NET         | Notas                                                     |
|----------------------|--------------------------|-----------------------------------------------------------|
| resistentes              | n/a.                      | Bus de servicio solo admite mensajes resistentes.               |
| prioridad             | n/a.                      | Bus de servicio solo es compatible con una prioridad de mensaje.      |
| TTL                  | Message.TimeToLive       | Conversión, Python protones TTL se define en milisegundos. |
| primera\_comprador      | n/a.                      | -                                                           |
| entrega\_recuento      | n/a.                      | -                                                           |
| Id.                   | Message.MessageID        | -                                                           |
| usuario\_Id.             | n/a.                      | -                                                           |
| dirección              | Message.To               | -                                                           |
| Asunto              | Message.Label            | -                                                           |
| respuesta\_a            | Message.ReplyTo          | -                                                           |
| correlación\_Id.      | Message.CorrelationID    | -                                                           |
| contenido\_tipo        | Message.ContentType      | -                                                           |
| contenido\_codificación    | n/a.                      | -                                                           |
| expiración\_tiempo         | n/a.                      | -                                                           |
| creación\_tiempo       | n/a.                      | -                                                           |
| grupo\_Id.            | Message.SessionId        | -                                                           |
| grupo\_secuencia      | n/a.                      | -                                                           |
| respuesta\_a\_grupo\_Id. | Message.ReplyToSessionId | -                                                           |
| formato               | n/a.                      | -                                                           |

| Bus de servicio .NET        | Protones                       | Notas                                                     |
|-------------------------|------------------------------|-----------------------------------------------------------|
| ContentType             | Message.Content\_tipo        | -                                                           |
| CorrelationId           | Message.Correlation\_Id.      | -                                                           |
| EnqueuedTimeUtc         | n/a.                          | -                                                           |
| Etiqueta                   | Message.Subject              | -                                                           |
| Id. de mensaje               | Message.Id                   | -                                                           |
| Origen                 | Message.Reply\_a            | -                                                           |
| ReplyToSessionId        | Message.Reply\_a\_grupo\_Id. | -                                                           |
| ScheduledEnqueueTimeUtc | n/a.                          | -                                                           |
| Id. de sesión               | Message.Group\_Id.            | -                                                           |
| Período de vida              | Message.TTL                  | Conversión, Python protones TTL se define en milisegundos. |
| Para                      | Message.Address              | -                                                           |

## <a name="next-steps"></a>Pasos siguientes

¿Listo para obtener más información? Visite los siguientes vínculos:

- [Información general de AMQP de Bus de servicio]
- [AMQP de Bus de servicio de Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP de Bus de servicio de Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

[Información general de AMQP de Bus de servicio]: service-bus-amqp-overview.md
