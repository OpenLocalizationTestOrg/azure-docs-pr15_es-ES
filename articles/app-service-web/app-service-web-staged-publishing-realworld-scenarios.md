<properties
  pageTitle="Utilizar DevOps entornos de forma eficaz para la aplicación web"
  description="Obtenga información sobre cómo usar ranuras de implementación para configurar y administrar varios entornos de desarrollo de la aplicación"
  services="app-service\web"
  documentationCenter=""
  authors="sunbuild"
  manager="yochayk"
  editor=""/>

<tags
  ms.service="app-service"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="web"
  ms.date="10/24/2016"
  ms.author="sumuth"/>

# <a name="use-devops-environments-effectively-for-your-web-apps"></a>Utilizar DevOps entornos de forma eficaz para las aplicaciones web

Este artículo muestra a configurar y administrar la implementación de aplicaciones web de varias versiones de la aplicación como desarrollo, prueba, QA y producción. Cada versión de la aplicación puede considerarse como entorno de desarrollo para una necesidad específica de dentro de su proceso de implementación. Por ejemplo puede utilizarse entorno de q & a por el equipo de desarrolladores a probar la calidad de la aplicación antes de aplicar los cambios a producción.
Configurar varios entornos de desarrollo puede ser una tarea difícil como necesite para realizar un seguimiento, administrar los recursos (cálculo, aplicación web, base de datos, caché, etc.) e implementar código en entornos.

## <a name="setting-up-a-non-production-environment-stagedevqa"></a>Configurar un entorno de producción no (fase, desarrollo, QA)
Una vez que tenga una aplicación web de producción marcha, el siguiente paso es crear un entorno de producción no. Para poder usar ranuras de implementación Asegúrese de que está ejecutando en el modo de plan **estándar** o **Premium** aplicación de servicio. Ranuras de implementación son aplicaciones web realmente directo con sus propios nombres de host. Elementos de contenido y la configuración de la aplicación Web se pueden intercambiar entre dos ranuras de implementación, incluida la franja de producción. Implementar su aplicación en una franja de implementación tiene las siguientes ventajas:

