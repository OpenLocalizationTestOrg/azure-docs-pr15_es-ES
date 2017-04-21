<properties 
    pageTitle="Introducción al nivel Premium de caché Redis Azure | Microsoft Azure" 
    description="Obtenga información sobre cómo crear y administrar Redis persistencia, Redis clústeres y soporte técnico VNET en las instancias de Azure Redis caché de nivel Premium" 
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
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="introduction-to-the-azure-redis-cache-premium-tier"></a>Introducción al nivel Premium caché Redis de Azure
Caché de Azure Redis es distribuidas y administradas que le ayuda a crear aplicaciones altamente scalable y responde proporcionando acceso muy rápido a los datos. 

El nuevo nivel de Premium es un nivel listo Enterprise, que incluye todas las características de nivel estándar y obtener más información, como un mejor rendimiento, las cargas de trabajo más grandes, recuperación, importar o exportar y seguridad mejorada. Continúe leyendo para obtener más información acerca de las características adicionales del nivel de caché Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Mejor rendimiento en comparación con el nivel estándar o básico
**Mejor rendimiento sobre estándar o básica nivel.** Caché en el nivel de Premium se implementan en el hardware que tiene procesadores más rápidos y proporciona un mejor rendimiento en comparación a la Basic o nivel estándar. Caché de nivel Premium tienen un mayor rendimiento y latencia inferior. 

**El rendimiento de la misma caché tamaño es superior en Premium en comparación con nivel estándar.** Por ejemplo, el rendimiento de un 53 P4 GB de memoria caché (Premium) es solicitudes de 250K por segundo en comparación con 150 K para C6 (estándar).

