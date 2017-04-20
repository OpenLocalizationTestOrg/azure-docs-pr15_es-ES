<properties 
    pageTitle="Crear una aplicación web de PHP SQL e implementar al servicio de aplicación de Azure con Git" 
    description="Tutorial que muestra cómo crear una aplicación web PHP que almacena datos en la base de datos de SQL Azure y utiliza implementación Git al servicio de la aplicación de Azure." 
    services="app-service\web, sql-database" 
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

# <a name="create-a-php-sql-web-app-and-deploy-to-azure-app-service-using-git"></a>Crear una aplicación web de PHP SQL e implementar al servicio de aplicación de Azure con Git

En este tutorial se muestra cómo crear una aplicación web PHP en la [Aplicación de servicio de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) que se conecta a la base de datos de SQL Azure y cómo implementar mediante Git. En este tutorial se supone que tiene [PHP][install-php], [SQL Server Express][install-SQLExpress], los [Controladores de Microsoft para SQL Server para PHP](http://www.microsoft.com/download/en/details.aspx?id=20098)y [Git] [ install-git] instalado en su equipo. Tras completar a esta guía, tendrá una aplicación web de PHP SQL ejecuta en Azure.

> [AZURE.NOTE]
> Puede instalar y configurar PHP, SQL Server Express y Drivers de Microsoft para SQL Server para PHP mediante el [Instalador de plataforma Web de Microsoft](http://www.microsoft.com/web/downloads/platform.aspx).

Aprenderá:

* Cómo crear una aplicación web de Azure y una base de datos de SQL con el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Dado que PHP está activada de forma predeterminada en la aplicación de servicio Web Apps, nada especial es necesaria para ejecutar el código PHP.
* Cómo publicar y volver a publicar la aplicación en Azure con Git.
 
Siguiendo este tutorial, va a crear una aplicación web de registro simple en PHP. La aplicación se alojarán en un sitio Web de Azure. Captura de pantalla de la aplicación completada está por debajo de:

![Sitio Web PHP Azure](./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png)

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

##<a name="create-an-azure-web-app-and-set-up-git-publishing"></a>Crear una aplicación web de Azure y configurar publicación Git

Siga estos pasos para crear una aplicación web de Azure y una base de datos de SQL:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. Abrir el catálogo de soluciones de Azure haciendo clic en el icono de **nuevo** en la parte superior izquierda del panel, haga clic en **Seleccionar todo** junto a Marketplace y seleccionando **Web + Mobile**.
    
3. En el catálogo de soluciones, seleccione **Web + Mobile**.

4. Haga clic en el icono de la **aplicación Web + SQL** .

5. Después de leer la descripción de la aplicación Web + SQL aplicación, seleccione **crear**.

6. Haga clic en cada elemento (**Grupo de recursos**, **Aplicación Web**, **base de datos**y **suscripción**) y escriba o seleccione los valores para los campos necesarios:
    
    - Escriba un nombre de dirección URL de su elección   
    - Configurar las credenciales de servidor de base de datos
    - Seleccione la región más cercana a usted

    ![configurar la aplicación](./media/web-sites-php-sql-database-deploy-use-git/configure-db-settings.png)

7. Cuando haya terminado la definición de la aplicación web, haga clic en **crear**.

    Cuando se ha creado la aplicación web, el botón **notificaciones** parpadea **éxito** verde y el módulo de grupo de recursos abierto para mostrar la aplicación web y la base de datos SQL en el grupo.

4. Haga clic en el icono de la aplicación web en el módulo de grupo de recursos para abrir el módulo de la aplicación web.

    ![grupo de recursos de la aplicación de Web](./media/web-sites-php-sql-database-deploy-use-git/resource-group-blade.png)

5. En **configuración** , haga clic en **implementación continua** > **configurar configuración necesaria**. Seleccione **Repositorio Git Local** y haga clic en **Aceptar**.

    ![¿dónde está el código fuente](./media/web-sites-php-sql-database-deploy-use-git/setup-local-git.png)

    Si no ha configurado un repositorio Git antes, debe proporcionar un nombre de usuario y contraseña. Para ello, haga clic en **configuración** > **credenciales de implementación** de módulo de la aplicación web.

    ![](./media/web-sites-php-sql-database-deploy-use-git/deployment-credentials.png)

6. En **configuración** , haga clic en **Propiedades** para ver la dirección URL remota Git debe usar para implementar la aplicación PHP más adelante.

##<a name="get-sql-database-connection-information"></a>Obtener información de conexión de base de datos SQL

Para conectarse a la instancia de base de datos SQL que está vinculada a la aplicación web de sus necesita la información de conexión que especificó al crear la base de datos. Para obtener la información de conexión de base de datos SQL, siga estos pasos:

1. En el módulo del grupo de recursos, haga clic en el icono de base de datos SQL.

2. Módulo de base de datos SQL, haga clic en **configuración** > **Propiedades**, haga clic en **mostrar cadenas de conexión de base de datos**. 

    ![Ver propiedades de base de datos](./media/web-sites-php-sql-database-deploy-use-git/view-database-properties.png)
    
3. En la sección **PHP** del cuadro de diálogo resultante, tome nota de los valores de `Server`, `SQL Database`, y `User Name`. Estos valores que usa más adelante al publicar la aplicación web de PHP al servicio de la aplicación de Azure.

##<a name="build-and-test-your-application-locally"></a>Crear y probar la aplicación localmente

La aplicación de registro es una aplicación PHP sencilla que le permite registrar un evento proporcionando su dirección de correo electrónico y nombre. Información sobre inscriban anterior se muestra en una tabla. Información de registro se almacena en una instancia de base de datos de SQL. La aplicación consta de dos archivos (copiar y pegar código disponible a continuación):

* **index.php**: muestra un formulario de registro y una tabla que contiene la información del titular.
* **CreateTable.php**: crea la tabla de base de datos SQL de la aplicación. Una vez solo se usará este archivo.

Para ejecutar la aplicación localmente, siga los pasos siguientes. Tenga en cuenta que el procedimiento siguiente asume PHP y SQL Server Express instalado en su equipo local, y que ha habilitado la [extensión p para SQL Server][pdo-sqlsrv].

1. Crear una base de datos de SQL Server denominada `registration`. Puede hacerlo desde la `sqlcmd` símbolo del sistema con estos comandos:

        >sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
        1> create database registration
        2> GO   


2. En el directorio de raíz de la aplicación, cree dos archivos - una llamada `createtable.php` y una llamada `index.php`.

3. Abrir la `createtable.php` archivo en un editor de texto o IDE y agregue el código siguiente. Este código se usará para crear la `registration_tbl` de la tabla en la `registration` base de datos.

        <?php
        // DB connection info
        $host = "localhost\sqlexpress";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
            $conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            $sql = "CREATE TABLE registration_tbl(
            id INT NOT NULL IDENTITY(1,1) 
            PRIMARY KEY(id),
            name VARCHAR(30),
            email VARCHAR(30),
            date DATE)";
            $conn->query($sql);
        }
        catch(Exception $e){
            die(print_r($e));
        }
        echo "<h3>Table created.</h3>";
        ?>

    Tenga en cuenta que debe actualizar los valores de <code>$user</code> y <code>$pwd</code> con su nombre de usuario de SQL Server local y la contraseña.

