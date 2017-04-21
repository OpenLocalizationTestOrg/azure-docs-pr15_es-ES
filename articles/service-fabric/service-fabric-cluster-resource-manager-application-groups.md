<properties
   pageTitle="Administrador de recursos de clúster de tela servicio - grupos de aplicación | Microsoft Azure"
   description="Información general sobre la funcionalidad de grupo de aplicaciones en el Administrador de recursos de clúster tela de servicio"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# <a name="introduction-to-application-groups"></a>Introducción a los grupos de aplicación
Administrador de recursos de clúster del servicio de tela normalmente administra los recursos de clúster al distribuir la carga (representada mediante métricas) uniformemente en todo el clúster. Servicio tela también administra la capacidad de los nodos en el clúster y el clúster completo mediante el concepto de capacidad. Esto resulta muy útil para una gran cantidad de diferentes tipos de cargas de trabajo, pero los patrones que utilizan de distintas instancias de aplicación de servicio tela a veces reunir requisitos adicionales. Algunos requisitos adicionales suelen:

- Capacidad para reservar capacidad para los servicios de la instancia de aplicación en un número de nodos
- Capacidad para limitar el número total de nodos de un conjunto determinado de servicios dentro de una aplicación se puede ejecutar
- Definir las capacidades de la instancia de la aplicación para limitar el número o el consumo de recursos total de los servicios que hay dentro de ella

Para cumplir estos requisitos, hemos desarrollado compatibilidad para lo que llamamos grupos de aplicación.

## <a name="managing-application-capacity"></a>Administrar la capacidad de aplicación
Capacidad de aplicación puede usarse para limitar el número de nodos abarcadas una aplicación, así como la carga de métrica total de las instancias de las aplicaciones en los nodos individuales. También puede usarse para reservar recursos en el clúster de la aplicación.

Capacidad de la aplicación se puede configurar para las nuevas aplicaciones cuando se crean; También puede actualizarse para las aplicaciones existentes que se crearon sin especificar la capacidad de aplicación.

### <a name="limiting-the-maximum-number-of-nodes"></a>Limitar el número máximo de nodos
El caso de uso más sencillo para la capacidad de aplicación es cuando necesita una instancia de la aplicación se limite a un determinado número máximo de nodos. Si no se especifica ningún capacidad de aplicación, el Administrador de recursos de clúster de servicio tela creará instancias réplicas según las reglas normales (equilibrio o desfragmentación), que normalmente significa que sus servicios se extienden a través de todos los nodos disponibles en el clúster, o si desfragmentación está activada algunos arbitrario pero menor número de nodos.

La imagen siguiente muestra la ubicación de una instancia de aplicación posible sin el número máximo de nodos definidos y, a continuación, la misma aplicación con un número máximo de nodos establecida. Tenga en cuenta que no hay ninguna garantía sobre qué réplicas o instancias de las cuales servicios obtener pondrán juntas.

![Definir el número máximo de nodos de la instancia de aplicación][Image1]

En el ejemplo de la izquierda, la aplicación no tiene capacidad de aplicación establecer y tiene tres servicios. CRM ha realizado una decisión lógica para distribuir todas las réplicas en seis nodos disponibles para alcanzar el mejor equilibrio en el clúster. En el ejemplo de la derecha, vemos la misma aplicación que se limita en tres nodos y donde servicio tela CRM ha logrado el mejor equilibrio de las réplicas de los servicios de aplicación.

El parámetro que controla este comportamiento se denomina MaximumNodes. Este parámetro puede establecerse durante la creación de la aplicación, o actualización de una instancia de aplicación que ya se está ejecutando, en el caso servicio tela CRM restringe las réplicas de servicios de aplicación para el número máximo definido de nodos.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## <a name="application-metrics-load-and-capacity"></a>Capacidad, carga y medidas de aplicación
Grupos de aplicación permiten definir métrica asociada a una instancia de aplicación determinada, así como la capacidad de la aplicación en relación con esas métricas. Por ejemplo, se podría definir como muchos servicios como desee se creara en

Para cada métrica, hay 2 valores que se pueden establecer para describir la capacidad para esa instancia de aplicación:

-   Total de la aplicación capacidad: representa la capacidad total de la aplicación para una métrica determinada. Servicio tela CRM intentará limitar la suma de las cargas métricas de servicios de la aplicación en el valor especificado; Además, si los servicios de la aplicación ya consuman carga hasta este límite, Administrador de recursos de clúster de tela de servicio no permite la creación de nuevos servicios o particiones que haría que la carga total volver a este límite.
-   Capacidad máxima de nodo: especifica la carga máxima total para réplicas de servicios de las aplicaciones en un único nodo. Si carga total en el nodo trata sobre esta capacidad, servicio tela CRM intentará mover réplicas a otros nodos para que se respeta la restricción de capacidad.

