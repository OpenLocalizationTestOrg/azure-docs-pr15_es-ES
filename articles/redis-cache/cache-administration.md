<properties 
    pageTitle="Cómo administrar la caché de Azure Redis | Microsoft Azure"
    description="Obtenga información sobre cómo realizar tareas de administración como actualizaciones reinicio y programación para Azure Redis caché"
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
    ms.date="09/27/2016"
    ms.author="sdanie" />

# <a name="how-to-administer-azure-redis-cache"></a>Cómo administrar la memoria caché Redis de Azure

Este tema describe cómo realizar tareas de administración, como reiniciar y programar las actualizaciones para las instancias de Azure Redis caché.

>[AZURE.IMPORTANT] La configuración y las características descritas en este artículo solo están disponibles para la caché de nivel Premium.


## <a name="administration-settings"></a>Configuración de administración

La configuración de **administración** de la caché de Azure Redis permite realizar las siguientes tareas administrativas en la memoria caché de premium. Para obtener acceso a la configuración de administración, haga clic en **configuración** o **toda la configuración** desde el módulo de caché Redis y desplácese a la sección **administración** de la hoja de **configuración** .

![Administración](./media/cache-administration/redis-cache-administration.png)

-   [Reiniciar el equipo](#reboot)
-   [Actualizaciones de programación](#schedule-updates)

## <a name="reboot"></a>Reiniciar el equipo

El módulo **reinicie** permite reiniciar uno o varios de los nodos de la memoria caché. Esto le permite probar la aplicación de la resistencia en caso de error.

![Reiniciar el equipo](./media/cache-administration/redis-cache-reboot.png)

Si tiene una caché premium con clúster habilitado, puede seleccionar qué shards de la caché de reiniciar.

![Reiniciar el equipo](./media/cache-administration/redis-cache-reboot-cluster.png)

Para reiniciar uno o varios de los nodos de la memoria caché, seleccione los nodos que desee y haga clic en **reiniciar**. Si tiene una caché premium con clúster habilitado, seleccione la shard(s) reiniciar y, a continuación, haga clic en **reiniciar**. Después de unos minutos, el reinicio de nodos seleccionados y son volver a unos minutos más tarde.

El impacto en las aplicaciones cliente varía dependiendo de los nodos que reiniciar.

-   **Patrón** : cuando se reinicia el nodo maestro, Azure Redis caché pasa al nodo de réplica y promueve patrón. Durante esta migración tras error, puede haber un breve intervalo en el que pueden producir un error de conexiones a la memoria caché.
-   **Subordinado** - cuando se reinicia el nodo secundario, normalmente no es afectar a los clientes de la memoria caché.
-   **Ambas maestro y subordinado** - cuando se reinicien ambos nodos de caché, todos los datos se pierde en la caché y conexiones con el error de caché hasta que el nodo principal vuelve a estar conectado. Si ha configurado [persistencia de los datos](cache-how-to-premium-persistence.md), se restaura la copia de seguridad más reciente cuando la caché vuelve a estar conectada. Tenga en cuenta que cualquier escritura de caché que se ha producido después de la copia de seguridad más reciente se pierden.
-   **Nodos de una caché premium con clúster habilitado** - al reiniciar los nodos de una caché premium con clúster habilitado, el comportamiento es igual al reiniciar nodos de una caché no agrupado.


>[AZURE.IMPORTANT] Reinicie solo está disponible para la caché de nivel Premium.

## <a name="reboot-faq"></a>Reinicie preguntas más frecuentes

-   [¿Qué nodo debo reiniciar para probar mi aplicación?](#which-node-should-i-reboot-to-test-my-application)
-   [¿Reiniciar la caché para desactivar las conexiones de cliente?](#can-i-reboot-the-cache-to-clear-client-connections)
-   [¿Se pierden datos de la caché de caso de un reinicio?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
-   [¿Reiniciar la caché con PowerShell, CLI u otras herramientas de administración?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
-   [¿Qué precios niveles pueden usar la funcionalidad de reinicio?](#what-pricing-tiers-can-use-the-reboot-functionality)


### <a name="which-node-should-i-reboot-to-test-my-application"></a>¿Qué nodo debo reiniciar para probar mi aplicación?

Para probar la resistencia de la aplicación contra errores del nodo principal de la memoria caché, reinicie el nodo de **patrón** . Para probar la resistencia de la aplicación contra errores del nodo secundario, reinicie el nodo **secundario** . Para probar la resistencia de la aplicación con error total de la caché, reinicie **ambos** nodos.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>¿Reiniciar la caché para desactivar las conexiones de cliente?

Sí, si reiniciar la caché se desactivan todas las conexiones de cliente. Esto puede útil en el caso de que todas las conexiones de cliente se utilizan, por ejemplo debido a un error lógico o un error en la aplicación cliente. Cada nivel de precios tiene diferentes [límites de conexión de cliente](cache-configure.md#default-redis-server-configuration) para los distintos tamaños y, a continuación, una vez que se ha alcanzado estos límites, no se aceptarán más conexiones de cliente. Reiniciar la caché proporciona una manera de borrar todas las conexiones de cliente.

>[AZURE.IMPORTANT] Si las conexiones de cliente se utilizan debido a un error lógico o un error en el código de cliente, tenga en cuenta que volverá a conectarse al StackExchange.Redis automáticamente una vez que el nodo Redis es volver a conectarse. Si no se resuelve el problema subyacente, seguirá utilizarse hasta las conexiones de cliente.

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>¿Se pierden datos de la caché de caso de un reinicio?

Si reinicia el **principal** y el **secundario** nodos se pierden todos los datos en la memoria caché (o en esa fragmentar si está utilizando una caché premium con clúster habilitado). Si ha configurado [persistencia de los datos](cache-how-to-premium-persistence.md), la copia de seguridad más reciente se restablece cuando la caché vuelve a estar conectada. Tenga en cuenta que cualquier escritura de caché que se ha producido después de la copia de seguridad se pierden.

Si reinicia solo uno de los nodos, no se pierden normalmente datos, pero aún puede ser. Por ejemplo, si se reinicia el nodo maestro y memoria caché de escritura está en curso, los datos de la memoria caché de escritura se pierden. Otro escenario de pérdida de datos sería reinicie un nodo y el otro nodo ocurre ir hacia abajo, debido a un error al mismo tiempo. Para obtener más información sobre las posibles causas de pérdida de datos, vea [¿Qué ha ocurrido con mis datos en Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md).

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>¿Reiniciar la caché con PowerShell, CLI u otras herramientas de administración?

Sí, para PowerShell [reiniciar una caché Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache)ver instrucciones.

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>¿Qué precios niveles pueden usar la funcionalidad de reinicio?

Reinicie solo está disponible en la prima nivel de precios.

## <a name="schedule-updates"></a>Actualizaciones de programación

El módulo de **programar actualizaciones** le permite designar una ventana de mantenimiento de la memoria caché. Cuando se especifica la ventana de mantenimiento, se realizan las actualizaciones de servidor Redis durante esta ventana. Tenga en cuenta que la ventana de mantenimiento solo se aplica a Redis actualizaciones de servidor y no a cualquier Azure actualiza o actualizaciones para el sistema operativo de las máquinas virtuales que la caché de host.

![Actualizaciones de programación](./media/cache-administration/redis-schedule-updates.png)

Para especificar una ventana de mantenimiento, active los días que desee especificar la hora de inicio de la ventana de mantenimiento para cada día y haga clic en **Aceptar**. Tenga en cuenta que el tiempo de la ventana de mantenimiento en UTC. 

>[AZURE.NOTE] La ventana de mantenimiento de forma predeterminada para actualizaciones es 5 horas. Este valor no es configurable desde el portal de Azure, pero puede configurar en PowerShell utilizando el `MaintenanceWindow` parámetro del cmdlet [AzureRmRedisCacheScheduleEntry de nuevo](https://msdn.microsoft.com/library/azure/mt763833.aspx) . Para obtener más información, vea [¿administra la con PowerShell, CLI u otras herramientas de administración de actualizaciones programadas?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

## <a name="schedule-updates-faq"></a>Preguntas más frecuentes sobre actualizaciones de programación

-   [¿Cuándo las actualizaciones se producen si no se usa la característica de actualizaciones de la programación?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
-   [¿Qué tipos de actualizaciones se realizan durante la ventana de mantenimiento programada?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
-   [¿Se puede administradas actualizaciones programadas con PowerShell, CLI u otras herramientas de administración?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
-   [¿Qué precios niveles pueden usar la funcionalidad de actualizaciones de la programación?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>¿Cuándo las actualizaciones se producen si no se usa la característica de actualizaciones de la programación?

Si no especifica una ventana de mantenimiento, se pueden realizar actualizaciones en cualquier momento.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>¿Qué tipos de actualizaciones se realizan durante la ventana de mantenimiento programada?

Redis solo se actualicen durante el mantenimiento programado del servidor. La ventana de mantenimiento no se aplica a actualizaciones de Azure o en el sistema operativo de máquina virtual.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>¿Se puede administradas actualizaciones programadas con PowerShell, CLI u otras herramientas de administración?

Sí, puede administrar las actualizaciones programadas mediante los siguientes cmdlets de PowerShell.

-   [Get-AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763835.aspx)
-   [Nueva AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763834.aspx)
-   [Nueva AzureRmRedisCacheScheduleEntry](https://msdn.microsoft.com/library/azure/mt763833.aspx)
-   [Quitar AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763837.aspx)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>¿Qué precios niveles pueden usar la funcionalidad de actualizaciones de la programación?

Programar actualizaciones solo está disponible en la prima nivel de precios.

## <a name="next-steps"></a>Pasos siguientes

-   Explorar más características de [nivel de caché de Azure Redis premium](cache-premium-tier-intro.md) .





