<properties
   pageTitle="Notas actores confiables actor escriba serialización | Microsoft Azure"
   description="Describe los requisitos básicos para definir clases serializables que se pueden usar para definir los Estados de servicio tela fiable actores e interfaces"
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
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Notas de servicio tela confiable actores escriba serialización


Los argumentos de todos los métodos, tipos de resultados de las tareas devueltas por cada método en una interfaz de actor y objetos almacenados en el Administrador de estado de un actor deben ser [Serializable de contrato de datos](https://msdn.microsoft.com/library/ms731923.aspx)... Esto también se aplica a los argumentos de los métodos definidos en [interfaces de eventos de actor](service-fabric-reliable-actors-events.md#actor-events). (Métodos de interfaz de eventos de actor siempre devuelven anulados.)

## <a name="custom-data-types"></a>Tipos de datos personalizados

En este ejemplo, la siguiente interfaz actor define un método que devuelve un tipo de datos personalizado denominado `VoicemailBox`.

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

La interfaz es impelemented por un actor, que utiliza el Administrador de estado para almacenar una `VoicemailBox` objeto:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

En este ejemplo, el `VoicemailBox` objeto es serie cuando:
 - El objeto se transmite entre una instancia de actor y una llamada.
 - El objeto se guarda en el Administrador de estado donde se guarda en el disco y replicar en otros nodos.
 
El marco de Actor confiable utiliza serialización DataContract. Por lo tanto, los objetos de datos personalizados y sus miembros se deben agregar con los atributos **DataContract** y **DataMember** , respectivamente

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```

```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```

## <a name="next-steps"></a>Pasos siguientes
 - [Colección de ciclo de vida y basura de actor](service-fabric-reliable-actors-lifecycle.md)
 - [Avisos y temporizadores de actor](service-fabric-reliable-actors-timers-reminders.md)
 - [Eventos de actor](service-fabric-reliable-actors-events.md)
 - [Volver a entrar actor](service-fabric-reliable-actors-reentrancy.md)
 - [Polimorfismo actor y patrones de diseño orientado a objetos](service-fabric-reliable-actors-polymorphism.md)
 - [Supervisar el rendimiento y el diagnóstico de actor](service-fabric-reliable-actors-diagnostics.md)
