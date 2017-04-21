<properties 
    pageTitle="Cómo usar AMQP 1.0 con la API de Bus de servicio de Java | Microsoft Azure" 
    description="Cómo usar Java Message Service (JMS) con Bus de servicio de Azure y avanzadas mensaje Queue Protodol (AMQP) 1.0." 
    services="service-bus" 
    documentationCenter="java" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Cómo usar la API de Java Message Service (JMS) con Bus de servicio y AMQP 1.0

La avanzada mensaje Queue protocolo (AMQP) 1.0 es un protocolo de mensajería eficaz, confiable nivel de alambre que puede usar para crear aplicaciones de mensajería sólidas entre plataformas.

Soporte para AMQP 1.0 en Bus de servicio significa que puede usar la cola y de publicación o suscripción con características de mensajería de un intervalo de plataformas con un protocolo binario eficaz. Además, puede crear aplicaciones conformadas de los componentes creados mediante una combinación de idiomas, marcos y sistemas operativos.

En este artículo se explica cómo usar Bus de servicios de mensajería características (colas y temas de publicación o suscripción) desde aplicaciones de Java utilizando la populares Java Message Service (JMS) API estándar. Hay un [artículo complementario](service-bus-dotnet-advanced-message-queuing.md) que explica cómo hacer lo mismo con la API de .NET de Bus de servicio. Puede usar a estas dos guías juntos para obtener información sobre la mensajería entre plataformas con AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Introducción a Bus de servicio

Esta guía se supone que ya tiene un espacio de nombres de Bus de servicio que contiene una cola denominada **Cola1**. Si no lo hace, a continuación, puede [crear la cola y el espacio de nombres](service-bus-create-namespace-portal.md) con el [portal de Azure](https://portal.azure.com). Para obtener más información sobre cómo crear espacios de nombres de Bus de servicio y colas, vea [cómo usar colas de Bus de servicio](service-bus-dotnet-get-started-with-queues.md).

> [AZURE.NOTE] Temas y colas divididas también admiten AMQP. Para obtener más información, vea [con particiones mensajería entidades](service-bus-partitioning.md) y [compatibilidad con AMQP 1.0 Bus de servicio particiones colas y temas](service-bus-partitioned-queues-and-topics-amqp-overview.md).

## <a name="downloading-the-amqp-10-jms-client-library"></a>Descargar la biblioteca de cliente de AMQP 1.0 JMS

Para obtener información acerca de dónde descargar la versión más reciente de la biblioteca de cliente Apache Qpid JMS AMQP 1.0, visite [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Debe agregar los siguientes archivos JAR cuatro desde el archivo de distribución Apache Qpid JMS AMQP 1.0 a la ruta de clases de Java para crear y ejecutar aplicaciones JMS con Bus de servicio:

- jms geronimo\_1.1\_1.0.jar especificación
- qpid-amqp-1-0-Client-[Version].jar
- qpid-amqp-1-0-Client-JMS-[Version].jar
- qpid-amqp-1-0-Common-[Version].jar

## <a name="coding-java-applications"></a>Codificación de aplicaciones de Java

### <a name="java-naming-and-directory-interface-jndi"></a>Java Naming y Directory interfaz JNDI)

JMS utiliza Java Naming and Directory interfaz JNDI () para crear una separación entre nombres lógicos y físicos. Dos tipos de objetos JMS se resuelven utilizando JNDI: ConnectionFactory y destino. JNDI utiliza un modelo de proveedor en el que puede conectar los diferentes servicios de directorio para controlar los derechos de resolución de nombre. Dar formato a la Apache Qpid JMS AMQP versión 1.0 biblioteca se suministra con una simple propiedades proveedor JNDI basados en archivos que está configurado con un archivo de propiedades de las siguientes acciones:

```
# servicebus.properties - sample JNDI configuration
    
# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    
# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="configure-the-connectionfactory"></a>Configurar la ConnectionFactory

La entrada que se utiliza para definir un **ConnectionFactory** en el proveedor de JNDI Qpid propiedades tiene el siguiente formato:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Donde **[jndi_name]** y **[ConnectionURL]** tienen los siguientes significados:

- **[jndi_name]**: el nombre de la ConnectionFactory lógico. Este es el nombre que se va a resolver en la aplicación de Java con el método IntialContext.lookup() JNDI.
- **[ConnectionURL]**: una URL que proporciona la biblioteca JMS con la información necesaria al intermediario AMQP.

El formato de la **ConnectionURL** es la siguiente:

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Donde **[nombres]**, **[SASPolicyName]** y **[SASPolicyKey]** tienen los siguientes significados:

- **[nombres]**: espacio de nombres del Bus de servicio.
- **[SASPolicyName]**: nombre de la directiva de firma de acceso compartido de la cola.
- **[SASPolicyKey]**: clave de la directiva de firma de acceso compartido de la cola.

> [AZURE.NOTE] Debe codificar a la dirección URL de la contraseña manualmente. Una codificación URL útiles está disponible en [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

#### <a name="configure-destinations"></a>Configurar destinos

La entrada que se utiliza para definir un destino en el proveedor de JNDI Qpid propiedades tiene el siguiente formato:

```
queue.[jndi_name] = [physical_name]
```

o

```
topic.[jndi_name] = [physical_name]
```

Donde **[jndi\_nombre]** y **[físico\_nombre]** tiene los siguientes significados:

- **[jndi_name]**: el nombre lógico de destino. Este es el nombre que se va a resolver en la aplicación de Java con el método IntialContext.lookup() JNDI.
- **[el argumento physical_name]**: el nombre de la entidad de Bus de servicio para que la aplicación envía o recibe mensajes.

> [AZURE.NOTE] Al recibir una suscripción de tema de Bus de servicio, el nombre físico especificado en JNDI debe ser el nombre del tema. Cuando se crea la suscripción resistente en el código de la aplicación de JMS, se proporciona el nombre de suscripción. La [Guía del desarrollador de servicio Bus AMQP 1.0](service-bus-amqp-dotnet.md) proporciona información detallada sobre cómo trabajar con suscripciones de tema de Bus de servicio de JMS.

### <a name="write-the-jms-application"></a>Escribir la aplicación de JMS

No existen opciones necesarias con JMS Bus de servicio o API especial. Sin embargo, hay algunas restricciones que se explicará más adelante. Al igual que con cualquier aplicación JMS, lo primero que requiere la configuración del entorno JNDI, para que pueda resolver un **ConnectionFactory** y destinos.

#### <a name="configure-the-jndi-initialcontext"></a>Configurar la InitialContext JNDI

El entorno de JNDI está configurado, pase el constructor de la clase javax.naming.InitialContext en una tabla hash de la información de configuración. Los dos necesarios elementos en la tabla hash son el nombre de clase de la fábrica de contexto inicial y la dirección URL del proveedor. El código siguiente muestra cómo configurar el entorno de JNDI para usar el archivo de propiedades de Qpid según JNDI proveedor con un archivo de propiedades denominado **servicebus.properties**.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Una aplicación de JMS sencilla mediante una cola de Bus de servicio

El programa de ejemplo siguientes JMS TextMessages envía a una cola de Bus de servicio con el nombre lógico JNDI de cola y recibe los mensajes de nuevo.

```
// SimpleSenderReceiver.java
    
import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;
    
public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();
    
    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);
    
        // Look up ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");
    
        // Create Connection
        connection = cf.createConnection();
    
        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);
    
        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }
    
    public static void main(String[] args) {
        try {
    
            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }
    
            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));
    
            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    
    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }
    
    public void close() throws JMSException {
        connection.close();
    }
    
    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}   
