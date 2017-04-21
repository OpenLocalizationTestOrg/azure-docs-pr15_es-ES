<properties
   pageTitle="Introducción a servicios confiables | Microsoft Azure"
   description="Introducción a la creación de una aplicación de Microsoft Azure servicio tela con y sin estado de los servicios."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="vturecek"/>

# <a name="get-started-with-reliable-services"></a>Introducción a servicios confiables

> [AZURE.SELECTOR]
- [C# en Windows](service-fabric-reliable-services-quick-start.md)
- [Java en Linux](service-fabric-reliable-services-quick-start-java.md)

En este artículo se explica los conceptos básicos de Azure servicios tela confiable y le guiará a través de crear e implementar una aplicación de servicio confiable simple escrita en Java.

## <a name="installation-and-setup"></a>Instalación y configuración
Antes de empezar, asegúrese de que tiene el entorno de desarrollo de servicio tela configurado en su equipo.
Si necesita configurarlo, vaya a [Introducción en Mac](service-fabric-get-started-mac.md) o [Introducción en Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Conceptos básicos
Para empezar a usar servicios confiables, solo necesita comprender algunos conceptos básicos:

 - **Tipo de servicio**: se trata de la implementación del servicio. Se ha definido la clase que escriba que extiende `StatelessService` y otro código ni dependencias utilizadas en él, junto con un nombre y un número de versión.

 - **Nombre de servicio**: para ejecutar el servicio, crear instancias con nombre del tipo de servicio, tanto como crear instancias de objeto de un tipo de clase. Instancias del servicio son en realidad instancias del objeto de la clase de servicio que se escribe. 

 - **Host de servicio**: las instancias de servicio que desee crear necesitan para ejecutar dentro de un host. El host del servicio es simplemente un proceso donde se pueden ejecutar instancias del servicio.

 - **Registro de servicio**: todo lo que reúne el registro. El tipo de servicio debe estar registrado con el tiempo de ejecución en un host de servicio para permitir tela de servicio crear instancias de la misma tela de servicio para ejecutar.  

## <a name="create-a-stateless-service"></a>Crear un servicio de estado

Empiece por crear una nueva aplicación de servicio tela. El SDK de tela de servicio para Linux incluye una Yeoman generador para proporcionar el scaffolding para una aplicación de servicio tela con un servicio de estado. Iniciar ejecutando el Yeoman siguiente comando:

```bash
$ yo azuresfjava
```

Siga las instrucciones para crear un **Servicio de estado confiable**. En este tutorial, asigne un nombre a la aplicación "HelloWorldApplication" y el servicio de "Hola a todos". El resultado incluirá directorios para la `HelloWorldApplication` y `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>Implementar el servicio

Abra **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Esta clase define el tipo de servicio y puede ejecutar cualquier código. La API del servicio proporciona dos puntos de entrada para el código:

 - Un método de punto de entrada abiertas, denominado `runAsync()`, donde puede comenzar la ejecución de las cargas de trabajo, incluidas las cargas de trabajo de cálculo de ejecución larga.

```java
@Override
protected CompletableFuture<?> runAsync() {
    ...
}
```

 - Punto de entrada de comunicación donde puede conectar en la pila de comunicaciones de elección. Esto es donde puede empezar a recibir las solicitudes de los usuarios y otros servicios.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

En este tutorial, nos centraremos en la `runAsync()` método de punto de entrada. Esto es donde puede iniciar inmediatamente ejecutando el código.

### <a name="runasync"></a>RunAsync

La plataforma llama a este método cuando una instancia de un servicio está colocada y listos para ejecutarse. El ciclo de apertura y cierre de una instancia de servicio puede producirse muchas veces a lo largo del servicio como un todo. Esto puede ocurrir por varios motivos, incluidos:

- El sistema mueve las servicio instancias de recurso equilibrio.
- Se producen errores en el código.
- La aplicación o el sistema se actualiza.
- El hardware subyacente experiencias de una interrupción.

Esta orquestación administrada por tela de servicio para mantener su servicio altamente disponible y equilibradas correctamente.

#### <a name="cancellation"></a>Cancelación

Es fundamental que el código en `runAsync()` puede detener la ejecución cuando una notificación de servicio tela. La `CompletableFuture` devuelto desde `runAsync()` se cancela cuando el servicio tela requiere su servicio para detener la ejecución. En el ejemplo siguiente se muestra cómo controlar un evento de cancelación: 

```java
    @Override
    protected CompletableFuture<?> runAsync() {

        CompletableFuture<?> completableFuture = new CompletableFuture<>();
        ExecutorService service = Executors.newFixedThreadPool(1);
        
        Future<?> userTask = service.submit(() -> {
            while (!Thread.currentThread().isInterrupted()) {
                try
                {
                   logger.log(Level.INFO, this.context().serviceName().toString());
                   Thread.sleep(1000);
                }
                catch (InterruptedException ex)
                {
                    logger.log(Level.INFO, this.context().serviceName().toString() + " interrupted. Exiting");
                    return;
                }
            }
         });
 
        completableFuture.handle((r, ex) -> {
            if (ex instanceof CancellationException) {
                userTask.cancel(true);
                service.shutdown();
            }
            return null;
        });
 
        return completableFuture;
   }
``` 

### <a name="service-registration"></a>Registro de servicio

Tipos de servicio deben estar registrados con la ejecución del servicio tela. El tipo de servicio se define en la `ServiceManifest.xml` y la clase de servicio implementa `StatelessService`. Registro de servicio se realiza en el punto de entrada principal del proceso. En este ejemplo, es el punto de entrada principal del proceso `HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    } 
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration: {0}", ex.toString());
        throw ex;
    }
}
```

## <a name="run-the-application"></a>Ejecute la aplicación

La Yeoman scaffolding incluye una secuencia de comandos gradle para generar la aplicación y bash secuencias de comandos para implementar y anule-implementar la aplicación. Para ejecutar la aplicación, cree primero la aplicación con gradle:

```bash
$ gradle
```

Se producirá un paquete de aplicación de servicio tela que se puede implementar con servicio tela Azure CLI. La secuencia de comandos install.sh contiene los comandos de Azure CLI necesarios para implementar el paquete de aplicación. Simplemente ejecute el script install.sh para implementar:

```bask
$ ./install.sh
```
