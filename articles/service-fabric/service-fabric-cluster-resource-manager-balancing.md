<properties
   pageTitle="El clúster con el Azure tela clúster recursos Administrador de servicios de equilibrio | Microsoft Azure"
   description="Introducción a su clúster con el Administrador de recursos de clúster tela de servicios de equilibrio."
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

# <a name="balancing-your-service-fabric-cluster"></a>El clúster de tela de servicio de equilibrio
El Administrador de recursos de clúster de tela de servicio permite reporting dinámico de carga, solucionando cambios en el clúster, corregir las transgresiones de restricción y volver a equilibrar el clúster si es necesario. Pero, ¿con qué frecuencia hace lo y lo que lo activa? Existen varios controles relacionados con esto.

El primer conjunto de controles de equilibrio son un conjunto de temporizador de. Estos temporizadores controlan la frecuencia con el Administrador de recursos de clúster examina el estado del clúster para las cosas que debe solucionarse. Existen tres categorías distintas del trabajo, cada una con su propio temporizador correspondiente. Son:

1.  Posición: esta fase se ocupa de colocar las instancias de estado que faltan o de réplicas con estado. Esto abarca nuevos servicios y tratamiento réplicas con estado o sin estado casos en los que se han producido errores y necesitan volver a crear. Eliminar y soltar réplicas o instancias también se administran aquí.
2.  Comprobaciones de restricción: esta fase comprueba y corrige violaciones de las restricciones de colocación diferentes (reglas) dentro del sistema. Ejemplos de reglas son cosas como garantizar que los nodos no son capacidad y que se cumplen las restricciones de posición de un servicio (más información sobre estas posterior).
3.  Equilibrio: esta fase comprueba ver si volver a equilibrar proactiva es necesario basándose en el nivel que desee configurado del saldo de diferentes métricas y si es así intenta encontrar una organización en el clúster que está equilibrado más.

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>Configuración del Administrador de recursos de clúster pasos y temporizadores
Cada uno de estos tipos de correcciones puede hacer que el Administrador de recursos de clúster está controlado por un temporizador de diferentes que rige su frecuencia. Por ejemplo, si solo desea tratar colocar nuevas cargas de trabajo de servicio en el clúster cada hora (en un lote hacia arriba), pero desea equilibrio regular comprueba cada pocos segundos, puede configurar ese comportamiento. Cuando se activa cada temporizador, la tarea se programará. De forma predeterminada, el Administrador de recursos analiza su estado y aplica actualizaciones (por lotes todos los cambios realizados desde el último análisis, como observar que el nodo está desactivado) cada 1/10 de un segundo, Establece la ubicación y la restricción comprobación los indicadores de cada segundo y el equilibrio de marcar cada 5 segundos.

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Hoy sólo realizar una de estas acciones a la vez, secuencialmente (por eso nos referimos a estas configuraciones como "intervalos mínimos")). Esto es que, por ejemplo, ya hemos respondido a cualquier solicitud pendiente para crear réplicas nuevas antes de seguir el clúster de equilibrio. Como puede ver por los intervalos de tiempo predeterminada especificados, nos podemos digitalizar y buscar todo lo que necesitamos con frecuencia, lo que significa que el conjunto de cambios ofrecemos al final de cada paso es normalmente menor: no nos estamos examinar horas de cambios en el clúster e intentando corregirlos a la vez, que estamos intentando controlar cosas más o menos que se producen, pero con lotes cuando se producen muchas cosas en la mismo tiempo. De esta forma el recurso de servicio tela manager muy responde a los elementos que se producen en el clúster.

Mientras que la mayoría de estas tareas son sencillas (si hay violaciones de restricción, revisión, si hay servicios que se creará, crean), el Administrador de recursos de clúster también necesita alguna información adicional para determinar si el clúster desequilibrado. Para que tenemos dos otros datos de configuración: *Equilibrio umbrales* y *Umbrales de actividad*.