## <a name="reserving-capacity"></a>Reservar capacidad
Otro uso común de grupos de aplicación es asegurarse de que los recursos en el clúster están reservados para una instancia de la aplicación, incluso si la instancia de aplicación todavía no dispone de los servicios dentro de ella, o incluso si no se consuman todavía los recursos. Echemos un vistazo a cómo funcionarían.  

### <a name="specifying-a-minimum-number-of-nodes-and-resource-reservation"></a>Especificar un número mínimo de nodos y reserva de recursos
Reservar recursos para una instancia de aplicación requiere especificar algunos parámetros adicionales: *MinimumNodes* y *NodeReservationCapacity*

- MinimumNodes - igual que especifica un número máximo de destino de nodos que se pueden ejecutar los servicios dentro de una aplicación, también puede especificar el número mínimo de nodos que debe ejecutar una aplicación. Esta configuración eficaz define el número de nodos que se deben reservar los recursos en mínimo, garantizar la capacidad en el clúster como parte de la creación de instancia de la aplicación.
- NodeReservationCapacity - la NodeReservationCapacity puede definirse para cada métrica dentro de la aplicación. Define la cantidad de carga métrica reservado para la aplicación en cualquier nodo donde se colocan cualquiera de las réplicas o instancias de los servicios dentro de ella.

Echemos un vistazo a un ejemplo de reserva de capacidad:

![Instancias de aplicación definir capacidad reservada][Image2]

En el ejemplo de la izquierda, aplicaciones no tiene la capacidad de cualquier aplicación definido. Administrador de recursos de clúster de servicio tela se saldo instancias junto con las de otros servicios (fuera de la aplicación) y réplicas de servicios secundario de la aplicación para garantizar que el saldo en el clúster.

En el ejemplo de la derecha, supongamos que la aplicación se creó con una MinimumNodes establecida en 2, MaximumNodes establecido en 3 y una aplicación métrica definida con una reserva de 20, capacidad máxima en un nodo de 50 y la capacidad de aplicación total de 100, tela de servicio se reservar capacidad en dos nodos para la aplicación de color azul y no permite otras réplicas en el clúster para consumir esa capacidad. La capacidad de aplicación reservadas se considera consumido y contado contra la capacidad de clúster restante.

Cuando se crea una aplicación con reserva, el Administrador de recursos de clúster se reservar capacidad igual a MinimumNodes * NodeReservationCapacity en el clúster, pero no reservar capacidad en nodos específicos hasta que se crean y se coloca el réplicas de servicios de la aplicación. Esto permite flexibilidad, ya que se eligen los nodos para las nuevas réplicas solo cuando se crean. Capacidad está reservada en un nodo específico cuando se coloca al menos una réplica en él.

## <a name="obtaining-the-application-load-information"></a>Obtener la información de carga de la aplicación
Para cada aplicación que tiene la capacidad de aplicación definidas pueden obtener la información sobre la carga agregada notificada réplicas de sus servicios. Servicio tela proporciona consultas de PowerShell y API administrada para ello.

Por ejemplo, se puede recuperar carga mediante el siguiente cmdlet de PowerShell:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

El resultado de esta consulta contendrá la información básica sobre la capacidad de las aplicaciones que se especificó para la aplicación, como los nodos de valor en mínima y máxima. También habrá información sobre el número de nodos que la aplicación esté usando. Por lo tanto, para cada métrica de carga habrá información sobre:
- : Métrica nombre de la métrica.
-   Capacidad de reserva: Capacidad de clúster que se ha reservado en el clúster de esta aplicación.
-   Carga de la aplicación: Carga Total de réplicas de secundario de la aplicación.
-   Capacidad de aplicación: Máximo permitido de valor de carga de la aplicación.

## <a name="removing-application-capacity"></a>Quitar la capacidad de aplicación
Una vez que se establecen los parámetros de capacidad de aplicación para una aplicación, se pueden quitar usar cmdlets de PowerShell o la API de aplicación de actualización. Por ejemplo:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Este comando quitará todos los parámetros de capacidad de aplicación de la aplicación y se inicie el Administrador de recursos de servicio tela clúster tratar esta aplicación como cualquier otra aplicación en el clúster que no tiene estos parámetros definidos. El efecto del comando es inmediato y Administrador de recursos de clúster, se eliminarán todos los parámetros de capacidad de aplicación de esta aplicación; especificación de nuevo, se requeriría API de aplicación de actualización al que se llama con los parámetros adecuados.

