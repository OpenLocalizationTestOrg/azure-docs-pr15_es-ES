<properties 
    pageTitle="Cómo usar AMQP 1.0 con la API de Bus de servicio .NET | Microsoft Azure" 
    description="Aprenda a usar avanzada mensaje Queue Protodol (AMQP) 1.0 con la API de Bus de servicio de Azure .NET." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-amqp-10-with-the-service-bus-net-api"></a>Cómo usar AMQP 1.0 con la API de .NET de Bus de servicio

La avanzada mensaje Queue protocolo (AMQP) 1.0 es un protocolo de mensajería eficaz, confiable nivel de alambre que puede usar para generar sólidos, en varias plataformas, aplicaciones de mensajería.

Soporte para AMQP 1.0 en Bus de servicio significa que puede usar la cola y de publicación o suscripción con características de mensajería de un intervalo de plataformas con un protocolo binario eficaz. Además, puede crear aplicaciones conformadas de los componentes creados mediante una combinación de idiomas, marcos y sistemas operativos.

En este artículo se explica cómo usar el Bus de servicio con características de mensajería (colas y temas de publicación o suscripción) de las aplicaciones de .NET con la API de .NET de Bus de servicio. Hay un [artículo complementario](service-bus-java-how-to-use-jms-api-amqp.md) que explica cómo llevar a cabo la misma utilizando la API de Java Message Service (JMS) estándar. Puede usar a estas dos guías juntos para obtener información sobre la mensajería entre plataformas con AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Introducción a Bus de servicio