1. Para validar los cambios de aplicación web en una franja de implementación provisional antes de intercambiar con la franja de producción.
2. Implementar una aplicación web para un espacio en primer lugar e intercambiar en producción asegura que todas las instancias de la franja se caliente antes de que se ha intercambiado en producción. Esto elimina el tiempo de inactividad cuando se implementa la aplicación web. El redireccionamiento de tráfico sea transparente y no se eliminan solicitudes debido a las operaciones de intercambio. Este flujo de trabajo completo se puede automatizar mediante la configuración [Automática intercambiar](web-sites-staged-publishing.md#configure-auto-swap-for-your-web-app) cuando no es necesario intercambiar previa validación.
3. Después de un intercambio la franja con la aplicación web previamente preconfigurada ahora tiene la aplicación web de producción anterior. Si los cambios intercambiados en la franja de producción son no tal como se esperaba, puede realizar la misma intercambiar inmediatamente para obtener la "última conocidos buena aplicación web de" volver.

Para configurar un espacio de implementación provisional, vea [Configurar el ensayo entornos de web apps en el servicio de aplicación de Azure](web-sites-staged-publishing.md). Cada entorno debe incluir su propio conjunto de recursos, por ejemplo si web app que usa una base de datos, a continuación, producción y ensayo Online deben estar usando diferentes bases de datos. Agregar recursos provisional del entorno de desarrollo como base de datos, almacenamiento o caché para configurar su entorno de desarrollo de ensayo.

## <a name="examples-of-using-multiple-development-environments"></a>Ejemplos de uso de múltiples entornos de desarrollo

Cualquier proyecto debería seguir una administración de código fuente con al menos dos entornos, un entorno de producción y desarrollo, pero cuando con sistemas de administración de contenido, marcos de aplicaciones, etc. que podríamos surgir problemas donde la aplicación no admite este escenario fuera del cuadro. Esto es cierto para algunos de los marcos populares, que se describen a continuación. Una gran cantidad de preguntas vienen a la mente cuando trabaje con un CMS/marcos como

1. Cómo dividirla alejar en distintos entornos
2. ¿Qué archivos ¿cambiar e imposible afectar actualizaciones de versión de framework
3. Cómo administrar la configuración de entorno
4. Cómo administrar las actualizaciones de versión de módulos y complementos, actualizaciones de versión de framework core

Hay muchas formas de configurar un entorno de múltiples para el proyecto y los ejemplos siguientes son solo como método de las aplicaciones correspondientes.

### <a name="wordpress"></a>WordPress
En esta sección, aprenderá cómo configurar un flujo de trabajo de implementación con ranuras para WordPress. Trabajar con varios entornos de desarrollo fuera del cuadro no admite WordPress como la mayoría de las soluciones CMS. Servicio de aplicación Web Apps tiene algunas características que hacen que sea más fácil almacenar fuera de su código de configuración.

Antes de crear un espacio provisional, configure el código de la aplicación a varios entornos de soporte técnico. Para admitir varios entornos en WordPress necesita modificar `wp-config.php` en la aplicación web de desarrollo local, agregue el código siguiente al principio del archivo. Este le permite a su aplicación elegir la configuración correcta basada en el entorno seleccionado.

```
// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) {
// local development
 $config_file = 'config/wp-config.local.php';
}
elseif ((strpos(getenv('WP_ENV'),'stage') !== false) || (strpos(getenv('WP_ENV'),'prod' )!== false ))
//single file for all azure development environments
 $config_file = 'config/wp-config.azure.php';
}
$path = dirname(__FILE__). '/';
if (file_exists($path. $config_file)) {
// include the config file if it exists, otherwise WP is going to fail
require_once $path. $config_file;
```

Crear una carpeta en la raíz de aplicación web denominada `config` y agregar un archivo dos archivos: `wp-config.azure.php` y `wp-config.local.php` que representa el entorno local y azure respectivamente.

Copie el siguiente en `wp-config.local.php` :

```
<?php
// MySQL settings
/** The name of the database for WordPress */

define('DB_NAME', 'yourdatabasename');

/** MySQL database username */
define('DB_USER', 'yourdbuser');

/** MySQL database password */
define('DB_PASSWORD', 'yourpassword');

/** MySQL hostname */
define('DB_HOST', 'localhost');
/**
 * For developers: WordPress debugging mode.
 * * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 */
define('WP_DEBUG', true);

//Security key settings
define('AUTH_KEY', 'put your unique phrase here');
define('SECURE_AUTH_KEY','put your unique phrase here');
define('LOGGED_IN_KEY','put your unique phrase here');
define('NONCE_KEY', 'put your unique phrase here');
define('AUTH_SALT', 'put your unique phrase here');
define('SECURE_AUTH_SALT', 'put your unique phrase here');
define('LOGGED_IN_SALT', 'put your unique phrase here');
define('NONCE_SALT', 'put your unique phrase here');

/**
 * WordPress Database Table prefix.
 *
 * You can have multiple installations in one database if you give each a unique
 * prefix. Only numbers, letters, and underscores please!
 */
$table_prefix = 'wp_';
```

Configuración de las claves de seguridad anteriores puede ayudar a impedir que la aplicación web de piratería informática, entonces usar valores únicos. Si necesita generar la cadena de claves de seguridad mencionados arriba, puede ir al Generador automático para crear nuevas claves/valores con este [link] (https://api.wordpress.org/secret-key/1.1/salt)

Copie el siguiente código en `wp-config.azure.php`:


``` <?php
    // MySQL settings
    /** The name of the database for WordPress */
    
    define('DB_NAME', getenv('DB_NAME'));
    
    /** MySQL database username */
    define('DB_USER', getenv('DB_USER'));
    
    /** MySQL database password */
    define('DB_PASSWORD', getenv('DB_PASSWORD'));
    
    /** MySQL hostname */
    define('DB_HOST', getenv('DB_HOST'));
    
    /**
    * For developers: WordPress debugging mode.
    *
    * Change this to true to enable the display of notices during development.
    * It is strongly recommended that plugin and theme developers use WP_DEBUG
    * in their development environments.
    * Turn on debug logging to investigate issues without displaying to end user. For WP_DEBUG_LOG to
    * do anything, WP_DEBUG must be enabled (true). WP_DEBUG_DISPLAY should be used in conjunction
    * with WP_DEBUG_LOG so that errors are not displayed on the page */
    
    */
    define('WP_DEBUG', getenv('WP_DEBUG'));
    define('WP_DEBUG_LOG', getenv('TURN_ON_DEBUG_LOG'));
    define('WP_DEBUG_DISPLAY',false);
    
    //Security key settings
    /** If you need to generate the string for security keys mentioned above, you can go the automatic generator to create new keys/values: https://api.wordpress.org/secret-key/1.1/salt **/
    define('AUTH_KEY',getenv('DB_AUTH_KEY'));
    define('SECURE_AUTH_KEY', getenv('DB_SECURE_AUTH_KEY'));
    define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
    define('NONCE_KEY', getenv('DB_NONCE_KEY'));
    define('AUTH_SALT', getenv('DB_AUTH_SALT'));
    define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
    define('LOGGED_IN_SALT',  getenv('DB_LOGGED_IN_SALT'));
    define('NONCE_SALT',  getenv('DB_NONCE_SALT'));
    
    /**
    * WordPress Database Table prefix.
    *
    * You can have multiple installations in one database if you give each a unique
    * prefix. Only numbers, letters, and underscores please!
    */
    $table_prefix = getenv('DB_PREFIX');
```

#### <a name="use-relative-paths"></a>Usar rutas relativas
Por último consiste en configurar la aplicación de WordPress para usar rutas de acceso relativas. WordPress almacena información de dirección URL en la base de datos. De este modo mover contenido desde un entorno a otro más difícil como necesite para actualizar la base de datos cada vez que se mueve de local a la fase o etapa a entornos de producción. Para reducir el riesgo de problemas que puede deberse a la implementación de una base de datos cada vez implementa desde un entorno a otro use el [complemento de raíz relativa vínculos](https://wordpress.org/plugins/root-relative-urls/) que puede instalarse con panel de administrador de WordPress o descárguelo manualmente desde [aquí](https://downloads.wordpress.org/plugin/root-relative-urls.zip).


Agregue las siguientes entradas para su `wp-config.php` antes de la `That's all, stop editing!` comentario:

```

  define('WP_HOME', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_SITEURL', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_CONTENT_URL', '/wp-content');
    define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

Activar el complemento a través de la `Plugins` menú en el panel de administrador de WordPress. Guardar la configuración de vínculo permanente WordPress aplicación.

#### <a name="the-final-wp-configphp-file"></a>El último `wp-config.php` archivo
Las actualizaciones principales WordPress no afectará a su `wp-config.php`, `wp-config.azure.php` y `wp-config.local.php` archivos. En el extremo este cómo `wp-config.php` archivo tendrá este aspecto

```
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, and ABSPATH. You can find more information by visiting
 *
 * Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web web app, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) { // local development
  $config_file = 'config/wp-config.local.php';
}
elseif ((strpos(getenv('WP_ENV'),'stage') !== false) ||(strpos(getenv('WP_ENV'),'prod' )!== false )){
  $config_file = 'config/wp-config.azure.php';
}


$path = dirname(__FILE__). '/';
if (file_exists($path. $config_file)) {
  // include the config file if it exists, otherwise WP is going to fail
  require_once $path. $config_file;
}

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/* That's all, stop editing! Happy blogging. */

define('WP_HOME', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_SITEURL', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_CONTENT_URL', '/wp-content');
define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__). '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH. 'wp-settings.php');
```

#### <a name="set-up-a-staging-environment"></a>Configurar un entorno de ensayo
Suponiendo que ya tiene una aplicación web de WordPress ejecuta en Azure Web, inicie sesión en el [portal de vista previa de administración de Azure](http://portal.azure.com) y vaya a la aplicación web de WordPress. Aplicaciones si no se puede crear uno desde el catálogo de soluciones. Para obtener más información, [haga clic aquí](web-sites-php-web-site-gallery.md).
Haga clic en Opciones -> implementación ranuras -> Agregar para crear un espacio de implementación con la región de nombre. Un espacio de implementación es otra aplicación de web compartir los mismos recursos que la aplicación web principal creado anteriormente.

![Crear la franja de implementación de región](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

Agregar otra base de datos MySQL, supongamos, por ejemplo `wordpress-stage-db` a su grupo de recursos `wordpressapp-group`.

 ![Agregar base de datos MySQL al grupo de recursos](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

Actualizar las cadenas de conexión para la franja de implementación de la fase para que apunten a la base de datos recién creado, `wordpress-stage-db`. Nota que su producción web app, `wordpressprodapp` y provisional web app `wordpressprodapp-stage` deben apuntar a diferentes bases de datos.

#### <a name="configure-environment-specific-app-settings"></a>Establecer la configuración de la aplicación específicas del entorno
Los desarrolladores pueden almacenar pares de clave y valor cadena en Azure como parte de la información de configuración asociada a una aplicación web denominada configuración de la aplicación. En tiempo de ejecución aplicación de servicio Web Apps automáticamente recupere estos valores y están disponibles para el código que se ejecuta en la aplicación web. De un valor bursátil perspectiva de un lado bueno beneficiarse desde información confidencial, como cadenas de conexión de base de datos con las contraseñas nunca se muestran como texto sin cifrar en un archivo como `wp-config.php`.

Este proceso descrito a continuación es útil cuando realiza que incluya cambios en los archivos y los cambios de base de datos de aplicación WordPress:
- Actualización de la versión de WordPress
- Agregar o editar o actualizar complementos
- Agregar o editar o actualizar temas

Configurar opciones de aplicación para:

- información de la base de datos
- Activar o desactivar el registro de WordPress
- Configuración de seguridad de WordPress

![Configuración de la aplicación de Wordpress web app](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

Asegúrese de que ha agregado la siguiente configuración de aplicación para la franja de aplicación y fase de web de producción. Observe que la aplicación web de producción y ensayo web app utilizan diferentes bases de datos.
Desactive la casilla de verificación de **Configuración de la franja** de todos los parámetros de configuración excepto WP_ENV. Esto cambiará la configuración para la aplicación web, junto con el contenido del archivo y la base de datos. Si la **Configuración de franja** está **activada**, configuración de la aplicación de la aplicación web y la configuración de la cadena de conexión no se moverá en entornos al realizar una operación de intercambio y, por tanto, si los cambios de la base de datos están presentes esto no dividirá la aplicación web de producción.

Implemente la aplicación web de entorno de desarrollo local a fase web app y base de datos usando WebMatrix o herramientas de su elección, como FTP, Git o PhpMyAdmin.

![Cuadro de diálogo Publicar matriz WordPress web aplicación Web](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

Busque y probar la aplicación web de ensayo. Un escenario donde el tema de la aplicación web es actualizarse, aquí es la aplicación web provisional.

![Examinar Online provisional antes de intercambiar ranuras](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)


 Si todo es satisfactorio, haga clic en el botón **intercambiar** en la aplicación web de ensayo para mover el contenido al entorno de producción. En este caso intercambiar la aplicación web y la base de datos en entornos durante cada operación de **intercambio** .

![Intercambiar vista previa de cambios para WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

 > [AZURE.NOTE]
 >Si tiene un escenario donde se necesita a solo los archivos de inserción (no hay actualizaciones de base de datos), a continuación, **Compruebe** la **Configuración de la franja** de la base de datos relacionados con *configuración de cadenas de conexión* en el módulo de configuración de aplicación web en el portal de vista previa de Azure y *configuración de la aplicación* antes de realizar el intercambio. En este caso DB_NAME, DB_HOST, DB_PASSWORD, DB_USER, configuración predeterminada de la cadena de conexión no debe aparecer en la vista previa de cambios cuando se realiza una **intercambiar**. En este momento, cuando se completa la operación de **intercambio de** la aplicación web de WordPress tendrá las actualizaciones de archivos **solo**.

Antes de hacer un cambio, aquí es la aplicación web de WordPress de producción ![Online de producción antes de intercambiar ranuras](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

Después de la operación de intercambio, el tema se ha actualizado en la aplicación web de producción.

![Aplicación web de producción después de intercambiar ranuras](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

En una situación cuando necesita **Deshacer**, puede ir a la configuración de aplicación web de producción y haga clic en el botón **intercambiar** cambiar la aplicación web y base de datos de producción provisional franja. Una importante que debe recordar es que si los cambios de base de datos se incluyen con una operación **de intercambio** en cualquier momento, a continuación, la próxima vez que vuelva a implementa en la aplicación web de ensayo que necesita para implementar la base de datos cambia a la base de datos actual para la aplicación web de ensayo que podría ser la base de datos de producción anterior o la base de datos de la fase.

#### <a name="summary"></a>Resumen
Generalizar el proceso de cualquier aplicación con una base de datos

1. Instalar aplicaciones en su entorno local
2. Incluir configuración específica del entorno (local y Azure Web App)
3. Configurar los entornos de aplicación de servicio Web Apps: ensayo, producción
4. Si tiene una aplicación de producción ya en ejecución en Azure, sincronizar el contenido de producción (archivos y código + base de datos) para entorno local y provisional.
5. Desarrollar su aplicación en su entorno local
6. Colocar el contenido de producción para entornos de ensayo y desarrollo de la aplicación web de producción en mantenimiento o modo bloqueado y base de datos de sincronización
7. Implementar en el entorno de prueba y probar
8. Implementar en el entorno de producción
9. Repita los pasos del 4 al 6

### <a name="umbraco"></a>Umbraco
En esta sección, aprenderá cómo el CMS Umbraco utiliza un módulo personalizado para implementar desde en todo el entorno de DevOps varios. Este ejemplo proporciona un enfoque diferente para la administración de múltiples entornos de desarrollo.

[Umbraco CMS](http://umbraco.com/) es una de las soluciones CMS de popular.NET utilizadas muchos desarrolladores que proporciona el módulo [Courier2](http://umbraco.com/products/more-add-ons/courier-2) para implementar desde el desarrollo de ensayo para entornos de producción. Puede crear fácilmente un entorno de desarrollo local para una aplicación web de CMS Umbraco utilizando Visual Studio o WebMatrix.

1. Crear una aplicación web de Umbraco con Visual Studio, [haga clic aquí](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget).
2. Para crear una aplicación web de Umbraco con WebMatrix, [haga clic aquí](http://umbraco.com/help-and-support/video-tutorials/getting-started/working-with-webmatrix).

No olvide quitar la `install` carpeta en la aplicación y nunca cárguelo en aplicaciones web de producción o región. En este tutorial, se pueden usar WebMatrix

#### <a name="set-up-a-staging-environment"></a>Configurar un entorno de ensayo
- Crear un espacio de implementación como se mencionó anteriormente Umbraco CMS web app, suponiendo que ya tiene una aplicación web de Umbraco CMS y ejecutar. Si no puede crear uno desde el catálogo de soluciones.

- Actualice la cadena de conexión para la franja de implementación fase para que apunten a la base de datos recién creado, **umbraco db de región**. La aplicación web de producción (umbraositecms-1) y provisional Online (umbracositecms 1-etapa) **debe** apuntan a diferentes bases de datos.

![Actualizar la cadena de conexión de ensayo web app con la nueva base de datos provisional](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

- Haga clic en **configuración de publicación de obtener** la franja de implementación **fase**. Esto descarga un archivo de configuración de publicación que almacenan toda la información requerida por Visual Studio o matriz de Web para publicar la aplicación de la aplicación web de desarrollo local a la aplicación web de Azure.

 ![Obtener publicar la configuración de la aplicación web provisional](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)

- Abra la aplicación web de desarrollo local en **WebMatrix** o **Visual Studio**. En este tutorial uso Web Matrix pero primero tiene que importar el archivo de configuración de publicación para la aplicación web de ensayo

![Importar configuración de publicación para Umbraco mediante Web Matrix](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

- Revisar los cambios en el cuadro de diálogo e implemente la aplicación web local de la aplicación web de Azure, *umbracositecms-1-fase*. Cuando se implementación archivos directamente en la aplicación web de ensayo se omite los archivos de la `~/app_data/TEMP/` carpeta estos se volverá a generar cuando la aplicación web de escenario es el primera iniciado. También debe omitir el `~/app_data/umbraco.config` como archivo, también se volverá a generar.

![Revisar los cambios de publicar en matriz de web](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

- Después de publicar correctamente la aplicación web local Umbraco a la aplicación web de ensayo, vaya a la aplicación web de ensayo y ejecute algunas pruebas para descartar cualquier problema.

#### <a name="set-up-courier2-deployment-module"></a>Configurar el módulo de implementación Courier2
Con módulo de [Courier2](http://umbraco.com/products/more-add-ons/courier-2) puede insertar contenido, hojas de estilo, módulos de desarrollo y mucho más con una simple contextual desde una aplicación web provisional a la aplicación web de producción para una más implementaciones libre de problemas y reducir el riesgo de interrumpir la aplicación web de producción al implementar una actualización.
Comprar una licencia para Courier2 del dominio `*.azurewebsites.net` y su dominio personalizado (por ejemplo http://abc.com) una vez que ha adquirido la licencia, coloque la licencia descargada (. Archivo LIC) en el `bin` carpeta.

![Coloque el archivo de licencia en la carpeta bin](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

Descargar el paquete Courier2 desde [aquí](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/). Inicie sesión en la aplicación web de fase, diga http://umbracocms-site-stage.azurewebsites.net/umbraco y haga clic en el menú de **desarrollador** y seleccione **paquetes**. Haga clic en **instalar** paquete local

![Instalador del paquete de Umbraco](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

Cargar el paquete de courier2 mediante el programa de instalación.

![Cargar el paquete de módulo courier](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

Para configurar necesite actualizar archivo courier.config en la carpeta de **configuración** de la aplicación web.

```xml
<!-- Repository connection settings -->
 <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
 <repositories>
    <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
    <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
      <url>http://umbracositecms-1.azurewebsites.net</url>
      <user>0</user>
      <!--<login>user@email.com</login> -->
      <!-- <password>user_password</password>-->
      <!-- <passwordEncoding>Clear</passwordEncoding>-->
      </repository>
 </repositories>
 ```

En `<repositories>`, escriba la información de usuario y la dirección URL del sitio de producción. Si está utilizando el proveedor de pertenencia Umbraco predeterminado, a continuación, agregue el identificador del usuario de administración de <user> sección. Si está utilizando un proveedor de pertenencia Umbraco personalizado, use `<login>`,`<password>` Courier2 módulo saber cómo conectar con el sitio de producción. Para obtener más detalles, revise la [documentación](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation) del módulo de Courier.

Del mismo modo, instale el módulo de Courier en su sitio de producción y configúrelo punto fase web app en su archivo courier.config respectivos como se muestra aquí

```xml
 <!-- Repository connection settings -->
 <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
 <repositories>
    <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
    <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
      <url>http://umbracositecms-1-stage.azurewebsites.net</url>
      <user>0</user>
      </repository>
 </repositories>
```

Haga clic en pestaña Courier2 en paneles de CMS Umbraco web app y seleccione ubicaciones. Verá el nombre del repositorio como se mencionó en `courier.config`. Hacer esto en su producción y de ensayo aplicaciones web.

![Repositorio de aplicación web de vista destino](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

Ahora le permite distribuir contenido del sitio de ensayo al sitio de producción. Vaya a contenido y seleccionar una página existente o crear una nueva página. Voy a seleccionar una página existente de mi aplicación web donde se cambia el título de la página Introducción **: nueva** y ahora, haga clic en **Guardar y publicar**.

![Cambiar el título de página y publicar](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

Ahora seleccione la página modificada y el *botón secundario en* para ver todas las opciones. Haga clic en **correo** para ver el cuadro de diálogo de implementación. Haga clic en **implementar** para iniciar la implementación

![Cuadro de diálogo de implementación de módulo de Courier](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

Revisar los cambios y haga clic en continuar.

![Cambios de la revisión de cuadro de diálogo de implementación de Courier módulo](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

Registro de implementación de muestra si la implementación es correcta.

 ![Ver registros de implementación de módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

Busque la aplicación web de producción para ver si se reflejarán los cambios.

 ![Busque la aplicación web de producción](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Para obtener más información sobre cómo usar a Courier, revise la documentación.

#### <a name="how-to-upgrade-umbraco-cms-version"></a>Cómo actualizar Umbraco CMS versión

Courier no le ayudará a implementar con la actualización de una versión de Umbraco CMS a otro. Al actualizar la versión de Umbraco CMS, debe comprobar incompatibilidad con sus módulos personalizados o módulos de terceros y las bibliotecas de Umbraco Core. Como práctica recomendada

1. SIEMPRE copia de seguridad de la aplicación web y base de datos antes de realizar una actualización. En la aplicación Web de Azure, puede configurar copias de seguridad automáticas para características de los sitios Web con la copia de seguridad y restauración el sitio si es necesario utilizar Restaurar característica. Para obtener más detalles, consulte [cómo hacer copia de seguridad de la aplicación web](web-sites-backup.md) y [cómo restaurar la aplicación web](web-sites-restore.md).

2. Compruebe si está usando los paquetes de terceros son compatibles con la versión que está actualizando. En el paquete de la página de descarga, revise la compatibilidad de proyecto con la versión de Umbraco CMS.

Para obtener más detalles sobre cómo actualizar la aplicación web de forma local, siga las directrices como mencionada [aquí](https://our.umbraco.org/documentation/getting-started/set up/upgrading/general).

Una vez que se actualiza su sitio de desarrollo local, publicar los cambios en la aplicación web de ensayo. Probar la aplicación y si todo es satisfactorio, use el botón **intercambiar** **intercambiar** su sitio de ensayo a producción web app. Cuando se realiza la operación **intercambiar** , puede ver los cambios que se verán afectados en configuración de la aplicación web. Con esta operación **intercambiar** , nos estamos intercambiar las aplicaciones web y bases de datos. Esto significa que, después de base de datos de db de etapa umbraco haga el intercambio de la aplicación web de producción y base de datos de db de producción umbraco señale provisional aplicación web.

![Intercambiar vista previa para implementar Umbraco CMS](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

La ventaja de intercambio de la aplicación web y la base de datos:
1. Le ofrece la capacidad de volver a la versión anterior de la aplicación web con otro **intercambiar** si hay problemas de aplicación.
2. Para realizar una actualización debe implementar archivos y base de datos desde la aplicación web a la aplicación web de producción y base de datos de ensayo. Hay muchas cosas que pueden fallar al implementar archivos y base de datos. Mediante la característica de **intercambio** de ranuras, podemos reducir el tiempo de inactividad durante la actualización y reducir el riesgo de errores que pueden producirse al implementar los cambios.
3. Le permite hacer **A / B pruebas** mediante la característica de [prueba de producción](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

Este ejemplo muestra la flexibilidad de la plataforma donde puede crear módulos personalizados similares a módulo Umbraco Courier para administrar la implementación en entornos.

## <a name="references"></a>Referencias
[Desarrollo de software ágil con el servicio de aplicación de Azure](app-service-agile-software-development.md)

[Configurar entornos de web apps en el servicio de aplicación de Azure de ensayo](web-sites-staged-publishing.md)

[Cómo bloquear el acceso web a las casillas de implementación no es de producción](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)
