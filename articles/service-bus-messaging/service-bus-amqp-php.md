<properties 
    pageTitle="Servicio Bus y PHP con AMQP 1.0 | Microsoft Azure"
    description="Uso de Bus de servicio de PHP con AMQP."
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

# <a name="using-service-bus-from-php-with-amqp-10"></a>Uso de Bus de servicio de PHP con AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Protones PHP es un enlace de idioma PHP a protones-C; es decir, protones PHP se implementa como un contenedor alrededor de un motor ha implementado en C.

## <a name="downloading-the-proton-client-library"></a>Descargar la biblioteca de cliente protones

Puede descargar protones-C y sus enlaces asociados (incluyendo PHP) desde [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). La descarga está en formato de código fuente. Para generar el código, siga las instrucciones incluidas en el paquete descargado.

> [AZURE.IMPORTANT] En el momento de escribir este documento, la compatibilidad con SSL en C protones solo está disponible para sistemas operativos Linux. Porque Bus de servicio de Azure requiere el uso de SSL, solo se utiliza protones C (y los enlaces language) para acceder Bus de servicios de Linux en este momento. Trabajo para habilitar protones-C con SSL en Windows en curso es sencillo, así que atrás con frecuencia para las actualizaciones.

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-php"></a>Trabajar con colas Bus de servicio, temas y las suscripciones de PHP

El código siguiente muestra cómo enviar y recibir mensajes de un Bus de servicios de mensajería entidad.

### <a name="sending-messages-using-proton-php"></a>Enviar mensajes con PHP protones

El código siguiente muestra cómo enviar un mensaje a un Bus de servicios de mensajería entidad.

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### <a name="receiving-messages-using-proton-php"></a>Recibir mensajes con PHP protones

El código siguiente muestra cómo recibir un mensaje de un Bus de servicios de mensajería entidad.

```
$messenger = new Messenger();
$address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## <a name="messaging-between-net-and-proton-php"></a>Mensajería entre .NET y PHP protones

### <a name="application-properties"></a>Propiedades de la aplicación

#### <a name="protonphp-to-service-bus-net-apis"></a>ProtonPHP a las API de .NET de Bus de servicio

Propiedades de la aplicación de los siguientes tipos de soporte técnico de mensajes de PHP protones: **integer**, **double**, **booleana**, **cadena**y **objeto**. El siguiente código PHP muestra cómo establecer propiedades en un mensaje con cada uno de estos tipos de propiedades.

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

En las API de .NET Bus de servicio, propiedades de la aplicación de mensaje se ejecutan en la colección de **Propiedades** de [BrokeredMessage][]. El código siguiente muestra cómo leer las propiedades de la aplicación de un mensaje recibido de un cliente PHP.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

La siguiente tabla asigna los tipos de propiedades PHP a los tipos de propiedad .NET.

| Tipo de propiedad PHP | Tipo de propiedad de .NET |
|-------------------|--------------------|
| entero           | int                |
| doble            | doble             |
| valor booleano           | BOOL               |
| cadena            | cadena             |
| objeto            | Objeto             |

#### <a name="service-bus-net-apis-to-php"></a>Bus de servicio de la API de .NET a PHP

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
```

El siguiente código PHP muestra cómo leer las propiedades de la aplicación de un mensaje recibido de un cliente .NET de Bus de servicio.

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

La siguiente tabla asigna los tipos de propiedades de .NET a los tipos de propiedad PHP.