En este artículo se supone que ya tiene un espacio de nombres de Bus de servicio que contiene una cola denominada "cola 1". Si no lo hace, puede crear la cola con el [portal de Azure][]y el espacio de nombres. Para obtener más información sobre cómo crear espacios de nombres de Bus de servicio y colas, vea [Introducción a las colas de Bus de servicio](service-bus-dotnet-get-started-with-queues.md#1-create-a-namespace-using-the-Azure-portal).

## <a name="download-the-service-bus-sdk"></a>Descargar el SDK de Bus de servicio

Compatibilidad con AMQP 1.0 está disponible en el SDK de Bus de servicio versión 2.1 o posterior. Puede descargar la última bits de NuGet en [http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/).

## <a name="code-net-applications"></a>Aplicaciones de código .NET

De forma predeterminada, la biblioteca de cliente .NET de Bus de servicio se comunica con el servicio de Bus de servicio con un protocolo basado en SOAP dedicado. Para utilizar AMQP 1.0 en lugar del predeterminado protocolo requiere configuración explícita en la cadena de conexión de Bus de servicio, como se describe en la siguiente sección. Distinto de este cambio, código de la aplicación se cambiará básicamente al usar AMQP 1.0.

En la versión actual, hay algunas características de la API que no son compatibles al usar AMQP. Estas características no compatibles se muestran más adelante en la sección [restricciones y características no compatibles](#unsupported-features-and-restrictions). Algunas de las opciones de configuración avanzada también tienen un significado diferente al usar AMQP. Ninguno de estos valores se usan en este artículo, pero están disponibles en la [Visión general de servicio Bus AMQP](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences)más detalles.

### <a name="configure-via-appconfig"></a>Configurar a través de App.config

Es una práctica recomendada que aplicaciones utilizan el archivo de configuración App.config para almacenar la configuración. Para las aplicaciones de Bus de servicio, puede usar App.config para almacenar la **cadena de conexión**de Bus de servicio. Esta aplicación de ejemplo también usa App.config para almacenar el nombre del Bus de servicio de mensajería entidad que utiliza.

A continuación se muestra un archivo App.config de ejemplo:

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
            <add key="EntityName" value="queue1" />
    </appSettings>
</configuration>
```

### <a name="configure-the-service-bus-connection-string"></a>Configurar la cadena de conexión de Bus de servicio

El valor de la configuración de **Microsoft.ServiceBus.ConnectionString** es la cadena de conexión de Bus de servicio que se usa para configurar la conexión de Bus de servicio. El formato es como sigue:

```
Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp
```

Donde `[namespace]` y `[SAS key]` se obtienen desde el [portal de Azure][]. Para obtener más información, vea [cómo usar colas de Bus de servicio] [].

Al utilizar AMQP, se anexa la cadena de conexión con `;TransportType=Amqp`, que indica la biblioteca de cliente para asegurarse de su conexión al servicio Bus con AMQP 1.0.

### <a name="configure-the-entity-name"></a>Configurar el nombre de la entidad

La aplicación de este ejemplo usa la `EntityName` configuración en la sección **appSettings** del archivo App.config para configurar el nombre de la cola con la que la aplicación intercambie mensajes.

### <a name="a-simple-net-application-using-a-service-bus-queue"></a>Una aplicación de .NET sencilla mediante una cola de Bus de servicio

En el ejemplo siguiente se envía y recibe mensajes de una cola de Bus de servicio.

```
// SimpleSenderReceiver.cs
    
using System;
using System.Configuration;
using System.Threading;
using Microsoft.ServiceBus.Messaging;
    
namespace SimpleSenderReceiver
{
    class SimpleSenderReceiver
    {
        private static string connectionString;
        private static string entityName;
        private static Boolean runReceiver = true;
        private MessagingFactory factory;
        private MessageSender sender;
        private MessageReceiver receiver;
        private MessageListener messageListener;
        private Thread listenerThread;
    
        static void Main(string[] args)
        {
            try
            {
                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
                    runReceiver = false;
                    
                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
                string entityNameKey = "EntityName";
                entityName = ConfigurationManager.AppSettings[entityNameKey];
                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
    
                Console.WriteLine("Press [enter] to send a message. " +
                    "Type 'exit' + [enter] to quit.");
    
                while (true)
                {
                    string s = Console.ReadLine();
                    if (s.ToLower().Equals("exit"))
                    {
                        simpleSenderReceiver.Close();
                        System.Environment.Exit(0);
                    }
                    else
                        simpleSenderReceiver.SendMessage();
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Caught exception: " + e.Message);
            }
        }
    
        public SimpleSenderReceiver()
        {
            factory = MessagingFactory.CreateFromConnectionString(connectionString);
            sender = factory.CreateMessageSender(entityName);
    
            if (runReceiver)
            {
                receiver = factory.CreateMessageReceiver(entityName);
                messageListener = new MessageListener(receiver);
                listenerThread = new Thread(messageListener.Listen);
                listenerThread.Start();
            }
        }
    
        public void Close()
        {
            messageListener.RequestStop();
            listenerThread.Join();
            factory.Close();
        }
    
        private void SendMessage()
        {
            BrokeredMessage message = new BrokeredMessage("Test AMQP message from .NET");
            sender.Send(message);
            Console.WriteLine("Sent message with MessageID = " 
                + message.MessageId);
        }

    }
    
    public class MessageListener
    {
        private MessageReceiver messageReceiver;
        public MessageListener(MessageReceiver receiver)
        {
            messageReceiver = receiver;
        }
    
        public void Listen()
        {
            while (!_shouldStop)
            {
                try
                {
                    BrokeredMessage message = 
                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
                    if (message != null)
                    {
                        Console.WriteLine("Received message with MessageID = " + 
                            message.MessageId);
                        message.Complete();
                    }
                }
                catch (Exception e)
                {
                    Console.WriteLine("Caught exception: " + e.Message);
                    break;
                }
            }
        }
    
        public void RequestStop()
        {
            _shouldStop = true;
        }
    
        private volatile bool _shouldStop;
    }
}
```

### <a name="run-the-application"></a>Ejecute la aplicación

Ejecutar la aplicación genera el resultado del formulario:

```
> SimpleSenderReceiver.exe
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
    
Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
Received message with MessageID = d00e2e088f06416da7956b58310f7a06
    
Received message with MessageID = f27f79ec124548c196fd0db8544bca49
Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Mensajería entre JMS y .NET entre plataformas

En este tema se mostraba cómo enviar mensajes a Bus de servicio mediante .NET y también cómo recibir los mensajes con .NET. Sin embargo, una de las principales ventajas de AMQP 1.0 es que permite a las aplicaciones a partir de componentes escritos en idiomas diferentes, con mensajes intercambiados confiable y completa fidelidad.

Mediante la aplicación de .NET de ejemplo descrito anteriormente y una aplicación de Java similar copiado de una guía complementaria, [cómo usar la API con Bus de servicio & AMQP 1.0 Java Message Service (JMS)](service-bus-java-how-to-use-jms-api-amqp.md), es posible intercambiar mensajes entre .NET y Java. 

Para obtener más información acerca de los detalles de mensajería usando Bus de servicio y AMQP 1.0 entre plataformas, vea la [información general del servicio de Bus AMQP 1.0](service-bus-amqp-overview.md).

### <a name="jms-to-net"></a>JMS para .NET

Para demostrar JMS .NET mensajería:

* Inicie la aplicación de ejemplo de .NET sin ningún argumento de línea de comandos.
* Inicie la aplicación de ejemplo de Java con el argumento de línea de comandos "sendonly". En este modo, la aplicación no recibirá mensajes de la cola, solo le enviará.
* Presione **ENTRAR** varias veces en la consola de aplicación Java, lo que provocará que se envíen mensajes.
* La aplicación .NET reciben estos mensajes.

### <a name="output-from-jms-application"></a>Resultado de la aplicación de JMS

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

### <a name="output-from-net-application"></a>Resultado de la aplicación.

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

## <a name="net-to-jms"></a>.NET JMS

Para demostrar .NET JMS mensajería:

* Inicie la aplicación de ejemplo de .NET con el argumento de la línea de comandos "sendonly". En este modo, la aplicación no recibirá mensajes de la cola, solo le enviará.
* Inicie la aplicación de ejemplo de Java sin ningún argumento de línea de comandos.
* Presione **ENTRAR** varias veces en la consola de aplicación .NET, lo que provocará que se envíen mensajes.
* La aplicación Java reciben estos mensajes.

#### <a name="output-from-net-application"></a>Resultado de la aplicación.

```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3  
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>Resultado de la aplicación de JMS

```
> java SimpleSenderReceiver 
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Restricciones y características no compatibles

Las siguientes características de la API de Bus de servicio de .NET no se admiten actualmente al usar AMQP:

 * Transacciones
 * Enviar a través de destino de transferencia

Para obtener más información, vea [características no compatibles, las restricciones y diferencias de comportamiento](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences).

## <a name="summary"></a>Resumen

En este artículo mostramos cómo tener acceso a las características de Bus de servicio con mensajería (colas y temas de publicación o suscripción) de .NET con las API de .NET de Bus de servicio y AMQP 1.0.

También puede usar el servicio de Bus AMQP 1.0 de otros idiomas incluidos Java, C, Python y PHP. Componentes creados con estos lenguajes pueden intercambiar mensajes confiable y en fidelidad completa con AMQP 1.0 en Bus de servicio. Para obtener más información, vea la [información general de AMQP de Bus de servicio](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha leído una descripción general de Bus de servicio y AMQP con. NET, vea los siguientes vínculos para obtener más información.

* [Soporte técnico de AMQP 1.0 de Bus de servicio de Azure](service-bus-amqp-overview.md)
* [Cómo utilizar la API de Java Message Service (JMS) con Bus de servicio y AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
* [Cómo usar colas de Bus de servicio](service-bus-dotnet-get-started-with-queues.md)
 
[Portal de Azure]: https://portal.azure.com