4. En un terminal en el directorio de raíz de la aplicación, escriba el siguiente comando:

        php -S localhost:8000

4. Abra un navegador web y vaya a **http://localhost:8000/createtable.php**. Esta opción creará el `registration_tbl` tabla en la base de datos.

5. Abra el archivo de **index.php** en un editor de texto o IDE y agregar el código HTML y CSS básico de la página (el código PHP se agregará en pasos posteriores).

        <html>
        <head>
        <Title>Registration Form</Title>
        <style type="text/css">
            body { background-color: #fff; border-top: solid 10px #000;
                color: #333; font-size: .85em; margin: 20; padding: 20;
                font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
            }
            h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
            h1 { font-size: 2em; }
            h2 { font-size: 1.75em; }
            h3 { font-size: 1.2em; }
            table { margin-top: 0.75em; }
            th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
            td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
        </style>
        </head>
        <body>
        <h1>Register here!</h1>
        <p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
        <form method="post" action="index.php" enctype="multipart/form-data" >
              Name  <input type="text" name="name" id="name"/></br>
              Email <input type="text" name="email" id="email"/></br>
              <input type="submit" name="submit" value="Submit" />
        </form>
        <?php

        ?>
        </body>
        </html>

6. En las etiquetas PHP, agregar código PHP para conectarse a la base de datos.

        // DB connection info
        $host = "localhost\sqlexpress";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }

    De nuevo, debe actualizar los valores de <code>$user</code> y <code>$pwd</code> con su nombre de usuario de MySQL local y la contraseña.

