<properties
   pageTitle="Cómo utilizar la caché de Azure Redis con Java | Microsoft Azure"
    description="Introducción a la caché de Azure Redis con Java"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor=""/>

<tags
    ms.service="cache"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/24/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-java"></a>Cómo utilizar la caché de Azure Redis con Java

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure proporciona de caché Redis que acceso a dedicada Redis caché, administrada por Microsoft. La memoria caché es accesible desde cualquier aplicación de Microsoft Azure.

Este tema muestra cómo empezar a trabajar con Azure Redis caché con Java.

## <a name="prerequisites"></a>Requisitos previos

[Jedis](https://github.com/xetorthio/jedis) - cliente Java para Redis

Este tutorial utiliza Jedis, pero puede utilizar cualquier cliente de Java que se indican a [http://redis.io/clients](http://redis.io/clients).

## <a name="create-a-redis-cache-on-azure"></a>Crear una caché Redis en Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Recuperar las claves de acceso y de nombre de host

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-the-non-ssl-endpoint"></a>Habilitar el extremo no SSL

Algunos clientes Redis no admite SSL y de forma predeterminada el [puerto SSL no está deshabilitado para nuevas instancias de Azure Redis caché](cache-configure.md#access-ports). En el momento de escribir este documento, el cliente de [Jedis](https://github.com/xetorthio/jedis) no admite SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]




## <a name="add-something-to-the-cache-and-retrieve-it"></a>Agregar contenido a la memoria caché y recuperarlos

    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    /* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
    public class App
    {
      public static void main( String[] args )
      {
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>Pasos siguientes

- [Habilitar diagnósticos de memoria caché](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que pueda [supervisar](https://msdn.microsoft.com/library/azure/dn763945.aspx) el mantenimiento de la memoria caché.
- Lea la [documentación de Redis](http://redis.io/documentation)oficial.

