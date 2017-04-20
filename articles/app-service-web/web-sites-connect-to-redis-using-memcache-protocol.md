<properties
    pageTitle="Conectar una aplicación web en la aplicación de servicio de Azure en caché Redis mediante el protocolo Memcache | Microsoft Azure"
    description="Conectar una aplicación web de servicio de aplicaciones de Azure en caché Redis mediante el protocolo Memcache"
    services="app-service\web"
    documentationCenter="php"
    authors="SyntaxC4"
    manager="wpickett"
    editor="riande"/>

<tags
    ms.service="app-service-web"
    ms.devlang="php"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="windows"
    ms.workload="na"
    ms.date="02/29/2016"
    ms.author="cfowler"/>

# <a name="connect-a-web-app-in-azure-app-service-to-redis-cache-via-the-memcache-protocol"></a>Conectar una aplicación web en la aplicación de servicio de Azure en caché Redis mediante el protocolo Memcache

En este artículo, aprenderá cómo conectar una aplicación web de WordPress en [Servicio de la aplicación de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) a [Azure Redis caché] [ 12] utilizando la [Memcache] [ 13] protocolo. Si tiene una aplicación web existente que usa un servidor Memcached para en caché, puede migrar al servicio de la aplicación de Azure y utilizar la solución de almacenamiento en caché de cookies en Microsoft Azure con poco o ningún cambio en el código de la aplicación. Además, puede usar su Memcache existente experiencia para crear y escalabilidad distribuido aplicaciones de servicio de la aplicación de Azure con Azure Redis caché para en la memoria caché, con el uso de marcos de aplicaciones populares como NET, PHP, Node.js, Java y Python.  

Servicio de aplicación Web Apps permite este escenario de aplicación con la corrección Memcache de aplicaciones Web, que es un servidor local de Memcached que actúa como proxy Memcache de almacenamiento en caché llamadas a Azure Redis caché. Esto permite a cualquier aplicación que se comunica con el protocolo Memcache los datos en caché Redis caché. Esta corrección Memcache funciona en el nivel de protocolo, por lo que puede utilizar cualquier aplicación o un marco de la aplicación siempre y cuando se comunica mediante el protocolo Memcache.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="prerequisites"></a>Requisitos previos

La corrección Memcache de aplicaciones Web se puede utilizar con cualquier aplicación siempre que se comunica mediante el protocolo Memcache. En este ejemplo concreto, la aplicación de referencia es un sitio de WordPress Scalable que se pueden aprovisionar de Azure Marketplace.

Siga los pasos descritos en estos artículos:

* [Aprovisionar una instancia de servicio de caché Redis de Azure][0]
* [Implementar un sitio de WordPress Scalable en Azure][1]

Una vez que tenga el sitio de WordPress Scalable implementado y una instancia de caché Redis aprovisionado estará listo para continuar con la habilitación de la corrección Memcache en las aplicaciones de Azure aplicación de servicio Web.

## <a name="enable-the-web-apps-memcache-shim"></a>Habilitar la corrección de Memcache de aplicaciones Web

Para configurar la corrección Memcache, debe crear tres opciones de configuración de aplicación. Esto se puede hacer con una amplia variedad de métodos, incluido el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715), el [portal clásica][3], los [Cmdlets de PowerShell de Azure] [ 5] o la [interfaz de línea de comandos de Azure][5]. Para los fines de esta entrada, voy a usar el [Portal de Azure] [ 4] para establecer la configuración de la aplicación. Los valores siguientes se pueden recuperar desde el módulo de **configuración** de la instancia de caché Redis.

![Azure Redis módulo de configuración de caché](./media/web-sites-connect-to-redis-using-memcache-protocol/1-azure-redis-cache-settings.png)

### <a name="add-redishost-app-setting"></a>Agregar la configuración de la aplicación REDIS_HOST

La primera configuración de aplicación que necesita para crear la **REDIS\_HOST** configuración de aplicación. Establece el destino a la que la corrección reenvía la información de la memoria caché. El valor necesario para la configuración de la aplicación REDIS_HOST se puede recuperar desde la hoja de **Propiedades** de la instancia de caché Redis.

![Azure Redis nombre de Host de caché](./media/web-sites-connect-to-redis-using-memcache-protocol/2-azure-redis-cache-hostname.png)

Establecer la clave de la configuración de aplicación a **REDIS\_HOST** y el valor de la configuración de la aplicación en el **nombre de host** de la instancia de caché Redis.

![Web App AppSetting REDIS_HOST](./media/web-sites-connect-to-redis-using-memcache-protocol/3-azure-website-appsettings-redis-host.png)

### <a name="add-rediskey-app-setting"></a>Agregar la configuración de la aplicación REDIS_KEY

La segunda configuración de aplicación que necesita para crear la **REDIS\_clave** configuración de aplicación. Esta opción proporciona que el símbolo de autenticación requiere acceso a la instancia de caché Redis. Puede recuperar el valor necesario para la configuración de la aplicación REDIS_KEY desde el módulo de **teclas de acceso** de la instancia de caché Redis.