7. El código de conexión de base de datos, agregar código para insertar la información de registro en la base de datos.

        if(!empty($_POST)) {
        try {
            $name = $_POST['name'];
            $email = $_POST['email'];
            $date = date("Y-m-d");
            // Insert data
            $sql_insert = "INSERT INTO registration_tbl (name, email, date) 
                           VALUES (?,?,?)";
            $stmt = $conn->prepare($sql_insert);
            $stmt->bindValue(1, $name);
            $stmt->bindValue(2, $email);
            $stmt->bindValue(3, $date);
            $stmt->execute();
        }
        catch(Exception $e) {
            die(var_dump($e));
        }
        echo "<h3>Your're registered!</h3>";
        }

8. Por último, siguiendo el código anterior, agregue el código para recuperar datos de la base de datos.

        $sql_select = "SELECT * FROM registration_tbl";
        $stmt = $conn->query($sql_select);
        $registrants = $stmt->fetchAll(); 
        if(count($registrants) > 0) {
            echo "<h2>People who are registered:</h2>";
            echo "<table>";
            echo "<tr><th>Name</th>";
            echo "<th>Email</th>";
            echo "<th>Date</th></tr>";
            foreach($registrants as $registrant) {
                echo "<tr><td>".$registrant['name']."</td>";
                echo "<td>".$registrant['email']."</td>";
                echo "<td>".$registrant['date']."</td></tr>";
            }
            echo "</table>";
        } else {
            echo "<h3>No one is currently registered.</h3>";
        }

Ahora puede examinar **http://localhost:8000/index.php** para probar la aplicación.

##<a name="publish-your-application"></a>Publicar su aplicación

Después de haber probado local de la aplicación, puede publicarlo en la aplicación de servicio Web Apps con Git. Sin embargo, primero deberá actualizar la información de conexión de base de datos en la aplicación. Con la información de conexión de base de datos ha obtenido anteriormente (en la sección **información de conexión de base de datos de SQL de obtener** ), actualice la información siguiente en **tanto** la `createdatabase.php` y `index.php` archivos con los valores apropiados:

    // DB connection info
    $host = "tcp:<value of Server>";
    $user = "<value of User Name>";
    $pwd = "<your password>";
    $db = "<value of SQL Database>";

> [AZURE.NOTE]
> En el <code>$host</code>, se debe agregar el valor de servidor con <code>tcp:</code>.


Ahora está listo para configurar la publicación Git y publicar la aplicación.

> [AZURE.NOTE]
> Estos son los mismos pasos que se indican al final de la sección **crear una aplicación web de Azure y configurar publicación Git** anterior.


1. Abra GitBash (o un terminal, si Git está en su `PATH`), cambie los directorios al directorio de raíz de la aplicación (el directorio de **registro** ) y ejecute los comandos siguientes:

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Se le pedirá la contraseña que creó anteriormente.

2. Vaya a **http://[web aplicación name].azurewebsites.net/createtable.php** para crear la tabla de base de datos SQL de la aplicación.
3. Vaya a **http://[web aplicación name].azurewebsites.net/index.php** para empezar a usar la aplicación.

Una vez que haya publicado la aplicación, puede empezar a realizar cambios en él y utilizar Git publicarlas. 

##<a name="publish-changes-to-your-application"></a>Publicar los cambios en la aplicación

Para publicar los cambios en la aplicación, siga estos pasos:

1. Realizar cambios en la aplicación localmente.
2. Abra GitBash (o un terminal TI Git está en su `PATH`), cambie los directorios al directorio de raíz de la aplicación y ejecute los comandos siguientes:

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Se le pedirá la contraseña que creó anteriormente.

3. Vaya a **http://[web aplicación name].azurewebsites.net/index.php** para ver los cambios.

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)




[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv
 
