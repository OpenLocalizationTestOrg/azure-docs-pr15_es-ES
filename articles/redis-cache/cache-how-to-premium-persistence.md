<properties 
    pageTitle="Cómo configurar persistencia de los datos de una caché Redis Premium en Azure" 
    description="Obtenga información sobre cómo configurar y administrar persistencia de los datos de las instancias de Azure Redis caché de nivel Premium" 
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
    ms.date="09/30/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-data-persistence-for-a-premium-azure-redis-cache"></a>Cómo configurar persistencia de los datos de una caché Redis Premium en Azure

Caché de Azure Redis tiene ofertas de caché diferente que proporcionan flexibilidad en la opción de tamaño de caché y características, como el nuevo nivel de Premium.

El nivel de caché de Azure Redis premium incluye características como clúster, persistencia y compatibilidad de red virtual. En este artículo se describe cómo configurar persistencia de una instancia de Azure Redis caché premium.

Para obtener información sobre otras características premium de caché, vea [Introducción al nivel Premium caché Redis de Azure](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>¿Qué es persistencia de los datos?
Redis persistencia le permite conservar los datos almacenados en Redis. También puede tomar instantáneas y realizar copias de seguridad de los datos, que se pueden cargar en caso de un error de hardware. Esta es una gran ventaja sobre Basic o estándar nivel donde todos los datos se almacena en la memoria y puede haber posibles pérdidas de datos en caso de un error ¿dónde están los nodos de caché hacia abajo. 

Caché de Azure Redis ofrece persistencia Redis mediante el [modelo RDB](http://redis.io/topics/persistence), donde se almacenan los datos en una cuenta de almacenamiento de Azure. Cuando se configura persistencia, Azure Redis caché conserva una instantánea de la caché Redis en un formato binario Redis en disco basada en una frecuencia de copia de seguridad configurable. Si se produce un evento grave que deshabilita la caché principal y de réplica, la caché es reconstruir con la instantánea más reciente.

Persistencia puede configurarse desde el módulo de **Caché Redis nueva** durante la creación de caché y en el módulo de **configuración** para la caché de premium existente.

## <a name="create-a-premium-cache"></a>Crear una caché premium

Para crear una caché y configurar persistencia, inicie sesión en el [portal de Azure](https://portal.azure.com) y haga clic en **nuevo**->**datos + almacenamiento**>**Redis caché**.

![Crear una caché Redis][redis-cache-new-cache-menu]

Para configurar persistencia, primero seleccione uno de la caché de **Premium** en el módulo de **Elegir el nivel de precios** .

![Elija el nivel de precios][redis-cache-premium-pricing-tier]

Tras seleccionar un nivel de precios de premium, haga clic en **Redis persistencia**.

![Redis persistencia][redis-cache-persistence]

Los pasos de la sección siguiente describen cómo configurar Redis persistencia en su nueva caché premium. Una vez configurada Redis persistencia, haga clic en **crear** para crear la nueva caché premium con persistencia Redis.

## <a name="configure-redis-persistence"></a>Configurar persistencia Redis

Redis persistencia está configurada en el módulo **Redis persistencia de los datos** . Para la caché de nuevas, este módulo se tiene acceso durante el proceso de creación de caché, como se describe en la sección anterior. Para la caché existentes, el módulo **Redis persistencia de los datos** se accede desde el módulo de **configuración** de la memoria caché.

![Redis configuración][redis-cache-settings]

Para habilitar la persistencia Redis, haga clic en **habilitado** para habilitar la copia de seguridad RDB (Redis base de datos). Para deshabilitar Redis conservación en una caché premium habilitadas previamente, haga clic en **deshabilitado**.

Para configurar el intervalo de copia de seguridad, seleccione una **Frecuencia de copia de seguridad** de la lista desplegable. Opciones incluyen **15 minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas**y **24 horas**. Este intervalo empieza a contar hacia abajo después de la operación de copia de seguridad anterior se completa correctamente y transcurre se inicia una nueva copia de seguridad.

Haga clic en la **Cuenta de almacenamiento** para seleccionar la cuenta de almacenamiento para utilizar y elija la **clave principal** o **clave secundaria** para usar en la lista desplegable de **Clave de almacenamiento** . Debe elegir una cuenta de almacenamiento en la misma región como la memoria caché y una cuenta de **Almacenamiento Premium** se recomienda porque el almacenamiento de premium tiene un rendimiento más alto. 

>[AZURE.IMPORTANT] Si la clave de almacenamiento para su cuenta de persistencia se regenera, debe rechoose la clave que desee en la lista desplegable de **Clave de almacenamiento** .

![Redis persistencia][redis-cache-persistence-selected]

Haga clic en **Aceptar** para guardar la configuración de persistencia.

La siguiente copia de seguridad (o primera para la caché de nuevas) se inicia una vez que transcurre el intervalo de copia de seguridad de frecuencia.



## <a name="persistence-faq"></a>Preguntas más frecuentes de persistencia

La siguiente lista contiene las respuestas a las preguntas más frecuentes sobre persistencia Azure Redis caché.

-   [¿Habilitar la persistencia en una caché creada previamente?](#can-i-enable-persistence-on-a-previously-created-cache)
-   [¿Puedo cambiar la frecuencia de copia de seguridad después de crear la memoria caché?](#can-i-change-the-backup-frequency-after-i-create-the-cache)
-   [¿Por qué si tengo una frecuencia de copia de seguridad de 60 minutos hay más de 60 minutos entre las copias de seguridad?](#why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
-   [¿Qué sucede para las copias de seguridad antiguos cuando se realiza una nueva copia de seguridad?](#what-happens-to-the-old-backups-when-a-new-backup-is-made)
-   [¿Qué ocurre si he escalar a un tamaño diferente y se restaura una copia de seguridad que se realizó antes de la operación de escalado?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>¿Habilitar la persistencia en una caché creada previamente?

Sí, se puede configurar Redis persistencia durante la creación de caché y en la caché de premium existente.

### <a name="can-i-change-the-backup-frequency-after-i-create-the-cache"></a>¿Puedo cambiar la frecuencia de copia de seguridad después de crear la memoria caché?

Sí, puede cambiar la frecuencia de copia de seguridad en el módulo **Redis persistencia de los datos** . Para obtener instrucciones, consulte [Configurar Redis persistencia](#configure-redis-persistence).

### <a name="why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>¿Por qué si tengo una frecuencia de copia de seguridad de 60 minutos hay más de 60 minutos entre las copias de seguridad?

El intervalo de frecuencia de copia de seguridad no se inicia hasta que el proceso de copia de seguridad anterior se ha completado correctamente. Si la frecuencia de copia de seguridad es 60 minutos y que tarda un proceso de copia de seguridad 15 minutos en completar correctamente, la siguiente copia de seguridad no se iniciará hasta 75 minutos después de la hora de inicio de la copia de seguridad anterior.

### <a name="what-happens-to-the-old-backups-when-a-new-backup-is-made"></a>¿Qué sucede para las copias de seguridad antiguos cuando se realiza una nueva copia de seguridad?

Todas las copias de seguridad excepto el más reciente se eliminan automáticamente. Esta eliminación no puede ocurrir inmediatamente, pero las copias de seguridad anteriores no se conservan indefinidamente.

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>¿Qué ocurre si he escalar a un tamaño diferente y se restaura una copia de seguridad que se realizó antes de la operación de escalado?

-   Si ha escalado a un tamaño más grande, no hay ningún efecto.
-   Si ha escalado a un tamaño menor y tiene una personalizado [bases de datos de](cache-configure.md#databases) configuración es mayor que el [límite de bases de datos](cache-configure.md#databases) para el nuevo tamaño de datos en las bases de datos no pueden restaurarse. Para obtener más información, vea [es mi bases de datos personalizadas configuración afectada durante el ajuste?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
-   Si ha escalado a un tamaño menor y hay suficiente espacio en el tamaño más pequeño que contenga todos los datos desde la última copia de seguridad, las claves se puede expulsar durante el proceso de restauración, normalmente mediante la directiva de expulsión [allkeys lru](http://redis.io/topics/lru-cache) .

## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre cómo usar las características premium de caché más.

-   [Introducción al nivel Premium caché Redis de Azure](cache-premium-tier-intro.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
