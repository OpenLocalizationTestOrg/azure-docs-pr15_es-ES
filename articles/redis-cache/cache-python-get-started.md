<properties
    pageTitle="Cómo utilizar la caché de Azure Redis con Python | Microsoft Azure"
    description="Introducción a Azure Redis caché mediante Python"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/16/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-python"></a>Cómo utilizar la caché de Azure Redis con Python

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Este tema muestra cómo empezar a trabajar con Azure Redis caché usando Python.


## <a name="prerequisites"></a>Requisitos previos

Instalar [Copiar redis](https://github.com/andymccurdy/redis-py).


## <a name="create-a-redis-cache-on-azure"></a>Crear una caché Redis en Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Recuperar las claves de acceso y de nombre de host

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-the-non-ssl-endpoint"></a>Habilitar el extremo no SSL

Algunos clientes Redis no admite SSL y de forma predeterminada el [puerto SSL no está deshabilitado para nuevas instancias de Azure Redis caché](cache-configure.md#access-ports). En el momento de escribir este documento, el cliente de [Copiar redis](https://github.com/andymccurdy/redis-py) no admite SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## <a name="add-something-to-the-cache-and-retrieve-it"></a>Agregar contenido a la memoria caché y recuperarlos


    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'


Reemplazar `<name>` con el nombre de la memoria caché y `key` con la clave de acceso.


<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