![Azure Redis clave principal de caché](./media/web-sites-connect-to-redis-using-memcache-protocol/4-azure-redis-cache-primarykey.png)

Establecer la clave de la configuración de aplicación a **REDIS\_clave** y el valor de la configuración de aplicación a la **Clave principal** de la instancia de caché Redis.

![Sitio Web de Azure AppSetting REDIS_KEY](./media/web-sites-connect-to-redis-using-memcache-protocol/5-azure-website-appsettings-redis-primarykey.png)

### <a name="add-memcacheshimredisenable-app-setting"></a>Agregar la configuración de la aplicación MEMCACHESHIM_REDIS_ENABLE

El último valor de la aplicación se utiliza para habilitar la corrección Memcache en aplicaciones Web, que usa el REDIS_HOST y REDIS_KEY para conectarse a la caché de Azure Redis y reenviar la caché de llamadas. Establecer la clave de la configuración de aplicación a **MEMCACHESHIM\_REDIS\_habilitar** y el valor **true**.

![Web App AppSetting MEMCACHESHIM_REDIS_ENABLE](./media/web-sites-connect-to-redis-using-memcache-protocol/6-azure-website-appsettings-enable-shim.png)

Una vez que haya terminado de agregar la configuración de la aplicación de tres (3), haga clic en **Guardar**.

## <a name="enable-memcache-extension-for-php"></a>Habilitar la extensión Memcache para PHP

Fin de la aplicación para leer el protocolo Memcache, es necesario instalar la extensión Memcache a PHP--el marco de idioma para el sitio de WordPress.

### <a name="download-the-phpmemcache-extension"></a>Descargar la extensión php_memcache

Vaya a [PECL][6]. En la categoría almacenamiento en caché, haga clic en [memcache][7]. En la columna de descargas, haga clic en el vínculo DLL.

![Sitio Web de PHP PECL](./media/web-sites-connect-to-redis-using-memcache-protocol/7-php-pecl-website.png)

Descargue el vínculo de subproceso no seguros (hacia) x86 de la versión de PHP habilitada en aplicaciones Web. (El valor predeterminado es PHP 5.4)

![Sitio Web de PHP PECL Memcache paquete](./media/web-sites-connect-to-redis-using-memcache-protocol/8-php-pecl-memcache-package.png)

### <a name="enable-the-phpmemcache-extension"></a>Habilitar la extensión php_memcache

Después de descargar el archivo, descomprima y cargue el **php\_memcache.dll** en la **d:\\principal\\sitio\\wwwroot\\Papelera\\ext\\ ** directorio. Después de cargar la php_memcache.dll en la aplicación web, debe habilitar la extensión para el tiempo de ejecución de PHP. Para habilitar la extensión Memcache en el Portal de Azure, abra el módulo de **Configuración de la aplicación** para la aplicación web, a continuación, agregar una nueva configuración de la aplicación con la clave de **PHP\_extensiones** y el valor **Papelera\\ext\\php_memcache.dll**.


> [AZURE.NOTE] Si la aplicación web debe cargar varias extensiones PHP, el valor de PHP_EXTENSIONS debe ser una lista delimitada por comas de rutas de acceso relativas a archivos DLL.

![Web App AppSetting PHP_EXTENSIONS](./media/web-sites-connect-to-redis-using-memcache-protocol/9-azure-website-appsettings-php-extensions.png)

Una vez que haya terminado, haga clic en **Guardar**.

## <a name="install-memcache-wordpress-plugin"></a>Instale el complemento de Memcache WordPress