## <a name="restrictions-on-application-capacity"></a>Restricciones de capacidad de aplicación
Hay varias restricciones en los parámetros de capacidad de las aplicaciones que se deben respetar. Si hay errores de validación, la creación o actualización de la aplicación será rechazada con un error.
Todos los parámetros de entero deben ser números no negativos.
Además, para los parámetros individuales restricciones son:

-   MinimumNodes nunca debe ser mayor que MaximumNodes.
-   Si se definen las capacidades de una métrica de carga, deben seguir estas reglas:
  - Capacidad de reserva de nodos no debe ser mayor que la capacidad máxima de nodo. Por ejemplo, no puede limitar la capacidad de métrica "CPU" en el nodo a 2 unidades e Intente reservar 3 unidades en cada nodo.
  - Si se especifica MaximumNodes, el producto de MaximumNodes y capacidad máxima de nodo no debe ser mayor que la capacidad Total de aplicación. Por ejemplo, si establece la capacidad máxima de nodo de carga de métrica "CPU" a 8 y los nodos máximo 10, a continuación, la capacidad Total de aplicación debe ser mayor que 80 para esta métrica de carga.

Las restricciones se aplican durante la creación de la aplicación (en el cliente) y durante la actualización de la aplicación (en el servidor). Durante la creación, este es un ejemplo de una infracción clara de los requisitos desde MaximumNodes < MinimumNodes y a continuación, el comando producirá un error en el cliente antes de la solicitud incluso se envía a clúster de tela de servicio:

``` posh
New-ServiceFabricApplication –Name fabric:/MyApplication1 –MinimumNodes 6 –MaximumNodes 2
```

Un ejemplo de actualización no válida es el siguiente. Si se tomar una aplicación existente y actualización los nodos máximos a algún valor, se pasará la actualización:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MaximumNodes 2
```

Después de eso, podemos intentar actualizar nodos mínimos:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MinimumNodes 6
```

El cliente no tiene suficiente contexto acerca de la aplicación para permitir a la actualización pasar al clúster tela de servicio. Sin embargo, en el clúster, servicio tela valida el nuevo parámetro junto con los parámetros existentes y no se la operación de actualización porque los nodos mínimo de valor y aparece el compañero es mayor que el valor de nodos máximos. En este caso, se modificará parámetros de capacidad de las aplicaciones.

Estas restricciones se colocan en lugar de orden Administrador de recursos de clúster puedan proporcionar la mejor ubicación para réplicas de servicios de aplicaciones.

## <a name="how-not-to-use-application-capacity"></a>Utilizar la capacidad de aplicación

-   No use la capacidad de aplicación para restringir la aplicación a un subconjunto específico de nodos: tela de servicio aunque se asegurará de que se respeta nodos máximo para cada aplicación que tiene la capacidad de aplicación especificada, los usuarios no pueden decidir qué nodos se crea una instancia de. Esto puede conseguirse mediante restricciones de posición para los servicios.
-   No use la capacidad de las aplicaciones para asegurarse de que siempre se colocarán dos servicios desde la misma aplicación junto con los demás. Esto se puede lograr mediante afinidad relación entre servicios y afinidad puede limitar solo a los servicios que realmente se coloquen juntos.

## <a name="next-steps"></a>Pasos siguientes
- Para obtener más información acerca de las opciones disponibles para configurar los servicios, vea el tema sobre las otras configuraciones del Administrador de recursos de clúster disponibles [Obtenga más información sobre la configuración de servicios](service-fabric-cluster-resource-manager-configure-services.md)
- Para obtener información sobre cómo el Administrador de recursos de clúster administra y equilibra carga en el clúster, consulte el artículo sobre el [Equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md)
- Empezar desde el principio y [obtener una introducción al administrador de recursos en clúster servicio tela](service-fabric-cluster-resource-manager-introduction.md)
- Para obtener más información acerca de cómo métricas funcionan por lo general, infórmese de las [Estadísticas de carga de tela de servicio](service-fabric-cluster-resource-manager-metrics.md)
- El Administrador de recursos de clúster tiene muchas opciones para describir el clúster. Para saber más acerca de ellos desproteger en este artículo en la [Descripción de un clúster de tela de servicio](service-fabric-cluster-resource-manager-cluster-description.md)


[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
