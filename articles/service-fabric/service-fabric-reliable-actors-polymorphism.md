<properties
   pageTitle="Polimorfismo en el marco de trabajo confiable actores | Microsoft Azure"
   description="Crear jerarquías de interfaces de .NET y tipos de framework actores fiable reutilizar funcionalidad y definiciones de API."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/07/2016"
   ms.author="seanmck"/>

# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polimorfismo en el marco de trabajo de actores confiable

El marco de trabajo confiable actores le permite crear actores con muchas de las mismas técnicas que se debe usar en el diseño orientado a objetos. Una de las técnicas es polimorfismo, que permite a los tipos y las interfaces para heredar de más generalizado padres. Herencia en el marco de trabajo confiable actores generalmente sigue el modelo de .NET con algunas restricciones adicionales.

## <a name="interfaces"></a>Interfaces

El marco de trabajo confiable actores, debe definir al menos una interfaz que se implementa por su tipo de actor. Esta interfaz se usa para generar una clase proxy que puede ser usada por los clientes para comunicarse con los participantes. Interfaces pueden heredar de otras interfaces como cada interfaz que se implementa mediante un tipo de actor y todos sus padres finalmente derivan IActor. IActor es la interfaz base definida plataforma agentes. Por lo tanto, en el ejemplo de polimorfismo clásica con formas se verá algo similar a esta:

![Jerarquía de la interfaz de los participantes de forma][shapes-interface-hierarchy]


## <a name="types"></a>Tipos de

También puede crear una jerarquía de tipos de actor, que se deriva de la clase de Actor base proporcionada por la plataforma. En el caso de formas, es posible que tenga una base de `Shape` tipo:

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```

Subtipos de `Shape` puede reemplazar los métodos de la base.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```

Nota la `ActorService` atributo en el tipo de actor. Este atributo indica el marco de trabajo de Actor fiable que debe crear automáticamente un servicio de hospedaje actores de este tipo. En algunos casos, puede crear un tipo base que sirve únicamente para compartir la funcionalidad con subtipos y nunca se utilizará para crear una instancia actores concretas. En estos casos, debe usar la `abstract` palabra clave para indicar que nunca se creará un actor en función de ese tipo.


## <a name="next-steps"></a>Pasos siguientes

- Vea [cómo el marco de trabajo confiable actores aprovecha la plataforma de tela de servicio](service-fabric-reliable-actors-platform.md) para proporcionar fiabilidad, escalabilidad y estado coherente.
- Obtenga información sobre el [ciclo de vida de actor](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
