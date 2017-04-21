<properties
    pageTitle="Crear y conectarse a una base de datos MySQL en Azure"
    description="Obtenga información sobre cómo usar el portal de Azure para crear una base de datos MySQL y, a continuación, conectarse a él desde una aplicación web PHP en Azure."
    documentationCenter="php"
    services="app-service\web"
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm;cephalin"/>

# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Crear y conectarse a una base de datos MySQL en Azure

En este tutorial se muestra cómo crear una base de datos MySQL en el [portal de Azure](https://portal.azure.com) (proveedor es [ClearDB](http://www.cleardb.com/)) y cómo conectarse a él desde una aplicación web PHP ejecuta en el [Servicio de aplicación de Azure](./app-service/app-service-value-prop-what-is.md). 

> [AZURE.NOTE] También puede crear una base de datos MySQL como parte de una [plantilla de aplicación de catálogo de soluciones](./app-service-web/app-service-web-create-web-app-from-marketplace.md).

## <a name="create-a-mysql-database-in-azure-portal"></a>Crear una base de datos MySQL en el portal de Azure

Para crear una base de datos MySQL en el portal de Azure, haga lo siguiente:

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).

2. En el menú de la izquierda, haga clic en **nuevo** > **datos + almacenamiento** > **Base de datos MySQL**.

    ![Crear una base de datos MySQL en Azure - inicio](./media/store-php-create-mysql-database/create-db-1-start.png)

2. En el [módulo](azure-portal-overview.md)de nueva base de datos MySQL, configure la nueva base de datos MySQL como sigue (*módulo*: una página de portal abre horizontalmente):

    - **Nombre de la base de datos**: escriba un nombre identificarse de forma exclusiva
    - **Suscripción**: elija la suscripción usar
    - **Tipo de base de datos**: seleccione **compartido** para niveles de bajo costo o libres, o **dedicado** a obtener recursos dedicados. 
    - **Grupo de recursos**: agregar la base de datos MySQL a un [grupo de recursos](../azure-resource-manager/resource-group-overview.md) de existente o colocarla en una nueva. Recursos en el mismo grupo pueden administrarse fácilmente entre sí.
    - **Ubicación**: seleccione una ubicación más cercanos. Cuando se agrega a un grupo de recursos existente, está bloqueada a la ubicación del grupo de recursos.
    - **Nivel de precios**: haga clic en el **Nivel de precios**, seleccione una opción de precio (nivel de**mercurio** es gratuita) y, a continuación, haga clic en **Seleccionar**. 
    - **Condiciones legales**: haga clic en **Condiciones legales**, revise los detalles de la compra y haga clic en **comprar**.
    - **Anclar al panel**: seleccione si desea acceder directamente desde el panel. Esto es especialmente útil si no está familiarizado con navegación portal todavía.
    
    La siguiente captura de pantalla es simplemente un ejemplo de cómo puede configurar la base de datos MySQL.  
    ![Crear una base de datos MySQL en Azure: configurar](./media/store-php-create-mysql-database/create-db-2-configure.png)

