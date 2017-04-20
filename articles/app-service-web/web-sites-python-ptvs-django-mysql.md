<properties 
    pageTitle="Django y MySQL en Azure con herramientas de Python 2.2 para Visual Studio" 
    description="Obtenga información sobre cómo usar las herramientas de Python para Visual Studio para crear una aplicación web Django que almacena los datos en una instancia de base de datos MySQL e implementar a Azure aplicación de servicio Web Apps." 
    services="app-service\web" 
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python"
    ms.topic="get-started-article" 
    ms.date="07/07/2016"
    ms.author="huvalo"/>

# <a name="django-and-mysql-on-azure-with-python-tools-22-for-visual-studio"></a>Django y MySQL en Azure con herramientas de Python 2.2 para Visual Studio 

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

En este tutorial, usará [Python Tools para Visual Studio] (PTVS) para crear una aplicación web de sondeos simple mediante una de las plantillas de ejemplo PTVS. Aprenderá cómo usar un servicio de MySQL alojado en Azure, cómo configurar la aplicación web para usar MySQL y cómo publicar la aplicación web en la [Aplicación de servicio Web Apps de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

> [AZURE.NOTE] La información contenida en este tutorial también está disponible en el siguiente vídeo:
> 
> [PTVS 2.1: Aplicación de Django con MySQL][video]

Consulte el [Centro para desarrolladores de Python] para ver más artículos que tratan el desarrollo de aplicaciones de Azure aplicación de servicio Web con PTVS con botella, matraz y Django marcos web, con los servicios de almacenamiento de tablas de Azure, MySQL y base de datos de SQL. Mientras en este artículo se centra en la aplicación de servicio, los pasos son similares al desarrollar [Servicios de nube de Azure].

## <a name="prerequisites"></a>Requisitos previos

 - Visual Studio de 2015
 - [Python 2.7 32 bits] o [Python 3.4 32 bits]
 - [Python 2.2 las herramientas de Visual Studio]
 - [Python herramientas 2.2 de ejemplos de Visual Studio VSIX]
 - [Herramientas del SDK Azure VS 2015]
 - Django 1,9 o posterior

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<!-- This note should not render as part of the the previous include. -->

> [AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. No se requiere ninguna tarjeta de crédito y compromisos no son necesarios.

## <a name="create-the-project"></a>Crear el proyecto

En esta sección, creará un proyecto de Visual Studio con una plantilla de ejemplo. Podrá crear un entorno virtual e instalar paquetes necesarios. Debe crear una base de datos local con sqlite. A continuación, deberá ejecutar la aplicación localmente.

1. En Visual Studio, seleccione **archivo**, **Nuevo proyecto**.

1. Las plantillas de proyecto de la [Python 2.2 de herramientas de Visual Studio ejemplos VSIX] están disponibles en **Python**, **ejemplos**. Seleccione **Sondeos Django Web Project** y haga clic en Aceptar para crear el proyecto.

    ![Cuadro de diálogo nuevo proyecto](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1. Se le pedirá que instalar paquetes externos. Seleccione **instalar en un entorno virtual**.

    ![Cuadro de diálogo paquetes externos](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1. Seleccione **Python 2.7** o **Python 3.4** como intérprete de base.

    ![Agregar el cuadro de diálogo de entorno Virtual](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1. En el **Explorador de soluciones**, haga clic en el nodo del proyecto y, a continuación, seleccione **Migrar Django** **Python**.  A continuación, seleccione **Django crear superusuario**.

1. Se abrirá una consola de administración de Django y crear una base de datos de sqlite en la carpeta del proyecto. Siga las indicaciones para crear un usuario.

1. Confirme que la aplicación funciona presionando `F5`.

1. En la barra de navegación en la parte superior, haga clic en **iniciar sesión** .

    ![Barra de navegación de Django](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1. Escriba las credenciales del usuario que creó al sincronizar la base de datos.

    ![Inicie sesión en el formulario](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1. Haga clic en **crear sondeos de ejemplo**.

    ![Crear sondeos de ejemplo](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1. Haga clic en un sondeo y votar.

    ![Votar en sondeos de ejemplo](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## <a name="create-a-mysql-database"></a>Crear una base de datos MySQL

La base de datos, debe crear una base de datos ClearDB MySQL hospedado en Azure.

Como alternativa, puede crear su propia máquina Virtual que se ejecuta en Azure, a continuación, instalar y administrar MySQL usted mismo.

Puede crear una base de datos con un plan libre siguiendo estos pasos.

1. Inicie sesión en el [Portal de Azure].

1. En la parte superior del panel de navegación, haga clic en **nuevo**, a continuación, haga clic en **datos + almacenamiento**y, a continuación, haga clic en **Base de datos MySQL**. 

1. Configurar la base de datos MySQL mediante la creación de un nuevo grupo de recursos y seleccione la ubicación apropiada para el mismo.

1. Una vez creada la base de datos MySQL, haga clic en **Propiedades** en el módulo de base de datos.

1. Use el botón Copiar para devolver el valor de **Cadena de conexión** en el Portapapeles.

## <a name="configure-the-project"></a>Configurar el proyecto

En esta sección, deberá configurar la aplicación web para usar la base de datos MySQL que acaba de crear. También deberá instalar paquetes de Python adicionales necesarios para usar las bases de datos MySQL con Django. A continuación, deberá iniciar la aplicación web localmente.

1. En Visual Studio, abra **settings.py**, desde la carpeta de *nombre de proyecto* . Pegar temporalmente la cadena de conexión en el editor. La cadena de conexión está en este formato:

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    Cambiar la base de datos predeterminada **motor** usar MySQL y establecer los valores de **nombre de** **usuario**, **contraseña** y **HOST** de la **cadena de conexión**.

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }


1. En el Explorador de soluciones, en **Entornos de Python**, haga clic en el entorno virtual y seleccione **Instalar el paquete de Python**.

1. Instalar el paquete `mysqlclient` con **puntos**.

    ![Cuadro de diálogo de paquete de instalación](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1. En el **Explorador de soluciones**, haga clic en el nodo del proyecto y, a continuación, seleccione **Migrar Django** **Python**.  A continuación, seleccione **Django crear superusuario**.

    Se creará las tablas de la base de datos MySQL que creó en la sección anterior. Siga las indicaciones para crear un usuario que no tiene que coincidir con el usuario en la base de datos de sqlite que creó en la primera sección de este artículo.

1. Ejecute la aplicación con `F5`. En la base de datos MySQL se serializarán sondeos que se crean con **Crear sondeos de ejemplo** y los datos enviados por voto.

## <a name="publish-the-web-app-to-azure-app-service"></a>Publicar la aplicación web en la aplicación de servicio de Azure

Azure .NET SDK proporciona una forma sencilla para implementar la aplicación web de servicio de aplicaciones de Azure.

1. En el **Explorador de soluciones**, haga clic en el nodo del proyecto y seleccione **Publicar**.

    ![Publicar el cuadro de diálogo Web](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1. Haga clic en **Servicio de aplicaciones de Microsoft Azure**.

1. Haga clic en **nuevo** para crear una nueva aplicación web.

1. Rellene los siguientes campos y haga clic en **crear**:
    - **Nombre de la aplicación Web**
    - **Plan de servicios de aplicación**
    - **Grupo de recursos**
    - **Región**
    - Deje el **servidor de base de datos** establecido en **ninguna base de datos**

1. Aceptar todos los demás valores predeterminados y haga clic en **Publicar**.

1. El explorador web se abrirá automáticamente a la aplicación web publicado. Verá la aplicación web funciona como se espera, usar la base de datos **MySQL** hospedado en Azure.

    ![Explorador Web](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

    ¡Felicidades! Ha publicado correctamente la aplicación web basada en MySQL en Azure.

## <a name="next-steps"></a>Pasos siguientes

Siga estos vínculos para obtener más información sobre las herramientas de Python para Visual Studio, Django y MySQL.

- [Python documentación Tools para Visual Studio]
  - [Proyectos Web]
  - [Proyectos de servicio de nube]
  - [Depuración remota en Microsoft Azure]
- [Documentación de Django]
- [MySQL]

Para obtener más información, consulte el [Centro para desarrolladores de Python](/develop/python/).

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
[Python 3.4 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python documentación Tools para Visual Studio]: http://aka.ms/ptvsdocs
[Depuración remota en Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Proyectos Web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Proyectos de servicio de nube]: http://go.microsoft.com/fwlink/?LinkId=624028
[Documentación de Django]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/
[video]: http://youtu.be/oKCApIrS0Lo
