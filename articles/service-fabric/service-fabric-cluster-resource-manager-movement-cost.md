<properties
   pageTitle="Costo de movimiento de servicio Administrador de recursos de clúster de tela: | Microsoft Azure"
   description="Información general de costo de movimiento para los servicios de tela de servicio"
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

# <a name="service-movement-cost-for-influencing-cluster-resource-manager-choices"></a>Costo de movimiento de servicio para influir en las opciones del Administrador de recursos de clúster
Un factor importante para tener en cuenta cuando intenta determinar qué cambios realizar en un clúster y la calificación de una solución es el costo total de lograr esa solución.

Mover el servicio instancias o réplicas costos CPU tiempo y ancho de banda como mínimo. Para los servicios con estado, también cuesta la cantidad de espacio en disco que necesita para crear una copia del estado antes de cerrar duplicados antiguos. Claramente que desea minimizar el costo de cualquier solución de administrador de recursos de clúster de Azure servicio tela aparezca con. Pero no desea omitir las soluciones que podrían mejorar significativamente la asignación de recursos en el clúster.

Administrador de recursos de clúster tiene dos formas de calcular los costos y limitar, incluso cuando intenta administrar el clúster según sus objetivos de otros. La primera es que, al administrador de recursos de clúster es planear un nuevo diseño para el clúster, cuente cada movimiento que lo haga. En un caso sencillo, si recibe dos soluciones con la misma saldo total (resultado) al final y luego realice alguna con el menor costo (número total de movimientos).

Esto funciona bien. Pero, al igual que con predeterminado o cargas estáticas, es poco probable en cualquier sistema complejo que todos los movimientos son iguales. Algunos suelen ser muy costosas mucho más.

## <a name="changing-a-replicas-move-cost-and-factors-to-consider"></a>Cambiar una réplica mover costo y factores que deben contemplarse
Como con los informes de carga (otra característica del Administrador de recursos de clúster), se asigne el servicio de una forma de informes automáticamente el servicio cómo costosas es para moverse en un momento determinado.

Código:

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost tiene cuatro niveles: cero, baja, Media y alta. Estos son respecto a los demás, excepto el cero. Cero significa que mover una réplica es gratuita y no debe contar con la puntuación de la solución. Establecer el costo de mover a alta es *no* garantiza que no se mueve la réplica, solo que no se moverán a menos que haya una buena razón.

![Costo de movimiento como un factor en Seleccionar réplicas de movimiento][Image1]

MoveCost le ayuda a encontrar las soluciones que producen la menor interrupción general y resulta más fácil de lograr mientras sigue llegar a saldo equivalente. Concepto de un servicio de costo puede ser relativa muchas cosas. Los factores para calcular el costo de mover más comunes son:

- La cantidad de datos que tiene el servicio para mover o estado.
- Costo de desconexión de los clientes. El coste de mover una réplica principal generalmente es mayor que el coste de mover una réplica secundaria.
- Costo de interrumpir una operación en curso. Nivel de almacén de algunas operaciones en los datos o las operaciones realizadas en respuesta a una llamada de cliente son costosas. Después de un cierto punto, no quiere dejar de si no es necesario. Así que durante la duración de la operación, saliente el costo para reducir la probabilidad de que se moverá la réplica de servicio o la instancia. Cuando haya terminado la operación, colocarlo a normal.

## <a name="next-steps"></a>Pasos siguientes
- Administrador de recursos de clúster de servicio tela utiliza medidas para administrar el consumo y la capacidad del clúster. Para obtener más información sobre cómo métricas y cómo configurarlos, consulte [administrar el consumo de recursos y carga en tela de servicio con métricas](service-fabric-cluster-resource-manager-metrics.md).
- Para obtener información sobre cómo el Administrador de recursos de clúster administra y equilibra carga en el clúster, consulte [el clúster de servicio tela de equilibrio](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
