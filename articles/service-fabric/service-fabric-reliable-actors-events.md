<properties
   pageTitle="Eventos de actores fiables | Microsoft Azure"
   description="Introducción a los eventos de servicio tela fiable actores."
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
   ms.date="08/30/2016"
   ms.author="amanbha"/>


# <a name="actor-events"></a>Eventos de actor
Eventos de actor proporcionan una manera de enviar notificaciones de mejor esfuerzo de actor a los clientes. Eventos de actor están diseñados para la comunicación de cliente de actor y no se deben utilizar para la comunicación de actor para el actor.

Fragmentos de código siguientes muestran cómo utilizar los eventos de actor en la aplicación.

Defina una interfaz que describe los eventos publicados por el actor. Esta interfaz debe derivarse de la `IActorEvents` interfaz. Los argumentos de los métodos deben ser [serializable de contratos de datos](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Los métodos deben devolver anulados, como evento notificaciones son una forma y mejor esfuerzo.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

Declare los eventos publicados por el actor en la interfaz de actor.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```

En el cliente, implementar el controlador de eventos.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

En el cliente, cree a un proxy al actor que publica el evento y suscribirse a sus eventos.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

En el caso de migraciones tras error, el actor puede conmutar a un nodo o un proceso diferente. El proxy de actor administra las suscripciones activas y automáticamente suscribe volver a ellos. Puede controlar el intervalo de nueva suscripción a través de la `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API. Para cancelar la suscripción, utilice el `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API.

En el actor, simplemente publicar los eventos que se producen. Si hay suscriptores al evento, el tiempo de ejecución de actores enviará la notificación.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```

## <a name="next-steps"></a>Pasos siguientes
 - [Volver a entrar actor](service-fabric-reliable-actors-reentrancy.md)
 - [Supervisar el rendimiento y el diagnóstico de actor](service-fabric-reliable-actors-diagnostics.md)
 - [Documentación de referencia de actor API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Código de ejemplo](https://github.com/Azure/servicefabric-samples)
