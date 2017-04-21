<properties
   pageTitle="Desfragmentación de métricas en tela servicio Azure | Microsoft Azure"
   description="Información general sobre uso desfragmentación o embalaje como una estrategia de métricas en tela de servicio"
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

# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Desfragmentación de métricas y carga en tela de servicio
El Administrador de recursos de clúster de servicio tela principalmente se ocupa de equilibrio en cuanto a distribuir la carga: asegurarse de que todos los nodos en el clúster se utilizan igualmente. Suele ser el diseño más seguro y más listo en términos de supervivencia errores, ya que se asegura de que cualquier error dado no extraer la algunos gran porcentaje de carga de trabajo determinada. El Administrador de recursos de clúster de servicio tela admiten una estrategia diferente, que es la desfragmentación. Desfragmentación normalmente significa que en lugar de intentar distribuir la utilización de una métrica en el clúster, deberíamos realmente intentar consolidar. Se trata de una inversión de nuestra estrategia normal: en lugar de optimizar el clúster basado en minimizar la desviación estándar promedio de carga métrica de una métrica suerte, empezaremos optimizar para aumentos de desviación. Pero, ¿por qué querría esta estrategia?

Bien, si se ha expande la carga uniformemente entre los nodos en el clúster ha comen parte de los recursos que tienen los nodos para ofrecer. Normalmente esto no es un problema, pero a veces algunas cargas de trabajo crean servicios que son muy grandes y consuman la mayoría de un nodo, supongamos, por ejemplo 75% al 95% de los recursos de un nodo terminaría dedicado a una sola instancia de servicio o réplica. Esto no es un problema, el Administrador de recursos de clúster detectar en la hora de creación de servicio que necesita para reorganizar el clúster para liberar espacio para esta carga de trabajo grande y establecer sobre lo que suceda, pero mientras tanto esa carga de trabajo tiene que esperar para ser programada en el clúster.

Dado que la programación de nuevas cargas de trabajo suele ser al menos un poco latencia confidencial, hacerlo algo diferente a veces puede derecha golpe por los SLA si hay una gran cantidad de servicios y estado para moverse, especialmente si las cargas de trabajo en el clúster son generalmente grandes (y, por tanto, tardando más moverse en el clúster). De hecho, cuando se miden horas de creación en simulaciones basados en datos de clúster real, observamos que si el clúster bastante se utilizó que ralentizar la creación de estos servicios grandes hacia abajo y, a continuación, que podríamos mejorar esto mediante la introducción de la directiva de métricas desfragmentación y servicios pedidos lo suficientemente grandes.

Como archivo creación o acceso podría obtener más lento hacia abajo que si el disco duro del equipo se fragmentar y se puede acelerar desfragmentando la unidad de modo que había bloques contiguos más grandes, puede configurar métricas desfragmentación para que el Administrador de recursos de clúster de manera proactiva intenta condensar la carga de los servicios en menos nodos para que (casi) siempre hay espacio para los servicios de grandes , lo que les permite crear rápidamente. Mayoría de las personas no es necesario, porque servicios normalmente deberían ser pequeños y, por tanto, no es difícil de encontrar el salón para ellos, pero si tiene servicios grandes y necesiten crear rápidamente (y está dispuesta a aceptar las otras compensaciones como impactfulness aumento de errores y algunos recursos se deja los mientras espera para que cargas de trabajo se programe), a continuación, la estrategia de desfragmentación es para usted.

El siguiente diagrama ofrece una representación visual de los dos clústeres diferentes, uno que está desfragmentado y otro que no está. En el caso equilibrado, tenga en cuenta los movimientos que sería necesarios lugar uno de los objetos de servicio mayor, si fuera una nueva que se va a crear, en comparación con el clúster desfragmentado, donde puede colocarse inmediatamente en los nodos 4 y 5.

![Comparación de equilibrio y desfragmentado clústeres][Image1]

## <a name="defragmentation-pros-and-cons"></a>Desfragmentación ventajas y desventajas
Entonces, ¿cuáles son las otras compensaciones conceptuales? Se recomienda medidas profundo de las cargas de trabajo antes de girar métrica de desfragmentación. Aquí presentamos una tabla rápida de las cosas que pensar:

| Los profesionales de TI de desfragmentación  | Desventajas de desfragmentación |
|----------------------|----------------------|
|Permite la creación más rápida de servicios de gran tamaño | Se concentra carga en menos nodos, conflictos en aumento
|Permite un menor durante la creación de movimientos de datos    | Pueden afectar más servicios y provocar renovación más errores
|Permite enriquecido descripción de requisitos y recuperación del espacio | Configuración general de administración de recursos más compleja

Puede combinar métricas desfragmentadas y normales en el mismo clúster y el Administrador de recursos lo es mejor para asegurarse de que obtendrá un diseño que consolida como gran parte de las mediciones desfragmentación como puede mientras intenta separar los el resto. Los resultados exactos que obtendrá dependerá del número de equilibrio métricas en comparación con el número de métricas desfragmentación y sus pesos, cargas actuales, etcetera.

## <a name="configuring-defragmentation-metrics"></a>Configurar métricas desfragmentación
Configurar métricas desfragmentación es una decisión global en el clúster y mediciones individuales pueden seleccionarse para desfragmentación:

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

## <a name="next-steps"></a>Pasos siguientes
- El Administrador de recursos de clúster tiene muchas opciones para describir el clúster. Para saber más acerca de ellos desproteger en este artículo en la [Descripción de un clúster de tela de servicio](service-fabric-cluster-resource-manager-cluster-description.md)
- Métricas son cómo se administra el Administrador de recursos de clúster de servicio tela consumo y la capacidad del clúster. Para obtener más información acerca de ellos y cómo configurarlos consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
