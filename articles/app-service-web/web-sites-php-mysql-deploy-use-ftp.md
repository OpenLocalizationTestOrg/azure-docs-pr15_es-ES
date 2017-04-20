<properties 
    pageTitle="Crear una aplicación web de PHP MySQL en servicio de la aplicación de Azure e implementar mediante FTP" 
    description="Tutorial que muestra cómo crear una aplicación web PHP que almacena datos en la implementación de FTP MySQL y uso en Azure." 
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


#<a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-ftp"></a>Crear una aplicación web de PHP MySQL en servicio de la aplicación de Azure e implementar mediante FTP

Este tutorial muestra cómo crear una aplicación web de PHP MySQL y cómo implementar mediante FTP. En este tutorial se supone que tiene [PHP][install-php], [MySQL][install-mysql], un servidor web y un cliente FTP instalado en su equipo. Pueden seguir las instrucciones de este tutorial en ningún sistema operativo, incluyendo Windows, Mac y Linux. Tras completar a esta guía, tendrá una aplicación web PHP y MySQL ejecuta en Azure.
 
Aprenderá:

* Cómo crear una aplicación web y una base de datos MySQL con el Portal de Azure. Como PHP está habilitada en las aplicaciones Web de forma predeterminada, nada especial es necesaria para ejecutar el código PHP.
* Cómo publicar su aplicación en Azure mediante FTP.
 
Siguiendo este tutorial, creará una aplicación web de registro simple en PHP. La aplicación se alojarán en una aplicación Web. Captura de pantalla de la aplicación completada está por debajo de:

![Sitio Web PHP Azure][running-app]

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. No requeridas de tarjetas de crédito, sin compromisos. 


##<a name="create-a-web-app-and-set-up-ftp-publishing"></a>Crear una aplicación web y configurar publicación FTP

Siga estos pasos para crear una aplicación web y una base de datos MySQL:

1. Inicie sesión en el [Portal de Azure][management-portal].
2. Haga clic en el icono **+ nuevo** en la parte superior izquierdo del Portal de Azure.

    ![Crear nuevo sitio Web de Azure][new-website]

3. En la búsqueda, escriba **Online + MySQL** y haga clic en la **aplicación Web + MySQL**.

    ![Crear personalizado un nuevo sitio Web][custom-create]

4. Haga clic en **crear**. Escriba un nombre de servicio de aplicación única, un nombre válido para el grupo de recursos y un nuevo plan de servicio.

    ![Nombre de grupo de recursos de conjunto][resource-group]


6. Escriba los valores para la nueva base de datos, incluidos aceptando las condiciones legales.

    ![Crear nueva base de datos MySQL][new-mysql-db]
    
7. Cuando se ha creado la aplicación web, verá el nuevo módulo de servicio de aplicación.


6. Haga clic en **configuración** > **credenciales de implementación**. 

    ![Establezca las credenciales de implementación][set-deployment-credentials]

7. Para habilitar la publicación de FTP, debe proporcionar un nombre de usuario y contraseña. Guardar las credenciales y anote el nombre de usuario y contraseña que cree.

    ![Crear credenciales de publicación][portal-ftp-username-password]

##<a name="build-and-test-your-app-locally"></a>Crear y probar la aplicación localmente

La aplicación de registro es una aplicación PHP sencilla que le permite registrar un evento proporcionando su dirección de correo electrónico y nombre. Información sobre inscriban anterior se muestra en una tabla. Información de registro se almacena en una base de datos MySQL. La aplicación consta de dos archivos:

* **index.php**: muestra un formulario de registro y una tabla que contiene la información del titular.
* **CreateTable.php**: crea la tabla de MySQL para la aplicación. Una vez solo se usará este archivo.

Crear y ejecutar la aplicación localmente, siga los pasos siguientes. Tenga en cuenta que el procedimiento siguiente asume tiene PHP, MySQL, y un servidor web configurado en el equipo local y que se ha habilitado la [extensión p para MySQL][pdo-mysql].

1. Crear una base de datos MySQL denominado `registration`. Puede hacerlo desde el símbolo de MySQL con este comando:

        mysql> create database registration;

2. En el directorio de raíz del servidor web, cree una carpeta denominada `registration` y crear dos archivos en él - una llamada `createtable.php` y una llamada `index.php`.

