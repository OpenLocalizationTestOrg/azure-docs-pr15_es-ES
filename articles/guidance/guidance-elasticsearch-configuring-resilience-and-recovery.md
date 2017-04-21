<properties
   pageTitle="Configuración resistencia y recuperación en Elasticsearch en Azure"
   description="Consideraciones relacionadas con la resistencia y recuperación para Elasticsearch."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>
   
# <a name="configuring-resilience-and-recovery-on-elasticsearch-on-azure"></a>Configuración resistencia y recuperación en Elasticsearch en Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie](guidance-elasticsearch.md). 

Una característica clave de Elasticsearch es la compatibilidad que proporciona para resistencia en caso de errores de nodo o eventos de partición de red. Replicación de la forma más obvios en la que puede mejorar la resistencia de ningún clúster habilitar Elasticsearch para asegurarse de que más de una copia de cualquier elemento de datos está disponible en distintos nodos en caso de un nodo debe dejar de estar accesible. Si un nodo no está disponible temporalmente, otros nodos que contienen réplicas de datos desde el nodo que falta pueden servir los datos que faltan hasta que se resuelva el problema. Si se produce un problema de términos más largo, puede reemplazar el nodo que falta por una nueva y Elasticsearch puede restaurar los datos en el nuevo nodo de las réplicas.

A continuación se resumen las opciones de la resistencia y recuperación disponibles con Elasticsearch al alojado en Azure y a continuación, se describen algunos aspectos importantes de un clúster de Elasticsearch que debe tener en cuenta para minimizar las posibilidades de pérdida de datos y tiempos de recuperación de datos extendido.

En este artículo también muestra algunas pruebas de ejemplo que se realizaron para mostrar los efectos de los diferentes tipos de errores en un clúster de Elasticsearch y cómo responde el sistema mientras se recupera.

Un clúster de Elasticsearch usa réplicas para mantener la disponibilidad y mejorar el rendimiento de lectura. Réplicas deben almacenarse en diferentes máquinas virtuales de la shards principales que ellos replicar. La intención es que si la máquina virtual de un nodo de datos de host falla o no está disponible, el sistema puede continuar funcionando con las VM manteniendo las réplicas.

## <a name="using-dedicated-master-nodes"></a>Uso de los nodos de patrón dedicados

Un nodo en un clúster de Elasticsearch se elige como el nodo maestro. El propósito de este nodo es realizar operaciones de administración de clúster como:

- Detectar errores nodos y cambiar a réplicas.

- Reubicar shards equilibrar la carga de trabajo de nodo.

- Recuperar shards cuando un nodo vuelva a estar en línea.

Debe piense en usar nodos maestros dedicados en clústeres críticos y asegúrese de que hay 3 nodos dedicados cuyo rol solo es ser maestras. Esta configuración reduce la cantidad de trabajo intensivo de recursos que tienen estos nodos para realizar (no almacenar datos ni manejar consultas) y ayuda a mejorar su estabilidad. Solo uno de estos nodos elegido, pero los demás contendrán una copia del estado del sistema y pueden tomar el control si falla el patrón seleccionado.

## <a name="controlling-high-availability-with-azure--update-domains-and-fault-domains"></a>Control de alta disponibilidad con Azure: actualización de los dominios y errores 

Máquinas virtuales diferentes pueden compartir el mismo hardware físico. En un centro de datos de Azure, un único bastidor puede hospedar un número de máquinas virtuales y todas estas máquinas virtuales comparten un modificador de origen y de red power comunes. Error de un nivel de bastidor único, por tanto, puede afectar a un número de máquinas virtuales. Azure usa el concepto de dominios de error probar y extender el riesgo. Un dominio de error aproximadamente corresponde a un grupo de máquinas virtuales que comparten el mismo bastidor. Para asegurarse de que un error de nivel de bastidor no bloquea un nodo y los nodos manteniendo todas sus réplicas simultáneamente, debe asegurarse de que las máquinas virtuales se distribuyen entre dominios de error.

