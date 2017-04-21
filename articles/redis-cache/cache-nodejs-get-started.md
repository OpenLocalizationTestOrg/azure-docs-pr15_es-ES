<properties
    pageTitle="Cómo utilizar la caché de Azure Redis con Node.js | Microsoft Azure"
    description="Introducción a la caché de Azure Redis con Node.js y node_redis."
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>Cómo utilizar la caché de Azure Redis con Node.js

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Caché de Azure Redis le proporciona acceso a una segura y dedicada Redis caché, administrada por Microsoft. La memoria caché es accesible desde cualquier aplicación de Microsoft Azure.

Este tema muestra cómo empezar a trabajar con Azure Redis caché usando Node.js. Para obtener otro ejemplo de uso de caché de Azure Redis con Node.js, vea [crear una aplicación de Chat de Node.js con Socket.IO en un sitio Web de Azure](../app-service-web/web-sites-nodejs-chat-app-socketio.md).


## <a name="prerequisites"></a>Requisitos previos

Instalar [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Este tutorial usa [node_redis](https://github.com/mranney/node_redis). Para obtener ejemplos de uso de otros clientes de Node.js, consulte la documentación individual de los clientes de Node.js que se indican a [clientes Redis Node.js](http://redis.io/clients#nodejs).

## <a name="create-a-redis-cache-on-azure"></a>Crear una caché Redis en Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Recuperar las claves de acceso y de nombre de host

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Conectarse a la caché de forma segura con SSL

Las últimas versiones de [node_redis](https://github.com/mranney/node_redis) proporcionan soporte técnico para conectarse a Azure Redis caché con SSL. En el ejemplo siguiente se muestra cómo conectarse a Azure Redis caché utilizando el extremo SSL de 6380. Reemplazar `<name>` con el nombre de la memoria caché y `<key>` con cualquiera la clave principal o secundaria como se describe en la sección anterior [recuperar las claves de acceso y de nombre de host](#retrieve-the-host-name-and-access-keys) .

     var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## <a name="add-something-to-the-cache-and-retrieve-it"></a>Agregar contenido a la memoria caché y recuperarla

En el ejemplo siguiente se muestra cómo conectarse a una instancia de Azure Redis caché y almacenar y recuperar un elemento de la caché. Para obtener más ejemplos de uso de Redis con el cliente de [node_redis](https://github.com/mranney/node_redis) , consulte [http://redis.js.org/](http://redis.js.org/).

     var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});
    
    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });
    
    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

Resultado:

    OK
    value


## <a name="next-steps"></a>Pasos siguientes

- [Habilitar diagnósticos de memoria caché](cache-how-to-monitor.md#enable-cache-diagnostics) para que pueda [supervisar](cache-how-to-monitor.md) el mantenimiento de la memoria caché.
- Lea la [documentación de Redis](http://redis.io/documentation)oficial.



