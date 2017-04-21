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
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="getting-started-with-reliable-actors"></a>Introducción a los actores confiable

> [AZURE.SELECTOR]
- [C# en Windows](service-fabric-reliable-actors-get-started.md)
- [Java en Linux](service-fabric-reliable-actors-get-started-java.md)

En este artículo se explica los conceptos básicos de Azure servicio tela confiable actores y le guiará a través de la creación, depuración e implementación de una aplicación de Actor confiable simple en Visual Studio.

## <a name="installation-and-setup"></a>Instalación y configuración
Antes de empezar, asegúrese de que tiene el entorno de desarrollo de servicio tela configurado en su equipo.
Si necesita configurarlo, vea instrucciones detalladas sobre [cómo configurar el entorno de desarrollo](service-fabric-get-started.md).

## <a name="basic-concepts"></a>Conceptos básicos
Para empezar con actores confiable, solo necesita comprender algunos conceptos básicos:

 * **Servicio de actor**. Actores fiables están incluidas en servicios confiables que se puede implementar en la infraestructura de tela de servicio. Instancias de actor se activan en una instancia de servicio con nombre.
 
 * **Registro de actor**. Como con servicios confiables, un servicio de Actor confiable debe estar registrado con la ejecución del servicio tela. Además, el tipo de actor debe estar registrado con el tiempo de ejecución de Actor.
 
 * **Interfaz de actor**. La interfaz de actor se utiliza para definir una interfaz pública inflexible de un actor. En la terminología de modelo Actor confiable, la interfaz de actor define los tipos de mensajes que puede comprender el actor y proceso. La interfaz de actor se utiliza por otros participantes y aplicaciones cliente "Enviar" (asincrónica) mensajes al actor. Actores confiables pueden implementar varias interfaces.
 
 * **Clase ActorProxy**. La clase ActorProxy se usa las aplicaciones cliente para invocar los métodos expuestos a través de la interfaz de actor. La clase ActorProxy proporciona dos funciones importantes:
    * Resolución de nombres: es capaz de encontrar el actor del clúster (buscar el nodo del clúster donde se hospeda).
    * El control de errores: puede volver a intentar las llamadas a método y volver a resolver la ubicación de actor tras, por ejemplo, un error que requiere el actor reubicar a otro nodo del clúster.

Las siguientes reglas que pertenecen a interfaces de actor son mencionar:

- Métodos de la interfaz de actor no se sobrecargue.
- Interfaz de actor no puede tener los métodos, ref o parámetros opcionales.
- Interfaces genéricas no son compatibles.

## <a name="create-a-new-project-in-visual-studio"></a>Crear un nuevo proyecto de Visual Studio
Después de haber instalado las herramientas de servicio tela para Visual Studio, puede crear nuevos tipos de proyecto. Los nuevos tipos de proyecto están en la categoría de **nube** del cuadro de diálogo **Nuevo proyecto** .


![Herramientas de servicio tela para Visual Studio - nuevo proyecto][1]

En el cuadro de diálogo siguiente, puede elegir el tipo de proyecto que desea crear.

![Plantillas de proyectos de tela de servicio][5]

Para el proyecto Hola a todos, vamos a usar el servicio de servicio tela confiable actores.

Después de haber creado la solución, verá la siguiente estructura:

![Estructura de proyecto de tela de servicio][2]

## <a name="reliable-actors-basic-building-blocks"></a>Confiable actores pilares básicos

Una solución confiable actores típica se compone de tres proyectos:

* **El proyecto de la aplicación (MyActorApplication)**. Éste es el proyecto que empaqueta todos los servicios juntos para su implementación. Contiene las secuencias de comandos *ApplicationManifest.xml* y PowerShell para administrar la aplicación.

* **El proyecto de la interfaz (MyActor.Interfaces)**. Éste es el proyecto que contiene la definición de interfaz para el actor. En el proyecto MyActor.Interfaces, puede definir las interfaces que se usan los participantes en la solución. Las interfaces de actor pueden definirse en cualquier proyecto con cualquier nombre, aunque la interfaz define el contrato de actor que se comparte con la implementación de actor y los clientes que llama el actor, así que normalmente tiene sentido definir en un conjunto diferente de la implementación de actor y que puede compartir varios otros proyectos.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **El proyecto de servicio de actor (MyActor)**. Éste es el proyecto que se usa para definir el servicio de tela de servicio que se va a hospedar el actor. Contiene la implementación de actor. Una implementación de actor es una clase que se deriva del tipo base `Actor` e implementa las interfaces que se definen en el proyecto MyActor.Interfaces. Una clase de actor también debe implementar un constructor que acepta una `ActorService` instancia y un `ActorId` y los pasa a la base de `Actor` clase. Esto permite la inserción de dependencia constructor de las dependencias de plataforma.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

El servicio de actor debe estar registrado con un tipo de servicio en el tiempo de ejecución de tela de servicio. En orden al servicio de Actor ejecutar las instancias de actor, también debe registrar su tipo de actor con el servicio de Actor. La `ActorRuntime` método de registro realiza este trabajo por actores.

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Si se inicia desde un nuevo proyecto de Visual Studio y tiene una única definición de actor, el registro se incluye en el código de Visual Studio genera de forma predeterminada. Si define otros actores en el servicio, debe agregar el registro de actor mediante:

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [AZURE.TIP] El tiempo de ejecución de servicio tela actores emite algunos [contadores de rendimiento y eventos relacionados con los métodos de actor](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Son útiles de diagnósticos y la supervisión de rendimiento.


## <a name="debugging"></a>Depuración

Las herramientas de servicio tela para Visual Studio admiten la depuración en el equipo local. Puede iniciar una sesión de depuración presionando la tecla F5. (Si es necesario), Visual Studio crea paquetes. También se implementa la aplicación en el clúster de servicio tela local y se asocia al depurador.

Durante el proceso de implementación, puede ver el progreso en la ventana de **resultados** .

![Ventana de resultados de depuración de tela de servicio][3]


## <a name="next-steps"></a>Pasos siguientes
 - [¿Cómo actores confiable usa la plataforma de tela de servicio](service-fabric-reliable-actors-platform.md)
 - [Administración de estado de actor](service-fabric-reliable-actors-state-management.md)
 - [Colección de ciclo de vida y basura de actor](service-fabric-reliable-actors-lifecycle.md)
 - [Documentación de referencia de actor API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Código de ejemplo](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