3. Cuando haya terminado de configurar, haga clic en **crear**.

    ![Crear una base de datos MySQL en Azure: crear](./media/store-php-create-mysql-database/create-db-3-create.png)

    Verá una notificación emergente informarle de que ha iniciado la implementación.

    ![Crear una base de datos MySQL en Azure - en curso](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    Obtendrá otro emergente una vez realizada la implementación. El portal también abrirá automáticamente el módulo de base de datos MySQL.

<a name="connect"></a>
## <a name="connect-to-your-mysql-database"></a>Conectarse a la base de datos MySQL

Para ver la información de conexión para la base de datos MySQL, simplemente haga clic en **Propiedades** en el módulo de la aplicación web.
    
![Crear una base de datos MySQL en Azure - módulo de base de datos MySQL](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Ahora puede utilizar esa información de conexión en cualquier aplicación web. Un ejemplo que muestra cómo usar la información de conexión de una aplicación PHP simple está disponible [aquí](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql).

## <a name="connect-a-laravel-web-app-from-the-php-get-started-tutorial"></a>Conectar una aplicación web de Laravel (desde el tutorial de introducción de obtener PHP)

Supongamos que ha terminado el tutorial [crear, configurar e implementar una aplicación web PHP en Azure](./app-service-web/app-service-web-php-get-started.md) y tiene una aplicación web de [Laravel](https://www.laravel.com/) ejecuta en Azure. Puede agregar fácilmente las capacidades de base de datos a su aplicación Laravel. Siga los pasos siguientes:

>[AZURE.NOTE] Pasos siguientes se asume que ha finalizado el tutorial [crear, configurar e implementar una aplicación web PHP en Azure](./app-service-web/app-service-web-php-get-started.md).

1. Configurar la aplicación de Laravel en el entorno de desarrollo local para que apunten a la base de datos MySQL. Para ello, abra `.env` desde la aplicación Laravel directorio raíz y configurar las opciones de la base de datos MySQL.

        DB_CONNECTION=mysql
        DB_HOST=<HOSTNAME_from_properties_blade>
        DB_PORT=<PORT_from_properties_blade>
        DB_DATABASE=<see_note_below>
        DB_USERNAME=<USERNAME_from_properties_blade>
        DB_PASSWORD=<PASSWORD_from_properties_blade>

    >[AZURE.NOTE] En la hoja de **Propiedades** , el nombre de la base de datos MySQL puede o no puede que se muestra en el campo **Nombre de la base de datos** . Es mejor comprobar el parámetro de base de datos en el campo de **Cadena de conexión** . 
    >
    >![Crear una base de datos MySQL en Azure - en curso](./media/store-php-create-mysql-database/connect-db-1-database-name.png)

2. La forma más rápida de comprobar que tiene acceso MySQL ahora es usar [scaffolding de autenticación predeterminado de Laravel](https://laravel.com/docs/5.2/authentication#authentication-quickstart). En la línea de comandos terminal, ejecute los comandos siguientes desde el directorio de raíz de la aplicación Laravel:

         php artisan migrate
         php artisan make:auth

    El primer comando crea las tablas en Azure basado en las migraciones predefinidas en el `database/migrations` directorio y el segundo comando scaffolds la vistas básicas y rutas de registro de usuario y la autenticación.

3. Ejecutar el servidor de desarrollo ahora:

        php artisan serve

4. En el explorador, vaya a http://localhost:8000 y registrar un nuevo usuario, tal como se muestra:

    ![Conectarse a la base de datos MySQL en Azure: registrar el usuario](./media/store-php-create-mysql-database/connect-db-2-development-server.png)

    Siga el indicador de interfaz de usuario completo en el registro. Una vez completado el registro, se registrará.
    
    ![Conectarse a la base de datos MySQL en Azure: registrar el usuario](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)

    Ahora se desarrollar su aplicación en la base de datos MySQL en Azure.

5. Ahora, ¿necesita replicar su `.env` configuración de la aplicación web de Azure. Ejecute los siguientes comandos de Azure CLI:

        azure site appsetting add DB_CONNECTION=mysql
        azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
        azure site appsetting add DB_PORT=<PORT_from_properties_blade>
        azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
        azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
        azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>

    Descubra cómo funciona esto en [Configurar la aplicación web de Azure](./app-service-web/app-service-web-php-get-started.md#configure).

6. A continuación, confirmar e ingresar a Azure los cambios locales realizados anteriormente mientras se está ejecutando `php artisan make:auth`.

        git add .
        git commit -m "scaffold auth views and routes"
        git push azure master

7. Vaya a la aplicación web de Azure.

        azure site browse

8. Inicie sesión con las credenciales del usuario que creó anteriormente.

    ![Conectarse a la base de datos MySQL en Azure - vaya a la aplicación web de Azure](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)

    Después de iniciar sesión, debe ver la pantalla de inicio de sesión posterior al descriptiva.
    
    ![Conectarse a la base de datos MySQL en Azure - conectado](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)

    Enhorabuena, la aplicación web de PHP en Azure ahora tiene acceso a datos de la base de datos MySQL. 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de PHP](/develop/php/).
