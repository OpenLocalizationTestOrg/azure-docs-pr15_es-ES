<properties 
    pageTitle="Django y base de datos SQL Azure con herramientas de Python 2.2 para Visual Studio" 
    description="Obtenga información sobre cómo usar las herramientas de Python para Visual Studio para crear una aplicación web Django que almacena los datos en una instancia de base de datos SQL e implementar a Azure aplicación de servicio Web Apps." 
    services="app-service\web" 
    tags="python"
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="07/07/2016"
    ms.author="huguesv"/>




# <a name="django-and-sql-database-on-azure-with-python-tools-22-for-visual-studio"></a>Django y base de datos SQL Azure con herramientas de Python 2.2 para Visual Studio 

En este tutorial, usaremos [Python Tools para Visual Studio] para crear una aplicación web de sondeos simple mediante una de las plantillas de ejemplo PTVS. Este tutorial también está disponible como un [vídeo](https://www.youtube.com/watch?v=ZwcoGcIeHF4).

Nos aprenderá cómo usar una base de datos SQL hospedado en Azure, cómo configurar la aplicación web para usar una base de datos SQL y cómo publicar la aplicación web en la [Aplicación de servicio Web Apps de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Consulte el [Centro para desarrolladores de Python] para ver más artículos que tratan el desarrollo de aplicaciones de Azure aplicación de servicio Web con PTVS con botella, matraz y Django marcos web, con los servicios de almacenamiento de tablas de Azure, MySQL y base de datos de SQL. Mientras en este artículo se centra en la aplicación de servicio, los pasos son similares al desarrollar [Servicios de nube de Azure].

## <a name="prerequisites"></a>Requisitos previos

 - Visual Studio de 2015
 - [Python 2.7 32 bits]
 - [Python 2.2 las herramientas de Visual Studio]
 - [Python herramientas 2.2 de ejemplos de Visual Studio VSIX]
 - [Herramientas del SDK Azure VS 2015]
 - Django 1,9 o posterior

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="create-the-project"></a>Crear el proyecto

En esta sección, se creará un proyecto de Visual Studio con una plantilla de ejemplo. Se creará un entorno virtual e instalar paquetes necesarios. Vamos a crear una base de datos local con sqlite. A continuación, se podrá ejecutar la aplicación web localmente.

1.  En Visual Studio, seleccione **archivo**, **Nuevo proyecto**.

1.  Las plantillas de proyecto de la [Python 2.2 de herramientas de Visual Studio ejemplos VSIX] están disponibles en **Python**, **ejemplos**. Seleccione **Sondeos Django Web Project** y haga clic en Aceptar para crear el proyecto.

    ![Cuadro de diálogo nuevo proyecto](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Se le pedirá que instalar paquetes externos. Seleccione **instalar en un entorno virtual**.

    ![Cuadro de diálogo paquetes externos](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Seleccione **Python 2.7** como intérprete de base.

    ![Agregar el cuadro de diálogo de entorno Virtual](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  En el **Explorador de soluciones**, haga clic en el nodo del proyecto y, a continuación, seleccione **Migrar Django** **Python**.  A continuación, seleccione **Django crear superusuario**.

1.  Se abrirá una consola de administración de Django y crear una base de datos de sqlite en la carpeta del proyecto. Siga las indicaciones para crear un usuario.

1.  Confirme que la aplicación funciona, presione <kbd>F5</kbd>.

1.  En la barra de navegación en la parte superior, haga clic en **iniciar sesión** .

    ![Explorador Web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Escriba las credenciales del usuario que creó al sincronizar la base de datos.

    ![Explorador Web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Haga clic en **crear sondeos de ejemplo**.

    ![Explorador Web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Haga clic en un sondeo y votar.

    ![Explorador Web](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## <a name="create-a-sql-database"></a>Crear una base de datos SQL

La base de datos, vamos a crear una base de datos de SQL Azure.

Puede crear una base de datos siguiendo estos pasos.

1.  Inicie sesión en el [Portal de Azure].

1.  En la parte inferior del panel de navegación, haga clic en **nuevo**. , haga clic en **datos + almacenamiento** > **Base de datos de SQL**.

1.  Configurar la nueva base de datos de SQL mediante la creación de un nuevo grupo de recursos y seleccione la ubicación apropiada para el mismo.

1.  Una vez creada la base de datos de SQL, haga clic en **Abrir en Visual Studio** , en el módulo de base de datos.
2.  Haga clic en **Configurar el firewall**.
3.  En el módulo de la **Configuración del Firewall** , agregue que una regla de firewall con **IP inicial** y **Final IP** establecida en la dirección IP pública de su equipo de desarrollo. Haga clic en **Guardar**.

    Esto le permitirá conexiones con el servidor de base de datos desde el equipo de desarrollo.

4.  En el módulo de base de datos, haga clic en **Propiedades**, haga clic en **mostrar cadenas de conexión de base de datos**. 

2.  Use el botón Copiar para devolver el valor de **ADO.NET** en el Portapapeles.

## <a name="configure-the-project"></a>Configurar el proyecto

En esta sección, se configurará nuestra aplicación web para usar la base de datos SQL que acaba de crear. También se deberá instalar paquetes de Python adicionales necesarios para usar bases de datos SQL con Django. A continuación, se podrá ejecutar la aplicación web localmente.

1.  En Visual Studio, abra **settings.py**, desde la carpeta de *nombre de proyecto* . Pegar temporalmente la cadena de conexión en el editor. La cadena de conexión está en este formato:

        Server=<ServerName>,<ServerPort>;Database=<DatabaseName>;User ID=<UserName>;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

Editar la definición de `DATABASES` utilizar los valores anteriores.

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<UserName>',
                'PASSWORD': '{your_password_here}',
                'HOST': '<ServerName>',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

1.  En el Explorador de soluciones, en **Entornos de Python**, haga clic en el entorno virtual y seleccione **Instalar el paquete de Python**.

1.  Instalar el paquete `pyodbc` con **puntos**.

    ![Instalar Python paquete diálogo](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Instalar el paquete `django-pyodbc-azure` con **puntos**.

    ![Instalar Python paquete diálogo](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  En el **Explorador de soluciones**, haga clic en el nodo del proyecto y, a continuación, seleccione **Migrar Django** **Python**.  A continuación, seleccione **Django crear superusuario**.

    Se creará las tablas de la base de datos SQL que se creó en la sección anterior. Siga las indicaciones para crear un usuario que no tiene que coincidir con el usuario en la base de datos de sqlite que creó en la primera sección.

1.  Ejecute la aplicación con `F5`. Sondeos que se crean con **Crear sondeos de ejemplo** y los datos enviados por voto se serializarán en la base de datos SQL.


## <a name="publish-the-web-app-to-azure-app-service"></a>Publicar la aplicación web en la aplicación de servicio de Azure

Azure .NET SDK proporciona una forma sencilla de implementar la aplicación web de la web para las aplicaciones de Azure aplicación de servicio Web.

1.  En el **Explorador de soluciones**, haga clic en el nodo del proyecto y seleccione **Publicar**.

    ![Publicar el cuadro de diálogo Web](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)

1.  Haga clic en **aplicaciones Web de Microsoft Azure**.

1.  Haga clic en **nuevo** para crear una nueva aplicación web.

1.  Rellene los siguientes campos y haga clic en **crear**.
    -   **Nombre de la aplicación Web**
    -   **Plan de servicios de aplicación**
    -   **Grupo de recursos**
    -   **Región**
    -   Deje el **servidor de base de datos** establecido en **ninguna base de datos**

1.  Aceptar todos los demás valores predeterminados y haga clic en **Publicar**.

1.  El explorador web se abrirá automáticamente a la aplicación web publicado. Verá la aplicación web funcionan según lo esperado, con la base de datos **SQL** hospedado en Azure.

    ¡Felicidades!

    ![Explorador Web](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## <a name="next-steps"></a>Pasos siguientes

Siga estos vínculos para obtener más información sobre las herramientas de Python para Visual Studio, Django y base de datos de SQL.

- [Python documentación Tools para Visual Studio]
  - [Proyectos Web]
  - [Proyectos de servicio de nube]
  - [Depuración remota en Microsoft Azure]
- [Documentación de Django]
- [Base de datos SQL]

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)


<!--Link references-->
[Centro para desarrolladores de Python]: /develop/python/
[Servicios de nube de Azure]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[Portal de Azure]: https://portal.azure.com
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Python 2.2 las herramientas de Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python herramientas 2.2 de ejemplos de Visual Studio VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Herramientas del SDK Azure VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python documentación Tools para Visual Studio]: http://aka.ms/ptvsdocs
[Depuración remota en Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Proyectos Web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Proyectos de servicio de nube]: http://go.microsoft.com/fwlink/?LinkId=624028
[Documentación de Django]: https://www.djangoproject.com/
[Base de datos SQL]: /documentation/services/sql-database/