## <a name="balancing-thresholds"></a>Umbrales de equilibrio
Umbral de equilibrio es el control principal para la activación de volver a equilibrar proactiva (Recuerde que el temporizador es solo para la frecuencia con que debe comprobar el Administrador de recursos de clúster: no significa nada sucederá). El umbral de equilibrio define cómo desequilibradas debe ser de una métrica específica en orden para el Administrador de recursos de clúster para tener en cuenta que desequilibradas y equilibrio de desencadenador el clúster.

Umbrales de equilibrio se definen en una base por métrica como parte de la definición del clúster. Para obtener más información sobre métricas desprotección [en este artículo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

El umbral de equilibrio de una métrica es una relación. Si la cantidad de carga en el nodo más cargado dividido por la cantidad de carga en el nodo menos carga supera este número, a continuación, el clúster se considera desequilibrado y equilibrio se activará la próxima vez que el Administrador de recursos de clúster comprueba (de forma predeterminada, nunca 5 segundos, como controlados por el MinLoadBalancingInterval, mostrado arriba).

![Ejemplo de umbral de equilibrio][Image1]

En este sencillo ejemplo cada servicio consumo una unidad de algunos métrica. En el ejemplo superior, la carga máxima en un nodo es 5 y el valor mínimo es 2. Supongamos que el umbral de equilibrio para esta métrica es 3. Por lo tanto, en el ejemplo superior, el clúster se considera equilibrada y equilibrio no se activará cuando comprueba el Administrador de recursos de clúster (como la relación en el clúster es 5/2 = 2,5, que es menor que el umbral especificado equilibrio de 3).

En el ejemplo de la parte inferior, la carga máxima de un nodo es 10, mientras que el valor mínimo es 2, como resultado una relación de 5. Esto coloca el clúster por encima del umbral de equilibrio diseñado de 3 para esa métrica. Como resultado, una ejecución rebalancing global estará programada próxima vez que se activa el temporizador de equilibrio. Tenga en cuenta que se inicia una búsqueda de equilibrio no significa nada moverá: a veces es desequilibrado el clúster, pero no se puede mejorar la situación - pero en una situación como ésta (al menos de forma predeterminada) algunos la carga seguramente se distribuirá Nodo3. Tenga en cuenta que puesto que no se está utilizando un enfoque gracias cierta carga también puede distribuirse a nodo 2 ya que daría como resultado reducción de las diferencias entre los nodos generales, pero se pueden esperar a que la mayoría de la carga se flujo para Nodo3.

![Acciones de ejemplo de umbral de equilibrio][Image2]

Nota que recibe por debajo del umbral de equilibrio no es un objetivo explícito: umbrales de equilibrio son solo un *desencadenador* que indica el Administrador de recursos de clúster que debería tener un aspecto en el clúster para determinar qué mejoras puede hacer, si existe.

## <a name="activity-thresholds"></a>Umbrales de actividad
A veces, aunque los nodos son relativamente desequilibrados, la cantidad *total* de carga en el clúster es baja. Esto puede ser simplemente debido a la hora del día o porque el clúster es nuevo y obtener solo programados previamente. En cualquier caso, es posible que no desea perder tiempo el clúster de equilibrio porque hay realmente muy poco se gana: va justo a pasar recursos de red y calcular para mover cosas, sin hacer cualquier diferencia absoluta. Porque queremos evitar hacerlo, hay otro control en el Administrador de recursos, conocida como umbrales de actividad, que le permite especificar algún límite inferior absoluto de actividad: si no hay nodo tiene al menos esta cantidad de carga y equilibrio no se activará incluso si se cumple el umbral de equilibrio.

Como ejemplo supongamos que tenemos informes con los siguientes totales para su consumo en los nodos. Supongamos que se conservan nuestro umbral equilibrio de 3 para esta métrica, pero ahora también tenemos un umbral de actividad de 1536. En el primer caso, mientras el clúster está desequilibrado por el umbral de equilibrio de ningún nodo cumple ese umbral mínimo de actividad, por lo que nos dejar cosas. En el ejemplo de la parte inferior, nodo 1 forma por encima del umbral de actividad para que equilibrio se realizará (ya que se superan el umbral de equilibrio y el umbral de la actividad de la métrica)

![Ejemplo de umbral de actividad][Image3]

Igual que umbrales de equilibrio umbrales de actividad son definidos por métrica a través de la definición del clúster:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

Tenga en cuenta que los umbrales de equilibrio y actividad ambas están vinculadas a la métrica - equilibrio sólo se activará si se superen los umbrales de equilibrio y actividad de la misma métrica. Por lo tanto, si se supera el umbral de equilibrio de memoria y el umbral de la actividad de la CPU, equilibrio no se activará siempre y cuando no se superen los umbrales restantes (equilibrio de umbral para CPU) y actividad de memoria.

## <a name="balancing-services-together"></a>Servicios de equilibrio juntos
Algo que es interesante observar es que si el clúster es desequilibrado o no es una decisión de todo el clúster, pero la forma de hacer para solucionar el problema es mover réplicas de servicio individuales e instancias alrededor. ¿Esto tiene sentido, derecha? Así que si memoria apilada en varias copias de un nodo o podrían estar causando instancias, podría requerir mover cualquiera de las réplicas con estado o sin estado instancias que utilizan la métrica afectada, desequilibrada.

En ocasiones aunque un cliente llamará nos hacia arriba o archivo obtuvo mueve un vale que indica que un servicio que no desequilibrado. ¿Cómo puede llevarse a cabo que obtiene desplazan un servicio incluso si todo métricas del servicio se lleva a cabo, incluso perfectamente es así, en el momento de la otro descuadre? ¡Veamos!

Tomar, por ejemplo, cuatro servicios, Service1, Servicio2 al cliente3 y Service4. Informes de Service1 contra métricas Metric1 y Metric2, Servicio2 contra Metric2 y Mmetric3 al cliente3 contra Metric3 y Metric4 y Service4 contra algunos Metric99 métrica. Seguramente puede ver dónde vamos a continuación. Tenemos una cadena. Desde la perspectiva del Administrador de recursos de clúster, realmente no hay cuatro servicios independientes, tenemos un conjunto de servicios que están relacionados (Service1, Servicio2 y al cliente3) y que está desactivado en su propia.

![Servicios de equilibrio juntos][Image4]

Por lo que es posible que puede producir un descuadre en Metric1 réplicas o casos en los que pertenecen al cliente3 moverse. Estos movimientos suelen ser muy limitados, pero puede ser mayor según exactamente cómo desequilibradas Metric1 tiene y qué cambios eran necesarios en el clúster para corregirlo. También podemos decir con certeza que un descuadre en métricas 1, 2 o 3 nunca provocará los movimientos Service4: no sería ningún punto desde mover las réplicas o casos en los que pertenecen a Service4 alrededor pueden hacen nada para tener un impacto en el saldo de métrica de 1, 2 o 3.

El Administrador de recursos de clúster imagina automáticamente qué servicios están relacionados, ya que los servicios se han agregado, eliminado, o tenían sus cambios de configuración de métrica: por ejemplo, entre dos ejecuciones del equilibrio Servicio2 se volvió a podrán para quitar Metric2. Esto rompe la cadena entre Service1 y Servicio2. Ahora en lugar de dos grupos de servicios, tiene tres:

![Servicios de equilibrio juntos][Image5]

## <a name="next-steps"></a>Pasos siguientes
- Métricas son cómo se administra el Administrador de recursos de clúster de servicio tela consumo y la capacidad del clúster. Para obtener más información acerca de ellos y cómo configurarlos consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md)
- Costo de movimiento es una manera de señalización al administrador de recursos de clúster que determinados servicios son más costosas para desplazarse a otras personas. Para obtener más información sobre el costo de movimiento, consulte [este artículo](service-fabric-cluster-resource-manager-movement-cost.md)
- El Administrador de recursos de clúster tiene varios límites que puede configurar para ralentizar la renovación en el clúster. No son necesarias normalmente, pero si las necesita puede obtener información acerca de ellos [aquí](service-fabric-cluster-resource-manager-advanced-throttling.md)


[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
