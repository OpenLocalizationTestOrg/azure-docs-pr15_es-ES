<properties 
    pageTitle="Cómo configurar la caché de Azure Redis | Microsoft Azure"
    description="Comprender la configuración predeterminada de Redis para Azure Redis caché y obtenga información sobre cómo configurar las instancias de Azure Redis caché"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/25/2016"
    ms.author="sdanie" />

# <a name="how-to-configure-azure-redis-cache"></a>Cómo configurar la memoria caché Redis de Azure

Este tema describe cómo revisar y actualizar la configuración de las instancias de Azure Redis caché y trata sobre la configuración predeterminada de Redis server para instancias de Azure Redis caché.

>[AZURE.NOTE] Para obtener más información sobre cómo configurar y usar las características premium de caché, consulte [cómo configurar persistencia para una caché de Redis Premium Azure](cache-how-to-premium-persistence.md), [cómo configurar clústeres para una caché de Redis Premium Azure](cache-how-to-premium-clustering.md)y [cómo configurar la compatibilidad de red Virtual para una caché de Redis Premium Azure](cache-how-to-premium-vnet.md).

## <a name="configure-redis-cache-settings"></a>Configurar las opciones de caché Redis

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Caché Redis Azure proporciona la siguiente configuración en el módulo de **configuración** .

![Configuración de la caché de Redis](./media/cache-configure/redis-cache-settings.png)