| Tipo de propiedad de .NET | Tipo de propiedad PHP | Notas                                                                                                                                                               |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Byte               | entero           | -                                                                                                                                                                     |
| SByte              | entero           | -                                                                                                                                                                     |
| Char               | Char              | Clase protones PHP                                                                                                                                                    |
| breve              | entero           | -                                                                                                                                                                     |
| ushort             | entero           | -                                                                                                                                                                     |
| int                | entero           | -                                                                                                                                                                     |
| uint               | Entero           | -                                                                                                                                                                     |
| largo               | entero           | -                                                                                                                                                                     |
| ulong              | entero           | -                                                                                                                                                                     |
| flotante              | doble            | -                                                                                                                                                                     |
| doble             | doble            | -                                                                                                                                                                     |
| decimal            | cadena            | Decimal no es compatible actualmente con protones.                                                                                                                     |
| BOOL               | valor booleano           | -                                                                                                                                                                     |
| GUID               | UUID              | Clase protones PHP                                                                                                                                                    |
| cadena             | cadena            | -                                                                                                                                                                     |
| Fecha y hora           | entero           | -                                                                                                                                                                     |
| DateTimeOffset     | DescribedType     | DateTimeOffset.UtcTicks asignadas al tipo de AMQP:<type name="datetime-offset" class=restricted source="long"><descriptor name="com.microsoft:datetime-offset" /></type> |
| Intervalo de tiempo           | DescribedType     | Timespan.Ticks asignadas al tipo de AMQP:<type name="timespan" class=restricted source="long"><descriptor name="com.microsoft:timespan" /></type>                        |
| URI                | DescribedType     | Uri.AbsoluteUri asignadas al tipo de AMQP:<type name="uri" class=restricted source="string"><descriptor name="com.microsoft:uri" /></type>                               |

### <a name="standard-properties"></a>Propiedades estándar

Las siguientes tablas muestran las asignaciones entre las propiedades de mensaje estándar de PHP protones y las propiedades de mensaje estándar [BrokeredMessage][] .

| PHP protones           | Bus de servicio .NET         | Notas                                                    |
|----------------------|--------------------------|----------------------------------------------------------|
| Resistentes              | n/a.                      | Bus de servicio solo admite mensajes resistentes.          |
| Prioridad             | n/a.                      | Bus de servicio solo es compatible con una prioridad de mensaje. |
| TTL                  | Message.TimeToLive       | Conversión de PHP protones TTL se define en milisegundos.   |
| primera\_comprador      | -                          | -                                                          |
| entrega\_recuento      | -                          | -                                                          |
| Id.                   | Message.Id               | -                                                          |
| usuario\_Id.             | -                          | -                                                          |
| Dirección              | Message.To               | -                                                          |
| Asunto              | Message.Label            | -                                                          |
| respuesta\_a            | Message.ReplyTo          | -                                                          |
| correlación\_Id.      | Message.CorrelationId    | -                                                          |
| contenido\_tipo        | Message.ContentType      | -                                                          |
| contenido\_codificación    | n/a.                      | -                                                          |
| expiración\_tiempo         | Message.ExpiresAtUTC     | -                                                          |
| creación\_tiempo       | n/a.                      | -                                                          |
| grupo\_Id.            | Message.SessionId        | -                                                          |
| grupo\_secuencia      | -                          | -                                                          |
| respuesta\_a\_grupo\_Id. | Message.ReplyToSessionId | -                                                          |
| Formato               | n/a.                      | -

#### <a name="service-bus-net-apis-to-proton-php"></a>Bus de servicio de la API de .NET a PHP protones

| Bus de servicio .NET        | PHP protones                                             | Notas                                                  |
|-------------------------|--------------------------------------------------------|--------------------------------------------------------|
| ContentType             | Mensaje -\>contenido\_tipo                                | -                                                        |
| CorrelationId           | Mensaje -\>correlación\_Id.                              | -                                                        |
| EnqueuedTimeUtc         | Mensaje -\>anotaciones [x-participar-en cola-time]             | -                                                        |
| Etiqueta                   | Mensaje -\>asunto                                      | -                                                        |
| Id. de mensaje               | Mensaje -\>Id.                                           | -                                                        |
| Origen                 | Mensaje -\>respuesta\_a                                    | -                                                        |
| ReplyToSessionId        | Mensaje -\>respuesta\_a\_grupo\_Id.                         | -                                                        |
| ScheduledEnqueueTimeUtc | Mensaje -\>anotaciones ["x-participar-programada-poner-time"] | -                                                        |
| Id. de sesión               | Mensaje de\>grupo\_Id.                                    | -                                                        |
| Período de vida              | Mensaje -\>ttl                                          | Conversión de PHP protones TTL se define en milisegundos. |
| Para                      | Mensaje -\>dirección                                      | -                                                        |

## <a name="next-steps"></a>Pasos siguientes

¿Listo para obtener más información? Visite los siguientes vínculos:

- [Información general de AMQP de Bus de servicio]
- [AMQP de Bus de servicio de Windows Server]


[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP de Bus de servicio de Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[Información general de AMQP de Bus de servicio]: service-bus-amqp-overview.md
