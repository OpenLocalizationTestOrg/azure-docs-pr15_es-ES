<properties
    pageTitle="Crear una aplicación web de PHP MySQL en servicio de la aplicación de Azure e implementar con Git"
    description="Tutorial que muestra cómo crear una aplicación web PHP que almacena datos en MySQL y utiliza implementación Git en Azure."
    services="app-service\web"
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-git"></a>Crear una aplicación web de PHP MySQL en servicio de la aplicación de Azure e implementar con Git

¡Este tutorial muestra cómo crear una aplicación web de PHP MySQL y cómo implementar [Aplicación de](http://go.microsoft.com/fwlink/?LinkId=529714) servicio con Git. Usará [PHP][install-php], la herramienta de línea de comandos de MySQL (parte de [MySQL][install-mysql]) y [Git] [ install-git] instalado en su equipo. Pueden seguir las instrucciones de este tutorial en ningún sistema operativo, incluyendo Windows, Mac y Linux. Tras completar a esta guía, tendrá una aplicación web PHP y MySQL ejecuta en Azure.

Aprenderá:

* Cómo crear una aplicación web y una base de datos MySQL con el [Portal de Azure][management-portal]. Dado que PHP está activada de forma predeterminada en la [Aplicación de servicio Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) , nada especial es necesaria para ejecutar el código PHP.
* Cómo publicar y volver a publicar la aplicación en Azure con Git.
* Cómo habilitar la extensión de compositor Compositor de automatizar tareas en cada `git push`.

Siguiendo este tutorial, creará una aplicación web de registro simple en PHP. La aplicación se alojarán en aplicaciones Web. Captura de pantalla de la aplicación completada está por debajo de:

![Sitio web de PHP Azure][running-app]

## <a name="set-up-the-development-environment"></a>Configurar el entorno de desarrollo

En este tutorial se supone que tiene [PHP][install-php], la herramienta de línea de comandos de MySQL (parte de [MySQL][install-mysql]) y [Git] [ install-git] instalado en su equipo.

<a id="create-web-site-and-set-up-git"></a>
## <a name="create-a-web-app-and-set-up-git-publishing"></a>Crear una aplicación web y configurar publicación Git

Siga estos pasos para crear una aplicación web y una base de datos MySQL:

1. Inicie sesión en el [Portal de Azure][management-portal].
2. Haga clic en el icono de **nuevo** .

3. Haga clic en **Ver todo** junto a **Marketplace**. 

4. Haga clic en **Web + Mobile**, **Web app + MySQL**. A continuación, haga clic en **crear**.

4. Escriba un nombre válido para el grupo de recursos.

    ![Nombre de grupo de recursos de conjunto][resource-group]

5. Escriba los valores de la nueva aplicación web.

    ![Crear aplicaciones web][new-web-app]

6. Escriba los valores para la nueva base de datos, incluidos aceptando las condiciones legales.

    ![Crear nueva base de datos MySQL][new-mysql-db]

7. Cuando se ha creado la aplicación web, verá el nuevo módulo de aplicación web.

7. En **configuración** de **Implementación continua**, haga clic en haga clic en _Configurar la configuración necesaria_.

    ![Configurar publicación Git][setup-publishing]

8. Seleccione **Repositorio Git Local** para el origen.

    ![Configurar Git repositorio][setup-repository]


9. Para habilitar la publicación de Git, debe proporcionar un nombre de usuario y contraseña. Anote el nombre de usuario y contraseña que cree. (Si ha configurado un repositorio Git antes, se omitirá este paso.)

    ![Crear credenciales de publicación][credentials]


## <a name="get-remote-mysql-connection-information"></a>Obtener información de conexión MySQL remota

Para conectarse a la base de datos MySQL que se ejecuta en aplicaciones Web, sus necesita la información de conexión. Para obtener información de conexión de MySQL, siga estos pasos:

1. En el grupo de recursos, haga clic en la base de datos:

    ![Seleccione la base de datos][select-database]

2. En la base de datos de **configuración**, seleccione **Propiedades**.

    ![Seleccione Propiedades][select-properties]

2. Tome nota de los valores de `Database`, `Host`, `User Id`, y `Password`.

    ![Nota las propiedades][note-properties]

## <a name="build-and-test-your-app-locally"></a>Crear y probar la aplicación localmente

Ahora que ha creado una aplicación web, puede desarrollar su aplicación localmente y luego implementar después de probar.

La aplicación de registro es una aplicación PHP sencilla que le permite registrar un evento proporcionando su dirección de correo electrónico y nombre. Información sobre inscriban anterior se muestra en una tabla. Información de registro se almacena en una base de datos MySQL. La aplicación consta de un archivo (copiar y pegar código disponible a continuación):

* **index.php**: muestra un formulario de registro y una tabla que contiene la información del titular.

Crear y ejecutar la aplicación localmente, siga los pasos siguientes. Tenga en cuenta que el procedimiento siguiente asume la PHP y configurar en su equipo local de herramienta de línea de comandos de MySQL (parte de MySQL), y que ha habilitado la [extensión p para MySQL][pdo-mysql].

1. Conectar con el servidor MySQL remoto, con el valor de `Data Source`, `User Id`, `Password`, y `Database` que haya encontrado anteriormente:

        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. Aparecerá el símbolo del sistema de MySQL:

        mysql>

3. Pegue el siguiente `CREATE TABLE` comando para crear la `registration_tbl` tabla en la base de datos:

        CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. En la raíz de su carpeta local de la aplicación cree **index.php** archivo.

5. Abra el archivo de **index.php** en un editor de texto o IDE y agregue el código siguiente y complete los cambios necesarios marcados con `//TODO:` comentarios.


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
            // DB connection info
            //TODO: Update the values for $host, $user, $pwd, and $db
            //using the values you retrieved earlier from the Azure Portal.
            $host = "value of Data Source";
            $user = "value of User Id";
            $pwd = "value of Password";
            $db = "value of Database";
            // Connect to database.
            try {
                $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
                $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            }
            catch(Exception $e){
                die(var_dump($e));
            }
            // Insert registration info
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
            // Retrieve data
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
        ?>
        </body>
        </html>

4.  En un terminal vaya a la carpeta de la aplicación y escriba el siguiente comando:

        php -S localhost:8000

Ahora puede ir a **http://localhost:8000 /** para probar la aplicación.


## <a name="publish-your-app"></a>Publicar su aplicación

Después de probar la aplicación localmente, puede publicar aplicaciones Web usando Git. Se inicialice repositorio Git local y publicar la aplicación.

> [AZURE.NOTE]
> Estos son los mismos pasos que se muestra en el Portal de Azure al final de la creación de una aplicación web y establezca Git publicación sección anterior.

1. (Opcional)  Si ha olvidado o perdido la dirección URL de repositorio remoto de Git, vaya a las propiedades de la aplicación web en el Portal de Azure.

1. Abra GitBash (o un terminal, si Git está en su `PATH`), cambie los directorios al directorio de raíz de la aplicación y ejecute los comandos siguientes:

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Se le pedirá la contraseña que creó anteriormente.

    ![Inserción inicial en Azure a través de Git][git-initial-push]

2. Vaya a **http://[site name].azurewebsites.net/index.php** para empezar a usar la aplicación (esta información se almacenará en el panel de cuenta):

    ![Sitio web de PHP Azure][running-app]

Una vez que haya publicado la aplicación, puede empezar a realizar cambios en él y utilizar Git publicarlas.

## <a name="publish-changes-to-your-app"></a>Publicar los cambios en la aplicación

Para publicar los cambios en la aplicación, siga estos pasos:

1. Realizar cambios en la aplicación localmente.
2. Abra GitBash (o un terminal TI Git está en su `PATH`), cambie los directorios al directorio de raíz de la aplicación y ejecute los comandos siguientes:

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Se le pedirá la contraseña que creó anteriormente.

    ![Insertar cambios en el sitio en Azure a través de Git][git-change-push]

3. Vaya a **http://[site name].azurewebsites.net/index.php** para ver los cambios realizados y la aplicación:

    ![Sitio web de PHP Azure][running-app]

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

<a name="composer"></a>
## <a name="enable-composer-automation-with-the-composer-extension"></a>Habilitar automatización compositor con la extensión de composición

De forma predeterminada, el proceso de implementación de git en la aplicación de servicio no hace nada con composer.json, si tiene uno en su proyecto PHP. Puede habilitar composer.json procesamiento durante `git push` habilitando la extensión compositor.

1. En PHP web módulo de la aplicación en el [portal de Azure][management-portal], haga clic en **Herramientas** > **extensiones**.

    ![Configuración de la extensión de composición][composer-extension-settings]

2. Haga clic en **Agregar**y luego haga clic en **Compositor**.

    ![Agregar extensión compositor][composer-extension-add]
    
3. Haga clic en **Aceptar** para aceptar las condiciones legales. Haga clic en **Aceptar** para agregar la extensión.

    El módulo de **extensiones de instalar** ahora mostrará la extensión compositor.  
    ![Vista de extensión compositor][composer-extension-view]
    
4. Ahora, realizar `git add`, `git commit`, y `git push` igual que en la sección anterior. Ahora verá que compositor está instalando dependencias definidas en composer.json.

    ![Compositor extensión éxito][composer-extension-success]

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de PHP](/develop/php/).

<!-- URL List -->

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[install-mysql]: http://dev.mysql.com/downloads/mysql/
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[management-portal]: https://portal.azure.com
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx

<!-- IMG List -->

[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/create_web_mysql.png
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-git/select_webapp.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-git/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-git/create_wa.png
[setup-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_deploy.png
[setup-repository]: ./media/web-sites-php-mysql-deploy-use-git/select_local_git.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-git/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-git/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-git/note-properties.png

[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png

[composer-extension-settings]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-settings.png
[composer-extension-add]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-add.png
[composer-extension-view]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-view.png
[composer-extension-success]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-success.png
