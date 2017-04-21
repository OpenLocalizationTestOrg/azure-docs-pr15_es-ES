<properties
   pageTitle="Ajuste del rendimiento de recopilación de datos para Elasticsearch en Azure | Microsoft Azure"
   description="Cómo maximizar el rendimiento de recopilación de datos con Elasticsearch en Azure."
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

# <a name="tuning-data-ingestion-performance-for-elasticsearch-on-azure"></a>Ajuste del rendimiento de recopilación de datos para Elasticsearch en Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie](guidance-elasticsearch.md). 

## <a name="overview"></a>Información general

Es un aspecto importante al crear una base de datos de búsqueda determinar la mejor manera de estructurar el sistema para recopilar datos de búsqueda rápida y eficaz. No solo la elección de infraestructura en la que implementar el sistema, pero también varias optimizaciones que puede usar para ayudar a garantizar que el sistema puede estar al día con los niveles esperados de flujo de datos refieren a las consideraciones que rodean este requisito. 

Este documento describe las opciones de implementación y la configuración que debe tener en cuenta para implementar un clúster de Elasticsearch que espera un alto índice de recopilación de datos. Para proporcionar datos sólidos con fines ilustrativos, este documento también muestra los resultados de pruebas diferentes configuraciones utilizando una carga de trabajo de recopilación de datos de gran volumen simple. En el [Apéndice](#appendix-the-bulk-load-data-ingestion-performance-test) al final de este documento, se describen los detalles de la carga de trabajo.

El propósito de la referencia era no para generar cifras de rendimiento absoluto para ejecutar Elasticsearch o incluso a recomienda una topología determinada, pero en su lugar para ilustrar métodos que puede usar para evaluar el rendimiento, los nodos de datos de tamaño e implementar clústeres que satisfagan sus propios requisitos de rendimiento. 

Cuando se cambia el tamaño de sus propios sistemas, es importante probar rendimiento completamente en función de sus propios cargas de trabajo. Recopile telemetría que permite obtener información sobre la configuración de hardware óptima para usar y los factores de escala horizontales que debe tener en cuenta. En particular, debe:

- Tenga en cuenta el tamaño total de la carga enviado y no solo el número de elementos en cada bloque inserta solicitud. Un número menor de elementos de masa grandes en cada solicitud podría ser más óptimo que un número mayor, dependiendo de los recursos disponibles para procesar cada solicitud.

Puede supervisar los efectos de variar la solicitud de inserción masiva usando [Marvel](https://www.elastic.co/products/marvel), usando la *readbytes*/*writebytes* i/OS contadores con [JMeter](https://jmeter.apache.org/)y herramientas de sistema operativo como *iostat* y *vmstat* en Ubuntu. 

- Realizar pruebas de rendimiento y recopilar telemetría procesamiento de medida CPU y tiempos de espera de i/OS, latencia de disco, rendimiento y tiempos de respuesta. Esta información puede ayudar a identificar botella potencial y evaluar los costes y las ventajas de usar almacenamiento premium. Tenga en cuenta que uso de CPU y disco puede no ser incluso en todos los nodos dependiendo de la forma en qué shards y réplicas se distribuyen en el clúster (algunos nodos pueden contener más shards que otros).

- Tenga en cuenta cómo el número de solicitudes simultáneas para la carga de trabajo se distribuirá en el clúster y evaluar el impacto de con un número diferente de nodos manejar esta carga de trabajo.

- Considere la posibilidad de cómo pueden aumentar las cargas de trabajo como se expande la empresa. Evaluar el impacto de este crecimiento en los costos de las máquinas virtuales y la utilizan los nodos de almacenamiento.

- Reconocer que usar un clúster con un mayor número de nodos con discos normales puede resultar más económico si su escenario requiere un gran número de solicitudes y la infraestructura de disco mantiene el rendimiento que satisfaga sus contratos de nivel de servicio (SLA). Sin embargo, aumenta el número de nodos puede introducir sobrecarga en forma de sincronización y comunicaciones entre nodos adicionales.

- Comprender que un mayor número de núcleos por nodo puede generar más tráfico de disco que se pueden procesar más documentos. En este caso, medir la utilización del disco para determinar si el subsistema de i/OS puede convertirse en una botella y determinar las ventajas de usar almacenamiento premium.

- Probar y analizar las ventajas y desventajas con un mayor número de nodos con menos núcleos frente a menos nodos con más núcleos. Tenga en cuenta que aumenta el número de réplicas pasa la demanda en el clúster y puede requerir que agregar nodos.

- Tenga en cuenta que con discos efímeros puede requerir que los índices tienen que recuperarse con más frecuencia.

- Medir el uso de volumen de almacenamiento para evaluar la capacidad y en la utilización de almacenamiento. Por ejemplo, en nuestro ejemplo se almacenan con 350GB de almacenamiento de documentos de 1,5 millones.

- Medir la velocidad de transferencia de las cargas de trabajo y considerar cómo cerrar es probable que llegar al límite de transferencia de i/OS tasa para cualquier cuenta de almacenamiento determinado en el que se crearon discos virtuales total.

## <a name="node-and-index-design"></a>Diseño de nodo e índice

En el sistema que debe admitir la recopilación de datos a gran escala, pida a las preguntas siguientes:

- **¿Son los datos relativamente estáticos o mover rápido?** Cuanto más dinámica los datos, mayor será el mantenimiento sobrecarga de Elasticsearch. Si los datos se replican, cada réplica se mantiene sincrónicamente. Datos rápida evolución que tiene solo un ciclo de vida limitado o fácilmente que puede reconstruirse pueden beneficiarse de deshabilitar la replicación por completo. Esta opción se describe en la sección [ajustar la recopilación de datos a gran escala.](#tuning-large-scale-data-ingestion)

- **¿Cómo actualizada ¿necesita los datos encontrados mediante la búsqueda se?** Para mantener el rendimiento, Elasticsearch búferes tantos datos en la memoria como sea posible. Esto significa que no todos los cambios están inmediatamente disponibles para las solicitudes de búsqueda. El proceso que elasticsearch se utiliza para conservar los cambios realizados y que sean visibles se describe en [Persistente de realizar cambios](https://www.elastic.co/guide/en/elasticsearch/guide/current/translog.html#translog). 

    Tasa de datos se hace visible se rige por la *actualizar\_intervalo* configuración del índice relevante. De forma predeterminada, este intervalo se establece en 1 segundo. Sin embargo, no todas las situaciones requieren actualizaciones que se produzca esto rápidamente. Por ejemplo, registrar datos del registro de índices posible que tenga que lidiar con un flujo rápido y continuo de la información que necesita recopilar rápidamente, pero no requiere la información que estén disponibles inmediatamente para las consultas. En este caso, considere la posibilidad de reducir la frecuencia de las actualizaciones de. Esta característica también se describe en la sección [ajustar la recopilación de datos a gran escala.](#tuning-large-scale-data-ingestion)

- **¿Con qué rapidez es los datos más probables ganar?** Capacidad de índice se determina el número de shards especificado cuando se crea el índice. Para permitir el crecimiento, especifique un número suficiente de shards (el valor predeterminado es cinco). Si el índice se crea inicialmente en un único nodo, todos los cinco shards se encuentran en ese nodo, pero que el volumen de datos crece adicional se pueden agregar nodos y Elasticsearch dinámicamente distribuirá shards en todos los nodos. Sin embargo, cada fragmentar tiene una sobrecarga. Todas las búsquedas en un índice consultará todas las shards, para crear una gran cantidad de shards para una cantidad pequeña de datos puede ralentizar recuperaciones de datos (evitar el escenario [Kagillion shards](https://www.elastic.co/guide/en/elasticsearch/guide/current/kagillion-shards.html) ).

    Algunas cargas de trabajo (como el registro) pueden crear un nuevo índice cada día y, a continuación, si observa que el número de shards no es suficiente para el volumen de datos, debe cambiar antes de crear el índice siguiente (índices existentes no se verán afectados). Si debe distribuir los datos existentes en shards más, una opción es indizar la información. Crear un nuevo índice con la configuración adecuada y copiar los datos en él. Este proceso puede hacerse transparente a aplicaciones mediante [alias de índice](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html).

- **¿Qué datos necesita dividir entre los usuarios en un escenario de multitenancy?** Puede crear índices independientes para cada usuario, pero esto puede ser muy costosa si cada usuario solo tiene una cantidad moderada de datos. En su lugar, considere la posibilidad de crear [índices compartidos](https://www.elastic.co/guide/en/elasticsearch/guide/current/shared-index.html) y use el [alias que se basa en filtros](https://www.elastic.co/guide/en/elasticsearch/guide/current/faking-it.html) dirigir solicitudes a los datos por el usuario. Para mantener los datos de un usuario juntos en la misma fragmentar, invalidar la configuración de enrutamiento predeterminado para los datos de índice y distribuir según algunos atributo de identificación del usuario.

- **¿Es datos largos o corta duración?** Si está utilizando un conjunto de máquinas virtuales de Azure para implementar un clúster de Elasticsearch, puede almacenar datos efímeros en un disco de sistema de recurso local en lugar de en una unidad conectada.
Utiliza un SKU VM que utiliza un SSD para el disco de recursos puede mejorar el rendimiento. Sin embargo, cualquier información contenida en el disco del recurso es temporal y se puede perder si la máquina virtual se reinicia (consulte la sección cuando los datos en un temporal unidad ser perdido en la [Descripción de la unidad temporal en Microsoft Azure máquinas virtuales de Windows](http://blogs.msdn.com/b/mast/archive/2013/12/07/understanding-the-temporary-drive-on-windows-azure-virtual-machines.aspx) para obtener más detalles). Si es necesario conservar los datos entre se reinicia, cree discos de datos que contenga esta información y vincularlos a la máquina virtual.

- **¿Cómo activos son los datos?** VHD Azure están sujetos a limitación si la cantidad de actividad de lectura y escritura supera los parámetros especificados (actualmente 500 operaciones de entrada y salida por segundo (IOPS) para un disco conectado a una máquina virtual de nivel estándar y 5000 IOPS para un disco de almacenamiento de Premium). 

    Para reducir las posibilidades de limitación y aumentar el rendimiento, considere la posibilidad de crear varios discos de datos para cada VM y configure Elasticsearch a los datos de las bandas en estos discos como se describe en el [disco y el archivo requisitos del sistema](guidance-elasticsearch-running-on-azure.md#disk-and-file-system-requirements).

    Debe seleccionar una configuración de hardware que ayuda a reducir el número de operaciones de lectura por asegurarse de que está disponible para los datos de caché que se accede con frecuencia suficiente memoria de i/OS de disco. Esto se describe en la sección [requisitos de memoria](guidance-elasticsearch-running-on-azure.md#memory-requirements) de Elasticsearch ejecuta en Azure.

- **¿Qué tipo de carga de trabajo necesitarán cada nodo admitir?** Elasticsearch prestaciones de tener la memoria disponible para los datos en caché (en el formulario de la caché del sistema de archivos) y para el montón JVM, como se describe en la sección [requisitos de memoria](guidance-elasticsearch-running-on-azure.md#memory-requirements) de Elasticsearch ejecuta en Azure. 

    La cantidad de memoria, número de núcleos de CPU y la cantidad de discos disponibles están establecidos por el SKU de la máquina virtual. Para obtener más información, consulte [Precios máquinas virtuales](http://azure.microsoft.com/pricing/details/virtual-machines/) en el sitio Web de Azure.

### <a name="virtual-machine-options"></a>Opciones de máquina virtual

Pueden aprovisionar máquinas virtuales en Azure con un número de SKU diferente. Los recursos disponibles para una máquina virtual de Azure dependen de SKU seleccionado. Cada SKU ofrece una combinación diferente de núcleos, memoria y almacenamiento. Debe seleccionar un tamaño apropiado de máquina virtual que va a controlar la carga de trabajo esperada, sino que también de demostrar rentable.
Empezar con una configuración que cumpla los requisitos actuales (realizar pruebas para probar, como se describe más adelante en este documento). Puede escalar un clúster más adelante agregando más máquinas virtuales ejecutando Elasticsearch nodos.

[Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-linux-sizes.md) en el sitio Web de Azure documenta las diversas opciones y SKU disponibles para máquinas virtuales.

Debe hacer coincidir el tamaño y recursos de una máquina virtual para la función que se ejecuta en la máquina virtual de nodos realizará.

Para un nodo de datos:

- Asignar hasta 30 GB o 50% de la memoria RAM disponible a Java montón, la menor. Deje el resto del sistema operativo que se utilizará para el almacenamiento en caché de archivos. Si está utilizando Linux, puede especificar la cantidad de memoria para asignar al montón Java estableciendo el ES\_MONTÓN\_variable del tamaño del entorno antes de ejecutar Elasticsearch. Como alternativa, si está utilizando Windows o Linux, puede establecer tamaño de la memoria con los parámetros *Xmx* y *Xms* al iniciar Elasticsearch.

    Dependiendo de la carga de trabajo, es podrán que no estén tan eficaces rendimiento como con un mayor número de máquinas virtuales moderadamente tamaños menos máquinas virtuales grandes. Debe realizar pruebas que pueden medir las compensaciones entre el tráfico de red adicional y el mantenimiento que implica frente a los costos de lo que aumenta el número de núcleos disponibles y los conflictos de disco reducida en cada nodo.

- Usar el almacenamiento de premium para almacenar los datos de Elasticsearch. Esto se explica con más detalle en la sección [Opciones de almacenamiento](#storage-options) .

- Usar varios discos del mismos tamaño y bandas los datos en estos discos. El SKU de sus máquinas virtuales determinará el número máximo de discos de datos que puede adjuntar. Para obtener más información, consulte [requisitos del sistema de disco y el archivo](guidance-elasticsearch-running-on-azure.md#disk-and-file-system-requirements).

- Use un SKU de CPU multinúcleo con al menos 2 núcleos, preferentemente 4 o más. 

Para un nodo de cliente:

- No se asignar el almacenamiento en disco para datos Elasticsearch, clientes dedicados no almacenan datos en el disco.

- Asegúrese de que la memoria adecuada está disponible para manejar las cargas de trabajo. Solicitudes de insertar masa se leen en memoria antes de los datos que se envían a los nodos de datos distintos y se acumulan los resultados de agregaciones y consultas en memoria antes de que se devuelven a la aplicación cliente. Evaluar sus propio cargas de trabajo y supervisar el uso de la memoria mediante una herramienta como Marvel o la [información de JVM](https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_jvm_section) devuelta por el *nodo estadísticas* API (`GET _nodes/stats`) para evaluar los requisitos óptimos.  En particular, supervisar la *montón\_utiliza\_porcentaje* métrica para cada nodo y objetivo para mantener el tamaño del montón por debajo del 75% del espacio disponible.

- Garantizar que suficiente núcleos de CPU estén disponibles para recibir y procesar el volumen de solicitudes esperado.
Solicitudes en la cola como se reciben antes de la transformación y, a continuación, el volumen de los elementos que pueden poner en cola es una función del número de núcleos de CPU en cada nodo. Puede supervisar las longitudes de cola con los datos en la [información de grupo de subprocesos](https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_threadpool_section) devuelta por las estadísticas de nodo API. 

    Si el recuento de *rechazado* de una cola indica que se que se rechacen las solicitudes, que indica que el clúster está empezando a frenar. Esto puede deberse a ancho de banda de CPU, pero puede también deberse a otros factores como la falta de memoria o reducir el rendimiento de i/OS, entonces usar esta información junto con otras estadísticas para ayudar a determinar la causa.

    Los nodos cliente pueden o no pueden ser necesarios, dependiendo de las cargas de trabajo. Cargas de trabajo de recopilación de datos no suelen beneficiarse del uso de los clientes dedicados, mientras que algunas búsquedas y agregaciones pueden ejecutar más rápidamente. Prepárese comparar sus propios escenarios.

    Los nodos cliente principalmente son útiles para las aplicaciones que utilizan la API de cliente de transporte para conectar con el clúster. También puede usar la API de cliente de nodo, que se crea dinámicamente un cliente dedicado para la aplicación con los recursos del entorno de host de aplicación. Si las aplicaciones utilizan la API de cliente de nodo, puede no ser necesario para el clúster que contenga los nodos de cliente dedicado preconfigurados. 
    
    Sin embargo, tenga en cuenta que un nodo creado con la API de nodo del cliente es un miembro del clúster de primera clase y así participa en la charla de red con otros nodos. Iniciar y detener los nodos cliente pueden crea a menudo ruido innecesario en todo el clúster.

Un nodo maestro:

- No se asignar el almacenamiento en disco para datos Elasticsearch, nodos maestros dedicados no almacenan datos en disco.

- Requisitos de CPU deben ser mínimos.

- Requisitos de memoria dependen del tamaño del clúster. Información sobre el estado del clúster se conserva en memoria. Para clústeres pequeños es mínima la cantidad de memoria necesaria, pero para un clúster de gran volumen y altamente activo donde se crean los índices con frecuencia y shards moverse por, la cantidad de información de estado se puede aumentar considerablemente. Supervisar el tamaño del montón JVM para determinar si necesita agregar más memoria.

> [AZURE.NOTE]Confiabilidad de clúster, siempre crear varios nodos maestros para y configurar el resto de los nodos para evitar la posibilidad de división cerebro que se produzca una. Lo ideal es que, debe ser un número impar de nodos maestros. En este tema se describe con más detalle en [configuración resistencia y recuperación de Elasticsearch en Azure][].

### <a name="storage-options"></a>Opciones de almacenamiento

Existen varias opciones de almacenamiento en máquinas virtuales de Azure con diferentes ventajas y desventajas que ello afecte de costo, rendimiento, disponibilidad y recuperación que debe considerar con cuidado.

Tenga en cuenta que debe almacenar datos Elasticsearch en discos de datos dedicado.  Esto le ayudará a reducir los conflictos con el sistema operativo y asegúrese de que grandes volúmenes de Elasticsearch i/OS no competir con funciones de sistema operativo para recursos de i/OS.

Discos Azure están sujetos a las restricciones de rendimiento. Si encuentra que un clúster por pasa periódicos ráfagas de actividad podrán reducirá las solicitudes de i/OS. Para evitar esto, ajustar el diseño de saldo el tamaño del documento en Elasticsearch contra el volumen de solicitudes es probable que se recibió por cada disco.

Disco en función de almacenamiento estándar admite una tasa de solicitud máximo de 500 IOPS mientras que pueden funcionar discos basados en el almacenamiento de premium a hasta 5.000 IOPS, dependiendo del tamaño de los discos de datos. Discos de almacenamiento de Premium solo están disponibles para la serie DS y GS de máquinas virtuales. Para buscar el disco máximo IOPS por tamaño VM, vea [tamaños para máquinas virtuales de Azure](../virtual-machines/virtual-machines-linux-sizes.md). Rendimiento en el almacenamiento de premium es determinado por el tamaño de la memoria virtual junto con la asignación de tamaño de disco. Para obtener más información, vea [almacenamiento Premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina Virtual de Azure](../storage/storage-premium-storage.md).

**Discos de datos persistentes**

Los discos de datos persistentes son VHD que usan el almacenamiento de Azure. Si la máquina virtual debe reconstruirse después de un error principal, se conecta fácilmente VHD existente a la nueva máquina virtual. VHD pueden crearse en función de almacenamiento estándar (que gira media) o almacenamiento premium (SSDs). Si desea usar SSDs debe crear máquinas virtuales con la serie DS o mejor. Costo de equipos DS igual que las VM D serie equivalentes, pero le cobrarán adicionales para el uso de almacenamiento de premium.

En casos donde la velocidad de transferencia máxima por disco es suficiente para permitir la carga de trabajo esperada, considerar la creación de varios discos de datos y permitir que Elasticsearch [los datos de las bandas en estos discos](guidance-elasticsearch-running-on-azure.md#disk-and-file-system-requirements)o implementar [RAID 0 bandas con discos virtuales](../virtual-machines/virtual-machines-linux-configure-raid.md)de nivel de sistema.

> [AZURE.NOTE]Experiencia de Microsoft ha mostrado que utilizar RAID 0 es especialmente útil para los efectos de i/OS de suavizado *spiky* cargas de trabajo que generan ráfagas frecuentes de actividad.

Premium uso localmente redundante (o localmente redundantes para inferiores o cargas de trabajo de control de calidad) almacenamiento de la cuenta de almacenamiento manteniendo los discos; replicar en ubicaciones geográficas y zonas no es necesario para Elasticsearch alta disponibilidad. 

**Discos efímeros**

Mediante los discos persistentes basados en SSDs requiere la creación de máquinas virtuales que admiten el almacenamiento de premium. Esto tiene las implicaciones de precio. Usando el disco efímero local para almacenar datos Elasticsearch puede ser una solución rentable para nodos moderadamente tamaños que requieren hasta aproximadamente 800 GB de almacenamiento. En la serie D estándar de máquinas virtuales, discos efímeros se implementan mediante SSDs que proporcionan mayor rendimiento y latencia mucho más baja que los discos normales

Al utilizar Elasticsearch, el rendimiento puede ser equivalente a usar el almacenamiento de premium sin el costo: vea la sección [problemas de latencia de disco de direcciones](#addressing-disk-latency-issues) para obtener más información.

El tamaño de la máquina virtual limita la cantidad de espacio disponible en almacenamiento efímero como se describe en la entrada de blog [Serie D las expectativas de rendimiento](https://azure.microsoft.com/blog/d-series-performance-expectations/).

Por ejemplo, un estándar\_D1 VM proporciona 50GB de almacenamiento efímero, un estándar\_D2 VM tiene un estándar y 100GB de almacenamiento efímero\_D14 VM proporciona 800GB de espacio efímero. Usar una máquina virtual de serie D con almacenamiento efímero puede ser rentable clústeres donde nodos sólo requieren esta cantidad de espacio.

Debe equilibrar el rendimiento mejorado almacenamiento efímeros con el tiempo y los costos implicados en la recuperación de datos después de reiniciar el equipo. El contenido del disco efímero se pierden si se mueve la máquina virtual a un servidor de host diferente, si se actualiza el host, o si el host experimenta un error de hardware. Si los datos en sí tienen un ciclo de vida limitado esta pérdida de datos posible tolerable. Para los datos más antiguos, es posible generar un índice o recuperar la información que falta una copia de seguridad. Es posible minimizar la posibilidad de pérdida utilizando réplicas llevan a cabo en otras máquinas virtuales.

> [AZURE.NOTE]No use una **sola** máquina virtual para mantener los datos de producción crítica. Si se produce un error en el nodo, todos los datos no está disponible. Para obtener información esencial, asegúrese de que los datos se repliquen en al menos un nodo.

**Archivos de Azure**

El [Servicio de archivo de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx) proporciona acceso a los archivos compartidos con el almacenamiento de Azure. Puede crear recursos compartidos de archivos, a continuación, puede montaje en máquinas virtuales de Azure. Varios VM pueden montaje el mismo recurso compartido de archivos, lo que les permite tener acceso a los mismos datos.

Por motivos de rendimiento, no se recomienda que use recursos compartidos de archivos de almacenamiento de datos de Elasticsearch que no es necesario que pueden compartirse entre nodos, discos de datos normal son más adecuados para ello. Recursos compartidos de archivos pueden utilizarse para crear Elasticsearch [índices de réplica de sombra](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-shadow-replicas.html). Sin embargo, esta característica está actualmente experimentación y no se implementa en un entorno de producción en este momento. Por este motivo, no se consideran más adelante en esta guía índices de sombra.

**Opciones de red**

Azure implementa un esquema de red compartido. Máquinas virtuales utilizando el mismo hardware bastidores compitan por los recursos de red. Por tanto ancho de banda de red disponibles puede variar según la hora del día y el día de ciclo de trabajo que se ejecutan en máquinas virtuales de uso compartido de la misma infraestructura de red física. Tiene poco control sobre estos factores. Es importante conocer el rendimiento de la red es probable que fluctúan con el tiempo, así que las expectativas del usuario en consecuencia.

## <a name="scaling-up-nodes-to-support-large-scale-data-ingestion"></a>Ajuste de escala de los nodos para que admita la recopilación de datos a gran escala

Puede crear clústeres Elasticsearch utiliza hardware razonablemente moderado y escalar, a continuación o escalar a medida que crece el volumen de datos y aumenta el número de solicitudes. Con Azure, escalado ejecutando en máquinas virtuales más grandes y más caras, o puede escalado usando máquinas virtuales más pequeñas y económicos adicionales. 

También puede realizar una combinación de ambas estrategias. No hay ninguna solución única para todos los escenarios para evaluar el mejor enfoque para dada la situación que debe estar preparado para llevar a cabo un rendimiento serie pruebas.

Esta sección está relacionada con el enfoque de escalado vertical, escalado se describe en la sección [ampliar: conclusiones](#scaling-out-conclusions).
Esta sección describen los resultados de una serie de pruebas que se realizaron en un conjunto de clústeres Elasticsearch que contienen máquinas virtuales de varios tamaños. Los clústeres se hayan designado como pequeño, mediano y grande. La siguiente tabla resume los recursos asignados a las máquinas virtuales de cada clúster.

| Clúster | SKU DE VM      | Número de núcleos | Número de discos de datos | RAM  |
|---------|-------------|-----------------|----------------------|------|
| Pequeña   | D2 estándar | 2               | 4                    | 7GB  |
| Medio  | D3 estándar | 4               | 8                    | 14GB |
| Grande   | D4 estándar | 8               | 16                   | 28GB |

Cada clúster Elasticsearch contenidos 3 nodos de datos. Estos nodos de datos administran las solicitudes de cliente, así como tratamiento de procesamiento de datos. No se utilizaron los nodos cliente independiente porque ofreció poco beneficio para el escenario de recopilación de datos usado por las pruebas. El clúster también contiene tres nodos maestros, uno de los cuales se ha optado por Elasticsearch para coordinar el clúster.

Las pruebas se realizaron utilizando Elasticsearch 1.7.3. Las pruebas se realizaron inicialmente en clústeres que ejecutan Ubuntu Linux 14.0.4 y, a continuación, repiten usando Windows Server 2012. Los detalles de la carga de trabajo realizado por las pruebas se describen en el [Apéndice](#appendix-the-bulk-load-data-ingestion-performance-test).

### <a name="data-ingestion-performance--ubuntu-linux-1404"></a>Rendimiento de recopilación de datos: Ubuntu Linux 14.0.4

La siguiente tabla resume los resultados de las pruebas para dos horas para cada configuración generales:

| Configuración | Recuento de ejemplo | Tiempo medio de respuesta (ms) | Rendimiento (operaciones/s) |
|---------------|--------------|----------------------------|---------------------------|
| Pequeña         | 67057        | 636                        | 9.3                       |
| Medio        | 123482       | 692                        | 17.2                      |
| Grande         | 197085       | 839                        | 27,4                      |

El rendimiento y el número de muestras procesan para las tres configuraciones están en la relación aproximada 1:2:3. Sin embargo, los recursos disponibles en cuanto a memoria, núcleos de CPU y discos tienen la relación 1:2:4. Se rotulador sea la pena investigar los detalles de bajo nivel de rendimiento de los nodos en el clúster para determinar por qué esto puede suceder. Esta información puede ayudarle a determinar si hay límites al escalado y cuando es mejor tener en cuenta el escalado. 

### <a name="determining-limiting-factors-network-utilization"></a>Determinar la limitación de factores: uso de la red

Elasticsearch depende de tener suficiente ancho de banda de red para admitir el flujo de solicitudes de cliente, así como la información de sincronización que fluye entre los nodos en el clúster. Anteriormente, como resaltados tienen limitada control sobre la disponibilidad de ancho de banda, que depende de muchas variables, como el centro de datos en uso y la carga actual de la red de las demás VM la misma infraestructura de red de uso compartido. Sin embargo, conviene aún examinar la actividad de red para cada clúster comprobar que el volumen de tráfico no es excesivo. El siguiente gráfico muestra una comparación del tráfico de red recibido por el nodo 2 en cada uno de los clústeres (los volúmenes de los demás nodos de cada clúster fue muy similar).

![](media/guidance-elasticsearch/data-ingestion-image1.png)

El promedio de bytes recibido por segundo para nodo 2 en cada configuración de clúster durante el período de dos horas fueron los siguientes:

| Configuración | Promedio de/seg. |
|---------------|--------------------------------------|
| Pequeña         | 3993640.3                            |
| Medio        | 7311689.9                            |
| Grande         | 11893874.2                           |

Las pruebas se realizaron mientras se ejecuta el sistema en **estado de equilibrio**. En situaciones donde el índice volver a equilibrar o se produce la recuperación de nodo, las transmisiones de datos entre los nodos manteniendo principal y shards de réplica pueden generar tráfico de red significativo. Los efectos de este proceso se describen más en el documento [configuración resistencia y recuperación de Elasticsearch en Azure][].

### <a name="determining-limiting-factors-cpu-utilization"></a>Determinar la limitación de factores: utilización de la CPU

La tasa a la que se administran las solicitudes al menos parcialmente se rige por la capacidad de procesamiento disponible. Elasticsearch acepta solicitudes de insertar masivas en masa insertar cola. Cada nodo tiene un conjunto de masa insertar colas determinadas por el número de procesadores disponibles. De forma predeterminada, hay una cola para cada procesador y cada cola puede contener hasta 50 solicitudes pendientes antes de que comiencen a rechazar. 

Aplicaciones deben enviar las solicitudes en una tasa que no se producen las colas de overspill. El número de elementos en cada una de ellas en cualquier momento va a ser una función de la tasa a la que se envían las solicitudes de aplicaciones cliente y la velocidad a la que se recupera y se procesan Elasticsearch estas mismas solicitudes. Por este motivo, una estadística importante genera preocupaciones la tasa de error resumidos en la tabla siguiente.

| Configuración | Ejemplos de total | Recuento de errores  | Tasa de error |
|---------------|---------------|--------------|------------|
| Pequeña         | 67057         | 0            | 0,00%      |
| Medio        | 123483        | 1            | % 0.0008    |
| Grande         | 200702        | 3617         | 1,8%      |

Cada uno de estos errores produjo la siguiente excepción de Java:

```
org.elasticsearch.action.support.replication.TransportShardReplicationOperationAction$PrimaryPhase$1@75a30c1b]; ]
[219]: index [systembase], type [logs], id [AVEAioKb2TRSNcPa_8YG], message [RemoteTransportException[[esdatavm2][inet[/10.0.1.5:9300]][indices:data/write/bulk[s]]]; nested: EsRejectedExecutionException[rejected execution (queue capacity 50)
```

Aumentar el número de colas y la longitud de cada cola puede reducir el número de errores, pero este método solo puede hacer frente a saber de corta duración. Hacerlo mientras se ejecuta una serie de tareas de recopilación de datos constante simplemente retrasará el punto en el que se errores de inicio que se producen. Además, este cambio no mejorará el rendimiento y es probable que dañen el tiempo de respuesta de las aplicaciones cliente como solicitudes se pondrán en cola para ya antes de que se está procesando.

La estructura de índice predeterminada de 5 shards con 1 réplica (10 shards en todos), da como resultado un descuadre pequeña de carga entre los nodos en un clúster de dos nodos contendrá tres shards mientras que el otro nodo contendrá cuatro. Es probable que sea el elemento que restringe el rendimiento al máximo, que es el motivo por el nodo más ocupado este nodo se ha seleccionado en cada caso. 

El siguiente conjunto de gráficos muestran el uso de CPU para el nodo más ocupado de cada clúster.

![](media/guidance-elasticsearch/data-ingestion-image2.png)

![](media/guidance-elasticsearch/data-ingestion-image3.png)

![](media/guidance-elasticsearch/data-ingestion-image4.png)

Para el pequeño, mediano y grandes clústeres, el uso de CPU promedio para estos nodos fue 75.01%, 64.93% y 64.64%. Casi nunca utilización alcanzó realmente 100% utilización y quita, así como el tamaño de los nodos y el disponible CPU power disponible aumenta. Capacidad de CPU, por tanto, es poco probable que ser un factor limitar el rendimiento del clúster grande.

### <a name="determining-limiting-factors-memory"></a>Determinar la limitación de factores: memoria

Uso de memoria es otro aspecto importante que puede influir en el rendimiento. Para las pruebas, Elasticsearch se ha asignado el 50% de la memoria disponible. Esto es en línea con [documentado recomendaciones](https://www.elastic.co/guide/en/elasticsearch/guide/current/heap-sizing.html#_give_half_your_memory_to_lucene). Mientras se ejecutan las pruebas, JVM se supervisa basura exceso colección (indicación de falta de memoria del montón). En todos los casos, el tamaño del montón era estable y JVM había mostrado la recopilación de basura baja. La siguiente captura de pantalla muestra una instantánea de Marvel, resaltar la clave de estadísticas JVM durante un período corto mientras se ejecuta la prueba en el clúster grande.

![](media/guidance-elasticsearch/data-ingestion-image5.png)

***JVM memoria basura colección la actividad y en el clúster grande.***

### <a name="determining-limiting-factors-disk-io-rrates"></a>Determinar la limitación de factores: i/OS rRates en disco

La característica física restante en el servidor que puede restringir el rendimiento es el rendimiento del subsistema de i/OS de disco. El gráfico siguiente compara la actividad del disco en términos de bytes escritos para los nodos de cada clúster más ocupados.

![](media/guidance-elasticsearch/data-ingestion-image6.png) 

La siguiente tabla muestra el promedio de bytes escrito por segundo para nodo 2 en cada configuración de clúster durante el período de dos horas:

| Configuración | Promedio de bytes escritos/seg. |
|---------------|-------------------------------------|
| Pequeña         | 25502361.94                         |
| Medio        | 48856124.5                          |
| Grande         | 88137675.46                         |

Aumenta el volumen de los datos escritos con el número de solicitudes procesando en un clúster, pero las tasas de i/OS dentro de los límites de almacenamiento de Azure (discos creados mediante el almacenamiento de Azure pueden admitir una constante tasas 10s a 100s de MB/s, dependiendo de si se usa almacenamiento estándar o Premium). Examinar la cantidad de tiempo invertido esperando disco i/OS ayuda a que explique por qué es el rendimiento del disco por debajo de su máximo teórico. Los gráficos y la siguiente tabla muestran estas estadísticas para los mismos tres nodos:

> [AZURE.NOTE]El tiempo de espera de disco se mide controlando el porcentaje de tiempo durante el cual se bloquean procesadores esperar operaciones de i/OS completar la CPU.

![](media/guidance-elasticsearch/data-ingestion-image7.png)

![](media/guidance-elasticsearch/data-ingestion-image8.png)

![](media/guidance-elasticsearch/data-ingestion-image9.png)

| Configuración | Tiempo de CPU (%) de espera de disco promedio |
|---------------|--------------------------------|
| Pequeña         | 21.04                          |
| Medio        | 14.48                          |
| Grande         | 15.84                          |

Estos datos indican que se ha invertido en una proporción significativa del tiempo de CPU (entre 16% y 21%) esperar disco i/OS completar. Esto es restringir la capacidad de Elasticsearch para procesar solicitudes y almacenar datos.

Durante la ejecución de prueba, el clúster grande insertado más de **cinco 100 millones de documentos**. Permitir la prueba continuar mostraba que los tiempos de espera aumentaron significativamente cuando la base de datos contiene más de seis 100 millones documentos. Las razones para este comportamiento no se investigación completamente, pero puede estar debido a disco fragmentación causando latencia de disco. 

Aumentar el tamaño del clúster sobre más nodos puede ayudar a reducir los efectos de este comportamiento. En casos extremos puede ser necesario desfragmentar un disco que se muestra tiempos de i/OS excesivo. Sin embargo, desfragmentar un disco grande puede tardar un tiempo considerable (posiblemente más de 48 horas para una unidad de disco duro virtual de 2TB) y simplemente a formatear la unidad y permitir Elasticsearch recuperar los datos que faltan de shards de réplica podrían ser el método más rentable.

### <a name="addressing-disk-latency-issues"></a>Solución de problemas de latencia de disco

Las pruebas se realizaron inicialmente utilizando máquinas virtuales configuradas con discos estándares. Un disco estándar se basa en la que gira multimedia y como resultado está sujeto a latencia de rotación y otro botella que puede restringir los tipos de i/OS. Azure también proporciona almacenamiento premium en que se crean discos con SSD dispositivos. Estos dispositivos no tienen latencia de rotación y como resultado, deben proporcionar la velocidad de i/OS mejorada. 

La siguiente tabla compara los resultados de reemplazo de discos estándares con discos premium en el clúster grande (el VM D4 estándar en el clúster grande se reemplazaron por VM DS4 estándar; el número de núcleos, memoria y discos era el mismo en ambos casos, la única diferencia es que las VM DS4 utiliza SSDs).

| Configuración    | Recuento de ejemplo | Tiempo medio de respuesta (ms) | Rendimiento (operaciones/s) |
|------------------|--------------|----------------------------|---------------------------|
| Grandes - estándar | 197085       | 839                        | 27,4                      |
| Grandes - Premium  | 255985       | 581                        | 35,6                      |

Tiempos de respuesta eran considerablemente mejorados, lo que un rendimiento medio mucho más cerca a 4 x de clúster pequeño. Esto es más en línea con los recursos disponibles en una máquina virtual de DS4 estándar. Promedio de la CPU en el nodo más ocupado en el clúster (nodo 1 en este caso) aumentada según lo invertido menos tiempo esperando i/OS completar:

![](media/guidance-elasticsearch/data-ingestion-image10.png)

La reducción de tiempo de espera de disco resulte cuando considere el siguiente gráfico, que muestra que el nodo más ocupado esta estadística colocado a alrededor de un 1% en promedio:

![](media/guidance-elasticsearch/data-ingestion-image11.png)

Hay un precio para esta mejora, sin embargo. El número de errores de recopilación aumentado un factor de 10 a 35797 (12.3%). De nuevo, la mayoría de estos errores eran el resultado de masa insertar desbordamiento de cola. Dado que el hardware ahora se está ejecutando cerca de capacidad, puede ser necesario agregar más nodos o moderar la tasa de inserciones masivas para reducir el volumen de errores. Estos problemas se explican más adelante en este documento.

### <a name="testing-with-ephemeral-storage"></a>Probar con almacenamiento efímero

Las mismas pruebas se han repetido en un clúster de máquinas virtuales de D4 mediante almacenamiento efímero. En máquinas virtuales de D4, almacenamiento efímero se implementa como una sola 400GB SSD. El número de muestras procesadas, el tiempo de respuesta y el rendimiento estaba todo muy similar a las cifras notificadas para el clúster basado en máquinas virtuales de DS14 con almacenamiento premium.

| Configuración                     | Recuento de ejemplo | Tiempo medio de respuesta (ms) | Rendimiento (operaciones/s) |
|-----------------------------------|--------------|----------------------------|---------------------------|
| Grandes - Premium                   | 255985       | 581                        | 35,6                      |
| Grande – estándar (disco efímero) | 255626       | 585                        | 35,5                      |

El tipo de error también era similar (33862 errores de solicitudes de 289488 en total: 11.7%).

Los gráficos siguientes muestran el uso de CPU y disco esperar estadísticas para el nodo más ocupado en el clúster (nodo 2 este momento):

![](media/guidance-elasticsearch/data-ingestion-image12.png)

! [] (media/guidance-elasticsearch/data-ingestion-image13.png

En este caso, en términos de rendimiento por sí sola, mediante almacenamiento efímero podría considerarse una solución más rentable que el uso de almacenamiento de premium.

### <a name="data-ingestion-performance--windows-server-2012"></a>Rendimiento de recopilación de datos: Windows Server 2012

Las mismas pruebas se repiten con un conjunto de Elasticsearch clústeres con nodos que ejecutan Windows Server 2012. El propósito de estas pruebas era establecer qué efectos, si la hay, la elección de sistema operativo puede tener en el rendimiento del clúster.

Para ilustrar la escalabilidad de Elasticsearch en Windows, la siguiente tabla muestra el rendimiento y tiempos de respuesta logrados para pequeño, mediano y configuraciones de clúster grandes. Tenga en cuenta que estas pruebas se realizaron con Elasticsearch configurado para utilizar el almacenamiento efímero SSD, como las pruebas con Ubuntu tenían se muestra que es probable que sea un factor clave para lograr un rendimiento máximo latencia de disco:

| Configuración | Recuento de ejemplo | Tiempo medio de respuesta (ms) | Rendimiento (operaciones/s) |
|---------------|--------------|----------------------------|---------------------------|
| Pequeña         | 90295        | 476                        | 12.5                      |
| Medio        | 169243       | 508                        | 23,5                      |
| Grande         | 257115       | 613                        | 35,6                      |

Estos resultados indicar cómo Elasticsearch escala con el tamaño de la máquina virtual y los recursos disponibles en Windows.

Las tablas siguientes se comparan los resultados para el clúster grande Ubuntu y Windows:

| Sistema operativo | Recuento de ejemplo | Tiempo medio de respuesta (ms) | Rendimiento (operaciones/s) | Tasa de error (%) |
|------------------|--------------|----------------------------|---------------------------|----------------|
| Ubuntu           | 255626       | 585                        | 35,5                      | 11.7           |
| Windows          | 257115       | 613                        | 35,6                      | 7.2            |

El rendimiento fue coherente con la gran clústeres Ubuntu, aunque el tiempo de respuesta era ligeramente superior. Esto puede tener en cuenta por la tasa de error inferior (los errores se notifican más rápidamente que las operaciones correctas, así que tiene un tiempo de respuesta inferior).

El uso de CPU notificado por las herramientas de supervisión de Windows era ligeramente superior de Ubuntu. Sin embargo, debe tratar las comparaciones directas de medidas como estos en sistemas operativos con precauciones debido a la forma distintos sistemas operativos informar estas estadísticas. Además, información sobre la latencia de disco en términos de CPU tiempo esperando i/OS no está disponible en la misma manera como para Ubuntu. El punto importante es que la CPU fue alta, que indica que tiempo de espera usado para i/OS fue bajo:

![](media/guidance-elasticsearch/data-ingestion-image14.png)

### <a name="scaling-up-conclusions"></a>Escalar: conclusiones

Rendimiento Elasticsearch para un clúster bien ajustado es probable que sea equivalente en Windows y Ubuntu y que lo escalas de seguridad en un patrón similar en ambos sistemas operativos. Para mejorar el rendimiento, **Utilice premium almacenamiento de almacenamiento de datos de Elasticsearch**.

## <a name="scaling-out-clusters-to-support-large-scale-data-ingestion"></a>Escalado clústeres para admitir la recopilación de datos a gran escala

Escalado es el enfoque gratuito al escalado investigado en la sección anterior. Una característica importante de Elasticsearch es la escalabilidad horizontal inherente integrada en el software. Aumentar el tamaño de un clúster es simplemente cuestión de agregar más nodos. No es necesario realizar ninguna operación manual para redistribuir índices o shards como estas tareas se administran automáticamente, aunque hay varias opciones de configuración que puede usar para influir en el proceso. 

Agregar más nodos ayuda a mejorar el rendimiento al distribuir la carga entre más maquinaria. Cuando agregue más nodos, también debe considerar la indización de datos para aumentar el número de shards disponibles. Puede tener preferencia sobre este proceso hasta cierto punto mediante la creación de índices que tienen shards más que hay nodos disponibles inicialmente. Cuando más se agregan los nodos, pueden distribuirse el shards.

Además de aprovechar la escalabilidad horizontal de Elasticsearch, existen otras razones para implementar índices que tienen más shards de nodos. Cada fragmentar se implementa como una estructura de datos independiente (un índice [Lucene](https://lucene.apache.org/) ) y tiene sus propio mecanismos internos para mantener la coherencia y controlar la simultaneidad. Crear varias shards ayuda a aumentar paralelismo dentro de un nodo y puede mejorar el rendimiento. 

Sin embargo, mantener el rendimiento mientras escala es un acto de equilibrio. Los nodos más y shards que contiene un clúster, el esfuerzo más es necesario para sincronizar el trabajo realizado por el clúster, que puede reducir el rendimiento. Para cualquier carga de trabajo determinada, hay una configuración óptima que aumente el rendimiento de recopilación y minimizar la sobrecarga de mantenimiento. Esta configuración depende en gran medida la naturaleza de la carga de trabajo y el clúster; más concretamente, el volumen, tamaño y contenido de los documentos, la tasa a la que se produce la recopilación y el hardware en el que se ejecuta el sistema.  

Esta sección resumen los resultados de investigaciones en tamaño de clústeres está pensados para ser compatible con la carga de trabajo usado por las pruebas de rendimiento que se ha descrito anteriormente. Se realizó la misma prueba en clústeres con VM basadas en el gran VM tamaño (D4 estándar con 8 núcleos de CPU, 16 discos de datos y 28GB de RAM) se ejecuta Ubuntu Linux 14.0.4, pero configurado con un número diferente de nodos y shards. Los resultados no están destinados a ser definitivos como se aplican solo a un escenario específico, pero puede actuar como un buen punto de partida para ayudarle a analizar la escalabilidad horizontal de los clústeres y generar los números de la relación óptima de shards a los nodos que mejor se adapten a sus propios requisitos.

### <a name="baseline-results--3-nodes"></a>Resultados de línea base: 3 nodos

Para obtener una ilustración de línea base, la prueba de rendimiento de recopilación de datos se ejecute en un clúster de 3 nodo con 5 shards y 1 réplica. Esta es la configuración predeterminada de un índice de Elasticsearch. En esta configuración, Elasticsearch distribuye 2 shards principales a 2 de los nodos y las restantes partes principales se almacenan en el tercer nodo. La siguiente tabla resume el rendimiento de las operaciones de recopilación de forma masiva por segundo y el número de documentos que se almacenaron correctamente la prueba.

> [AZURE.NOTE] En las tablas siguientes de esta sección, se presenta la distribución de la shards principales como un número para cada nodo separado por guiones. Por ejemplo, se describe el diseño de nodo 3 5 fragmentar como 2-2-1. No se incluye el diseño de shards de réplica. Se siguen un esquema similar a la shards principales.

| Configuración | Recuento de documentos | Rendimiento (operaciones/s)   | Diseño de partes |
|---------------|----------------|-----------------------------|--------------|
| 5 shards      | 200560412      | 27.86                       | 2-2-1        |

### <a name="6-node-results"></a>resultados de nodo de 6

La prueba se repite en un clúster de nodo de 6. El propósito de estas pruebas era probar y confirmar con mayor precisión los efectos de almacenar partes más de uno en un nodo.

| Configuración | Recuento de documentos | Rendimiento (operaciones/s)   | Diseño de partes |
|---------------|----------------|-----------------------------|--------------|
| 4 shards      | 227360412      | 31.58                       | 1-1-0-1-1-0  |
| 7 shards      | 268013252      | 37.22                       | 2-1-1-1-1-1  |
| 10 shards     | 258065854      | 35,84                       | 1-2-2-2-1-2  |
| 11 shards     | 279788157      | 38.86                       | 2-2-2-1-2-2  |
| 12 shards     | 257628504      | 35.78                       | 2-2-2-2-2-2  |
| 13 shards     | 300126822      | 41.68                       | 2-2-2-2-2-3  |

Estos resultados aparecen indicar las tendencias siguientes:

* Más shards por nodo mejora el rendimiento. Con el pequeño número de shards por nodo creado para estas pruebas, se esperaba este fenómeno, por razones descritas anteriormente.

* Un número impar de shards proporciona mejor rendimiento que un número par. Las razones para este son menos borrar, pero *puede* ser que el algoritmo de enrutamiento que usa Elasticsearch está mejor capacitado distribuir los datos a través de shards en este caso, lo que lleva a una carga más uniforme por nodo.

Para probar estas hipótesis, se realizaron varias pruebas más con un gran número de shards. Siguiendo los consejos de Elasticsearch, se decidió usar un número primo de shards para cada prueba como le ofrecen una distribución razonable de números impares para el rango en cuestión.

| Configuración | Recuento de documentos | Rendimiento (operaciones/s)   | Diseño de partes      |
|---------------|----------------|-----------------------------|-------------------|
| 23 shards     | 312844185      | 43.45                       | 4-4-4-3-4-4       |
| 31 shards     | 309930777      | 43.05                       | 5-5-5-5-6-5       |
| 43 shards     | 316357076      | 43.94                       | 8-7-7-7-7-7       |
| 61 shards     | 305072556      | 42.37                       | 10-11-10-10-10-10 |
| 91 shards     | 291073519      | 40.43                       | 15-15-16-15-15-15 |
| 119 shards    | 273596325      | 38.00                       | 20-20-20-20-20-19 |

Estos resultados sugerirán que se ha alcanzado un punto crítico en shards alrededor 23. Después de este punto, aumenta el número de shards provocó una pequeña degradación en el rendimiento (el rendimiento para 43 shards posiblemente es una anomalía).

### <a name="9-node-results"></a>resultados de nodo de 9

Las pruebas se han repetido mediante un clúster de 9 nodos, nuevo con un número primo de shards.

| Configuración | Recuento de documentos | Rendimiento (operaciones/s)   | Diseño de partes               |
|---------------|----------------|-----------------------------|----------------------------|
| 17 shards     | 325165364      | 45.16                       | 2-2-2-2-2-2-2-2-1          |
| 19 shards     | 331272619      | 46.01                       | 2-2-2-2-2-2-2-2-3          |
| 29 shards     | 349682551      | 48.57                       | 3-3-3-4-3-3-3-4-3          |
| 37 shards     | 352764546      | 49,00                       | 4-4-4-4-4-4-4-4-5          |
| 47 shards     | 343684074      | 47.73                       | 5-5-5-6-5-5-5-6-5          |
| 89 shards     | 336248667      | 46.70                       | 10-10-10-10-10-10-10-10-9  |
| 181 shards    | 297919131      | 41.38                       | 20-20-20-20-20-20-20-20-21 |

Estos resultados mostraron 37 alrededor shards de un patrón similar, con un punto crítico.

### <a name="scaling-out-conclusions"></a>Escalado: conclusiones

Usa una extrapolación bruto, los resultados de las pruebas de nodo de 6 y 9 indican que, para este escenario específico, el número ideal de shards para maximizar el rendimiento era 4n +/-1, donde n es el número de nodos. Este *puede* ser una función del número de subprocesos de insertar masiva disponibles, que por separado es depende del número de núcleos de CPU, la lógica de manera está (consulte [Multidocumento patrones](https://www.elastic.co/guide/en/elasticsearch/guide/current/distrib-multi-doc.html#distrib-multi-doc) para obtener información detallada):

- Cada masa insertar enviados por la aplicación cliente es recibida por un nodo de datos single.

- El nodo de datos, crea una nueva solicitud de insertar de forma masiva para cada principal partes afectadas por la solicitud original y los reenvía a los nodos, en paralelo.

- Mientras se escribe cada fragmentar principal, otra solicitud se envía a cada réplica para ese fragmentar. Las partes principales que espera de la solicitud que se envían a la réplica para completar antes de finalizar.

De forma predeterminada, Elasticsearch crea un subproceso de insertar de forma masiva para cada núcleo de CPU disponible en una máquina virtual. En el caso de las VM D4 usado por esta prueba, cada CPU había incluido 8 núcleos, para insertar el bloque de 8 subprocesos creados. El índice utilizado número 4 (en un caso 5) shards principales en cada nodo, sino que también eran 4 (5) réplicas en cada nodo. Insertar datos en estos shards y réplicas podría consumir hasta 8 subprocesos en cada nodo por solicitud, que coincida con el número disponible. Aumentar o reducir el número de shards puede provocar errores subprocesos como subprocesos posiblemente quedan libres o solicitudes en la cola. Sin embargo, sin más experimentación esto solo una teoría y no es posible ser definitiva.

Las pruebas también muestran otro punto importante. En este escenario, aumenta el número de nodos puede mejorar el rendimiento de recopilación de datos, pero los resultados no necesariamente escalar de manera lineal. Realizar pruebas más con 12 y 15 clústeres de nodo puede mostrar el punto de en qué escalado aporta pocas ventajas adicionales. Si este número de nodos proporciona espacio de almacenamiento suficiente, puede ser necesario volver a la escala de la estrategia y empezar a usar discos más grandes o más, según su almacenamiento premium.

> [AZURE.IMPORTANT] No lleve a cabo la 4n relación +/-1 como una fórmula mágica que siempre funcionan para cada clúster. Si tiene más o menos núcleos de CPU disponibles, la configuración de fragmentar óptimo podría ser diferente. Los resultados se basan en una carga de trabajo específico que hizo solo recopilación de datos. Para las cargas de trabajo que incluyen también una combinación de consultas y agregaciones los resultados podrían ser muy diversos.

> Además, la carga de trabajo de recopilación de datos utiliza un índice único. En muchos casos, los datos están probable que se distribuyan a través de varios índices llevando a diferentes patrones o uso de recursos.

> El punto importante de este ejercicio es comprender el método usado en lugar de los resultados obtenidos. Debe estar preparado para realizar su propia evaluación de escalabilidad en función de sus propios cargas de trabajo para obtener información que sea más adecuado para su propio escenario.

## <a name="tuning-large-scale-data-ingestion"></a>Ajustar la recopilación de datos a gran escala

Elasticsearch es muy configurable, con muchas modificadores y la configuración que puede usar para optimizar el rendimiento para los casos de uso específico y escenarios. Esta sección describen algunos ejemplos. Tenga en cuenta que la flexibilidad que proporciona Elasticsearch en este sentido se suministra con una advertencia, es muy fácil detune Elasticsearch y asegúrese de peor rendimiento. Al ajustar, solo realice uno de cambio en un momento y siempre medir los efectos de los cambios para asegurarse de que no son perjudiciales para el sistema.

### <a name="optimizing-resources-for-indexing-operations"></a>Optimización de los recursos para las operaciones de indización

La siguiente lista describen algunos aspectos que debe considerar cuando ajustar un clúster Elasticsearch para admitir la recopilación de datos a gran escala. Los dos primeros elementos están más probable que tienen un efecto obvio en el rendimiento mientras el resto es más marginal, dependiendo de la carga de trabajo:

*  Los nuevos documentos agregados a un índice sólo se hacen visibles a búsquedas cuando se actualiza el índice. Actualizar un índice es una operación cara, por lo que solo se realizan periódicamente en lugar de cada documento se crea. El intervalo de actualización predeterminado es 1 segundo. Si va a realizar operaciones de forma masiva, considere la posibilidad de deshabilitar temporalmente actualizaciones de índice. Establecer el índice *actualizar\_intervalo* -1.

    ```http
    PUT /my_busy_index
    {
        "settings" : {
            "refresh_interval": -1
        }
    }
    ```

    Desencadenar una actualización manualmente mediante el uso de la [* \_actualizar*](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-refresh.html) API al final de la operación para mostrar los datos. Para obtener más información, consulte [Uso de indización de forma masiva](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-update-settings.html#bulk) . Obtener más información sobre el [impacto de cambiar el intervalo de actualización de recopilación de datos](#the-impact-of-changing-the-index-refresh-interval-on-data-ingestion-performance) se describe más adelante.

* Si un índice está replicado, cada una operación de indización (documento crear, actualizar o eliminar) se repite en shards de réplica tal como aparecen en las partes principales. Considere la posibilidad de deshabilitar la replicación durante las operaciones de importación masiva y, a continuación, volver a habilitarla una vez completada la importación:

    ```http
    PUT /my_busy_index
    {
        "settings" : {
            "number_of_replicas": 0
        }
    }
    ```

    Al habilitar la duplicación Elasticsearch realiza a una transferencia de red byte por byte de datos del índice de cada réplica. Esto es más eficaz que el proceso de indización documento en cada nodo de repetición. El riesgo es que los datos se pueden perder de se produce un error de nodo principal al realizar la importación masiva, pero recuperación puede ser simplemente cuestión de iniciar la importación de nuevo. El [impacto de la replicación en el rendimiento de recopilación de datos](#the-impact-of-replicas-on-data-ingestion-performance) se describe más adelante con más detalle.

* Elasticsearch intenta equilibrar los recursos disponibles entre los necesarios para las consultas y los necesarios para recopilar datos. Como resultado, puede limitar el rendimiento de recopilación de datos (eventos limitación se graban en el registro de Elasticsearch). Esta restricción está pensada para evitar que un gran número de segmentos de índice se creen al mismo tiempo que requieren combinar y guardar en disco, un proceso que puede apropiarse en exclusiva de recursos. Si el sistema no está realizando consultas, puede deshabilitar el límite de recopilación de datos. Debe permitir la indización maximizar el rendimiento. Puede deshabilitar la limitación de todo un clúster como sigue:

    ```http
    PUT /_cluster/settings
    {
        "transient" : {
            "indices.store.throttle.type": "none"
        }
    }
    ```

    Establecer el tipo de Acelerador de clúster a *"combinación"* cuando haya finalizado la recopilación. También tenga en cuenta que deshabilitar límite puede producir inestabilidad en el clúster, así que asegúrese de que tiene procedimientos en el lugar donde puede recuperar el clúster si es necesario.

* Elasticsearch se reserva una parte de la memoria del montón para operaciones de indización, el resto se utiliza principalmente, las consultas y búsquedas. El propósito de estos búferes es reducir el número de operaciones de i/OS de disco, con el fin de realizar escribe menos, más grande que escribe más pequeños, más. La proporción predeterminada de memoria de montón asignada es 10%. Si la indización de un gran volumen de datos este valor podría ser insuficiente. Para sistemas que admiten la recopilación de datos de gran volumen, debe permitir hasta 512MB de memoria para cada activo fragmentar en el nodo. Por ejemplo, si se están ejecutando Elasticsearch en máquinas virtuales de D4 (28GB de memoria RAM) y ha asignado el 50% de la memoria disponible a JVM (14GB), a continuación, 1,4 GB estará disponible para su uso por las operaciones de indización. Si un nodo contiene 3 shards activas, esta configuración es suficiente probablemente. Sin embargo, si un nodo contiene más shards que este, considere la posibilidad de aumentar el valor de la *indices.memory.index\_búfer\_tamaño* parámetro en el archivo de configuración de elasticsearch.yml. Para obtener más información, consulte [Consideraciones de rendimiento para la indización de Elasticsearch](https://www.elastic.co/blog/performance-considerations-elasticsearch-indexing).

    Asignación de más de 512MB por fragmentar active más probable es que no mejora el rendimiento de la indización y realmente puede ser perjudicial como menos memoria disponible para realizar otras tareas. También tenga en cuenta que asignar más espacio del montón para búferes de índice quita la memoria para otras operaciones como buscar y agregar datos y puede ralentizar el rendimiento de las operaciones de consulta.

* Elasticsearch restringe el número de subprocesos (el valor predeterminado es 8) que simultáneamente se pueden realizar operaciones de indización en un fragmentar. Si un nodo solo contiene un pequeño número de shards, a continuación, considere aumentar el *índice\_simultaneidad* configuración para un índice que está sujeta a un gran volumen de las operaciones de indización o es el destino de una inserción masiva, como sigue:

    ```http
    PUT /my_busy_index
    {
        "settings" : {
            "index_concurrency": 20
        }
    }
    ```

* Si va a realizar un gran número de operaciones de indización y masivas durante un breve período de tiempo, puede aumentar el número de subprocesos de *índice* y *masa* disponibles en el grupo de subprocesos y extender el tamaño de la cola de *inserción masiva* para cada nodo de datos. Esto le permitirá más solicitudes de poner en cola en lugar de ser descartado. Para obtener más información, vea el [Grupo de subprocesos](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-threadpool.html). Si está realizando prolongados altos niveles de recopilación de datos, no es aconsejable aumentar el número de subprocesos de forma masiva. En su lugar crear los nodos adicionales y use sharding para distribuir la carga de indización en estos nodos. Como alternativa, considere la posibilidad de envío masivo insertar lotes en serie en lugar de en paralelo mientras funciona como un mecanismo de límite natural que puede reducir las probabilidades de los errores debido a un bloque insertar desbordamiento de cola.

### <a name="the-impact-of-changing-the-index-refresh-interval-on-data-ingestion-performance"></a>El impacto de cambiar el índice Actualizar intervalo en el rendimiento de recopilación de datos

El intervalo de actualización controla la velocidad a la que se hace visibles a consultas y agregaciones de datos integrados, pero las actualizaciones frecuentes pueden afectar al rendimiento de las operaciones de recopilación de datos. El intervalo de actualización predeterminado es 1 segundo. Puede deshabilitar la actualización de completamente, pero no puede ser apropiada para su carga de trabajo. Puede experimentar intentando diferentes intervalos y estableciendo la madurez que equilibra el rendimiento de recopilación frente a la necesidad para presentar información actualizada.

El impacto, por ejemplo, la prueba de rendimiento de recopilación de datos se repite en un clúster de Elasticsearch formada por 7 shards extendido por los nodos de 3 datos. El índice tenía una única réplica. Cada nodo de datos se basaba en una máquina virtual de D4 (28GB de memoria RAM, 8 núcleos de procesador) mediante copia SSD efímero almacenamiento para mantener los datos. Cada prueba ejecutó para 1 hora.

En esta prueba, se estableció la tasa de actualización en el valor predeterminado de 1 segundo. La siguiente tabla muestra el rendimiento y tiempos de respuesta para esta prueba en comparación con una ejecución independiente donde se redujo la velocidad de actualización a una vez cada 30 segundos.

| Frecuencia de actualización | Recuento de ejemplo | Tiempo medio de respuesta: operaciones correctas (ms) | Rendimiento: operaciones correctas (operaciones/s) |
|--------------|--------------|----------------------------------------------------|---------------------------------------------------|
| 1 segundo     | 93755        | 460                                                | 26.0                                              |
| 30 segundos   | 117758       | 365                                                | ahorro de 32,7                                              |

En esta prueba, anular la tasa de actualización da como resultado una mejora 18% en el rendimiento y un 21% de reducción de tiempo medio de respuesta. Los gráficos siguientes generados mediante Marvel ilustran la razón principal para esta diferencia. Las figuras siguientes muestran la actividad de combinación de índice que ocurrieron en el intervalo de actualización establecido en 1 segundo y 30 segundos. 

Las combinaciones de índice se realizan para evitar que el número de segmentos de índice en memoria demasiado numerosos. 1 segundo intervalo de actualización genera un gran número de segmentos pequeños que deben combinarse con frecuencia, mientras que el intervalo de actualización de un segundo 30 genera menos segmentos grandes que pueden combinarse de manera óptima.

![](media/guidance-elasticsearch/data-ingestion-image15.png)

***Actividad de combinación de índice para una tasa de actualización de índice de 1 segundo***

![](media/guidance-elasticsearch/data-ingestion-image16.png)

***Actividad de combinación de índice para una tasa de actualización de índice de 30 segundos***

### <a name="the-impact-of-replicas-on-data-ingestion-performance"></a>El impacto de réplicas en el rendimiento de recopilación de datos

Las réplicas son una característica esencial de cualquier clúster flexible y sin uso de riesgo perder información si se produce un error en un nodo. Sin embargo, réplicas aumentan la cantidad de i/OS se realiza de red y del disco y pueden ser perjudiciales a la velocidad a la que se ingestión datos. Por motivos descritos anteriormente, puede resultar útil deshabilitar temporalmente réplicas para la duración de las operaciones de carga de datos a gran escala.

Pruebas de rendimiento de recopilación de datos se han repetido con tres configuraciones:

* Usar un clúster con ningún réplicas.

* Usar un clúster con 1 réplica.

* Usar un clúster con 2 réplicas.

En todos los casos, el clúster contiene 7 shards extendido por 3 nodos y se ejecuta en máquinas virtuales configuradas como se describe en el conjunto de pruebas anterior. El índice de prueba utiliza un intervalo de actualización de 30 segundos.

La siguiente tabla resume los tiempos de respuesta y el rendimiento de cada prueba para realizar comparaciones:

| Configuración | Recuento de ejemplo | Tiempo medio de respuesta: operaciones correctas (ms) | Rendimiento: operaciones correctas (operaciones/s) | Errores de recopilación de datos |
|---------------|--------------|----------------------------------------------------|---------------------------------------------------|--------------------------|
| réplicas 0    | 215451       | 200                                                | 59.8                                              | 0                        |
| 1 réplica     | 117758       | 365                                                | ahorro de 32,7                                              | 0                        |
| 2 réplicas    | 94218        | 453                                                | 26.1                                              | 194262                   |


El descenso en el rendimiento como réplicas aumenta el número de está desactivada, pero también debe tener en cuenta el gran volumen de errores de recopilación de datos en la tercera prueba. Los mensajes generados por estos errores indican que se han debido a que el desbordamiento de masa insertar cola provocan solicitudes de rechazado. Estos rechazos ocurrieron muy rápidamente, que es el motivo por el número es grande.

> [AZURE.NOTE] Los resultados de la tercera prueba resaltar la importancia de utilizar una estrategia de reintento inteligente cuando se producen errores transitorios como esta: volver a desactivar durante un período corto permitir que la cola de insertar masa desagüe antes volviendo a intentar repetir masa Insertar operación.

Los siguientes conjuntos de gráficos comparan los tiempos de respuesta durante las pruebas. En cada caso que el primer gráfico muestra los tiempos de respuesta general, mientras que el segundo gráfico acerca los tiempos de respuesta para las operaciones más rápidas (tenga en cuenta que la escala del primer gráfico es diez veces que del segundo). Puede ver cómo varía el perfil de los tiempos de respuesta en todas las tres pruebas.

Con ningún réplicas, la mayoría de las operaciones tomó entre 75ms y 750ms, con la respuesta más rápida veces alrededor de 25 ms:

![](media/guidance-elasticsearch/data-ingestion-image17.png)

Con la 1 réplica el tiempo de respuesta operativas más llena estaba en el rango 125ms a 1250ms. Las respuestas más rápidas tardó aproximadamente 75ms, aunque había menos de estas respuestas rápidas que en el caso de réplicas 0. También había mucho más respuestas que tardan mucho más de los casos más comunes, superiores a 1250ms:

![](media/guidance-elasticsearch/data-ingestion-image18.png)

Con 2 réplicas, el intervalo de tiempo de respuesta más llena era 200ms a 1500ms, pero hay mucho menos resultados por debajo del rango mínimo que en la prueba de 1 réplica. Sin embargo, el patrón de resultados por encima del límite superior eran muy similar de la prueba de 1 réplica. Esto suele deberse a los efectos de la cola de insertar masa desbordamiento (sobrepasan una longitud de cola de solicitudes de 50). El trabajo adicional necesario para mantener 2 réplicas hace que la cola de desbordamiento con más frecuencia, impidiendo operaciones de recopilación de tener tiempos de respuesta excesiva. Operaciones rechazadas rápidamente en lugar de tomar un período de tiempo prolongado, posiblemente causando excepciones de tiempo de espera o que afectan a la capacidad de respuesta de las aplicaciones cliente (este es el propósito del mecanismo de cola de masa Insertar):

![](media/guidance-elasticsearch/data-ingestion-image19.png)

Marvel puede ver el efecto de la cantidad de réplicas en la cola de índice de forma masiva. La figura siguiente muestra los datos del Marvel que muestra cómo la inserción masiva cola llena durante la prueba. La longitud media de la cola era de alrededor de 40 solicitudes pero periódicos ráfagas causados desbordamiento y solicitudes rechazado como resultado:

![](media/guidance-elasticsearch/data-ingestion-image20.png)

***Tamaño de la cola de índice y el número de solicitudes rechazadas con 2 réplicas en masa.***

Se debe comparar con la siguiente ilustración que muestra los resultados de una única réplica. El motor de Elasticsearch era capaz de procesar las solicitudes lo suficientemente rápido para mantener la longitud media de la cola de alrededor de 25, y en ningún punto rechazó la longitud de cola supere 50 solicitudes para que ningún trabajo fue rechazado.

![](media/guidance-elasticsearch/data-ingestion-image21.png)

***Tamaño de la cola de índice y el número de solicitudes rechazadas con la 1 réplica en masa.***

## <a name="best-practices-for-clients-sending-data-to-elasticsearch"></a>Prácticas recomendadas para enviar datos a Elasticsearch de clientes

Muchos aspectos de rendimiento se refiere no sólo internamente dentro del sistema, pero con cómo se usa el sistema de las aplicaciones cliente. Elasticsearch proporciona muchas características que pueden utilizar el proceso de recopilación de datos; generar identificadores únicos para documentos, realizar análisis de documento y a continuación, incluso con secuencias de comandos para transformar los datos según se almacena se muestran algunos ejemplos. Sin embargo, estas funciones que todos agregan a la carga en el motor de Elasticsearch y en muchos casos pueden realizarse de forma más eficaz aplicaciones cliente antes de la transmisión. 

> [AZURE.NOTE] Esta lista de prácticas recomendadas se ocupa principalmente recopila nuevos datos prefiere modificar datos existentes previamente almacenados en un índice. Cargas de trabajo de recopilación se realizan como anexar operaciones por Elasticsearch, mientras que se realizan las modificaciones de datos como operaciones de eliminar o anexar. Esto es porque son inmutables, documentos en un índice para modificar un documento implica reemplazar todo el documento con una versión nueva. Puede realizar una solicitud de HTTP poner para sobrescribir un documento existente, o puede usar el Elasticsearch *Actualizar* API que resume una consulta para buscar un documento existente, incorpora los cambios y, a continuación, realiza una ubicación para almacenar el nuevo documento.

Además, considere la posibilidad de implementar las siguientes prácticas donde corresponda:

* Deshabilitar el análisis de texto para los campos de índice que no es necesario analizar. El análisis implica convirtiendo tokens texto para habilitar las consultas que pueden buscar términos específicos. Sin embargo, puede ser una tarea de la CPU, así que selectivo. Si está utilizando Elasticsearch para almacenar los datos del registro, puede ser útil para convertir los mensajes de registro detallado para permitir búsquedas complejas en. Otros campos, como, por ejemplo, probablemente no aquellos que contiene los códigos de error o identificadores deben token (la frecuencia con que es probable que solicitar los detalles de todos los mensajes está cuyo código de error contiene "3", para example?) el código siguiente deshabilita el análisis de los campos *nombre* y *hostip* en el tipo de *registros* del índice *systembase* .

    ```http
    PUT /systembase
    {
        "settings" : {
            ...
        },
        "logs" : {
            ...
            "name": {
                "type": "string",
                "index" : "not_analyzed"
            },
            "hostip": {
                "type": "string",
                "index" : "not_analyzed"
            },
            ...
        }
    }
    ```

* Deshabilitar el campo *_all* de un índice si no es necesario. La * \_todos los* campo concatena los valores de los otros campos en el documento para análisis y la indización. Es útil para realizar consultas que pueden buscar coincidencias con todos los campos de un documento. Si los clientes deben coincidir con los campos con nombre, habilitar * \_todos los* simplemente incurre sobrecarga de almacenamiento y CPU. En el ejemplo siguiente se muestra cómo deshabilitar la * \_todos los* campo para el tipo de *registros* en el índice de *systembase* .

    ```http
    PUT /systembase
    {
        "settings" : {
            ...
        },
        "logs" : {
            "_all": {
                "enabled" : false
            },
            ...,
        ...
        }
    }
    ```

    Tenga en cuenta que puede crear una versión selectiva de * \_todos los* que solo contiene información de campos específicos. Para obtener más información, vea [Deshabilitar la \_campo todos](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-all-field.html#disabling-all-field).

* Evitar las asignaciones dinámicas en índices. Asignación dinámica es una característica eficaz, pero agregar nuevos campos a un índice existente requiere coordinar cambios en la estructura del índice en todos los nodos y puede provocar temporalmente el índice esté bloqueado. Asignación dinámica también puede llevar a una explosión en el número de campos y el volumen de los metadatos de índice consecutivas si no se usa con cuidado. A su vez, el resultado mayores requisitos de almacenamiento e i/OS, para recopilar datos y al realizar consultas. Estos problemas afectará al rendimiento. Considere la posibilidad de deshabilitar la asignación dinámica y defina las estructuras de índice explícitamente. Para obtener más información, vea [Asignación de campo dinámico](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic-field-mapping.html#dynamic-field-mapping).

* Comprender cómo equilibrar la carga de trabajo para satisfacer requisitos en conflicto. Siempre debe tener en cuenta que recopilación de datos puede tener un impacto significativo en el rendimiento de otras operaciones simultáneas, como los usuarios realizar las consultas. Recopilación de datos pueden sufrir ráfagas rápidas y, si el sistema intenta consumir todos los datos que llegan inmediatamente el flujo podría provocar tasas de consulta ralentizar a un ritmo. Elasticsearch intenta impedir que se produzca mediante el control de la velocidad a la que se procesan las solicitudes de recopilación a través de masa insertar cola esta situación (consulte la sección [determinar limitación factores: uso de CPU](#determining-limiting-factors-cpu-utilization) para obtener más información), pero este mecanismo realmente se debe tratar como último recurso. Si el código de aplicación no está preparado para controlar rechaza solicitudes que corre el riesgo de pérdida de datos. En su lugar, considere el uso de un modelo como [basado en cola Levelling de carga](https://msdn.microsoft.com/library/dn589783.aspx) para controlar la velocidad a la que los datos se pasan a Elasticsearch.

* Asegúrese de que el clúster tiene recursos suficientes para controlar la carga de trabajo, especialmente si índices estén configurados con múltiples réplicas.

* Usar masa insertar API para cargar grandes lotes de documentos. Bloque de tamaño solicita adecuadamente. A veces mayores lotes no son mejores rendimiento y pueden provocar Elasticsearch subprocesos y otros recursos que se sobrecargue, retrasar otras operaciones simultáneas. Los documentos en un lote de insertar de masa se mantienen en memoria en el coordinador nodo mientras se realiza la operación. El tamaño de cada lote físico es más importante que el número de documento. No hay ninguna regla absoluta lo que son el tamaño del lote ideal, aunque se recomienda de documentación Elasticsearch con entre 5 y 15 MB como inicial punto para sus propio investigaciones. Realizar pruebas de rendimiento para establecer el tamaño de lote óptimo para su propia combinación de carga de trabajo y escenarios.

* Asegúrese de que las solicitudes de Insertar bloque se distribuye entre los nodos en lugar de dirigen a un único nodo. Dirigir todas las solicitudes de un solo nodo puede provocar agote la memoria como cada solicitud de insertar masa procesando se almacena en memoria en el nodo. Puede aumentar la latencia de red como las solicitudes se redirigen a otros nodos.

* Elasticsearch usa voto de la mayoría de los nodos principal y réplica al escribir datos. No se realiza una operación de escritura hasta que el quórum informes de éxito. Este enfoque ayuda a garantizar que no se escriben datos, si la mayoría de los nodos no están disponibles debido a un evento de partición (error) de la red. Usar un quórum puede ralentizar el rendimiento de las operaciones de escritura. Puede deshabilitar la escritura basado en quórum estableciendo el parámetro de *coherencia* en *una* al escribir datos. En el ejemplo siguiente se agrega un nuevo documento, pero se completa tan pronto como se complete la escritura en las partes principales.

    ```http
    PUT /my_index/my_data/104?consistency=one
    {
        "name": "Bert",
        "age": 23
    }
    ```

    Tenga en cuenta que como con la replicación asincrónica, desactivar la escritura en función de quórum puede provocar incoherencias entre las partes principales y cada una de las réplicas.

* Cuando se usa quórums, Elasticsearch espera si existen nodos insuficientes antes de determinar que se debe cancelar una operación de escritura porque no se puede llegar un quórum. Este período de espera es determinado por el parámetro de consulta de tiempo de espera (el valor predeterminado es 1 minuto). Puede modificar esta configuración mediante el parámetro de consulta de tiempo de espera. El ejemplo siguiente crea un nuevo documento y que espera un máximo de 5 segundos para el quórum responder antes de cancelar:

    ```http
    PUT /my_index/my_data/104?timeout=5s
    {
        "name": "Sid",
        "age": 27
    }
    ```

    Elasticsearch también le permite utilizar sus propios números de versión [generan externamente](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html#_version_types).

* Considere la posibilidad de deshabilitar la * \_origen* campo de un índice. Este campo contiene una copia del documento original JSON que utilizó cuando se almacena un documento. Guardar este campo incurre en los costos de almacenamiento adicional e i/OS de disco. Sin embargo, estos costos pueden ser marginales dependiendo de la estructura del documento, y también deben tener en cuenta que deshabilitar la * \_origen* campo impide que un cliente se pueden realizar las siguientes operaciones:

    * Uso de la API de actualización para modificar un documento.
    * Realizar en el resaltado de entrada y salida al ejecutar consultas.
    * Volver a indizar datos.
    * Depurar consultas y agregaciones al ver el documento original.

    En el ejemplo siguiente se deshabilita la * \_origen* campo para el tipo de *registros* en el índice de *systembase* .

  ```http
  PUT /systembase
  {
        "settings" : {
            ...
        },
        "logs" : {
            "_source": {
                "enabled": false
            },
            ...,
        ...
        }
  }
  ```

## <a name="general-guidelines-for-conducting-data-ingestion-performance-testing-with-elasticsearch"></a>Instrucciones generales para realizar pruebas con Elasticsearch de rendimiento de recopilación de datos

Los siguientes puntos resaltan algunos de los elementos que debe tener en cuenta cuando ejecute el rendimiento pruebas con Elasticsearch y analizar los resultados.

* Pruebas de rendimiento son necesariamente mucho tiempo y el costo. Como mínimo, recopilar estadísticas que medir la velocidad de transferencia en disco y de red, uso de CPU, tiempos de espera de la CPU y latencia de disco (si es posible). Esto puede enviar comentarios rápida en los esfuerzos de prueba con buena retorno de la inversión.

* Aproveche las capacidades de scripting proporcionadas por la herramienta de prueba de carga para recopilar métricas de lo contrario, no esté disponibles. Por ejemplo, Linux tiene una gran variedad de estadísticas de rendimiento confiables que puede recopilar mediante herramientas como *vmstat* y *iostat*. Puede usar scripting con JMeter para capturar datos como parte de un plan de pruebas.

* Ingeniería de rendimiento es principalmente acerca del análisis de estadísticas basadas en datos predecible y confiables. No se detenga en métrica de alto nivel que no le proporcionará la información necesaria. Infórmese con los datos y realice un proceso de operaciones de desarrollo con un bucle de realimentación rápida de ingeniería de rendimiento. Mire siempre las estadísticas de comparación de tendencias y resultados y configuraciones anteriores. Para hacerlo de forma regular que generará datos que se entienden, es reproducible con las cargas de trabajo, y que podrá evaluar los efectos de los cambios de configuración e implementación.

* Utilice una herramienta como Marvel para supervisar el rendimiento de clúster y nodo mientras se prueba para obtener información adicional. JMeter puede ser eficaces para capturar los datos sin formato para su análisis posterior, pero usando Marvel puede proporcionarle apariencia en tiempo real para cómo marcha rendimiento y las posibles causas de problemas técnicos y lentas listas desplegables. Además, muchas herramientas de prueba de carga no proporcionan visibilidad a las medidas internas de Elasticsearch. Use y comparar indización tasas de rendimiento, recuentos de segmento de combinación, las estadísticas de recolección (catálogo global) y límite de horas disponibles en las estadísticas de índice. Repita este análisis de forma periódica.

* Comparar las estadísticas de herramienta de prueba de carga con estadísticas de nodo en Marvel (disco y red tráfico, uso de CPU, memoria y subproceso uso de grupo) a entender el modelo de correlación entre las cifras indicada por la infraestructura y las estadísticas de Elasticsearch específicas.

* Por lo general considere *una partes de un nodo* como la línea de base para realizar pruebas de rendimiento y evaluar los costos de aplicación agregando nodos. Sin embargo, no dependen completamente extrapolar rendimiento basado en un pequeño número de nodos y shards. Los costos de sincronización y la comunicación del clúster pueden tener efectos imprevistos mayores el número de nodos y shards.

* Consultar la asignación de partes de nodos para comparar las estadísticas. Algunos nodos tendrá menos réplicas y shards que creen un descuadre de uso de recursos.

* Si va a realizar pruebas de carga, aumente el número de subprocesos que utiliza la herramienta de prueba para enviar el trabajo en el clúster hasta que se produzcan errores. Para probar el rendimiento sostenible, considere la posibilidad de mantener el nivel de prueba por debajo de la carga máxima esperada. Si la tasa de error supera el límite máximo, errores producirá costo en recursos de back-end debido a la capacidad de recuperación. En estos casos, el rendimiento disminuirá inevitable.

* Para simular cómo reacciona el sistema a una ráfaga inesperadamente grande de actividad, considere la posibilidad de ejecutar pruebas que generan un tipo de error que supera la carga máxima esperada. Esto le proporcionará cifras de rendimiento no sólo en términos de capacidad, sino también el costo de la capacidad de recuperación.

* Usar un recuento de documento para evaluar su perfil de rendimiento y reciclaje siguiendo los patrones de carga de trabajo de documentos. Tenga en cuenta que, cuando se agregan varios documentos, puede cambiar el perfil de rendimiento.

* Tenga en cuenta los SLA para IOPS y la transferencia de los límites de tasas para el almacenamiento que está usando. Tipos de almacenamiento diferente (SSD, que gira multimedia) tienen velocidades de transferencia diferente.

* Recuerde que puede colocar el rendimiento de la CPU por actividad de red y del disco, pero ya pueden utilizar aplicaciones de back-end mecanismos de bloqueo y comunicación con procesamiento distribuido pueden causar bajo aprovechamiento de procesador.

* Realizar pruebas de rendimiento al menos dos horas (no unos minutos). Indización puede afectar al rendimiento de maneras que no sean visibles inmediatamente. Por ejemplo, estadísticas de basura JVM y combina la indización pueden cambiar el perfil de rendimiento con el tiempo.

* Tenga en cuenta cómo actualizaciones de índice pueden límite con un clúster y rendimiento de recopilación de datos de gran impacto.

## <a name="summary"></a>Resumen

Es importante comprender cómo ampliar su solución que los volúmenes de datos y el número de solicitudes aumenta. Elasticsearch que se ejecuta en Azure permite el ajuste de escala horizontal y vertical. Puede ejecutar en máquinas virtuales más grandes con más recursos y distribuir un clúster Elasticsearch a través de una red de máquinas virtuales. La gama de opciones puede resultar confusa. ¿Es más rentables para implementar un clúster de un gran número de VM pequeñas, en un clúster con un pequeño número de máquinas virtuales grandes o en cualquier lugar en el medio? ¿Además, shards cuántas debe contener cada índice y cuáles son las compensaciones relativas a la recopilación de datos en comparación con el rendimiento de la consulta? La manera en que se distribuye shards entre nodos puede tener un impacto significativo en el rendimiento de recopilación de datos. Usar más shards puede reducir la cantidad de conflicto interno, lo que ocurre dentro de un fragmentar, pero debe equilibrar este beneficio con la carga que puede usar muchos shards imponer en un clúster. Para responder a estas preguntas eficaz, debe estar preparado para probar el sistema para determinar la estrategia más adecuada.

Para cargas de trabajo de recopilación de datos, el rendimiento del disco subsistema de i/OS es un factor importante. Usar SSDs puede mejorar el rendimiento al reducir la latencia de disco de operaciones de escritura. Si no necesita gran cantidad de espacio en disco en un nodo, considere la posibilidad de usar VM estándar con almacenamiento efímero en lugar de máquinas virtuales más caras que admite el almacenamiento de premium.

## <a name="appendix-the-bulk-load-data-ingestion-performance-test"></a>Apéndice: la masa datos recopilación rendimiento prueba de carga

Este apéndice describe la prueba de rendimiento realizada en el clúster Elasticsearch. Las pruebas se ejecutan mediante JMeter que se ejecuta en un conjunto de máquinas virtuales independiente. Detalles de la configuración del entorno de pruebas se describen en la [creación de un entorno de Elasticsearch en Azure de pruebas de rendimiento][]. Para realizar sus propias pruebas, puede crear su propio plan de pruebas JMeter manualmente o puede usar las secuencias de comandos de prueba automatizada disponibles por separado. Para obtener más información, vea [ejecutar las pruebas de rendimiento Elasticsearch automatizadas][] .

La carga de trabajo de recopilación de datos realiza una carga de documentos a gran escala mediante la API de inserción masiva. El propósito de este índice era simular un repositorio de recepción de datos de registro que representa los eventos del sistema para búsqueda subsiguientes y análisis. Cada documento se almacena en un índice único denominado *systembase*y tenía el tipo de *registros*. Todos los documentos tenían el mismo esquema fijo que se describe en la siguiente tabla:

| Campo         | Tipo de datos            | Ejemplo                           |
|---------------|---------------------|-----------------------------------|
| @timestamp    | fecha y hora            | 2013-12-11T08:01:45.000Z          |
| nombre          | cadena              | Checkout.Payment                  |
| Mensaje       | cadena              | Mensaje de solicitud entrante          |
| severityCode  | entero             | 1                                 |
| gravedad      | cadena              | información                              |
| nombre de host      | cadena              | sixshot                           |
| hostip        | cadena (dirección ip) | 10.0.0.4                          |
| PID           | int                 | 123                               |
| TID           | int                 | 4325                              |
| Id         | cadena (uuid)       | {00000000 0000-0000 000000000000} |
| nombre de aplicación       | cadena              | mytestapp                         |
| appVersion    | cadena              | 0.1.0.1234                        |
| tipo          | int                 | 5                                 |
| subtipo       | int                 | 1                                 |
| correlationId | GUID                | {00000000 0000-0000 000000000000} |
| sistema operativo            | cadena              | Linux                             |
| osVersion     | cadena              | 4.1.1                             |
| parámetros    | [ ]                | {clave:, valor clave:}             |

Puede usar la siguiente solicitud para crear el índice. La *número\_de\_réplicas*, *actualizar\_intervalo*, y *número\_de\_shards* configuración varía de los valores que se muestra a continuación en muchas de las pruebas.

> [AZURE.IMPORTANT] El índice se eliminan y vuelve a crear antes de cada ejecución de prueba.

```http
PUT /systembase
{
    "settings" : {
        "number_of_replicas": 1,
        "refresh_interval": "30s",
        "number_of_shards": "5"
    },
    "logs" : {
        "properties" : {
            "@timestamp": {
            "type": "date",
            "index" : "not_analyzed"
            },
            "name": {
                "type": "string",
                "index" : "not_analyzed"
            },
            "message": {
                "type": "string",
                "index" : "not_analyzed"
            },
            "severityCode": {
                "type": "integer",
                "index" : "not_analyzed"
            },
            "severity": {
                "type": "string",
                "index" : "not_analyzed"
            },
            "hostname": {
                "type": "string",
                "index" : "not_analyzed"
            },
            "hostip": {
                "type": "string",
                "index" : "not_analyzed"
            },
            "pid": {
                "type": "integer",
                "index" : "not_analyzed"
            },
            "tid": {
                "type": "integer",
                "index" : "not_analyzed"
            },
            "appId": {
                "type": "string",
                "index" : "not_analyzed"
            },
            "appName": {
                "type": "string",
                "index" : "not_analyzed"
            },
            "appVersion": {
                "type": "integer",
                "index" : "not_analyzed"
            },
            "type": {
                "type": "integer",
                "index" : "not_analyzed"
            },
            "subtype": {
                "type": "integer",
                "index" : "not_analyzed"
            },
            "correlationId": {
                "type": "string",
                "index" : "not_analyzed"
            },
            "os": {
                "type": "string",
                "index" : "not_analyzed"
            },
            "osVersion": {
                "type": "string",
                "index" : "not_analyzed"
            },
            "parameters": {
                "type": "string",     
                "index" : "not_analyzed"
            }
        }
    }
}
```

Cada lote de insertar masa contenía 1000 documentos. Cada documento se generó en función de una combinación de valores aleatorios para la *severityCode*, *hostname*, *hostip*, *pid*, *tid*, *nombre de aplicación*, *appVersion*, campos *tipo*, *subtipo*y *correlationId* y una selección de texto de un conjunto de términos para los campos *nombre*, *mensaje*, *gravedad*, *os*, *osVersion*, *parámetros*, *data1*y *datos2* fijo aleatoria. El número de instancias de la aplicación cliente usado para cargar los datos con cuidado se ha seleccionado para maximizar el volumen de entrada correcta. Pruebas ejecutaban dos horas habilitar el clúster liquidar y reducir la influencia de los problemas temporales en los resultados. En este momento, algunas pruebas cargan casi 1,5 millones de documentos.

Los datos se generan dinámicamente utilizando una muestra de solicitud JUnit personalizada que se ha agregado a un grupo de subproceso en un plan de pruebas JMeter. El código de JUnit se creó con la plantilla de caso de prueba de JUnit en IDE Eclipse.

> [AZURE.NOTE] Para obtener información sobre cómo crear una prueba de JUnit para JMeter, vea [implementar un muestrario JMeter JUnit para probar el rendimiento de Elasticsearch][].

El siguiente fragmento de código Java para realizar pruebas Elasticsearch 1.7.3. Tenga en cuenta que la clase de prueba de JUnit en este ejemplo se denomina *ElasticsearchLoadTest2*:

```java
/* Java */
package elasticsearchtest2;

    import static org.junit.Assert.*;

    import org.junit.*;

    import java.util.*;

    import java.io.*;

    import org.elasticsearch.action.bulk.*;
    import org.elasticsearch.common.transport.*;
    import org.elasticsearch.client.transport.*;
    import org.elasticsearch.common.settings.*;
    import org.elasticsearch.common.xcontent.*;

    public class ElasticsearchLoadTest2 {

        private String [] names={"checkout","order","search","payment"};
        private String [] messages={"Incoming request from code","incoming operation succeeded with code","Operation completed time","transaction performed"};
        private String [] severity={"info","warning","transaction","verbose"};
        private String [] apps={"4D24BD62-20BF-4D74-B6DC-31313ABADB82","5D24BD62-20BF-4D74-B6DC-31313ABADB82","6D24BD62-20BF-4D74-B6DC-31313ABADB82","7D24BD62-20BF-4D74-B6DC-31313ABADB82"};

        private String hostname = "";
        private String indexstr = "";
        private String typestr = "";
        private int port = 0;
        private int itemsPerInsert = 0;
        private String clustername = "";
        private static Random rand=new Random();

        @Before
        public void setUp() throws Exception {
        }

        public ElasticsearchLoadTest2(String paras) {
        \* Paras is a string containing a set of comma separated values for:
            hostname
            indexstr
            typestr
            port
            clustername
            node
            itemsPerInsert
        */

            // Note: No checking/validation is performed

            String delims = "[ ]*,[ ]*"; // comma surrounded by zero or more spaces
            String[] items = paras.split(delims);

            hostname = items[0];
            indexstr = items[1];
            typestr = items[2];
            port = Integer.parseInt(items[3]);
            clustername = items[4];
            itemsPerInsert = Integer.parseInt(items[5]);

            if (itemsPerInsert == 0)
                itemsPerInsert = 1000;
            }

        @After
        public void tearDown() throws Exception {
        }

        @Test
        public void BulkBigInsertTest() throws IOException {

            Settings settings = ImmutableSettings.settingsBuilder().put("cluster.name", clustername).build();

            TransportClient client;
            client = new TransportClient(settings);

            try {
                client.addTransportAddress(new InetSocketTransportAddress(hostname, port));
                BulkRequestBuilder bulkRequest = client.prepareBulk();
                Random random = new Random();
                char[] exmarks = new char[12000];
                Arrays.fill(exmarks, 'x');
                String dataString = new String(exmarks);

                for(int i=1; i &lt; itemsPerInsert; i++){
                    random.nextInt(10);
                    int host=random.nextInt(20);

                    bulkRequest.add(client.prepareIndex(indexstr, typestr).setSource(XContentFactory.jsonBuilder().startObject()
                        .field("@timestamp", new Date())
                        .field("name", names[random.nextInt(names.length)])
                        .field("message", messages[random.nextInt(messages.length)])
                        .field("severityCode", random.nextInt(10))
                        .field("severity", severity[random.nextInt(severity.length)])
                        .field("hostname", "Hostname"+host)
                        .field("hostip", "10.1.0."+host)
                        .field("pid",random.nextInt(10))
                        .field("tid",random.nextInt(10))
                        .field("appId", apps[random.nextInt(apps.length)])
                        .field("appName", "application" + host)
                        .field("appVersion", random.nextInt(5))
                        .field("type", random.nextInt(6))
                        .field("subtype", random.nextInt(6))
                        .field("correlationId", UUID.randomUUID().toString())
                        .field("os", "linux")
                        .field("osVersion", "14.1.5")
                        .field("parameters", "{key:value,key:value}")
                        .field("data1",dataString)
                        .field("data2",dataString)
                    .endObject()));
                }

                BulkResponse bulkResponse = bulkRequest.execute().actionGet();
                assertFalse(bulkResponse.hasFailures());
            }
            finally {
                client.close();
            }
        }

        @Test
        public void BulkDataInsertTest() throws IOException {
            Settings settings = ImmutableSettings.settingsBuilder().put("cluster.name", clustername).build();

            TransportClient client;
            client = new TransportClient(settings);

            try {
                client.addTransportAddress(new InetSocketTransportAddress(hostname, port));
                BulkRequestBuilder bulkRequest = client.prepareBulk();

                for(int i=1; i&lt; itemsPerInsert; i++){
                    rand.nextInt(10);
                    int host=rand.nextInt(20);

                    bulkRequest.add(client.prepareIndex(indexstr, typestr).setSource(XContentFactory.jsonBuilder().startObject()
                        .field("@timestamp", new Date())
                        .field("name", names[rand.nextInt(names.length)])
                        .field("message", messages[rand.nextInt(messages.length)])
                        .field("severityCode", rand.nextInt(10))
                        .field("severity", severity[rand.nextInt(severity.length)])
                        .field("hostname", "Hostname" + host)
                        .field("hostip", "10.1.0."+host)
                        .field("pid",rand.nextInt(10))
                        .field("tid",rand.nextInt(10))
                        .field("appId", apps[rand.nextInt(apps.length)])
                        .field("appName", "application"+host)
                        .field("appVersion", rand.nextInt(5))
                        .field("type", rand.nextInt(6))
                        .field("subtype", rand.nextInt(6))
                        .field("correlationId", UUID.randomUUID().toString())
                        .field("os", "linux")
                        .field("osVersion", "14.1.5")
                        .field("parameters", "{key:value,key:value}")
                    .endObject()));
                }

                BulkResponse bulkResponse = bulkRequest.execute().actionGet();
                assertFalse(bulkResponse.hasFailures());
            }
            finally {
                client.close();
            }
        }
    }
```

La privada *cadena* matrices *nombres*, *mensajes*, *gravedad*y *aplicaciones* contienen un pequeño conjunto de valores que se seleccionan elementos de forma aleatoria. Los elementos restantes de datos para cada documento se generan en tiempo de ejecución.

Se llama al constructor que toma el parámetro de *cadena* de JMeter y, a continuación, se especifican los valores que se pasan en la cadena como parte de la configuración de muestrario de solicitud de JUnit. Para esta prueba JUnit, se espera que el parámetro de *cadena* que contenga la información siguiente:

* **Nombre de host**. Este es el nombre o la dirección IP del equilibrador de carga de Azure. El equilibrador de carga intenta distribuir la solicitud a través de los nodos de datos en el clúster. Si no usa un equilibrador de carga puede especificar la dirección de un nodo en el clúster, pero todas las solicitudes se dirigirá al que nodo y puede ocasionar que se convierta en una botella.

* **Indexstr**. Este es el nombre del índice que se agregan los datos generados por la prueba JUnit. Si ha creado el índice, como se describió anteriormente, este valor debe ser *systembase*.

* **Typestr**. Esto es el tipo en el índice donde se almacenan los datos. Si ha creado el índice, como se describió anteriormente, este valor debe ser *registros*.

* **Puerto**. Este es el puerto para conectarse a en el host. En la mayoría de los casos, se debe establecer a 9300 (el puerto que se utiliza Elasticsearch para escuchar las solicitudes de clientes API, puerto 9200 solo se utiliza para las solicitudes HTTP).

* **NombreDeClúster**. Este es el nombre del clúster Elasticsearch que contiene el índice.

* **ItemsPerInsert**. Se trata de un parámetro numérico que indica el número de documentos que desea agregar en cada lote de inserción masiva. El tamaño del lote predeterminado es 1000.

Especifique los datos de la cadena de constructor en la página de solicitud de JUnit que se usa para configurar el Reproductor de JUnit en JMeter. La imagen siguiente muestra un ejemplo:

![](media/guidance-elasticsearch/data-ingestion-image22.png)

Los métodos *BulkInsertTest* y *BigBulkInsertTest* realizan el trabajo real de generar y cargar los datos. Ambos métodos son muy similares. Conecte al clúster Elasticsearch y, a continuación, crea un lote de documentos (según lo determinado por el parámetro de cadena de constructor *ItemsPerInsert* ). Los documentos se agregan al índice de uso de la API de masa Elasticsearch. La diferencia entre los dos métodos es que los campos de cadena *data1* y *datos2* en cada documento se omiten de la carga en el método *BulkInsertTest* , pero se rellenan con las cadenas de 12000 caracteres en el método *BigBulkInsertTest* . Tenga en cuenta que seleccione cuál de estos métodos para ejecutar mediante el cuadro *Método de prueba* en la página de solicitud de JUnit en JMeter (resaltado en la figura anterior).

> [AZURE.NOTE] El código de ejemplo que se presenta aquí utiliza la biblioteca de cliente de transporte de Elasticsearch 1.7.3. Si está utilizando Elasticsearch 2.0.0 o versiones posteriores, debe usar la biblioteca adecuada para la versión seleccionada. Para obtener más información acerca de la biblioteca de cliente de transporte Elasticsearch 2.0.0, consulte la página de [Cliente de transporte](https://www.elastic.co/guide/en/elasticsearch/client/java-api/2.0/transport-client.html) en el sitio Web de Elasticsearch.

[Configuración resistencia y recuperación en Elasticsearch en Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Crear un entorno de prueba para Elasticsearch en Azure de rendimiento]: guidance-elasticsearch-creating-performance-testing-environment.md
[Las pruebas de rendimiento Elasticsearch automatizado]: guidance-elasticsearch-running-automated-performance-tests.md
[Implementar un muestrario JMeter JUnit para realizar pruebas de rendimiento Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