```

### <a name="run-the-application"></a>Ejecute la aplicación

Ejecutar la aplicación genera el resultado del formulario:

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318
    
Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483
    
Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Mensajería entre JMS y .NET entre plataformas

Esta guía mostramos cómo enviar y recibir mensajes de Bus de servicio mediante JMS. Sin embargo, una de las principales ventajas de AMQP 1.0 es que permite a las aplicaciones a partir de componentes escritos en idiomas diferentes, con mensajes intercambiados confiable y completa fidelidad.

Uso de la aplicación de JMS de ejemplo descrito anteriormente y una aplicación de .NET similar copiado de una guía complementaria, [cómo usar AMQP 1.0 con la API de .NET .NET Bus de servicio](service-bus-dotnet-advanced-message-queuing.md), puede intercambiar mensajes entre .NET y Java. 

Para obtener más información acerca de los detalles de mensajería usando Bus de servicio y AMQP 1.0 entre plataformas, consulte la [Guía del desarrollador de servicio Bus AMQP 1.0](service-bus-amqp-dotnet.md).

### <a name="jms-to-net"></a>JMS para .NET

Para demostrar JMS .NET mensajería:

* Inicie la aplicación de ejemplo de .NET sin ningún argumento de línea de comandos.
* Inicie la aplicación de ejemplo de Java con el argumento de línea de comandos "sendonly". En este modo, la aplicación no recibirá mensajes de la cola, solo le enviará.
* Presione **ENTRAR** varias veces en la consola de aplicación Java, lo que provocará que se envíen mensajes.
* La aplicación .NET reciben estos mensajes.

#### <a name="output-from-jms-application"></a>Resultado de la aplicación de JMS

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>Resultado de la aplicación.

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>.NET JMS

Para demostrar .NET JMS mensajería:

* Inicie la aplicación de ejemplo de .NET con el argumento de línea de comandos "sendonly". En este modo, la aplicación no recibirá mensajes de la cola, solo le enviará.
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

Las siguientes restricciones existen mediante JMS sobre AMQP 1.0 con Bus de servicio, es decir:

* Se permite un único **MessageProducer** o **MessageConsumer** por **sesión**. Si necesita crear varias **MessageProducers** o **MessageConsumers** en una aplicación, cree una **sesión** de dedicada para cada uno de ellos.
* Suscripciones de tema volátiles no se admiten actualmente.
* **MessageSelectors** no se admiten actualmente.
* Destinos temporales, es decir, **TemporaryQueue**, **TemporaryTopic** no son actualmente compatibles, junto con el **QueueRequestor** y **TopicRequestor** API que usan.
* Sesiones de transacciones y las transacciones distribuidas no son compatibles.

## <a name="summary"></a>Resumen

Esta guía de procedimientos mostramos cómo usar Bus de servicio con características de mensajería (colas y temas de publicación o suscripción) de Java con la API de JMS populares y AMQP 1.0.

También puede usar el servicio de Bus AMQP 1.0 de otros idiomas, incluidos .NET, C, Python y PHP. Componentes generados con estas diferentes idiomas pueden intercambiar mensajes confiable y en fidelidad completa con la versión 1.0 AMQP se admite en Bus de servicio. Para obtener más información, consulte la [Guía del desarrollador de servicio Bus AMQP 1.0](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Pasos siguientes

* [Soporte técnico de AMQP 1.0 de Bus de servicio de Azure](service-bus-amqp-overview.md)
* [Cómo usar AMQP 1.0 con la API de .NET de Bus de servicio](service-bus-dotnet-advanced-message-queuing.md)
* [Bus AMQP 1.0 de servicio Guía del desarrollador](service-bus-amqp-dotnet.md)
* [Cómo usar colas de Bus de servicio](service-bus-dotnet-get-started-with-queues.md)
* [Centro para desarrolladores de Java](/develop/java/).



 
