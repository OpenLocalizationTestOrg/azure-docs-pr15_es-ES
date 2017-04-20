<properties
    pageTitle="WordPress empresariales en Azure de aplicación de servicio | Microsoft Azure"
    description="Obtenga información sobre cómo hospedar el sitio WordPress empresariales en la aplicación de servicio de Azure"
    services="app-service\web"
    documentationCenter=""
    authors="sunbuild"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.devlang="php"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="web"
    ms.date="10/24/2016"
    ms.author="sumuth"/>

# <a name="enterprise-class-wordpress-on-azure-app-service"></a>WordPress empresariales en Azure de aplicación de servicio

Servicio de aplicaciones de Azure proporciona un entorno scalable, seguro y fácil de usar para objetivos crítica de gran escala [WordPress] [ wordpress] sitios. Microsoft ejecuta sitios de clase empresarial como [Office] [ officeblog] y [Bing] [ bingblog] blogs. Este documento muestra cómo puede usar las aplicaciones de Azure aplicación de servicio Web para establecer y mantener una clase empresarial, sitio WordPress basada en nube que puede administrar un gran volumen de visitantes.

## <a name="architecture-and-planning"></a>Arquitectura y planificación

Una instalación básica de WordPress tiene sólo dos requisitos.

* **Base de datos MySQL** - disponibles a través de [ClearDB de Azure Marketplace][cdbnstore], o puede administrar su propios instalación MySQL en Azure máquinas virtuales de Windows mediante [Windows] [ mysqlwindows] o [Linux][mysqllinux].

  > [AZURE.NOTE] ClearDB proporciona varias configuraciones de MySQL, con distintas características de rendimiento para cada configuración. Ver el [Almacenamiento de Azure] [ cdbnstore] para obtener información sobre las ofertas proporcionado a través de la tienda de Azure o directamente tal como se muestra en el [sitio Web de ClearDB](http://www.cleardb.com/pricing.view).

* **PHP 5.2.4 o mayor** -servicio de aplicación de Azure actualmente proporcionar [versiones PHP 5.4, 5.5 y 5.6][phpwebsite].

    > [AZURE.NOTE] Se recomienda que siempre se ejecuta en la última versión de PHP para asegurarse de que tiene las últimas correcciones de seguridad.

### <a name="basic-deployment"></a>Implementación básica

Usando solo los requisitos básicos, puede crear una solución básica dentro de un área de Azure.

![una aplicación web de Azure y la base de datos MySQL alojado en una sola región de Azure][basic-diagram]

Mientras Esto le permitirá escalado su aplicación mediante la creación de varias instancias de aplicaciones Web del sitio, todo está hospedado en los centros de datos en una región geográfica específico. Pueden ver los visitantes desde fuera de esta región tiempos de respuesta al usar el sitio, si los centros de datos en esta área ir hacia abajo, así que no tiene la aplicación.


### <a name="multi-region-deployment"></a>Implementación de múltiples región

Con Azure [Administrador de tráfico][trafficmanager], es posible escalar su sitio WordPress varias regiones geográficas proporcionando sólo una dirección URL para los visitantes. Todos los visitantes llegan a través del Administrador de tráfico y, a continuación, se enrutan a una región según la configuración de equilibrio de carga.

![una aplicación web de Azure, alojada en varias regiones, con enrutador CDBR alta disponibilidad para enrutar a MySQL en todas las regiones][multi-region-diagram]

Dentro de cada región, el sitio de WordPress aún podría distribuirse entre varias instancias de aplicaciones Web, pero esta escala es región específica; pueden escalar distinta poco tráfico de las regiones de tráfico alta.

Replicación y enrutamiento a varias bases de datos MySQL pueden hacerse uso de ClearDB [CDBR alta disponibilidad enrutador] [ cleardbscale] (que se muestra izquierda) o [MySQL clúster CGE][cge].

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>Implementación de región múltiples con medios de almacenamiento y almacenamiento en caché
Si el sitio acepta cargas o archivos multimedia de host, utilice el almacenamiento de blobs de Windows Azure. Si necesita almacenamiento en caché, considere la posibilidad de [Redis caché][rediscache], [Nube Memcache](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/)o una de las otras ofertas de almacenamiento en caché en el [Almacenamiento de Azure](http://azure.microsoft.com/gallery/store/).

![una aplicación web de Azure, alojada en varias regiones, con enrutador CDBR alta disponibilidad para MySQL con caché administrada, almacenamiento de blobs y CDN][performance-diagram]

Almacenamiento de blobs es geo distribuye entre regiones de forma predeterminada, para que no tenga que preocuparse de réplica de archivos en todos los sitios. También puede habilitar la [Red de distribución de contenido (CDN)] de Azure[ cdn] para el almacenamiento de blobs, que distribuye archivos para finalizar nodos más cerca a los visitantes.

### <a name="planning"></a>Planificación

#### <a name="additional-requirements"></a>Requisitos adicionales

Para hacer esto... | Use esto...
------------------------|-----------
**Cargar o almacenar archivos de gran tamaño** | [Complemento de WordPress para el uso de almacenamiento de blobs][storageplugin]
**Enviar correo electrónico** | [SendGrid] [ storesendgrid] y el [complemento de WordPress para usar SendGrid][sendgridplugin]
**Nombres de dominio personalizado** | [Configurar un nombre de dominio personalizado en la aplicación de servicio de Azure][customdomain]
**HTTPS** | [Habilitar HTTPS para una aplicación web en la aplicación de servicio de Azure][httpscustomdomain]
**Validación de producción anterior** | [Configurar entornos de web apps en el servicio de aplicación de Azure de ensayo][staging] <p>Cambiar una aplicación web de ensayo a producción también mueve a la configuración de WordPress. Debe asegurarse de que todos los valores se actualizan los requisitos para la aplicación de producción antes de cambiar la aplicación preconfigurada en producción.</p>
**Supervisión y solución de problemas** | [Habilitar el registro de diagnóstico para las aplicaciones web en la aplicación de servicio de Azure] [ log] y [Monitor Web Apps en el servicio de aplicación de Azure][monitor]
**Implementar su sitio** | [Implementar una aplicación web en la aplicación de servicio de Azure][deploy]

#### <a name="availability-and-disaster-recovery"></a>Disponibilidad y recuperación de desastres

Para hacer esto... | Use esto...
------------------------|-----------
**Sitios de equilibrio de carga** o **geo-distribuir sitios** | [Enrutar el tráfico con el administrador del tráfico de Azure][trafficmanager]
**Realizar copias de seguridad y restauración** | [Haga una copia de seguridad de una aplicación web en la aplicación de servicio de Azure] [ backup] y [restaurar una aplicación web en la aplicación de servicio de Azure][restore]

#### <a name="performance"></a>Rendimiento

Rendimiento en la nube se logra principalmente a través de almacenamiento en caché y escalado; Sin embargo se deben considerar la memoria, ancho de banda y otros atributos de aplicaciones Web de hospedaje.

Para hacer esto... | Use esto...
------------------------|-----------
**Comprender las capacidades de instancia de servicio de aplicaciones** | [Detalles de precios, incluidas las capacidades de los niveles de servicio de aplicaciones][websitepricing]
**Recursos de la caché** | [Redis caché][rediscache], [Nube Memcache](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/)o una de las otras ofertas de almacenamiento en caché en el [Almacenamiento de Azure](/gallery/store/)
**Ajustar el tamaño de la aplicación** | [Ajustar el tamaño de una aplicación web en la aplicación de servicio de Azure] [ websitescale] [ClearDB alta disponibilidad enrutamiento]y[cleardbscale]. Si decide alojar y administrar su propios instalación MySQL, debe tener en cuenta [MySQL clúster CGE] [ cge] de escalado

#### <a name="migration"></a>Migración

Existen dos métodos para migrar un sitio de WordPress existente al servicio de la aplicación de Azure.

* ** [Exportar WordPress] [ export] ** -exporta el contenido de su blog, que se puede importar a un nuevo sitio de WordPress en servicio de la aplicación de Azure mediante el [complemento de importador WordPress][import].

    > [AZURE.NOTE] Mientras este proceso le permite migrar el contenido, no se migra los complementos, temas u otras personalizaciones. Debe estar instalados manualmente.

* **Migración manual** - [hacer copia de seguridad de su sitio] [ wordpressbackup] y [base de datos][wordpressdbbackup], a continuación, manualmente restaurar a una aplicación web de servicio de la aplicación de Azure y asociados de base de datos MySQL para migrar sitios altamente personalizados y evitar la tarea de instalar manualmente los complementos, temas y otras personalizaciones.

## <a name="step-by-step-instructions"></a>Instrucciones paso a paso

### <a name="create-a-wordpress-site"></a>Crear un sitio de WordPress

1. Usar el [Catálogo de soluciones de Azure] [ cdbnstore] para crear una base de datos MySQL del tamaño que ha identificado en la sección de la [arquitectura y la planeación](#planning) de las zonas que hospedará el sitio.

2. Siga los pasos de [crear una aplicación web de WordPress en servicio de la aplicación de Azure] [ createwordpress] para crear una aplicación web de WordPress. Al crear la aplicación web, seleccione **usar una base de datos MySQL existente** y seleccione la base de datos creado en el paso 1.

Si va a migrar un sitio de WordPress existente, vea [migrar un sitio de WordPress existente a Azure](#Migrate-an-existing-WordPress-site-to-Azure) después de crear una nueva aplicación web.

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>Migrar un sitio de WordPress existente a Azure

Como se mencionó en la sección [arquitectura y planificación](#planning) , hay dos formas de migrar un sitio de WordPress.

* **exportar e importar** - para sitios sin cuánta personalización o donde desea mover el contenido.

* **copia de seguridad y restauración** - en los sitios con una gran cantidad de personalización donde desea mover todo el contenido.

Utilice una de las siguientes secciones para migrar su sitio.

#### <a name="the-export-and-import-method"></a>El método de importación y exportación

1. Usar [WordPress exportar] [ export] para exportar el sitio existente.

2. Crear una aplicación web siguiendo los pasos de la sección [crear un sitio de WordPress](#Create-a-new-WordPress-site) .

3. Inicie sesión en el sitio de WordPress en aplicaciones Web y haga clic en **complementos** -> **Agregar nuevo**. Busque e instale, el complemento **WordPress importador** .

4. Después de instalar el complemento de importador, haga clic en **Herramientas** -> **Importar**y, a continuación, seleccione **WordPress** para usar el complemento de importador WordPress.

5. En la página **Importar WordPress** , haga clic en **Elegir archivo**. Busque el archivo WXR exportado desde el sitio de WordPress existente y, a continuación, elija **Cargar archivo e importar**.

6. Haga clic en **Enviar**. Se le pedirá que la importación es correcta.

8. Una vez haya completado todos estos pasos, reinicie su sitio de su módulo de la aplicación web en el [portal de Azure][mgmtportal].

Después de importar el sitio, debe realizar los pasos siguientes para habilitar a opciones no contenidas en el archivo de importación.

Si estuviera utilizando esto... | Haga esto...
------------------ | ----------
**Vínculos permanentes** | Desde el panel de WordPress del nuevo sitio, haga clic en **configuración** -> **permanentes** y, a continuación, actualizar la estructura de vínculos permanentes
**vínculos de imágenes y multimedia** | Para actualizar los vínculos a la nueva ubicación, utilice el [complemento de terciopelo azules actualizar las direcciones URL][velvet], una herramienta de búsqueda y reemplazo, o manualmente en la base de datos
**Temas** | Vaya a **apariencia** -> **tema** y actualizar el tema del sitio según sea necesario
**Menús** | Si el tema es compatible con los menús, vínculos a la página principal tenga todavía el directorio subcarpetas antiguo incrustado en ellos. Vaya a **apariencia** -> **menús** y actualizarlos

#### <a name="the-backup-and-restore-method"></a>El método de copia de seguridad y restauración

1. Haga una copia de seguridad de su WordPress existente del sitio con la información en [las copias de seguridad de WordPress][wordpressbackup].

2. Copia de seguridad de la base de datos existente con la información en [la copia de seguridad de la base de datos][wordpressdbbackup].

3. Crear una base de datos y restaurar la copia de seguridad.

    1. Comprar una nueva base de datos de [Azure Marketplace][cdbnstore], o configuración de una base de datos MySQL en un [Windows] [ mysqlwindows] o [Linux] [ mysqllinux] VM.

    2. Usando un cliente de MySQL como [MySQL trabajo][workbench], conectarse a la nueva base de datos e importar la base de datos de WordPress.

    3. Actualizar la base de datos para cambiar las entradas de dominio para el dominio de servicio de la aplicación de Azure nuevo. Por ejemplo, mywordpress.azurewebsites.net. Usar [Buscar y reemplazar para secuencia de comandos de bases de datos de WordPress] [ searchandreplace] para cambiar todas las instancias de forma segura.

4. Crear una aplicación web en el portal de Azure y publicar la copia de seguridad de WordPress.

    1. Crear una aplicación web en el [portal de Azure] [ mgmtportal] con una base de datos con el **nuevo** -> **Web + Mobile** -> **Azure Marketplace** -> **Aplicaciones Web** -> **Online + SQL** (o **aplicación Web + MySQL**) -> **crear**. Configurar toda la configuración necesaria para crear una aplicación web vacío.

    2. En la copia de seguridad de WordPress, busque el archivo **config.php wp** y ábralo en un editor. Reemplazar las siguientes entradas con la información para la nueva base de datos MySQL.

        * **DB_NAME** - el nombre de usuario de la base de datos

        * **DB_USER** - el nombre de usuario utilizado para tener acceso a la base de datos

        * **DB_PASSWORD** : la contraseña de usuario

        Después de cambiar estas entradas, guarde y cierre el archivo **config.php wp** .

    3. Usar la [implementar una aplicación web en la aplicación de servicio de Azure] [ deploy] información para habilitar el método de implementación que desea usar y, a continuación, implementar la copia de seguridad de WordPress en la aplicación web de la aplicación de servicio de Azure.

5. Una vez que se ha implementado el sitio de WordPress, debería tener acceso al nuevo sitio (como una aplicación de servicio de aplicación web) mediante la *. azurewebsite.net dirección URL del sitio.

### <a name="configure-your-site"></a>Configurar el sitio

Después de haberse creado el sitio de WordPress o migrado, use la siguiente información para mejorar el rendimiento o habilitar funciones adicionales.

Para hacer esto... | Use esto...
------------- | -----------
**Establecer el modo de plan de servicio de aplicaciones, tamaño y escala habilitar** | [Ajustar el tamaño de una aplicación web en la aplicación de servicio de Azure][websitescale]
**Habilitar las conexiones de base de datos persistente** | De forma predeterminada, WordPress no utiliza conexiones persistentes de la base de datos, lo que pueden provocar la conexión a la base de datos se convierten en acelerado después de varias conexiones. Para habilitar las conexiones persistentes, instale el (complemento de adaptador de conexiones persistentes) [https://wordpress.org/plugins/persistent-database-connection-updater/installation/]. 
**Mejorar el rendimiento** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Deshabilitar la cookie ARR</a> - puede mejorar el rendimiento cuando se ejecuta WordPress en varias instancias de aplicaciones Web</p></li><li><p>Habilitar almacenamiento en caché. <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis caché</a> (vista previa) puede usarse con la <a href="https://wordpress.org/plugins/redis-object-cache/">Redis objeto caché WordPress complemento</a>, o utilice una de las otras ofertas de almacenamiento en caché de la <a href="/gallery/store/">Tienda de Azure</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Cómo hacer WordPress más rápido con Wincache</a> - Wincache está habilitada de forma predeterminada para las aplicaciones Web</p></li><li><p><a href="../web-sites-scale/">Escala de una aplicación web de servicio de la aplicación de Azure</a> y usar <a href="http://www.cleardb.com/developers/cdbr/introduction">Enrutamiento de ClearDB alta disponibilidad</a> o <a href="http://www.mysql.com/products/cluster/">MySQL clúster CGE</a></p></li></ul>
**Usar BLOB de almacenamiento** | <ol><li><p><a href="../storage-create-storage-account/">Crear una cuenta de almacenamiento de Azure</a></p></li><li><p>Obtenga información sobre cómo <a href="../cdn-how-to-use/">usar la red de distribución de contenido (CDN)</a> para geo-distribuir datos almacenados en BLOB.</p></li><li><p>Instalar y configurar el <a href="https://wordpress.org/plugins/windows-azure-storage/">Almacenamiento de Azure para WordPress complemento</a>.</p><p>Configuración detalladas e información de configuración para el complemento, consulte la <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">Guía del usuario</a>.</p> </li></ol>
**Habilitar el correo electrónico** | Habilitar <a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a> utilizando el almacén de Azure. Instale el <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">complemento SendGrid</a> para WordPress.
**Configurar un nombre de dominio personalizado** | [Configurar un nombre de dominio personalizado en la aplicación de servicio de Azure][customdomain]
**Habilitar HTTPS para un nombre de dominio personalizado** | [Habilitar HTTPS para una aplicación web en la aplicación de servicio de Azure][httpscustomdomain]
**Cargar saldo o geo-distribuir su sitio** | [Enrutar el tráfico con el administrador del tráfico de Azure][trafficmanager]. Si está utilizando un dominio personalizado, vea [configurar un nombre de dominio personalizado en la aplicación de servicio de Azure] [ customdomain] para obtener información acerca de cómo utilizar el Administrador de tráfico con nombres de dominio personalizado
**Habilitar las copias de seguridad automatizadas** | [Realizar copias de seguridad de una aplicación web en la aplicación de servicio de Azure][backup]
**Habilitar el registro de diagnóstico** | [Habilitar el registro de diagnóstico para las aplicaciones web en la aplicación de servicio de Azure][log]

## <a name="next-steps"></a>Pasos siguientes

* [Optimización de WordPress](http://codex.wordpress.org/WordPress_Optimization)

* [Convertir WordPress en multisitio en Azure de aplicación de servicio](web-sites-php-convert-wordpress-multisite.md)

* [ClearDB actualizar el Asistente para Azure](http://www.cleardb.com/store/azure/upgrade)

* [Hospedaje WordPress en una subcarpeta de la aplicación web de la aplicación de servicio de Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Paso a paso: Crear un sitio de WordPress con Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Hospedar el blog de WordPress existente en Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Habilitar vínculos permanentes bella en WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Cómo migrar y ejecutar el blog de WordPress en servicio de la aplicación de Azure](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [Cómo ejecutar WordPress en Azure aplicación de servicio de forma gratuita](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress en Azure en 2 minutos o menos](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Mover un blog WordPress a Azure - parte 1: crear un blog WordPress en Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Mover un blog WordPress a Azure - parte 2: transferir su contenido](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Mover un blog WordPress a Azure - parte 3: configurar su dominio personalizado](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Mover un blog WordPress a Azure - parte 4: bastante permanentes y las reglas de la reescritura de URL](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Mover un blog WordPress a Azure - parte 5: pasar de una subcarpeta en la raíz](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Cómo configurar una aplicación web de WordPress en su cuenta de Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Propping arriba WordPress en Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Sugerencias para WordPress en Azure](http://www.johnpapa.net/azurecleardbmysql/)

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: ../virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md
[mysqllinux]: ../virtual-machines/virtual-machines-linux-classic-mysql-on-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: ../powershell-install-configure.md
[Azure CLI]: ../xplat-cli-install.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md