3. Abrir la `createtable.php` archivo en un editor de texto o IDE y agregue el código siguiente. Este código se usará para crear la `registration_tbl` de la tabla en la `registration` base de datos.

        <?php
        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            $sql = "CREATE TABLE registration_tbl(
                        id INT NOT NULL AUTO_INCREMENT, 
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

    > [AZURE.NOTE] 
    > Debe actualizar los valores de <code>$user</code> y <code>$pwd</code> con su nombre de usuario de MySQL local y la contraseña.

4. Abra un navegador web y vaya a [http://localhost/registration/createtable.php][localhost-createtable]. Esta opción creará el `registration_tbl` tabla en la base de datos.

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
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }

    > [AZURE.NOTE]
    > De nuevo, debe actualizar los valores de <code>$user</code> y <code>$pwd</code> con su nombre de usuario de MySQL local y la contraseña.

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

Ahora puede ir a [http://localhost/registration/index.php] [ localhost-index] para probar la aplicación.

##<a name="get-mysql-and-ftp-connection-information"></a>Obtener información de conexión MySQL y FTP

Para conectarse a la base de datos MySQL que se ejecuta en aplicaciones Web, sus necesita la información de conexión. Para obtener información de conexión de MySQL, siga estos pasos:

1. Desde el servicio de aplicación módulo de aplicación web haga clic en el vínculo del grupo de recursos:

    ![Seleccione el grupo de recursos][select-resourcegroup]

1. En el grupo de recursos, haga clic en la base de datos:

    ![Seleccione la base de datos][select-database]

2. En la base de datos de resumen, seleccione **Configuración del** > **Propiedades**.

    ![Seleccione Propiedades][select-properties]
    
2. Tome nota de los valores de `Database`, `Host`, `User Id`, y `Password`.

    ![Nota las propiedades][note-properties]

3. Desde la aplicación web, haga clic en el vínculo de **descarga Publicar perfil** en la esquina inferior derecha de la página:

    ![Descargar publicar perfil][download-publish-profile]

4. Abrir la `.publishsettings` archivo en un editor XML. 

3. Buscar la `<publishProfile >` elemento con `publishMethod="FTP"` que tiene un aspecto similar a este:

        <publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
            ...
        </publishProfile>
    
Tome nota de la `publishUrl`, `userName`, y `userPWD` atributos.

##<a name="publish-your-app"></a>Publicar su aplicación

Después de probar la aplicación localmente, puede publicar su aplicación web mediante FTP. Sin embargo, primero deberá actualizar la información de conexión de base de datos en la aplicación. Con la información de conexión de base de datos obtuvo anteriormente (en la sección **información de conexión de FTP y obtener MySQL** ), actualice la información siguiente en **tanto** la `createdatabase.php` y `index.php` archivos con los valores apropiados:

    // DB connection info
    $host = "value of Data Source";
    $user = "value of User Id";
    $pwd = "value of Password";
    $db = "value of Database";

Ahora ya está listo para publicar la aplicación mediante FTP.

1. Abra al cliente FTP de elección.

2. Escriba la *parte del nombre de host* de la `publishUrl` atributo mencionados en el cliente de FTP.

3. Escriba el `userName` y `userPWD` sin cambian los atributos que se mencionó anteriormente en el cliente de FTP.

4. Establecer una conexión.

Una vez que haya conectado podrá cargar y descargar archivos según sea necesario. Asegúrese de que va a cargar archivos en el directorio raíz, que es `/site/wwwroot`.

Tras cargar ambos `index.php` y `createtable.php`, vaya a **http://[site name].azurewebsites.net/createtable.php** a crear la tabla de MySQL para la aplicación, a continuación, vaya a **http://[site name].azurewebsites.net/index.php** para empezar a usar la aplicación.
 
## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de PHP](/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-ftp/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-ftp/create_web_mysql.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-ftp/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-ftp/select_webapp.png
[set-deployment-credentials]: ./media/web-sites-php-mysql-deploy-use-ftp/set_credentials.png
[portal-ftp-username-password]: ./media/web-sites-php-mysql-deploy-use-ftp/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-ftp/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-ftp/create_wa.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-ftp/select_database.png
[select-resourcegroup]: ./media/web-sites-php-mysql-deploy-use-ftp/select_resourcegroup.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/note-properties.png

[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://portal.azure.com
[download-publish-profile]: ./media/web-sites-php-mysql-deploy-use-ftp/download_publish_profile_3.png
 
