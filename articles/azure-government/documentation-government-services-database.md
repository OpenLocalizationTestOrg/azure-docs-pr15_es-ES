<properties
    pageTitle="Documentación de gobierno Azure | Microsoft Azure"
    description="Proporciona una comparación de características e instrucciones sobre cómo desarrollar aplicaciones para la administración pública de Azure"
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/18/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-databases"></a>Bases de datos de la administración pública de Azure

##  <a name="sql-database"></a>Base de datos SQL

Para obtener orientación adicional sobre configuración de visibilidad de metadatos y los procedimientos recomendados de protección, consulte el<a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx"> Centro de seguridad de Microsoft para el motor de base de datos de SQL</a> y la [Base de datos de SQL Azure documentación pública](https://azure.microsoft.com/documentation/services/sql-database/) .

### <a name="variations"></a>Variaciones

Base de datos de SQL V12 está disponible de forma general de administración pública de Azure.

La dirección para servidores de SQL Azure en Azure gobierno es diferente:

Tipo de servicio|Azure público|Administración pública de Azure
---|---|---
Base de datos SQL|*. database.windows.net|*. database.usgovcloudapi.net

### <a name="considerations"></a>Consideraciones

La siguiente información identifica el límite de la administración pública de Azure para SQL Azure:

| Datos regulados/controlados permitidos | Datos regulados/controlados no permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos los datos almacenados y procesan en Microsoft Azure SQL pueden contener datos regulados por el gobierno de Azure. Debe utilizar herramientas de base de datos de transmisión de datos de datos regulados por el gobierno de Azure. | No se permiten que los metadatos de SQL Azure contienen datos de exportación controlada. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener su producto de almacenamiento.  No escriba datos regulados de controlados en los siguientes campos: nombre, nombre de suscripción, grupos de recursos, nombre de servidor, inicio de sesión de administración de servidor, nombres de implementación, nombres de los recursos, etiquetas de recursos de la base de datos

## <a name="azure-redis-cache"></a>Caché de Azure Redis

Para obtener detalles sobre este servicio y cómo usarla, consulte la [documentación pública de Azure Redis caché](https://azure.microsoft.com/documentation/services/redis-cache/).

### <a name="variations"></a>Variaciones

Las direcciones URL de acceso y administración de Azure Redis caché en Azure gobierno son diferentes:

Tipo de servicio|Azure público|Administración pública de Azure
---|---|---
Extremo de caché|*. redis.cache.windows.net|*. redis.cache.usgovcloudapi.net
Portal de Azure|https://portal.Azure.com|https://portal.Azure.us

>[AZURE.NOTE] Todas las secuencias de comandos y el código necesita para tener en cuenta los entornos y extremos correspondientes. Para obtener más información, consulte [cómo conectarse a la nube de la administración pública de Azure](../redis-cache/cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).


### <a name="considerations"></a>Consideraciones

La siguiente información identifica el límite de la administración pública de Azure para Azure Redis caché:

| Datos regulados/controlados permitidos | Datos regulados/controlados no permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos los datos almacenados y procesan en Azure Redis caché pueden contener datos regulados por el gobierno de Azure. | No se permiten que los metadatos de caché Redis Azure contienen datos de exportación controlada. No escriba datos regulados de controlados en los siguientes campos: nombre, VNET, nombre de la suscripción, grupos de recursos, etiquetas de recursos, propiedades Redis en caché.  

##  <a name="next-steps"></a>Pasos siguientes

Para información adicional y actualizaciones suscripción a la <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog de Microsoft Azure gobierno.</a>
