<properties 
    pageTitle="Convertir WordPress en multisitio en Azure de aplicación de servicio" 
    description="Obtenga información sobre cómo realizar una aplicación de web WordPress existente creada a través de la galería en Azure y convertirla en WordPress multisitio" 
    services="app-service\web" 
    documentationCenter="php" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>



# <a name="convert-wordpress-to-multisite-in-azure-app-service"></a>Convertir WordPress en multisitio en Azure de aplicación de servicio

## <a name="overview"></a>Información general

*Por [Miguel Lobaugh][ben-lobaugh], [Microsoft Open tecnologías Inc.][ms-open-tech]*

En este tutorial, aprenderá cómo tomar una aplicación de web WordPress existente creada a través de la galería en Azure y convertirla en una instalación de WordPress multisitio. Además, obtendrá información sobre cómo asignar un dominio personalizado a cada uno de los subsitios dentro de la instalación.

Se supone que tiene una instalación existente de WordPress. Si no lo hace, siga las instrucciones proporcionadas en [crear un sitio web de WordPress desde la Galería de Azure][website-from-gallery].

Convertir un WordPress existente único sitio instalación a multisitio generalmente es bastante simple y muchos de los pasos iniciales aquí vienen directamente desde la [Red de A crear] [ wordpress-codex-create-a-network] página en el [WordPress Codex](http://codex.wordpress.org).

Vamos a empezar.

## <a name="allow-multisite"></a>Permitir multisitio

En primer lugar debe habilitar multisitio a través de la `wp-config.php` el archivo con el **WP\_permitir\_MULTISITIO** constante. Existen dos métodos para modificar los archivos de la aplicación web: el primero es a través de FTP y el segundo a través de Git. Si no está familiarizado con cómo configurar uno de estos métodos, consulte los siguientes tutoriales:

* [Sitio web PHP con MySQL y FTP][website-w-mysql-and-ftp-ftp-setup]

* [Sitio web PHP con MySQL y Git][website-w-mysql-and-git-git-setup]

Abrir la `wp-config.php` archivo con el editor de su elección y agregue lo siguiente anteriores la `/* That's all, stop editing! Happy blogging. */` línea.

    /* Multisite */

    define( 'WP_ALLOW_MULTISITE', true );

Asegúrese de guardar el archivo y cargue al servidor.

## <a name="network-setup"></a>Configuración de red

Inicie sesión en al área *wp administración* del sitio web app y debe aparecer un nuevo elemento en el menú **Herramientas** , denominado **Configuración de red**. Haga clic en **Configuración de red** y rellene la información de su red.

![Pantalla de configuración de red][wordpress-network-setup]

Este tutorial utiliza el esquema de sitio de *subdirectorios* porque siempre debe trabajar y vayan a configurar dominios personalizados para cada subsitio más adelante en el tutorial. Sin embargo, debe ser posible configurar un subdominio instalar si asignar un dominio a través de caracteres comodín [Azure Portal](https://portal.azure.com) y la configuración DNS correctamente.

Para obtener más información sobre el subdominio vs configuraciones subdirectorio ver los [tipos de red multisitio] [ wordpress-codex-types-of-networks] artículo en el WordPress Codex.

## <a name="enable-the-network"></a>Habilitar la red

La red ahora está configurada en la base de datos, pero hay un paso restante para habilitar la funcionalidad de red. Finalizar la `wp-config.php` configuración y asegúrese de que `web.config` enruta correctamente cada sitio.


Después de hacer clic en el botón **instalar** en la página *Configuración de red* , WordPress intentará actualizar el `wp-config.php` y `web.config` archivos. Sin embargo, debe comprobar siempre los archivos para asegurarse de que las actualizaciones fueron correcta. Si no es así, esta pantalla presentará con las actualizaciones necesarias. Editar y guardar los archivos.


Después de realizar estas actualizaciones que debe cerrar la sesión e inicie sesión nuevo en el panel de administración de wp.

Ahora debe haber un menú adicional en el Administrador de la barra etiquetada **Mis sitios**. Este menú permite controlar la nueva red mediante el panel de **Administración de red** .

## <a name="adding-custom-domains"></a>Agregar dominios personalizados

La [Asignación de dominio de MU WordPress] [ wordpress-plugin-wordpress-mu-domain-mapping] complemento hace que sea sencillo agregar dominios personalizados en cualquier sitio de su red. Para el complemento para que funcione correctamente, debe realizar algunos configuración adicional en el Portal y también en el registrador.

## <a name="enable-domain-mapping-to-the-web-app"></a>Habilitar la asignación de dominio a la aplicación web

El modo de plan de [Aplicación de servicio](http://go.microsoft.com/fwlink/?LinkId=529714) de **Free** no admite agregar dominios personalizados para las aplicaciones Web. Debe cambiar al modo **compartido** o **estándar** . Para hacer esto:

* Inicie sesión en el Portal de Azure y busque la aplicación web. 
* Haga clic en la pestaña **escalar** en **configuración**.
* En **General**, seleccione *compartido* o *estándar*
* Haga clic en **Guardar**

Puede recibir un mensaje que le pide que compruebe el cambio y confirmar su aplicación web ahora puede provocar que aparezca un costo, dependiendo de uso y la configuración de configuración.

Tardará unos segundos para procesar la nueva configuración, ahora es un buen momento para empezar a configurar su dominio.

## <a name="verify-your-domain"></a>Comprobar el dominio

Antes de aplicaciones Web de Azure le permitirá asignar un dominio para el sitio, debe comprobar que tiene la autorización para asignar el dominio. Para ello, debe agregar un nuevo registro CNAME para la entrada DNS.

* Inicie sesión en el Administrador de DNS de su dominio
* Crear una nueva CNAME *awverify*
* Seleccione *awverify* *awverify. YOUR_DOMAIN.azurewebsites.NET*

Puede tardar algún tiempo para que los cambios DNS ir a efecto completo, así que si los pasos siguientes no funcionan inmediatamente, vaya realizar una taza de café, a continuación, vuelva e inténtelo de nuevo.

## <a name="add-the-domain-to-the-web-app"></a>Agregar el dominio a la aplicación web

Volver a la aplicación web a través del Portal de Azure, haga clic en **configuración**y, a continuación, haga clic en **dominios personalizados y SSL**.

Cuando se muestra la *configuración de SSL* , verá los campos donde se introduce todos los dominios que desea asignar a la aplicación web. Si un dominio no se muestra aquí, no estará disponible para la asignación dentro de WordPress, independientemente de cómo el DNS del dominio es de configuración.

![Administrar el cuadro de diálogo de dominios personalizados][wordpress-manage-domains]

Después de escribir su dominio en el cuadro de texto, Azure comprobar el registro CNAME creado previamente. Si el DNS no haya propagado totalmente, se mostrará un indicador rojo. Si se realizó correctamente, verá una marca de verificación verde. 

Anote la dirección IP que se muestra en la parte inferior del cuadro de diálogo. Necesitará esta opción para configurar el registro para su dominio.

## <a name="setup-the-domain-a-record"></a>Configurar un registro de dominio

Si los pasos anteriores se realizaron correctamente, ahora puede asignar el dominio a su aplicación web de Azure a través de un registro DNS A. 

Es importante destacar que aplicaciones web Azure aceptan CNAME y registros, pero se *debe* usar un registro para habilitar la asignación de dominio adecuado. Un CNAME no se reenviarán a otro CNAME, que es lo Azure creado para usted con YOUR_DOMAIN.azurewebsites.net.

Usando la dirección IP del paso anterior, volver a su administrador de DNS y configurar el registro para que apunten a esa dirección IP.


## <a name="install-and-setup-the-plugin"></a>Instalar y configurar el complemento

Multisitio WordPress actualmente no tiene un método integrado para asignar dominios personalizados. Sin embargo, hay un complemento llamado [WordPress MU dominio asignación] [ wordpress-plugin-wordpress-mu-domain-mapping] que agrega la funcionalidad. Inicie sesión en la parte del Administrador de red de su sitio e instale el complemento **WordPress MU dominio asignación** .

Después de instalar y activar el complemento, visite **configuración** > **Asignación de dominio** para configurar el complemento. En el primer cuadro de texto, *Dirección IP del servidor*, introduzca la dirección IP que utilizó para configurar el registro para el dominio. Establezca las *Opciones del dominio* que desee (los valores predeterminados son a menudo finos) y haga clic en **Guardar**.

## <a name="map-the-domain"></a>Mapa del dominio

Visite los **paneles** para el sitio que desea asignar el dominio. Haga clic en **Herramientas** > **Asignación de dominio** y escriba el nuevo dominio en el cuadro de texto y haga clic en **Agregar**.

De forma predeterminada, se escribirá el nuevo dominio para el dominio del sitio generado automáticamente. Si desea tener todo el tráfico enviado al dominio nuevo, active la casilla de *dominio principal para este blog* antes de guardar. Puede agregar un número ilimitado de dominios en un sitio, pero sólo uno puede ser principal.

## <a name="do-it-again"></a>Hazlo otra vez

Aplicaciones Web de Azure le permiten agregar un número ilimitado de dominios en una aplicación web. Para agregar otro dominio debe ejecutar las secciones de **comprobar el dominio** y **configurar un registro de dominio** para cada dominio.  

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png

 