> [AZURE.NOTE] También puede descargar el [Complemento de caché de objeto Memcached](https://wordpress.org/plugins/memcached/) desde WordPress.org.

En la página de complementos WordPress, haga clic en **Agregar nuevo**.

![Página de complemento de WordPress](./media/web-sites-connect-to-redis-using-memcache-protocol/10-wordpress-plugin.png)

En el cuadro Buscar, escriba **memcached** y presione **ENTRAR**.

![WordPress agregar complemento nuevo](./media/web-sites-connect-to-redis-using-memcache-protocol/11-wordpress-add-new-plugin.png)

Buscar la **Caché de objetos de Memcached** en la lista y luego haga clic en **Instalar ahora**.

![WordPress instale el complemento de Memcache](./media/web-sites-connect-to-redis-using-memcache-protocol/12-wordpress-install-memcache-plugin.png)

### <a name="enable-the-memcache-wordpress-plugin"></a>Habilitar el complemento Memcache WordPress

>[AZURE.NOTE] Siga las instrucciones de este blog sobre [cómo habilitar una extensión de sitio en aplicaciones Web] [ 8] para instalar Visual Studio Team Services.

En el `wp-config.php` , agregue el siguiente código encima de la detención Modificar comentario cerca del final del archivo.

```php
$memcached_servers = array(
    'default' => array('localhost:' . getenv("MEMCACHESHIM_PORT"))
);
```

Una vez pegada este código, Mónaco guardará automáticamente el documento.

El siguiente paso es habilitar el complemento de la memoria caché de objetos. Para ello, arrastrar y soltar **cache.php objeto** de carpeta **wp-contenido/Plug-ins/memcached** a la carpeta de **contenido del elemento Web** para habilitar la funcionalidad de la memoria caché de objetos Memcache.

![Busque el complemento del objeto cache.php memcache](./media/web-sites-connect-to-redis-using-memcache-protocol/13-locate-memcache-object-cache-plugin.png)

Ahora que el archivo de **objeto cache.php** está en la carpeta de **contenido del elemento Web** , la caché de objetos Memcached está habilitada.

![Habilitar el complemento del objeto cache.php memcache](./media/web-sites-connect-to-redis-using-memcache-protocol/14-enable-memcache-object-cache-plugin.png)

## <a name="verify-the-memcache-object-cache-plugin-is-functioning"></a>Compruebe que está funcionando el complemento de la memoria caché de objetos Memcache

Todos los pasos para habilitar la corrección de Memcache de aplicaciones Web están completadas. Lo único que falta es comprobar que los datos rellenar la instancia de caché Redis.

### <a name="enable-the-non-ssl-port-support-in-azure-redis-cache"></a>Habilitar la compatibilidad de puerto de SSL no en caché Redis de Azure

>[AZURE.NOTE] En el momento de escribir este artículo, CLI Redis no admite conectividad SSL, por lo tanto los pasos siguientes son necesarios.

En el Portal de Azure, vaya a la instancia de caché Redis que ha creado para esta aplicación web. Una vez abierto el módulo de la memoria caché, haga clic en el icono **configuración** .

![Botón Configuración de caché de Redis de Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/15-azure-redis-cache-settings-button.png)

Seleccione **Los puertos de acceso** de la lista.

![Puerto de acceso de caché Redis de Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/16-azure-redis-cache-access-port.png)

Haga clic en **No** para **Permitir el acceso sólo a través de SSL**.

![Puerto de acceso de caché Redis Azure SSL solo](./media/web-sites-connect-to-redis-using-memcache-protocol/17-azure-redis-cache-access-port-ssl-only.png)

Verá que ahora se establece el puerto no SSL. Haga clic en **Guardar**.

![Redis Azure caché Redis acceder Portal no SSL](./media/web-sites-connect-to-redis-using-memcache-protocol/18-azure-redis-cache-access-port-non-ssl.png)

### <a name="connect-to-azure-redis-cache-from-redis-cli"></a>Conectarse a Azure Redis caché desde cli redis

>[AZURE.NOTE] Este paso asume que redis está instalado localmente en el equipo de desarrollo. [Instalar Redis localmente mediante estas instrucciones][9].

Abra la consola de línea de comandos de elección y escriba el siguiente comando:

```shell
redis-cli –h <hostname-for-redis-cache> –a <primary-key-for-redis-cache> –p 6379
```

Reemplazar el ** &lt;hostname-para-redis-caché&gt; ** con el nombre de host real xxxxx.redis.cache.windows.net y la ** &lt;principal-clave-para-redis-caché&gt; ** con la tecla de acceso para la caché, a continuación, presione **ENTRAR**. Una vez que se ha conectado a la instancia de caché Redis CLI, emitir cualquier comando redis. En la captura de pantalla siguiente, he elegido enumerar las claves.

![Conectarse a Azure Redis caché desde Redis CLI en Terminal](./media/web-sites-connect-to-redis-using-memcache-protocol/19-redis-cli-terminal.png)

La llamada a la lista de las claves debe devolver un valor. Si no es así, pruebe a desplazarse por la aplicación web y volver a intentarlo.

## <a name="conclusion"></a>Conclusión

¡Felicidades! La aplicación de WordPress tiene ahora una caché en memoria centralizada para ayudar a aumentar el rendimiento. Recuerde que la corrección de Memcache de aplicaciones Web se puede usar con cualquier cliente Memcache independientemente del lenguaje de programación o marco de la aplicación. Proporcionar comentarios o hacer preguntas acerca de la corrección Memcache de aplicaciones Web, publicar en los [Foros de MSDN] [ 10] o [Stackoverflow][11].

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)


[0]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache
[1]: http://bit.ly/1t0KxBQ
[2]: http://manage.windowsazure.com
[3]: http://portal.azure.com
[4]: ../powershell-install-configure.md
[5]: /downloads
[6]: http://pecl.php.net
[7]: http://pecl.php.net/package/memcache
[8]: http://blog.syntaxc4.net/post/2015/02/05/how-to-enable-a-site-extension-in-azure-websites.aspx
[9]: http://redis.io/download#installation
[10]: https://social.msdn.microsoft.com/Forums/home?forum=windowsazurewebsitespreview
[11]: http://stackoverflow.com/questions/tagged/azure-web-sites
[12]: /services/cache/
[13]: http://memcached.org
