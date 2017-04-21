<properties 
    pageTitle="Cómo supervisar Azure Redis caché | Microsoft Azure" 
    description="Obtenga información sobre cómo supervisar el rendimiento y la salud de las instancias de Azure Redis caché" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="sdanie"/>

# <a name="how-to-monitor-azure-redis-cache"></a>Cómo supervisar Azure Redis caché

Caché Redis Azure ofrece varias opciones para la supervisión de las instancias de la memoria caché. Puede ver métricas, anclar gráficos métricas a la Startboard, personalizar el intervalo de fecha y hora de supervisión gráficos, agregar y quitar métricas de los gráficos y establecer alertas cuando se cumplan ciertas condiciones. Estas herramientas permiten supervisar el estado de las instancias de Azure Redis caché y ayudarle a administrar las aplicaciones de almacenamiento en caché.

Cuando se habilitan diagnósticos de caché, métricas para instancias de Azure Redis caché se recopilan aproximadamente cada 30 segundos y se almacenan para que puedan muestra en los gráficos de métricas y examinando las reglas de alertas.

Se recopilan mediciones de caché mediante la Redis comando [INFO](http://redis.io/commands/info) . Para obtener más información acerca de la información de diferentes valores que se utilizan para cada métrica de caché, consulte [métricas disponibles y los intervalos de notificación](#available-metrics-and-reporting-intervals).

Para ver estadísticas de caché, [vaya](cache-configure.md#configure-redis-cache-settings) a la instancia de caché en el [portal de Azure](https://portal.azure.com). Métricas para instancias de Azure Redis caché se accede en el módulo **Redis métricas** .

![Redis métricas][redis-cache-redis-metrics-blade]

>[AZURE.IMPORTANT] Si se muestra el siguiente mensaje en el módulo **Redis métricas** , siga los pasos de la sección [Habilitar diagnósticos de memoria caché](#enable-cache-diagnostics) para habilitar diagnósticos de memoria caché.
>
>`Monitoring may not be enabled. Click here to turn on Diagnostics.`

El módulo **Redis métricas** tiene gráficos de **supervisión** que se muestran las mediciones de caché. Cada gráfico puede personalizarse agregando o quitando métricas y cambiar el intervalo de información. Para ver y configurar las operaciones y alertas, el módulo de **Caché Redis** tiene una sección de **operaciones** que se muestra la caché de **eventos** y **las reglas de alertas**.

## <a name="enable-cache-diagnostics"></a>Habilitar diagnósticos de caché

Caché de Redis Azure proporciona la capacidad de datos de diagnósticos almacenados en una cuenta de almacenamiento para poder usar todas las herramientas que desea obtener acceso y procesar los datos directamente. Fin de diagnósticos de caché que se recopilen, almacena y se muestra en el portal de Azure, debe configurarse una cuenta de almacenamiento. Caché en la misma región y de suscripción compartan la misma cuenta de almacenamiento de información de diagnóstico y cuando se cambia la configuración se aplica a todas las caché en la suscripción que están en esa región.

Para habilitar y configurar diagnósticos de caché, desplácese hasta el módulo de **Caché Redis** para la instancia de caché. Si aún no se ha habilitado diagnósticos, se muestra un mensaje en lugar de un gráfico de diagnósticos.

![Habilitar diagnósticos de caché][redis-cache-enable-diagnostics]

Haga clic en el mensaje para mostrar el módulo de **métrica** y haga clic en **Opciones de diagnóstico** para habilitar y configurar las opciones de diagnósticos para la instancia de servicio de caché.

![Configuración de diagnósticos][redis-cache-diagnostic-settings]

![Configurar diagnósticos][redis-cache-configure-diagnostics]

Haga clic en el botón **de** para habilitar diagnósticos de memoria caché y mostrar la configuración de diagnósticos.

Haga clic en la flecha situada a la derecha de la **Cuenta de almacenamiento** para seleccionar una cuenta de almacenamiento para almacenar los datos de diagnóstico. Para mejorar el rendimiento, seleccione una cuenta de almacenamiento en la misma región como la memoria caché.

Una vez que se haya configurado la configuración de diagnóstico, haga clic en **Guardar** para guardar la configuración. Tenga en cuenta que puede tardar unos minutos para que los cambios surtan efecto.

>[AZURE.IMPORTANT] Caché en la misma región y de suscripción comparten la misma configuración de almacenamiento de información de diagnóstico y cuando la configuración es modificado (diagnóstico habilitado o deshabilitado o cambiar la cuenta de almacenamiento) se aplica a todas las caché en la suscripción que están en esa región.

Para ver las métricas almacenadas, examine las tablas de su cuenta de almacenamiento con nombres que empiecen por `WADMetrics`. Para obtener más información sobre el acceso a las mediciones almacenadas fuera del portal de Azure, vea el ejemplo de [control de acceso Redis caché de datos](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) .

>[AZURE.NOTE] Sólo los indicadores que se almacenan en la cuenta de almacenamiento seleccionado se muestran en el portal de Azure. Si cambia las cuentas de almacenamiento, los datos de la cuenta de almacenamiento configurado previamente sigue estando disponibles para su descarga, pero no se muestra en el portal de Azure.  

## <a name="available-metrics-and-reporting-intervals"></a>Métricas disponibles y los intervalos de notificación

Métrica de caché se notifica mediante varios intervalos de informes, incluidos de **última hora**, **hoy**, **semana pasada**y **personalizado**. El módulo de **métrica** para cada gráfico métricas muestra los valores de promedio, mínimos y máximos para cada métrica en el gráfico y, a continuación, algunas métricas mostrar un total para el intervalo de información. 

Cada métrica incluye dos versiones. Una métrica mide el rendimiento de toda la caché y de caché que usan [clústeres](cache-how-to-premium-clustering.md), una segunda versión de la métrica que incluye `(Shard 0-9)` en el rendimiento de medidas de nombre para una sola fragmentar en la memoria caché. Por ejemplo, si una caché tiene 4 shards, `Cache Hits` es la cantidad total de visitas para toda la caché, y `Cache Hits (Shard 3)` es solo las visitas para ese partes de la memoria caché.

>[AZURE.NOTE] Incluso cuando la caché está inactiva con ninguna aplicación cliente active conectado, es posible que vea algunos actividad de caché, como los clientes conectados, el uso de memoria y realiza operaciones. Esta actividad es normal durante la operación de una instancia de Azure Redis caché.

| Métrica            | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Visitas a la caché        | El número de búsquedas de clave con éxito durante el intervalo informes especificado. Se asigna a `keyspace_hits` desde Redis [información](http://redis.io/commands/info) de comandos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Errores de caché      | El número de búsquedas de claves de errores durante el intervalo informes especificado. Se asigna a `keyspace_misses` desde el comando Redis INFO. Caché necesariamente que hay un problema con la memoria caché. Por ejemplo, cuando se utiliza el modelo de programación de caché separada, una aplicación busca primera en la caché de un elemento. Si el elemento no está (error de caché), el elemento se recuperados de la base de datos y se agrega a la caché para la próxima vez. Errores de caché son comportamiento normal del modelo de programación de caché separada. Si el número de errores de caché es mayor de lo esperado, examine la lógica de la aplicación que rellena y lee de la memoria caché. Si elementos que se elimina de la caché debido a memoria insuficiente, a continuación, es posible que algunos errores de caché, pero sería una métrica mejor para supervisar la memoria `Used Memory` o `Evicted Keys`. |
| Clientes conectados | El número de conexiones de cliente a la memoria caché durante el intervalo informes especificado. Se asigna a `connected_clients` desde el comando Redis INFO. Una vez que se ha alcanzado el [límite de conexión](cache-configure.md#default-redis-server-configuration) producirá intentos de conexión posteriores a la memoria caché. Tenga en cuenta que incluso si no hay ninguna aplicación cliente activo, puede haber algunos casos de clientes conectados debido a los procesos internos y conexiones.                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Teclas expulsadas      | El número de elementos que se elimina de la caché durante el intervalo informes especificado debido a la `maxmemory` límite. Se asigna a `evicted_keys` desde el comando Redis INFO.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Teclas expiradas      | Caducado el número de elementos de la memoria caché durante el intervalo informes especificado. Este valor se asigna a `expired_keys` desde el comando Redis INFO.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Obtiene              | El número de operaciones de obtener de la memoria caché durante el intervalo informes especificado. Este valor es la suma de los siguientes valores de la información de Redis todos los comandos: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, y `cmdstat_getrange`y es equivalente a la suma de caché y errores durante el intervalo de informes.                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Redis carga del servidor | El porcentaje de ciclos en el que el servidor Redis está ocupado procesar y no espera inactivo para los mensajes. Si este contador llega a 100 significa el servidor Redis ha llegado un límite de rendimiento y no puede procesar la CPU trabaje más rápido cualquiera. Si está viendo la carga es alta servidor Redis verá excepciones de tiempo de espera en el cliente. En este caso debe considerar escalado o dividir los datos en caché varios.                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Conjuntos de              | El número de operaciones de conjunto a la memoria caché durante el intervalo informes especificado. Este valor es la suma de los siguientes valores de la información de Redis comando todo: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange`, y `cmdstat_setnx`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Total de operaciones  | El número total de comandos procesados por el servidor de caché durante el intervalo informes especificado. Este valor se asigna a `total_commands_processed` desde el comando Redis INFO. Tenga en cuenta que cuando se utiliza la caché de Azure Redis puramente para pub/sub no habrá ninguna métricas para `Cache Hits`, `Cache Misses`, `Gets`, o `Sets`, pero habrá `Total Operations` métricas que reflejen el uso de la memoria caché para operaciones de pub/sub.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Memoria utilizada       | La cantidad de memoria caché que usa pares de clave y valor en la memoria caché en MB durante el intervalo informes especificado. Este valor se asigna a `used_memory` desde el comando Redis INFO. No se incluyen metadatos o fragmentación.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Memoria utilizada RSS   | La cantidad de memoria caché que usa en MB durante el intervalo especificado de generación de informes, incluidas fragmentación y metadatos. Este valor se asigna a `used_memory_rss` desde el comando Redis INFO.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| CPU               | El uso de CPU del servidor de Azure Redis caché como un porcentaje durante el intervalo informes especificado. Este valor se asigna al sistema operativo `\Processor(_Total)\% Processor Time` contador de rendimiento.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Lectura de caché        | Lea la cantidad de datos de la caché en Megabytes por segundo (MB/s) durante el intervalo informes especificado. Este valor se deriva de las tarjetas de interfaz de red que admiten la máquina virtual que hospeda la caché y no es Redis específicos. **Este valor corresponde al ancho de banda de red usado por esta caché. Si desea configurar las alertas de los límites de ancho de banda de red de lado de servidor, a continuación, crear mediante esta `Cache Read` contador. Consulte [esta tabla](cache-faq.md#cache-performance) para los límites de ancho de banda observado para caché distintos niveles y tamaños de precios.**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Memoria caché de escritura       | Intervalo de informes de la cantidad de datos que se escriben en la memoria caché en Megabytes por segundo (MB/s) durante la especificado. Este valor se deriva de las tarjetas de interfaz de red que admiten la máquina virtual que hospeda la caché y no es Redis específicos. Este valor corresponde al ancho de banda de red de datos que se envían a la memoria caché del cliente.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |


## <a name="how-to-view-metrics-and-customize-charts"></a>Cómo ver métricas y personalizar gráficos

Puede ver una descripción general de las mediciones de la memoria caché en el módulo **Redis métricas** . Para obtener acceso a la **Redis métricas** placa elija **toda la configuración** > **Redis métricas**.

![Redis métricas][redis-cache-redis-metrics]


El módulo **Redis métricas** contiene los siguientes gráficos.

| Redis gráfico métricas | Métrica de muestra     |
|------------------|-------------------|
| Lectura de caché y caché de escritura | Lectura de caché |
|                            | Memoria caché de escritura |
| Clientes conectados      | Clientes conectados |
| Visitas y errores  | Visitas a la caché        |
|                  | Errores de caché      |
| Total de comandos   | Total de operaciones  |
| Obtiene y conjuntos    | Obtiene              |
|                  | Conjuntos de              |
| Uso de la CPU         | CPU           |
| Uso de la memoria      | Memoria utilizada   |
|                   | Memoria utilizada RSS |
| Redis carga del servidor | Carga del servidor   |
| Recuento de claves | Total de claves |
|           | Teclas expulsadas |
|           | Teclas expiradas |


Para obtener una vista más detallada de las mediciones en un gráfico específico y para personalizar el gráfico, haga clic en el gráfico que desee desde el módulo **Redis métricas** para mostrar el módulo **métrica** de dicho gráfico.

![Métrica placa][redis-cache-metric-blade]

Se muestran todas las alertas que se establecen las métricas muestra un gráfico en la parte inferior del módulo **métrica** de dicho gráfico.

Para agregar o quitar métricas o cambiar el intervalo de informes, elija **Editar gráfico**.

Para agregar o quitar métricas del gráfico, haga clic en la casilla de verificación junto al nombre de la métrica. Para cambiar el intervalo de informes, haga clic en el intervalo deseado. Para cambiar el **tipo de gráfico**, haga clic en el estilo que desee. Una vez realizados los cambios que desee, haga clic en **Guardar**. 

![Editar gráfico][redis-cache-edit-chart]

Al hacer clic en **Guardar** los cambios se conservan hasta que abandone el módulo de **métrica** . Cuando vuelva más tarde, verá la métrica original y el intervalo de tiempo nuevamente. Para obtener más información sobre cómo personalizar gráficos, vea [métrica de servicio Monitor](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Para ver las métricas durante un período de tiempo específico en un gráfico, mantenga el mouse sobre uno de los puntos en el gráfico que corresponde a la hora deseada o barras concretas, y se muestran las mediciones para ese intervalo.

![Ver detalles de un gráfico][redis-cache-view-chart-details]

## <a name="how-to-monitor-a-premium-cache-with-clustering"></a>Cómo supervisar una caché premium con clústeres

Caché de Premium que tienen [clústeres](cache-how-to-premium-clustering.md) habilitado pueden tener hasta 10 shards. Cada fragmentar tiene sus propias mediciones y se agregan estas métricas para proporcionar métricas para la caché como un todo. Cada métrica incluye dos versiones. Una métrica mide el rendimiento de toda la memoria caché y una segunda versión de la métrica que incluye `(Shard 0-9)` en el rendimiento de medidas de nombre para una sola fragmentar en la memoria caché. Por ejemplo, si una caché tiene 3 shards, `Cache Hits` es la cantidad total de visitas para toda la caché, y `Cache Hits (Shard 2)` es solo las visitas para ese partes de la memoria caché.

Cada gráfico supervisión muestra las métricas de nivel superior de la memoria caché junto con las métricas de cada partes de la memoria caché.

![Monitor][redis-cache-premium-monitor]

Colocar el puntero del mouse sobre los puntos de datos muestra los detalles para ese punto en el tiempo. 

![Monitor][redis-cache-premium-point-summary]

Los valores mayores suelen ser los valores agregados de la caché, mientras que los valores más pequeños son las medidas individuales para las partes. Tenga en cuenta que en este ejemplo, hay tres shards y la caché está distribuidos uniformemente por la shards.

![Monitor][redis-cache-premium-point-shard]

Para ver más detalles haga clic en el gráfico para ver una vista expandida en el módulo de **métrica** .

![Monitor][redis-cache-premium-chart-detail]

De forma predeterminada cada gráfico incluye el contador de rendimiento de caché de nivel superior, así como los contadores de rendimiento para el shards individuales. Puede personalizar estos en el módulo de **Editar gráfico** .

![Monitor][redis-cache-premium-edit]

Para obtener más información sobre contadores de rendimiento disponibles, vea [métricas disponibles y los intervalos de notificación](#available-metrics-and-reporting-intervals).


## <a name="operations-and-alerts"></a>Operaciones y alertas

La sección de **operaciones** en el módulo de **Caché Redis** tiene secciones de **eventos** y **las reglas de alertas** .

![Oeprations][redis-cache-operations-events]

Para ver una lista de operaciones de caché recientes, haga clic en el gráfico de **eventos** para mostrar el módulo de **eventos** . Algunos ejemplos de operaciones recuperar y regenerar teclas de acceso y la activación y la resolución de las reglas de alertas. Para obtener más información acerca de cada evento, haga clic en el evento en el módulo de **eventos** .

Para obtener más información sobre los eventos, vea [Ver eventos y registros de auditoría](../monitoring-and-diagnostics/insights-debugging-with-events.md).

La sección **reglas de alerta** muestra el recuento de las alertas de la instancia de caché. Una regla de alerta le permite supervisar la instancia de caché y recibe un correo electrónico siempre que un valor de métrica alcanza el umbral definido en la regla. 

Las reglas de alertas se evalúan aproximadamente cada cinco minutos y, cuando se activa una regla de alerta, se envían las notificaciones configuradas. Notificaciones y activaciones de regla de alerta no se procesan instantáneamente; puede haber un retraso de unos minutos antes de que se activa una regla de alertas y notificaciones enviadas.

Pueden ver las reglas de alertas y establecer desde el módulo de **métrica** para un gráfico de supervisión específico, o desde el módulo de **reglas de alerta** .

Reglas de alerta tienen las siguientes propiedades.

| Propiedad regla de alerta                 | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
|-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Recursos                            | El recurso que se evalúa por la regla de alerta. Al crear una regla de alerta de la memoria caché Redis, la caché es el recurso.                                                                                                                                                                                                                                                                                                                                                  |
| Nombre                                | Nombre que identifica la regla de alerta dentro de la instancia actual de la memoria caché.                                                                                                                                                                                                                                                                                                                                                                                       |
| Descripción                         | Descripción opcional de la regla de alerta.                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Métrica                              | Métrica para supervisar la regla de alerta. Para una lista de las mediciones de caché, consulte métricas disponibles y los intervalos de notificación.                                                                                                                                                                                                                                                                                                                                             |
| Condición                           | Operador de condición para la regla de alerta. Posibles opciones son: mayor que, mayor o igual a, menor que, menor o igual a                                                                                                                                                                                                                                                                                                                             |
| Umbral                           | El valor que se usa para comparar con la métrica usando el operador especificado por la propiedad de condición. Dependiendo de la métrica, este valor puede ser bytes por segundo, en bytes, % o recuento.                                                                                                                                                                                                                                                                                     |
| Período                              | Especifica el período en los que se usa el valor promedio de la métrica para la comparación de regla de alerta. Por ejemplo, si el período es sobre la última hora, el valor promedio de la métrica sobre el intervalo de hora anterior se utiliza para la comparación. Si desea recibir una notificación cuando se alcanza el umbral debido a un aumento de actividad, un período más corto es adecuado. Para recibir una notificación cuando hay actividad prolongado por encima del umbral, use un período más largo. |
| Servicio de correo electrónico y coadministradores | Cuando es true, el administrador del servicio y el administrador compañeros están enviado un correo electrónico cuando se activa la alerta.                                                                                                                                                                                                                                                                                                                                                                    |
| Correo electrónico de administrador adicionales      | Dirección de correo electrónico opcional de administrador adicional para recibir una notificación cuando se activa la alerta.                                                                                                                                                                                                                                                                                                                                                                    |

Solo una notificación se envía por la activación de la regla de alerta. Una vez que se excede el umbral de una regla y se envía una notificación, la regla no se vuelve a evaluar hasta que la métrica está por debajo del umbral. Si la métrica posteriormente supera el umbral, se vuelve a activar la alerta y se envía una notificación de nuevo.

Para ver todas las reglas de alerta de la instancia de caché, haga clic en el elemento de **las reglas de alertas** en el módulo de **Caché Redis** . Para ver solo las reglas de alertas que usan una métrica específica, desplácese hasta el módulo de **métrica** para el gráfico que contiene dicha métrica.

![Reglas de alertas][redis-cache-alert-rules]

Para agregar una regla de alerta, haga clic en **Agregar aviso** en el módulo de **sistema métrico** o el módulo de **reglas de alerta** . 

Introduzca los criterios de la regla que desee en el módulo de regla de **Agregar una alerta** y haga clic en **Aceptar**. 

![Agregar regla de alerta][redis-cache-add-alert]

>[AZURE.NOTE] Cuando se crea una regla de alerta haciendo clic en **Agregar aviso** en el módulo de **métrica** , solo las medidas que se muestra en el gráfico en esa hoja aparecen en la lista desplegable **métrica** . Cuando se crea una regla de alerta haciendo clic en **Agregar aviso** en el módulo de **reglas de alertas** , todas las medidas de caché están disponibles en la lista desplegable **métrica** .

Una vez que se guarda una regla de alerta aparece en el módulo de **reglas de alertas** y también en el módulo de **métrica** para los gráficos que se muestran la métrica utilizada en la regla de alerta. Para editar una regla de alerta, haga clic en el nombre de la regla de alerta para mostrar el módulo de **Editar regla** . Desde el módulo de **Editar regla** puede editar las propiedades de la regla, eliminar o deshabilitar la regla de alerta o volver a habilitar la regla si previamente se ha deshabilitado.

>[AZURE.NOTE] Los cambios que realice en las propiedades de la regla pueden tardar unos instantes antes de que se reflejan en el módulo de **reglas de alertas** o el módulo de **métrica** .

Cuando se activa una regla de alerta, se envía un correo electrónico según la configuración de la regla de alerta y se muestra un icono de alerta en la parte de **las reglas de alertas** en el módulo de **Caché Redis** .

Se considera una regla de alerta resolverse cuando la condición de alerta ya no se evalúa como verdadero. Una vez que se soluciona la condición de regla de alerta, el icono de alerta se reemplaza con una marca de verificación. Para obtener detalles sobre la activación de alerta y resoluciones, haga clic en el elemento de **eventos** en el módulo de **Caché Redis** para ver los eventos en el módulo de **eventos** .

Para obtener más información acerca de las alertas en Azure, vea [recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

## <a name="metrics-on-the-redis-cache-blade"></a>Métricas en el módulo de caché Redis

El módulo de **Caché Redis** muestra las siguientes categorías de métricas.

-   [Gráficos de supervisión](#monitoring-charts)
-   [Gráficos de uso](#usage-charts)

### <a name="monitoring-charts"></a>Gráficos de supervisión

La sección de **supervisión** tiene **llega y errores**, **obtiene y establece**, **conexiones**y **Comandos Total** gráficos.

![Gráficos de supervisión][redis-cache-monitoring-part]

Los gráficos de **supervisión** muestran las siguientes métricas.

| Gráfico de supervisión | Métrica de caché     |
|------------------|-------------------|
| Visitas y errores  | Visitas a la caché        |
|                  | Errores de caché      |
| Obtiene y conjuntos    | Obtiene              |
|                  | Conjuntos de              |
| Conexiones      | Clientes conectados |
| Total de comandos   | Total de operaciones  |

Para obtener información sobre cómo ver las estadísticas y personalizar los gráficos individuales en esta sección, vea la siguiente sección [cómo métricas de ver y personalizar indicadores gráficos](#how-to-view-metrics-and-customize-charts) .

### <a name="usage-charts"></a>Gráficos de uso

La sección **uso** tiene **Redis carga del servidor**, **El uso de memoria**, **Ancho de banda de red**y gráficos de **Uso de CPU** y también muestra el **nivel de precios** para la instancia de caché.

![Gráficos de uso][redis-cache-usage-part]

El **nivel de precios** se muestra el precio de caché de nivel y se pueden utilizar a [escala de](cache-how-to-scale.md) la memoria caché a un nivel de precio diferente.

Los gráficos de **uso** muestran las siguientes métricas.

| Gráfico de uso       | Métrica de caché |
|-------------------|---------------|
| Redis carga del servidor | Carga del servidor   |
| Uso de la memoria      | Memoria utilizada   |
| Ancho de banda de red | Memoria caché de escritura   |
| Uso de la CPU         | CPU           |

Para obtener información sobre cómo ver las estadísticas y personalizar los gráficos individuales en esta sección, vea la siguiente sección [cómo métricas de ver y personalizar indicadores gráficos](#how-to-view-metrics-and-customize-charts) .
  
<!-- IMAGES -->

[redis-cache-enable-diagnostics]: ./media/cache-how-to-monitor/redis-cache-enable-diagnostics.png

[redis-cache-diagnostic-settings]: ./media/cache-how-to-monitor/redis-cache-diagnostic-settings.png

[redis-cache-configure-diagnostics]: ./media/cache-how-to-monitor/redis-cache-configure-diagnostics.png

[redis-cache-monitoring-part]: ./media/cache-how-to-monitor/redis-cache-monitoring-part.png

[redis-cache-usage-part]: ./media/cache-how-to-monitor/redis-cache-usage-part.png

[redis-cache-metric-blade]: ./media/cache-how-to-monitor/redis-cache-metric-blade.png

[redis-cache-edit-chart]: ./media/cache-how-to-monitor/redis-cache-edit-chart.png

[redis-cache-view-chart-details]: ./media/cache-how-to-monitor/redis-cache-view-chart-details.png

[redis-cache-operations-events]: ./media/cache-how-to-monitor/redis-cache-operations-events.png

[redis-cache-alert-rules]: ./media/cache-how-to-monitor/redis-cache-alert-rules.png

[redis-cache-add-alert]: ./media/cache-how-to-monitor/redis-cache-add-alert.png

[redis-cache-premium-monitor]: ./media/cache-how-to-monitor/redis-cache-premium-monitor.png

[redis-cache-premium-edit]: ./media/cache-how-to-monitor/redis-cache-premium-edit.png

[redis-cache-premium-chart-detail]: ./media/cache-how-to-monitor/redis-cache-premium-chart-detail.png

[redis-cache-premium-point-summary]: ./media/cache-how-to-monitor/redis-cache-premium-point-summary.png

[redis-cache-premium-point-shard]: ./media/cache-how-to-monitor/redis-cache-premium-point-shard.png

[redis-cache-redis-metrics]: ./media/cache-how-to-monitor/redis-cache-redis-metrics.png

[redis-cache-redis-metrics-blade]: ./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png