Asimismo, máquinas virtuales pueden tomarse hacia abajo el [Controlador de tela de Azure](https://azure.microsoft.com/documentation/videos/fabric-controller-internals-building-and-updating-high-availability-apps/) para realizar las actualizaciones de sistema operativo y mantenimiento planeadas. Azure asigna máquinas virtuales para actualizar los dominios. Cuando se produce un evento de mantenimiento planeado, máquinas virtuales sola en un dominio de actualización se haya realizado en cualquier momento. Máquinas virtuales en otros dominios de actualización quedan hasta que las máquinas virtuales en el dominio de actualización actualizando estén nuevamente en línea. Por lo tanto, también debe asegurarse de que máquinas virtuales de hospedaje nodos y sus réplicas pertenecen a los dominios de actualización diferente siempre que sea posible.

> [AZURE.NOTE] Para obtener más información acerca de los dominios de error y actualización, vea [Administrar la disponibilidad de máquinas virtuales de Windows][].

No puede asignar explícitamente una máquina virtual para un dominio de actualización y un dominio de error. Azure controla esta asignación cuando se crean máquinas virtuales. Sin embargo, puede especificar que se deben crear máquinas virtuales como parte de un conjunto de disponibilidad. Máquinas virtuales en el mismo conjunto de disponibilidad se extienden a través de los dominios de actualización y errores. Si crear máquinas virtuales de forma manual, Azure crea cada disponibilidad con dos dominios de error y cinco dominios de actualización. Máquinas virtuales están asignadas a estos dominios de error y actualización dominios, recorrido de ida y vuelta como más máquinas virtuales aprovisionadas, como sigue: 

| VM | Dominio de error | Actualizar dominio |
|----|--------------|---------------|
|  1 |            0 |             0 |
|  2 |            1 |             1 |
|  3 |            0 |             2 |
|  4 |            1 |             3 |
|  5 |            0 |             4 |
|  6 |            1 |             0 |
|  7 |            0 |             1 |

> [AZURE.IMPORTANT] Si crea máquinas virtuales con el Administrador de recursos de Azure, cada conjunto de disponibilidad puede asignar hasta 3 dominios de error y actualización de 20. Esta es una razón de peso para usar el Administrador de recursos.

En general, coloque todas las máquinas virtuales que tienen el mismo propósito en el mismo conjunto de disponibilidad, pero crear conjuntos de disponibilidad diferentes para las VM que realizan funciones diferentes. Con Elasticsearch que esto significa que debe considerar crear disponibilidad al menos independiente conjuntos para:

- Máquinas virtuales host nodos de datos.
- Máquinas virtuales host nodos de cliente (si se utiliza).
- Máquinas virtuales host nodos maestros.

Además, debe asegurarse de que cada nodo de un clúster tiene constancia de los dominios de actualización y errores que pertenece. Esta información puede ayudar a asegurarse de que Elasticsearch no crear shards y sus réplicas en el mismo error y actualizar dominios, minimizar la posibilidad de un fragmentar y sus réplicas desde teniendo al mismo tiempo. Puede configurar un nodo Elasticsearch para reflejar la distribución de hardware de clúster mediante la configuración de [reconocimiento de asignación fragmentar](https://www.elastic.co/guide/en/elasticsearch/reference/current/allocation-awareness.html#allocation-awareness). Por ejemplo, puede definir un par de atributos de nodo personalizado denominado *faultDomain* y *updateDomain* en el archivo elasticsearch.yml, como sigue:

```yaml
node.faultDomain: \${FAULTDOMAIN}
node.updateDomain: \${UPDATEDOMAIN}
```

En este caso, los atributos se establecen con los valores mantenidos en la * \${FAULTDOMAIN}* y * \${UPDATEDOMAIN}* variables de entorno cuando se inicia Elasticsearch. También debe agregar las siguientes entradas en el archivo Elasticsearch.yml para indicar que *faultDomain* y *updateDomain* son asignación atributos de concienciación y especifican los conjuntos de valores aceptables para estos atributos:

```yaml
cluster.routing.allocation.awareness.force.updateDomain.values: 0,1,2,3,4
cluster.routing.allocation.awareness.force.faultDomain.values: 0,1
cluster.routing.allocation.awareness.attributes: updateDomain, faultDomain
```

Puede usar reconocimiento de asignación fragmentar junto con el [filtrado de asignación de fragmentar](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/shard-allocation-filtering.html#shard-allocation-filtering) especificar explícitamente qué nodos pueden hospedar shards para cualquier índice.

Si necesita ampliar el número de dominios de error y actualizar los dominios en un conjunto de disponibilidad, puede crear máquinas virtuales en conjuntos de disponibilidad adicional. Sin embargo, necesitará saber que nodos en conjuntos de disponibilidad diferentes pueden ir hacia abajo para realizar el mantenimiento simultáneamente. Pruebe para asegurarse de que cada fragmentar y al menos uno de sus réplicas se encuentran en el mismo conjunto de disponibilidad.

> [AZURE.NOTE] Actualmente no hay un límite de 100 VM por conjunto de disponibilidad. Para obtener más información, consulte [suscripción Azure y límites de servicio, cuotas y las restricciones](../azure-subscription-service-limits.md).

### <a name="backup-and-restore"></a>Copia de seguridad y restauración

Uso de réplicas no proporciona una protección completa del error grave (por ejemplo, accidentalmente eliminar todo el clúster). Asegúrese de que realiza una copia de seguridad de los datos en un clúster con regularidad, y tiene una estrategia de intentar ni probar para restaurar el sistema de estas copias de seguridad.

Use las Elasticsearch [instantánea y restaurar las API](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html) : elástica no tapa estos. >> hacer copia de seguridad y restauración índices. Instantáneas pueden guardarse en un sistema de archivos compartido. Como alternativa, están disponibles los complementos que puede escribir instantáneas en el sistema de archivos distribuido Hadoop (HDFS) (el [complemento HDFS](https://github.com/elasticsearch/elasticsearch-hadoop/tree/master/repository-hdfs)) o para el almacenamiento de Azure (el [complemento de Azure](https://github.com/elasticsearch/elasticsearch-cloud-azure#azure-repository)).

Al seleccionar el mecanismo de almacenamiento de instantáneas, tenga en cuenta los siguientes puntos:

- Puede usar el [almacenamiento de archivos de Azure](https://azure.microsoft.com/services/storage/files/) para implementar un sistema de archivos compartido que es accesible desde todos los nodos.

- Use el complemento HDFS únicamente si está ejecutando Elasticsearch junto con Hadoop.

- El complemento HDFS debe deshabilitar al administrador de seguridad de Java ejecuta dentro de la instancia de Elasticsearch de la máquina virtual de Java (JVM).

- El complemento HDFS es compatible con ningún sistema de archivos compatibles con HDFS siempre que se usa la configuración correcta de Hadoop con Elasticsearch.

  
## <a name="handling-intermittent-connectivity-between-nodes"></a>Conectividad intermitente entre los nodos de control

Problemas de red intermitentes, VM reinicia después de mantenimiento en el centro de datos y otros eventos similares pueden provocar los nodos obtener acceso a ellos temporalmente. En estos casos, donde el evento es probable que sea vida corta, se produce la sobrecarga de volver a equilibrar la shards dos veces en rápido sucesivamente (una vez cuando se detecta el error y vuelva a cuando el nodo se hacen visibles en el patrón) puede convertirse en una sobrecarga importante que afecta al rendimiento. Puede impedir que inaccesibilidad nodo temporal causando el patrón para volver a equilibrar el clúster estableciendo la *retrasar\_tiempo de espera* propiedades de un índice, o para todos los índices. El ejemplo siguiente establece el tiempo de retraso en 5 minutos:

```http
PUT /_all/settings
{
    "settings": {
    "index.unassigned.node_left.delayed_timeout": "5m"
    }
}
```

Para obtener más información, vea [retrasar asignación cuando un nodo abandona](https://www.elastic.co/guide/en/elasticsearch/reference/current/delayed-allocation.html).

En una red que es susceptible a interrupciones, también puede modificar los parámetros que configure un patrón para detectar cuando otro nodo ya no es accesible. Estos parámetros son parte del módulo de [detección de zen](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html#modules-discovery-zen) proporcionado con Elasticsearch y puede establecer en el archivo Elasticsearch.yml. Por ejemplo, el parámetro *discovery.zen.fd.ping.retries* especifica cuántas veces intentará hacer ping a otro nodo del clúster antes de decidir que no haya un nodo maestro. Este parámetro predeterminado es 3, pero puede modificar como sigue:

```yaml
discovery.zen.fd.ping_retries: 6
```

## <a name="controlling-recovery"></a>Controlar la recuperación

Cuando se restaura la conectividad a un nodo después de un error, cualquier shards en ese nodo necesitará recuperarse para actualizarlos actualizados. De forma predeterminada, Elasticsearch recupera shards en el siguiente orden:

- Por fecha de creación de índice inverso. Índices más recientes se recuperan antes de índices más antiguos.

- Por nombre de índice inverso. Primero se restauran índices que tienen nombres que sean alfanumérico superiores a otras personas.

Si algunos índices son más importantes que otros usuarios, pero no coincidan con estos criterios puede reemplazar la prioridad de índices estableciendo la propiedad *index.priority* . Índices con un valor superior para esta propiedad se recuperarán antes de índices que tienen un valor inferior:

```http
PUT low_priority_index
{
    "settings": {
        "index.priority": 1
    }
}

PUT high_priority_index
{
    "settings": {
        "index.priority": 10
    }
}
```

Para obtener más información, consulte [Establecimiento de prioridades de recuperación de índice](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/recovery-prioritization.html#recovery-prioritization).

Puede supervisar el proceso de recuperación para uno o más índices mediante la * \_recuperación* API:

```http
GET /high_priority_index/_recovery?pretty=true
```

Para obtener más información, consulte [Recuperación de índices](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-recovery.html#indices-recovery).

> [AZURE.NOTE] Un clúster con shards que requieren recuperación tendrá un estado de *color amarillo* para indicar que no todos los shards están disponibles actualmente. Cuando todos los shards están disponibles, el estado de clúster debe volver a *verde*. Un clúster con un estado de *color rojo* indica que uno o más shards faltan física, puede ser necesario restaurar los datos desde una copia de seguridad.

## <a name="preventing-split-brain"></a>Prevención de cerebro dividido 

Un cerebro dividido puede ocurrir si no se realice correctamente en las conexiones entre los nodos. Si un nodo maestro está inaccesible a parte del clúster, producirá una elección en el segmento de red que sigue estando disponible las y otro nodo se convertirá en el patrón. En un clúster mal configurados, es posible para cada parte del clúster tener diferentes patrones de datos incoherencias o daños. Este fenómeno se conoce como un *cerebro dividido*.

Puede reducir las probabilidades de un cerebro dividido mediante la configuración de la *mínimo\_principal\_nodos* propiedad del módulo de detección, en el archivo elasticsearch.yml. Esta propiedad especifica el número de nodos debe estar disponible para habilitar la elección de un patrón. En el ejemplo siguiente se establece el valor de esta propiedad a 2:

```yaml
discovery.zen.minimum_master_nodes: 2
```

Este valor se debe establecer a la mayoría del número de nodos que se pueda cumplir la función de maestro más baja. Por ejemplo, si el clúster tiene 3 nodos maestros, *mínimo\_principal\_nodos* debe establecer a 2. Si tiene 5 nodos maestros, *mínimo\_principal\_nodos* debe estar establecida en 3. Lo ideal es que debería tener un número impar de nodos maestros.

> [AZURE.NOTE] Es posible que un cerebro dividido a producir si varios nodos principales en el mismo clúster se inician al mismo tiempo. Aunque esta repetición es rara, puede evitar iniciando nodos en serie con un breve retraso (5 segundos) entre cada uno de ellos. 

## <a name="handling-rolling-updates"></a>Control de actualizaciones graduales

Si está realizando una actualización de software para nodos usted mismo (como migrar a una versión más reciente o realizar una revisión), debe realizar el trabajo de nodos individuales que requiere tomar ellos sin conexión y mantener el resto del clúster disponible. En esta situación, considere la posibilidad de implementar este procedimiento. 

1. Asegúrese de que dicha reasignación fragmentar se retrasa suficiente para evitar que al patrón seleccionado de volver a equilibrar shards desde un nodo que faltan en el resto del clúster. De forma predeterminada, reasignación fragmentar retraso de 1 minuto, pero puede aumentar la duración si un nodo es probable que no esté disponible durante un período más largo. En el ejemplo siguiente se aumenta el tiempo de retraso de 5 minutos:

    ```http
    PUT /_all/_settings
    {
        "settings": {
            "index.unassigned.node_left.delayed_timeout": "5m"
        }
    }
    ```

    > [AZURE.IMPORTANT] También puede deshabilitar completamente reasignación fragmentar estableciendo la *cluster.routing.allocation.enable* del clúster en *ninguna*. Sin embargo, debe evitar el uso de este método si es probable que se crean mientras el nodo está sin conexión, ya que esto puede provocar asignación de índice errores resultantes en un clúster con estado rojo nuevos índices.

2. Detener Elasticsearch en el nodo mantenerse. Si está ejecutando Elasticsearch como un servicio, es posible que pueda detener el proceso de un modo controlado mediante un comando de sistema operativo. En el ejemplo siguiente se muestra cómo detener el servicio Elasticsearch en un único nodo ejecuta en Ubuntu:

    ```bash
    service elasticsearch stop
    ```

    Como alternativa, puede usar la API de cierre directamente en el nodo:

    ```http
    POST /_cluster/nodes/_local/_shutdown
    ```

3. Realizar el mantenimiento necesario en el nodo

4. Reinicie el nodo y espere a unirse al clúster.

5. Volver a habilitar la asignación de partes:

    ```http
    PUT /_cluster/settings
    {
        "transient": {
            "cluster.routing.allocation.enable": "all"
        }
    }
    ```

> [AZURE.NOTE] Si necesita mantener más de un nodo, repita los pasos 2&ndash;4 en cada nodo antes de volver a habilitar asignación fragmentar.

Si puede, detener indización nuevos datos durante el proceso. Esto le ayudará a minimizar el tiempo de recuperación cuando los nodos se ponen en línea y a unir el clúster.

Tenga cuidado de actualizaciones automáticas para elementos como JVM (lo ideal es que, deshabilitar actualizaciones automáticas para estos elementos), especialmente cuando se ejecutan Elasticsearch en Windows. El agente de actualización de Java puede descargar la versión más reciente de Java automáticamente, pero puede requerir Elasticsearch reiniciar para que la actualización surta efecto. Esto puede ocasionar una pérdida temporal coordinada de nodos, dependiendo de cómo está configurado el agente de actualización de Java. Esto también puede dar lugar a distintas instancias de Elasticsearch en el mismo clúster ejecutan diferentes versiones de JVM que pueden causar problemas de compatibilidad.

## <a name="testing-and-analyzing-elasticsearch-resilience-and-recovery"></a>Probar y analizar Elasticsearch resistencia y recuperación

Esta sección describe una serie de pruebas que se realizaron para evaluar la resistencia y recuperación de un clúster de Elasticsearch que contiene tres nodos de datos y tres principal.

Se han probado las situaciones siguientes: 

- Error de nodo y reiniciar sin pérdida de datos. Un nodo de datos se detenga y reinicie después de 5 minutos. Se ha configurado para Elasticsearch no para reasignar shards que faltan en este intervalo, por lo que no se incurre i/OS adicionales en desplazarse shards. Cuando se reinicia el nodo, el proceso de recuperación incluye la shards en ese nodo volver actualizado.

- Error de nodo con pérdida de datos grave. Un nodo de datos se detiene y se eliminan de los datos que contiene para simular un error de error de disco. Reiniciar el nodo después (después de 5 minutos), un modo eficaz que actúe como un reemplazo para el nodo original. El proceso de recuperación requiere reconstruir los datos que faltan para este nodo y puede implicar reubicar shards llevan a cabo en otros nodos.

- Error de nodo y reiniciar sin pérdida de datos, pero con reasignación fragmentar. Un nodo de datos se detiene y la shards que contiene se reasignen a otros nodos. A continuación, se reinicia el nodo y reasignación más se produce para equilibrar el clúster.

- Distribución de actualizaciones. Cada nodo del clúster se detenga y reinicie después de un intervalo breve para simular máquinas que se reinicia después de una actualización de software. Solo nodo se detiene en cualquier momento. No se reasignen shards mientras el nodo está desactivado.

Cada escenario era sujetos a la misma carga de trabajo que incluye una combinación de tareas de recopilación de datos, agregaciones y consultas de filtro mientras se realizaron nodos sin conexión y recuperado. Operaciones de inserción de masa de la carga de trabajo cada almacenado 1000 documentos y se realizaron con un índice mientras las agregaciones y consultas de filtro usan un índice independiente que contienen millones de varios documentos. Esto era habilitar el rendimiento de consultas evaluarse por separado desde las inserciones de forma masiva. Cada índice de contenido cinco shards y una réplica.

Las secciones siguientes resumen los resultados de estas pruebas, teniendo en cuenta cualquier degradación del rendimiento mientras un nodo está sin conexión o que se está recuperando y los errores que se han registrado. Los resultados se presentan gráficamente, resaltar los puntos en el que uno o más nodos son falta y estimar el tiempo necesario para que el sistema completo de recuperar y lograr un nivel de rendimiento que había antes de los nodos se desconecta similar.

> [AZURE.NOTE] El sistema de seguridad usado para realizar estas pruebas está disponible en línea. Puede adaptar y utilice estos aprovecha para comprobar la resistencia y la capacidad de recuperación de sus propias configuraciones de clúster. Para obtener más información, vea [ejecutar las pruebas de resistencia Elasticsearch automatizadas][].

## <a name="node-failure-and-restart-with-no-data-loss-results"></a>Error de nodo y reiniciar sin pérdida de datos: resultados

<!-- TODO; reformat this pdf for display inline --> 

Se muestran los resultados de esta prueba en el archivo [ElasticsearchRecoveryScenario1.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario1.pdf). Los gráficos muestran los perfiles de rendimiento de la carga de trabajo y recursos físicos para cada nodo del clúster. La parte inicial de los gráficos se muestran el sistema se ejecuta normalmente durante aproximadamente 20 minutos, momento en que se cierra el nodo 0 durante 5 minutos antes de que se reinicia. Se muestran las estadísticas de un más de 20 minutos; el sistema tarda aproximadamente 10 minutos para recuperar y estabilizar. Esto se muestra en los tipos de transacción y los tiempos de respuesta para los diferentes cargas de trabajo.

Tenga en cuenta los siguientes puntos:

- Durante la prueba, no se detectaron errores. Se ha perdido ningún dato y todas las operaciones se completan correctamente.

- Las tasas de transacción para los tres tipos de operación (inserción masiva, consulta de agregado y consulta de filtro) colocado y los tiempos de respuesta promedio aumenta mientras está desconectado nodo 0.

- Operaciones gradualmente restauradas durante el período de recuperación, las tasas de transacción y tiempos de respuesta de la consulta de agregado y consulta de filtro. El rendimiento de inserción masiva recuperado durante un período breve mientras antes de disminución. Sin embargo, este es probablemente porque el volumen de datos causando el índice utilizado la inserción masiva crezca y se pueden ver las tasas de transacción para realizar esta operación para ralentizar antes de nodo 0 se desconecta.

- El gráfico de utilización de CPU de nodo 0 muestra actividad reducida durante la fase de recuperación, esto es debido a la disco aumentado y actividad de la red causado por el mecanismo de recuperación, el nodo tiene ponerse al día con los datos que ha perdido mientras está sin conexión y actualizar la shards que contiene.

- La shards para los índices no se distribuyen exactamente igual en todos los nodos. Existen dos índices que contiene 5 shards y 1 réplica cada, que hace un total de 20 shards. Dos nodos, por tanto, contendrá 6 shards mientras las otras dos mantenga 7 cada. Esto es evidente en los gráficos de uso de CPU durante el período de 20 minutos inicial, nodo 0 es menos ocupado que los otros dos. Una vez completada la recuperación, algún cambio parece producirse tal como aparece nodo 2 puede convertirse en el nodo de cargado más ligera.

    
## <a name="node-failure-with-catastrophic-data-loss-results"></a>Error de nodo con pérdida de datos grave: resultados

<!-- TODO; reformat this pdf for display inline -->

En el archivo [ElasticsearchRecoveryScenario2.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario2.pdf)se representan los resultados de esta prueba. Como la parte inicial de los gráficos con la primera prueba, muestra el sistema se ejecuta normalmente durante aproximadamente 20 minutos, en qué punto de nodo 0 se cierra durante 5 minutos. Durante este intervalo, se quitan los datos de Elasticsearch en este nodo simulación de pérdida de datos grave, antes de que se reinicia. Recuperación completa aparece tomar 12 y 15 minutos antes de que se restauran los niveles de rendimiento visto antes de la prueba.

Tenga en cuenta los siguientes puntos:

- Durante la prueba, no se detectaron errores. Se ha perdido ningún dato y todas las operaciones se completan correctamente.

- Las tasas de transacción para los tres tipos de operación (inserción masiva, consulta de agregado y consulta de filtro) colocado y los tiempos de respuesta promedio aumenta mientras está desconectado nodo 0. En este momento, el perfil de rendimiento de la prueba es similar a la primera situación. Esto no es sorprendente como, en este punto, los escenarios son los mismos.

- Durante el período de recuperación, se restauraron las tasas de transacción y tiempos de respuesta, aunque durante este periodo había volatilidad mucho más en las cifras. Esto es más probable debido al trabajo adicional que están realizando los nodos en el clúster, que proporciona los datos para restaurar la shards que falta. Este trabajo adicional es evidente en la utilización de la CPU, la actividad del disco y gráficos de actividad de red.

- El gráfico de utilización de CPU para los nodos de 0 y 1 muestra reducida actividad durante la fase de recuperación, que esto es debido a la mayor disco y la actividad de la red causados por el proceso de recuperación. En el primer escenario, solo el nodo que se está recuperando había mostrado este comportamiento, pero en este escenario parece probable que la mayoría de los datos que faltan para nodo 0 se está restaurando del nodo 1.

- La actividad de i/OS de nodo 0 realmente se reduce en comparación con el primer escenario. Esto puede deberse a que la eficacia de i/OS simplemente copiar los datos para un fragmentar completo en lugar de la serie de menores solicitudes de i/OS necesarios para que aparezca una existente fragmentar actualizado.

- La actividad de red para todos los nodos de tres indican ráfagas de actividad como datos transmitidos y recibidos entre los nodos. En el escenario 1, sólo mostrado como cuánta actividad de la red, pero esta actividad nodo 0 parece mantenerse durante un período más largo. De nuevo, esta diferencia puede deberse a la eficacia de transmisión de todos los datos para un fragmentar como una sola solicitud, en lugar de la serie de solicitudes menores recibidas al recuperar un fragmentar.

## <a name="node-failure-and-restart-with-shard-reallocation-results"></a>Error de nodo y reiniciar con reasignación fragmentar: resultados

<!-- TODO; reformat this pdf for display inline -->

El archivo [ElasticsearchRecoveryScenario3.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario3.pdf) muestra los resultados de esta prueba. Como con la primera prueba, la parte inicial de los gráficos de mostrar el sistema se ejecuta normalmente durante aproximadamente 20 minutos, en qué punto de nodo 0 se cierra durante 5 minutos. En este momento, el clúster Elasticsearch intenta volver a crear la shards que falta y equilibrar la shards a través de los nodos restantes. Después de nodo de 5 minutos 0 vuelva a estar en línea y, a continuación, una vez más el clúster tiene a equilibrar la shards. Rendimiento se restaura después de 12 y 15 minutos.

Tenga en cuenta los siguientes puntos:

- Durante la prueba, no se detectaron errores. Se ha perdido ningún dato y todas las operaciones se completan correctamente.

- Quitar las tasas de transacción para los tres tipos de operación (inserción masiva, consulta de agregado y consulta de filtro) y los tiempos de respuesta promedio aumentaron significativamente mientras nodo 0 estaba sin conexión en comparación con las dos pruebas anterior. Esto es debido a la actividad de clúster mayor volver a crear la shards que faltan y volver a equilibrar el clúster como demuestra las cifras elevadas de actividad de red y del disco de nodos 1 y 2 de este período.

- Durante el período después de nodo 0 vuelva a estar en línea, las tasas de transacción y tiempos de respuesta permanecen volátiles.

- El disco y utilización actividad gráficos CPU de acción inicial de nodo 0 muestra muy reducida durante la fase de recuperación. Esto es porque en este momento, el nodo 0 no está atendiendo todos los datos. Tras un periodo de aproximadamente 5 minutos, el nodo realiza ráfagas en acción < RBC: Esto me hizo snort en voz alta. No estoy aproximan con una mejor manera de Diga esto aunque.  >> tal como se muestra en el aumento rápido de red, el disco y la actividad de la CPU. Esto ocurre más probable es que el clúster redistribuir shards en todos los nodos. Nodo de 0 a continuación muestra la actividad normal.
  
## <a name="rolling-updates-results"></a>Distribuir actualizaciones: resultados

<!-- TODO; reformat this pdf for display inline -->

Los resultados de esta prueba, en el archivo [ElasticsearchRecoveryScenario4.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario4.pdf), mostrarán cómo cada nodo está desconectado y, a continuación, volver a iniciar nuevo seguidas. Cada nodo se cierra durante 5 minutos antes de que se reinicia momento en que se detiene la siguiente nodo en secuencia.

Tenga en cuenta los siguientes puntos:

- Mientras se reanude el ciclo de cada nodo, el rendimiento en términos de rendimiento y tiempos de respuesta permanece razonablemente incluso.

- Actividad de disco aumenta para cada nodo en poco tiempo como lo vuelva a estar en línea. Esto es más probable debido a confirmar los cambios que se han producido mientras el nodo hacia abajo el proceso de recuperación.

- Cuando se desconecta un nodo, se producen picos de actividad de la red en los nodos restantes. También se producen picos cuando se reinicia un nodo.

- Una vez reciclado el nodo final, el sistema entra en un período de volatilidad significativa. Esto suele deberse a que el proceso de recuperación necesidad de sincronizar los cambios en todos los nodos y asegúrese de que todas las réplicas y sus correspondientes shards son coherentes. En un momento, esta masa sucesivos de causas esfuerzo insertar operaciones de tiempo de espera y no. Los errores notifican estaba cada caso:

```
Failure -- BulkDataInsertTest17(org.apache.jmeter.protocol.java.sampler.JUnitSampler$AnnotatedTestCase): java.lang.AssertionError: failure in bulk execution:
[1]: index [systwo], type [logs], id [AVEg0JwjRKxX_sVoNrte], message [UnavailableShardsException[[systwo][2] Primary shard is not active or isn't assigned to a known node. Timeout: [1m], request: org.elasticsearch.action.bulk.BulkShardRequest@787cc3cd]]

```

Experimentación subsiguientes mostraba que introducir un retraso de unos minutos entre ciclos cada nodo eliminado este error, por lo que es más probable causados por conflictos entre el proceso de recuperación intentando restaurar varios nodos simultáneamente y la inserción masiva operaciones intentando almacenar miles de documentos.


## <a name="summary"></a>Resumen

Las pruebas realizadas indican:

- Elasticsearch era muy flexible para los modos de error probable que se produzca en un clúster de más comunes.

- Elasticsearch puede recuperar rápidamente si un clúster bien diseñado está sujeto a una pérdida de datos grave en un nodo. Esto puede suceder si configura Elasticsearch para guardar los datos al almacenamiento efímero y el nodo posteriormente reaprovisionar el equipo después de reiniciar. Estos resultados muestran incluso en este caso, están más probable que los riesgos de usar almacenamiento efímero compensado por las ventajas de rendimiento que proporciona esta clase de almacenamiento.

- En las tres primeras situaciones, sin errores de inserción masiva simultáneas, agregación y las cargas de trabajo de consulta de filtro mientras se realizó un nodo sin conexión y recuperado.

- Solo el último escenario había indicado posibles pérdidas de datos y, a continuación, esta pérdida solo afectadas nuevos datos que se va a agregar. Es recomendable en aplicaciones de efectuar la recopilación de datos para mitigar esta probabilidad por reintentar las operaciones de inserción que no han pasado como el tipo de error notificado es muy probable que transitorias.

- Los resultados de la última prueba también mostrarán que si va a realizar el mantenimiento previsto de los nodos en un clúster, rendimiento le vendrá si permite varios minutos entre ciclos de un nodo y la siguiente. En una situación no planeada (por ejemplo, el centro de datos nodos de reciclaje después de realizar una actualización de sistema operativo), tiene menos control sobre cómo y cuándo se toman hacia abajo y se reinicia nodos. El conflicto que se produce cuando intenta Elasticsearch recuperar el estado del clúster después de interrupciones de nodo secuencial puede provocar errores y tiempos de espera. 

[Administrar la disponibilidad de máquinas virtuales]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md
[Las pruebas de resistencia Elasticsearch automatizado]: guidance-elasticsearch-running-automated-resilience-tests.md