-   [Soporte técnico y solución de problemas de configuración](#support-amp-troubleshooting-settings)
-   [Configuración general](#general-settings)
    -   [Propiedades](#properties)
    -   [Teclas de acceso](#access-keys)
    -   [Configuración avanzada](#advanced-settings)
    -   [Redis Asesor de caché](#redis-cache-advisor)
-   [Configuración de escala](#scale-settings)
    -   [Nivel de precios](#pricing-tier)
    -   [Tamaño de clúster de Redis](#cluster-size)
-   [Configuración de la administración de datos](#data-management-settings)
    -   [Redis persistencia de datos](#redis-data-persistence)
    -   [Importar o exportar](#importexport)
-   [Configuración de administración](#administration-settings)
    -   [Reiniciar el equipo](#reboot)
    -   [Actualizaciones de programación](#schedule-updates)
-   [Configuración de diagnósticos](#diagnostics-settings)
-   [Configuración de la red](#network-settings)
-   [Configuración de la administración de recursos](#resource-management-settings)

## <a name="support--troubleshooting-settings"></a>Soporte técnico y solución de problemas de configuración

La configuración en la sección de **soporte técnico + solución de problemas** le proporciona opciones para resolver problemas con la memoria caché.

![Soporte técnico + solución de problemas](./media/cache-configure/redis-cache-support-troubleshooting.png)

Haga clic en **diagnosticar y solucionar los problemas de** a problemas comunes y estrategias para resolverlos.

Haga clic en el **registro de actividad** para ver las acciones que se realizan en la memoria caché. También puede usar el filtrado para ampliar esta vista para incluir otros recursos. Para obtener más información sobre cómo trabajar con registros de auditoría, consulte [Ver eventos y registros de auditoría](../monitoring-and-diagnostics/insights-debugging-with-events.md) y [auditoría de las operaciones con el Administrador de recursos](../resource-group-audit.md). Para obtener más información sobre la supervisión de eventos de Azure Redis caché, vea [operaciones y alertas](cache-how-to-monitor.md#operations-and-alerts).

**Estado de los recursos** inspecciones de los recursos y le indica si se está ejecutando según lo esperado. Para obtener más información sobre el servicio de estado de los recursos de Azure, vea [información general sobre el estado de recursos de Azure](../resource-health/resource-health-overview.md).

>[AZURE.NOTE] Estado de los recursos está actualmente no puede informar sobre el mantenimiento de instancias de Azure Redis caché alojado en una red virtual. Para obtener más información, vea [todas las características de caché funcionan al hospedar una caché en un VNET?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

Haga clic en **nueva solicitud de soporte técnico** para abrir una solicitud de soporte técnico de la memoria caché.

## <a name="general-settings"></a>Configuración general

La configuración de la sección **General** permite acceso y configurar las siguientes opciones de la memoria caché.

![Configuración general](./media/cache-configure/redis-cache-general-settings.png)

-   [Propiedades](#properties)
-   [Teclas de acceso](#access-keys)
-   [Configuración avanzada](#advanced-settings)
-   [Redis Asesor de caché](#redis-cache-advisor)

### <a name="properties"></a>Propiedades

Haga clic en **Propiedades** para ver información sobre la memoria caché, incluidos los puertos y un punto final de la memoria caché.

![Redis propiedades de caché](./media/cache-configure/redis-cache-properties.png)

### <a name="access-keys"></a>Teclas de acceso

Haga clic en **las teclas de acceso** para ver o volver a crear las teclas de acceso de la memoria caché. Estas teclas se utilizan junto con el nombre de host y puertos de la hoja de **Propiedades de** los clientes a conectarse a la memoria caché.

![Redis teclas de acceso de caché](./media/cache-configure/redis-cache-manage-keys.png)






### <a name="advanced-settings"></a>Configuración avanzada

La siguiente configuración se configurar en el módulo de **Configuración avanzada** .

-   [Puertos de Access](#access-ports)
-   [Directiva de MaxMemory y reservado maxmemory](#maxmemory-policy-and-maxmemory-reserved)
-   [Notificaciones de espacio de claves (configuración avanzadas)](#keyspace-notifications-advanced-settings)


### <a name="access-ports"></a>Puertos de Access

De forma predeterminada, access no SSL está deshabilitado para la caché de nuevas. Para habilitar el puerto no SSL, haga clic en **No** para **Permitir el acceso sólo a través de SSL** en el **módulo de configuración avanzada** y, a continuación, haga clic en **Guardar**.

![Redis puertos de acceso de caché](./media/cache-configure/redis-cache-access-ports.png)

### <a name="maxmemory-policy-and-maxmemory-reserved"></a>Directiva de MaxMemory y reservado maxmemory

La configuración de **Directiva de Maxmemory** y **reservados maxmemory** en el módulo de **Configuración avanzada de** configura las directivas de memoria para la caché. La configuración de **Directiva maxmemory** configura la directiva de expulsión de la caché y **reservados maxmemory** la memoria reservada para procesos sin caché.

![Redis directiva Maxmemory de caché](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Directiva de MaxMemory** le permite elegir entre las siguientes directivas de expulsión.

-   volátiles-lru - esto es el valor predeterminado.
-   AllKeys lru
-   volátiles aleatorios
-   AllKeys aleatorios
-   ttl volátiles
-   noeviction

Para obtener más información acerca de las directivas de maxmemory, vea [directivas de expulsión](http://redis.io/topics/lru-cache#eviction-policies).

El valor **reservados maxmemory** configura la cantidad de memoria en MB que se reserva para operaciones de caché no como la replicación durante la migración tras error. También puede utilizarse cuando tenga una relación de alta fragmentación. Establecer este valor le permite tener una experiencia de servidor Redis más coherente cuando varía la carga. Este valor se debe establecer una versión posterior para cargas de trabajo que se escriben a visible. Cuando se reserva memoria para operaciones no está disponible para el almacenamiento de datos en caché.

>[AZURE.IMPORTANT] El valor **reservados maxmemory** solo está disponible para estándar y Premium almacena en caché.

### <a name="keyspace-notifications-advanced-settings"></a>Notificaciones de espacio de claves (configuración avanzadas)

Redis espacio de claves notificaciones se configuran en el módulo de **Configuración avanzada** . Notificaciones de espacio de claves permiten a los clientes recibir notificaciones cuando se producen determinados eventos.

![Redis caché configuración avanzada](./media/cache-configure/redis-cache-advanced-settings.png)

>[AZURE.IMPORTANT] Notificaciones de espacio de claves y la configuración de **eventos de espacio de claves le notifique** solo están disponibles para estándar y Premium almacena en caché.

Para obtener más información, vea [Redis notificaciones de espacio de claves](http://redis.io/topics/notifications). Código de ejemplo, vea el archivo [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) en el ejemplo de [Hola a todos](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) .

<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Redis Asesor de caché

El módulo de **recomendaciones** muestra recomendaciones para la memoria caché. Durante el funcionamiento normal, no se muestra ninguna recomendación. 

![Recomendaciones](./media/cache-configure/redis-cache-no-recommendations.png)

Si las condiciones que se producen durante las operaciones de la memoria caché como mucha memoria, ancho de banda o carga del servidor, se muestra una alerta en el módulo de **Caché Redis** .

![Recomendaciones](./media/cache-configure/redis-cache-recommendations-alert.png)

Puede encontrar más información en el módulo de **recomendaciones** .

![Recomendaciones](./media/cache-configure/redis-cache-recommendations.png)

Puede supervisar estas métricas en las secciones de [los gráficos de supervisión](cache-how-to-monitor.md#monitoring-charts) y de [uso](cache-how-to-monitor.md#usage-charts) del módulo de **Caché Redis** .

Cada nivel de precios tiene límites diferentes para las conexiones de cliente, memoria y ancho de banda. Si la memoria caché aproxima a la capacidad máxima para estas métricas sobre un período de tiempo prolongado, se crea una recomendación. Para obtener más información acerca de las mediciones y los límites a usar la herramienta de **recomendaciones** , vea la tabla siguiente.

| Redis métrica de caché      | Para obtener más información, vea                                                  |
|-------------------------|---------------------------------------------------------------------------|
| Uso de ancho de banda de red | [Rendimiento de la caché: ancho de banda disponible](cache-faq.md#cache-performance) |
| Clientes conectados       | [Configuración de servidor Redis - maxclients predeterminados](#maxclients)            |
| Carga del servidor             | [Gráficos de uso - Redis carga del servidor](cache-how-to-monitor.md#usage-charts)  |
| Uso de la memoria            | [Rendimiento de la caché - tamaño](cache-faq.md#cache-performance)                |

Para actualizar la memoria caché, haga clic en **Actualizar ahora** para cambiar el [nivel de precios](#pricing-tier) y ajustar el tamaño de la memoria caché. Para obtener más información sobre cómo elegir un nivel de precios, consulte [qué oferta de caché Redis y tamaño debo usar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="scale-settings"></a>Configuración de escala

La configuración en la sección de **escala de** permite acceso y configurar las siguientes opciones de la memoria caché.

![Red](./media/cache-configure/redis-cache-scale.png)

-   [Nivel de precios](#pricing-tier)
-   [Tamaño de clúster de Redis](#cluster-size)

### <a name="pricing-tier"></a>Nivel de precios

Haga clic en el **nivel de precios** para ver o cambiar el nivel de precios para la memoria caché. Para obtener más información sobre la escala, vea [Cómo escala Azure Redis caché](cache-how-to-scale.md).

![Redis precios nivel de caché](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>
### <a name="redis-cluster-size"></a>Tamaño de clúster de Redis

Haga clic en **Tamaño de clúster Redis (PREVIEW)** para cambiar el tamaño de clúster para un ejecución caché premium con clúster habilitado.

>[AZURE.NOTE] Tenga en cuenta que mientras se ha presentado el nivel de Azure Premium de caché Redis a disponibilidad General, la característica de tamaño de clúster Redis está actualmente en vista previa.

![Tamaño de clúster de Redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Para cambiar el tamaño de clúster, use el control deslizante o escriba un número comprendido entre 1 y 10 en el cuadro de texto de **recuento de partes** y haga clic en **Aceptar** para guardar.

>[AZURE.IMPORTANT] Redis clústeres solo está disponible para la caché de Premium. Para obtener más información, consulte [cómo configurar clústeres para una caché de Redis Premium Azure](cache-how-to-premium-clustering.md).


## <a name="data-management-settings"></a>Configuración de la administración de datos

La configuración en la sección **administración de datos** permite acceso y configurar las siguientes opciones de la memoria caché.

![Administración de datos](./media/cache-configure/redis-cache-data-management.png)

-   [Redis persistencia de datos](#redis-data-persistence)
-   [Importar o exportar](#importexport)

### <a name="redis-data-persistence"></a>Redis persistencia de datos

Haga clic en **Redis persistencia de datos** para habilitar, deshabilitar o configurar persistencia de los datos de la memoria caché de premium.

![Redis persistencia de datos](./media/cache-configure/redis-cache-persistence-settings.png)

Para habilitar la persistencia Redis, haga clic en **habilitado** para habilitar la copia de seguridad RDB (Redis base de datos). Para deshabilitar Redis conservación, haga clic en **deshabilitado**.

Para configurar el intervalo de copia de seguridad, seleccione una **Frecuencia de copia de seguridad** de la lista desplegable. Opciones incluyen **15 minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas**y **24 horas**. Este intervalo empieza a contar hacia abajo después de la operación de copia de seguridad anterior se completa correctamente y transcurre se inicia una nueva copia de seguridad.

Haga clic en la **Cuenta de almacenamiento** para seleccionar la cuenta de almacenamiento para utilizar y elija la **clave principal** o **clave secundaria** para usar en la lista desplegable de **Clave de almacenamiento** . Debe elegir una cuenta de almacenamiento en la misma región como la memoria caché y una cuenta de **Almacenamiento Premium** se recomienda porque el almacenamiento de premium tiene un rendimiento más alto. Cada vez que se regenera la clave de almacenamiento para su cuenta de persistencia, debe elegir volver a la clave que desee en la lista desplegable de **Clave de almacenamiento** .

Haga clic en **Aceptar** para guardar la configuración de persistencia.

>[AZURE.IMPORTANT] Solo está disponible para la caché de Premium Redis persistencia de los datos. Para obtener más información, consulte [cómo configurar persistencia para una caché de Redis Premium Azure](cache-how-to-premium-persistence.md).

### <a name="importexport"></a>Importar o exportar

Importar o exportar es una operación de administración de datos de Azure Redis caché que permite importar datos en caché de Azure Redis o exportar los datos de Azure Redis caché importar y exportar una instantánea de la base de datos de caché Redis (RDB) desde una caché premium a un blob de página en una cuenta de almacenamiento de Azure. Esto le permite migrar entre diferentes instancias de caché de Azure Redis o rellenar la caché de datos antes de usar.

Importar puede utilizarse para pasar los archivos Redis compatibles RDB de cualquier servidor Redis que se ejecute en cualquier nube o entorno, incluidos Redis ejecutando en Linux, Windows o en cualquier proveedor de nube como servicios Web de Amazon y otras personas. Importación de datos es una forma sencilla de crear una caché con datos rellenarán. Durante el proceso de importación Azure Redis caché carga los archivos RDB de Azure almacenamiento en memoria y luego inserta las teclas en la memoria caché.

Exportar le permite exportar los datos almacenados en caché de Redis de Azure Redis los archivos RDB compatibles. Puede usar esta característica para mover los datos de una instancia de Azure Redis caché a otro o a otro servidor Redis. Durante el proceso de exportación que se crea un archivo temporal en la máquina virtual que hospeda la instancia del servidor de Azure Redis caché y el archivo se cargará a la cuenta de almacenamiento designado. Cuando se complete la operación de exportación con estado de éxito o error, se elimina el archivo temporal.

>[AZURE.IMPORTANT] Importar o exportar solo está disponible para la caché de nivel Premium. Para obtener más información e instrucciones, consulte [Importar y exportar datos de Azure Redis caché](cache-how-to-import-export-data.md).


## <a name="administration-settings"></a>Configuración de administración

La configuración en la sección **administración** permite realizar las siguientes tareas administrativas en la memoria caché de premium. 

![Administración](./media/cache-configure/redis-cache-administration.png)

-   [Reiniciar el equipo](#reboot)
-   [Actualizaciones de programación](#schedule-updates)

>[AZURE.IMPORTANT] La configuración de esta sección solo está disponible para la caché de nivel Premium.

### <a name="reboot"></a>Reiniciar el equipo

El módulo **reinicie** permite reiniciar uno o varios de los nodos de la memoria caché. Esto le permite probar la aplicación de la resistencia en caso de error.

![Reiniciar el equipo](./media/cache-configure/redis-cache-reboot.png)

Si tiene una caché premium con clúster habilitado, puede seleccionar qué shards de la caché de reiniciar.

![Reiniciar el equipo](./media/cache-configure/redis-cache-reboot-cluster.png)

Reinicie uno o más nodos de la memoria caché, seleccione los nodos que desee y haga clic en **reiniciar**. Si tiene una caché premium con clúster habilitado, seleccione la shard(s) reiniciar y, a continuación, haga clic en **reiniciar**. Después de unos minutos, el reinicio de nodos seleccionados y son volver a unos minutos más tarde.

>[AZURE.IMPORTANT] Reinicie solo está disponible para la caché de nivel Premium. Para obtener más información, vea [administración de la caché de Azure Redis - reiniciar](cache-administration.md#reboot).

### <a name="schedule-updates"></a>Actualizaciones de programación

El módulo de **programar actualizaciones** le permite designar una ventana de mantenimiento Redis actualizaciones de servidor para la memoria caché. 

>[AZURE.IMPORTANT] Tenga en cuenta que la ventana de mantenimiento solo se aplica a Redis actualizaciones de servidor y no a cualquier Azure actualiza o actualizaciones para el sistema operativo de las máquinas virtuales que la caché de host.

![Actualizaciones de programación](./media/cache-configure/redis-schedule-updates.png)

Para especificar una ventana de mantenimiento, active los días que desee especificar la hora de inicio de la ventana de mantenimiento para cada día y haga clic en **Aceptar**. Tenga en cuenta que el tiempo de la ventana de mantenimiento en UTC. 

>[AZURE.IMPORTANT] Programar actualizaciones solo está disponible para la caché de nivel Premium. Para obtener más información, vea [administración de la caché de Azure Redis - programar actualizaciones](cache-administration.md#schedule-updates).

## <a name="diagnostics-settings"></a>Configuración de diagnósticos

La sección **diagnóstico** le permite configurar diagnósticos de la memoria caché Redis.

![Diagnósticos](./media/cache-configure/redis-cache-diagnostics.png)

Para [Configurar la cuenta de almacenamiento](cache-how-to-monitor.md#enable-cache-diagnostics) utilizado para almacenar los diagnósticos de caché, haga clic en **Diagnósticos** .

![Redis diagnósticos de caché](./media/cache-configure/redis-cache-diagnostics-settings.png)

Haga clic en **Redis métricas** para [Ver métricas](cache-how-to-monitor.md#how-to-view-metrics-and-customize-charts) para la memoria caché y **las reglas de alertas** para [configurar reglas de alerta](cache-how-to-monitor.md#operations-and-alerts).

Para obtener más información sobre diagnósticos de Azure Redis caché, vea [cómo supervisar Azure Redis caché](cache-how-to-monitor.md).


## <a name="network-settings"></a>Configuración de la red

La configuración de la sección **red** permite acceso y configurar las siguientes opciones de la memoria caché.

![Red](./media/cache-configure/redis-cache-network.png)

>[AZURE.IMPORTANT] Configuración de red virtual solo está disponible para la caché de premium que se han configurado con el soporte técnico VNET durante la creación de la memoria caché. Para obtener información sobre cómo crear una caché premium con VNET de soporte técnico y actualizar su configuración, consulte [cómo configurar la compatibilidad de red Virtual para una caché de Redis Premium Azure](cache-how-to-premium-vnet.md).

## <a name="resource-management-settings"></a>Configuración de la administración de recursos

![Administración de recursos](./media/cache-configure/redis-cache-resource-management.png)

La sección de **etiquetas** le ayuda a organizar los recursos. Para obtener más información, vea [usar etiquetas para organizar los recursos de Azure](../resource-group-using-tags.md).

La sección **bloquea** le permite bloquear una suscripción, el grupo de recursos o el recurso para evitar que otros usuarios de su organización desde accidentalmente, eliminar o modificar recursos críticos. Para obtener más información, consulte [los recursos de bloqueo con el Administrador de recursos de Azure](../resource-group-lock-resources.md).

La sección **usuarios** proporciona soporte técnico para control de acceso basado en roles (RBAC) en el portal de Azure para ayudar a las organizaciones satisfacer sus requisitos de administración de acceso sencilla y precisa. Para obtener más información, vea [control de acceso basado en roles en el portal de Azure](../active-directory/role-based-access-control-configure.md).

Haga clic en **Exportar plantilla** para crear y exportar una plantilla de los recursos de implementada para el futuro. Para obtener más información acerca de cómo trabajar con plantillas, vea [implementar recursos con las plantillas de administrador de recursos de Azure](../resource-group-template-deploy.md).

## <a name="default-redis-server-configuration"></a>Configuración predeterminada del servidor Redis

Las nuevas instancias de Azure Redis caché configuradas con los siguientes Redis configuración valores predeterminados.

>[AZURE.NOTE] No se puede cambiar la configuración de esta sección utilizando la `StackExchange.Redis.IServer.ConfigSet` método. Si se llama a este método con uno de los comandos de esta sección, se produce una excepción similar al siguiente:  
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>Los valores que se pueden configurables, como la **Directiva de memoria max**, son configurables a través de las herramientas de administración de línea de comandos o portal Azure como Azure CLI o PowerShell.

|Configuración|Valor predeterminado|Descripción|
|---|---|---|
|bases de datos|16|El número predeterminado de bases de datos es 16, pero puede configurar un número diferente basado en el nivel de precio. <sup>1</sup> base de datos predeterminada es DB 0, puede seleccionar uno diferente en una base de por conexión mediante `connection.GetDatabase(dbid)` donde dbid es un número entre `0` y `databases - 1`.|
|MaxClients|Depende de precios nivel<sup>2</sup>|Este es el número máximo de clientes conectados permitido al mismo tiempo. Una vez que se ha alcanzado el límite Redis cerrará todas las conexiones nuevas enviar un error 'número máximo de clientes alcanzado'.|
|Directiva de MaxMemory|lru volátiles|Directiva de MaxMemory es la configuración para cómo Redis seleccione lo que desea quitar cuando se alcance maxmemory (el tamaño de la caché de oferta seleccionado al crear la memoria caché). Con Azure Redis caché el valor predeterminado es lru volátiles, que elimina las claves con un caducar establecer mediante un algoritmo LRU. Esta configuración se puede configurar en el portal de Azure. Para obtener más información, vea [Maxmemory directiva y reservado maxmemory](#maxmemory-policy-and-maxmemory-reserved).|
|ejemplos de MaxMemory|3|LRU y algoritmos mínima de TTL no es precisos algoritmos pero aproximar algoritmos (con el fin de ahorrar memoria), para que pueda seleccionar también para comprobar el tamaño de la muestra. Por ejemplo, para predeterminado los Redis se active tres claves y elija el que se utilizó menos recientemente.|
|límite de tiempo de LUA|5.000|Tiempo máximo de ejecución de un script de Lua en milisegundos. Si se ha alcanzado el tiempo de ejecución máximo Redis registrará que una secuencia de comandos aún está en ejecución después de que el máximo permitido de tiempo y empezará a responder a las consultas con un error.|
|límite de eventos de LUA|500|Este es el tamaño máximo de cola de eventos de secuencia de comandos.|
|cliente de salida búfer límite normalclient salida búfer límite pubsub|0 0 mb 032 8mb 60|Los límites de búfer de salida de cliente se pueden utilizar para forzar desconexión de los clientes que no se está leyendo la datos desde el servidor lo suficientemente rápido por algún motivo (una razón habitual es que un cliente Pub/Sub no puede consumir mensajes tan rápidos como el publicador puede producir). Para obtener más información, consulte [http://redis.io/topics/clients](http://redis.io/topics/clients).|

<a name="databases"></a>
<sup>1</sup> El límite de `databases` es diferente para cada caché de Azure Redis precios nivel y se pueden establecer en la creación de la memoria caché. Si no hay `databases` configuración se especifica durante la creación de caché, el valor predeterminado es 16.

-   Básico y estándar
    -   C0 caché (250 MB) - hasta 16 bases de datos
    -   C1 caché (1 GB) - hasta 16 bases de datos
    -   C2 caché (2,5 GB) - hasta 16 bases de datos
    -   C3 (6 GB) de memoria caché - hasta 16 bases de datos
    -   C4 (13 GB) de memoria caché - hasta 32 bases de datos
    -   C5 (26 GB) de memoria caché - hasta 48 bases de datos
    -   C6 (53 GB) de memoria caché - hasta 64 bases de datos
-   Caché de Premium
    -   P1 (6 GB - 60 GB) - hasta 16 bases de datos
    -   P2 (13 GB - 130 GB) - hasta 32 bases de datos
    -   P3 (26 GB - 260 GB) - hasta 48 bases de datos
    -   P4 (53 GB - 530 GB) - hasta 64 bases de datos
    -   Todas las caché premium con clúster Redis habilitado - clúster Redis solo es compatible con uso de la base de datos 0 para que `databases` limitar para cualquier caché premium con clúster Redis habilitado eficaz es 1 y el comando [Seleccionar](http://redis.io/commands/select) no está permitido. Para obtener más información, vea [es necesario realizar los cambios en mi aplicación de cliente para utilizar clústeres?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)


>[AZURE.NOTE] La `databases` configuración puede ser configurado solo durante la creación de caché y solo con PowerShell, CLI u otros clientes de administración. Para obtener un ejemplo de configuración de `databases` durante la creación de caché con PowerShell, vea [AzureRmRedisCache de nuevo](cache-howto-manage-redis-cache-powershell.md#databases).


<a name="maxclients"></a>
<sup>2</sup> `maxclients` es diferente para cada caché de Azure Redis nivel de precios.

-   Básico y estándar
    -   C0 caché (250 MB) - hasta 256 conexiones
    -   C1 caché (1 GB) - hasta 1.000 conexiones
    -   C2 caché (2,5 GB) - hasta 2.000 conexiones
    -   C3 (6 GB) de memoria caché - hasta 5.000 conexiones
    -   C4 (13 GB) de memoria caché - hasta 10.000 conexiones
    -   C5 (26 GB) de memoria caché - hasta 15.000 conexiones
    -   C6 (53 GB) de memoria caché - hasta 20.000 conexiones
-   Caché de Premium
    -   P1 (6 GB - 60 GB) - hasta 7.500 conexiones
    -   P2 (13 GB - 130 GB) - hasta 15.000 conexiones
    -   P3 (26 GB - 260 GB) - hasta 30.000 conexiones
    -   P4 (53 GB - 530 GB) - hasta 40.000 conexiones

## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Redis comandos que no se admite en caché Redis de Azure

>[AZURE.IMPORTANT] Dado que la configuración y administración de casos de Azure Redis caché administrada por Microsoft están deshabilitados los comandos siguientes. Si intenta invocar ellos recibirá un mensaje de error similar a `"(error) ERR unknown command"`.
>
>-  BGREWRITEAOF
>-  BGSAVE
>-  CONFIG
>-  DEPURAR
>-  MIGRAR
>-  GUARDAR
>-  CIERRE
>-  SLAVEOF
>-  CLÚSTER - clúster escritura están deshabilitados, pero se permiten los comandos de clúster de sólo lectura.

Para obtener más información acerca de los comandos Redis, consulte [http://redis.io/commands](http://redis.io/commands).

## <a name="redis-console"></a>Redis consola

Puede emitir comandos de forma segura a las instancias de Azure Redis caché usando la **Consola Redis**, que está disponible para estándar y Premium almacena en caché.

>[AZURE.IMPORTANT] La consola Redis no funciona con VNET, clústeres y las bases de datos distinto de 0. 
>
>-  [VNET](cache-how-to-premium-vnet.md) - cuando la memoria caché forma parte de un VNET, solo los clientes de la VNET pueden tener acceso a la memoria caché. Dado que la consola Redis utiliza al cliente de cli.exe redis hospedado en máquinas virtuales que no forman parte de su VNET, no puede conectarse a la memoria caché.
>-  [Clúster](cache-how-to-premium-clustering.md) - la consola Redis utiliza al cliente de cli.exe redis que no admite clústeres en este momento. La utilidad de cli redis de la rama [estable](http://redis.io/download) del repositorio Redis en GitHub implementa compatibilidad básica cuando se inicie con el `-c` cambiar. Para obtener más información, vea [juego con el clúster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) en [http://redis.io](http://redis.io) en el [tutorial de clúster de Redis](http://redis.io/topics/cluster-tutorial).
>-  La consola Redis hace una nueva conexión a la base de datos 0 cada vez que envíe un comando. No puede usar el `SELECT` comando para seleccionar otra base de datos, porque la base de datos se restablece a 0 con cada comando. Para obtener información sobre cómo ejecutar comandos Redis, incluido el cambio a una base de datos diferente, vea [¿Cómo puedo ejecutar comandos Redis?](cache-faq.md#how-can-i-run-redis-commands)

Para obtener acceso a la consola Redis, haga clic en la **consola** desde el módulo de **Caché Redis** .

![Redis consola](./media/cache-configure/redis-console-menu.png)

Para ejecutar comandos en una instancia de la memoria caché, solo tiene que escribir en el comando que desee en la consola.

![Redis consola](./media/cache-configure/redis-console.png)

Para obtener una lista de comandos Redis que están deshabilitadas para Azure Redis caché, consulte la sección de [Redis comandos que no se admite en Azure Redis caché](#redis-commands-not-supported-in-azure-redis-cache) anterior. Para obtener más información acerca de los comandos Redis, consulte [http://redis.io/commands](http://redis.io/commands). 

## <a name="move-your-cache-to-a-new-subscription"></a>Mover la memoria caché a una suscripción nueva

Puede mover la memoria caché a una nueva suscripción haciendo clic en **mover**.

![Mover Redis caché](./media/cache-configure/redis-cache-move.png)

Para obtener información sobre cómo mover recursos de un grupo de recursos a otro y de una suscripción a otro, consulte [mover recursos al nuevo grupo de recursos o suscripción](../resource-group-move-resources.md).

## <a name="next-steps"></a>Pasos siguientes
-   Para obtener más información sobre cómo trabajar con comandos Redis, consulte [¿Cómo puedo ejecutar comandos Redis?](cache-faq.md#how-can-i-run-redis-commands).
