<properties
   pageTitle="Introducción a servicios tela confiable actores | Microsoft Azure"
   description="Este tutorial le guiará por los pasos de crear, depurar e implementar un servicio sencillo basado en actores con servicio tela fiable actores."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="getting-started-with-reliable-actors"></a>Introducción a los actores confiable

> [AZURE.SELECTOR]
- [C# en Windows](service-fabric-reliable-actors-get-started.md)
- [Java en Linux](service-fabric-reliable-actors-get-started-java.md)

En este artículo se explica los conceptos básicos de Azure servicio tela confiable actores y le guiará a través de crear e implementar una aplicación de Actor confiable simple en Java.

## <a name="installation-and-setup"></a>Instalación y configuración
Antes de empezar, asegúrese de que tiene el entorno de desarrollo de servicio tela configurado en su equipo.
Si necesita configurarlo, vaya a [Introducción en Mac](service-fabric-get-started-mac.md) o [Introducción en Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Conceptos básicos
Para empezar con actores confiable, solo necesita comprender algunos conceptos básicos:

 * **Servicio de actor**. Actores confiables están incluidas en servicios confiables que se puede implementar en la infraestructura de tela de servicio. Instancias de actor se activan en una instancia de servicio con nombre.
 
 * **Registro de actor**. Como con servicios confiables, un servicio de Actor confiable debe estar registrado con la ejecución del servicio tela. Además, el tipo de actor debe estar registrado con el tiempo de ejecución de Actor.
 
 * **Interfaz de actor**. La interfaz de actor se utiliza para definir una interfaz pública inflexible de un actor. En la terminología de modelo Actor confiable, la interfaz de actor define los tipos de mensajes que puede comprender el actor y proceso. La interfaz de actor se utiliza por otros participantes y aplicaciones cliente "Enviar" (asincrónica) mensajes al actor. Actores confiables pueden implementar varias interfaces.
 
 * **Clase ActorProxy**. La clase ActorProxy se usa las aplicaciones cliente para invocar los métodos expuestos a través de la interfaz de actor. La clase ActorProxy proporciona dos funciones importantes:
    * Resolución de nombres: es capaz de encontrar el actor del clúster (buscar el nodo del clúster donde se hospeda).
    * El control de errores: puede volver a intentar las llamadas a método y volver a resolver la ubicación de actor tras, por ejemplo, un error que requiere el actor reubicar a otro nodo del clúster.

Las siguientes reglas que pertenecen a interfaces de actor son mencionar:

- Métodos de la interfaz de actor no se sobrecargue.
- Interfaz de actor no puede tener los métodos, ref o parámetros opcionales.
- Interfaces genéricas no son compatibles.

## <a name="create-an-actor-service"></a>Crear un servicio de actor
Empiece por crear una nueva aplicación de servicio tela. El SDK de tela de servicio para Linux incluye una Yeoman generador para proporcionar el scaffolding para una aplicación de servicio tela con un servicio de estado. Iniciar ejecutando el Yeoman siguiente comando:

```bash
$ yo azuresfjava
```

Siga las instrucciones para crear un **Servicio de Actor confiable**. En este tutorial, asigne un nombre a la aplicación "HelloWorldActorApplication" y el actor "HelloWorldActor." Se creará el scaffolding siguiente:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="reliable-actors-basic-building-blocks"></a>Fiable actores pilares básicos

Los conceptos básicos descritos anteriormente se convierten en los pilares básicos de un servicio de Actor confiable.

### <a name="actor-interface"></a>Interfaz de actor

Esta página contiene la definición de interfaz para el actor. Esta interfaz define el contrato de actor que se comparte mediante la implementación de actor y los clientes que llama el actor, por lo que normalmente tiene sentido definir en un lugar diferente de la implementación de actor y puede ser compartido por varios otros servicios o aplicaciones de cliente.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Servicio de actor 
Esta página contiene la implementación de actor y el código de registro de actor. La clase de actor implementa la interfaz de actor. Esto es donde el actor hace su trabajo.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Registro de actor

El servicio de actor debe estar registrado con un tipo de servicio en el tiempo de ejecución de tela de servicio. En orden al servicio de Actor ejecutar las instancias de actor, también debe registrar su tipo de actor con el servicio de Actor. La `ActorRuntime` método de registro realiza este trabajo por actores.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {
    
    public static void main(String[] args) throws Exception {
        
        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);
            
        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

### <a name="test-client"></a>Cliente de prueba

Se trata de una aplicación de cliente de prueba simple puede ejecutar desde la aplicación de servicio tela para probar su servicio de actor por separado. Este es un ejemplo de dónde se puede usar el ActorProxy para activar y comunicar con instancias de actor. No se desarrollan con el servicio.

### <a name="the-application"></a>La aplicación 

Por último, los paquetes de la aplicación del servicio de actor y otros servicios que puede agregar en el futuro juntos para su implementación. Contiene los marcadores de *ApplicationManifest.xml* y lugar para el paquete de servicio de actor.

## <a name="run-the-application"></a>Ejecute la aplicación

La Yeoman scaffolding incluye una secuencia de comandos gradle para generar la aplicación y bash secuencias de comandos para implementar y anule-implementar la aplicación. Para ejecutar la aplicación, cree primero la aplicación con gradle:

```bash
$ gradle
```

Se producirá un paquete de aplicación de servicio tela que se puede implementar con servicio tela Azure CLI. La secuencia de comandos install.sh contiene los comandos de Azure CLI necesarios para implementar el paquete de aplicación. Simplemente ejecute el script install.sh para implementar:

```bask
$ ./install.sh
```