Para obtener más información sobre el tamaño, el rendimiento y el ancho de banda con caché premium, consulte [Preguntas más frecuentes de caché de Redis Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis persistencia de datos
El nivel Premium le permite conservar los datos en caché en una cuenta de almacenamiento de Azure. En la memoria caché de básico/estándar todos los datos se almacena en la memoria. En caso de infraestructura subyacente problemas allí pueden ser posibles pérdidas de datos. Se recomienda usar la característica de persistencia Redis datos en el nivel Premium para aumentar la resistencia contra la pérdida de datos. Caché de Azure Redis ofrece RDB y AOF (próximamente) opciones en [Redis persistencia](http://redis.io/topics/persistence). 

Para obtener instrucciones sobre cómo configurar persistencia, consulte [cómo configurar persistencia para una caché de Redis Premium Azure](cache-how-to-premium-persistence.md).

##<a name="redis-cluster"></a>Redis clúster
Si desea crear caché mayor que 53 GB o desea datos fragmentar en varios nodos Redis, puede usar Redis clústeres que está disponible en el nivel Premium. Cada nodo consta de un par de caché de réplica o principal administrado por Azure para alta disponibilidad. 

**Redis clústeres le da rendimiento y escala máxima.** Rendimiento lineal aumenta a medida que aumente el número de shards (nodos) en el clúster. Por ejemplo. Si crea un clúster P4 de 10 shards, entonces el rendimiento disponible es 250K * 10 = 2,5 millones de solicitudes por segundo. Consulte las [Preguntas más frecuentes de caché de Redis Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para obtener más detalles sobre el tamaño, el rendimiento y el ancho de banda con caché premium.

Para empezar con clústeres, consulte [cómo configurar clústeres para una caché de Redis Premium Azure](cache-how-to-premium-clustering.md).

##<a name="enhanced-security-and-isolation"></a>Aislamiento y seguridad mejorada

En caché que creó en el nivel Basic o estándar son accesibles en internet. Acceso a la memoria caché está restringido en función de la tecla de acceso. Con el nivel Premium más puede asegurarse de que solo los clientes dentro de una red especificado pueden tener acceso a la memoria caché. Puede implementar Redis caché en una [Red Virtual de Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). Puede utilizar todas las características de VNet como subredes, las directivas de control de acceso, y otras características aún más restringir el acceso a Redis.

Para obtener más información, consulte [cómo configurar la compatibilidad de red Virtual para una caché de Redis Premium Azure](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Importar o exportar

Importar o exportar es una operación de administración de datos de Azure Redis caché que permite importar datos en caché de Azure Redis o exportar los datos de Azure Redis caché importar y exportar una instantánea de la base de datos de caché Redis (RDB) desde una caché premium a un blob de página en una cuenta de almacenamiento de Azure. Esto le permite migrar entre diferentes instancias de caché de Azure Redis o rellenar la caché de datos antes de usar.

Importar puede utilizarse para pasar los archivos Redis compatibles RDB de cualquier servidor Redis que se ejecute en cualquier nube o entorno, incluidos Redis ejecutando en Linux, Windows o en cualquier proveedor de nube como servicios Web de Amazon y otras personas. Importación de datos es una forma sencilla de crear una caché con datos rellenarán. Durante el proceso de importación Azure Redis caché carga los archivos RDB de almacenamiento de Azure en la memoria y luego inserta las teclas en la memoria caché.

Exportar le permite exportar los datos almacenados en caché de Redis de Azure Redis los archivos RDB compatibles. Puede usar esta característica para mover los datos de una instancia de Azure Redis caché a otro o a otro servidor Redis. Durante el proceso de exportación, se crea un archivo temporal en la máquina virtual que hospeda la instancia del servidor de Azure Redis caché y el archivo se cargará a la cuenta de almacenamiento designado. Cuando se complete la operación de exportación con estado de éxito o error, se elimina el archivo temporal.

Para obtener más información, vea [cómo importar los datos y exportar datos de Azure Redis caché](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Reiniciar el equipo

El nivel premium le permite reiniciar uno o varios de los nodos de la caché a petición. Esto le permite probar la aplicación de la resistencia en caso de error. Puede reiniciar los siguientes nodos.

-   Nodo maestro de la memoria caché
-   Nodo secundario de la memoria caché
-   Nodos principal y secundario de la memoria caché
-   Cuando se usa una caché premium con clústeres, puede reiniciar el patrón, subordinada o ambos nodos de shards individuales en la caché.

Para obtener más información, consulte [reiniciar](cache-administration.md#reboot) y [Reinicie P+F](cache-administration.md#reboot-faq).

## <a name="schedule-updates"></a>Actualizaciones de programación

La característica de actualizaciones programadas le permite designar una ventana de mantenimiento de la memoria caché. Cuando se especifica la ventana de mantenimiento, se realizan las actualizaciones de servidor Redis durante esta ventana. Para designar una ventana de mantenimiento, seleccione los días que desee y especifique el mantenimiento ventana iniciar horas para cada día. Tenga en cuenta que el tiempo de la ventana de mantenimiento en UTC. 

Para obtener más información, consulte [las actualizaciones de programación](cache-administration.md#schedule-updates) y de [programación preguntas más frecuentes](cache-administration.md#schedule-updates-faq).

>[AZURE.NOTE] Redis solo se actualicen durante el mantenimiento programado del servidor. La ventana de mantenimiento no se aplica a actualizaciones de Azure o en el sistema operativo de máquina virtual.

## <a name="to-scale-to-the-premium-tier"></a>Escalar al nivel premium

Para ajustar al nivel premium, simplemente elija uno de los niveles de premium en el módulo de **cambiar el nivel de precios** . También puede ajustar el tamaño de la memoria caché al nivel premium con PowerShell y CLI. Para obtener instrucciones detalladas, consulte [Cómo escala Azure Redis caché](cache-how-to-scale.md) y [cómo automatizar una operación de escala](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Pasos siguientes

Crear una caché y explorar las nuevas características de nivel premium.

-   [Cómo configurar persistencia para una caché de Redis Premium Azure](cache-how-to-premium-persistence.md)
-   [Cómo configurar la compatibilidad con una red Virtual Premium Azure Redis caché](cache-how-to-premium-vnet.md)
-   [Cómo configurar clústeres para una caché de Redis Premium Azure](cache-how-to-premium-clustering.md)
-   [Cómo importar datos a y exportar datos de Azure Redis caché](cache-how-to-import-export-data.md)
-   [Cómo administrar la memoria caché Redis de Azure](cache-administration.md)
  

