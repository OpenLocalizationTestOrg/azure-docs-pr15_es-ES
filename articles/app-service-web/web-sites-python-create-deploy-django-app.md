<properties
    pageTitle="Crear aplicaciones web con Django en Azure"
    description="Tutorial que se presentan ejecuta una aplicación web de Python en Azure aplicación de servicio Web Apps."
    services="app-service\web"
    documentationCenter="python"
    tags="python"
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article" 
    ms.date="02/19/2016"
    ms.author="huvalo"/>


# <a name="creating-web-apps-with-django-in-azure"></a>Crear aplicaciones web con Django en Azure

Este tutorial describe cómo empezar a trabajar con Python [Azure aplicación de servicio Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Web aplicaciones proporciona hospedaje gratuita limitada e implementación rápida, y puede utilizar Python! A medida que crece la aplicación, puede cambiar la opción de pago de hospedaje, y también se puede integrar con todos los otros servicios de Azure.

Se creará una aplicación con el marco de trabajo de Django web (consulte versiones alternativas de este tutorial para [matraz](web-sites-python-create-deploy-flask-app.md) y [botella](web-sites-python-create-deploy-bottle-app.md)). Se crea la aplicación web de Azure Marketplace, configurar la implementación Git y clonar el repositorio localmente. A continuación, se ejecute la aplicación localmente, realizar cambios, confirmar e insertarlos en Azure. El tutorial muestra cómo hacerlo desde Windows o Mac o Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.


## <a name="prerequisites"></a>Requisitos previos

- Windows, Mac o Linux
- Python 2.7 o 3.4
- setuptools, puntos, virtualenv (solo Python 2.7)
- GIT
- [Python Tools para Visual Studio][] Tenga en cuenta (PTVS) -: opcional

**Nota**: la publicación TFS no es compatible actualmente para proyectos de Python.

### <a name="windows"></a>Windows

Si todavía no tiene Python 2.7 o 3.4 instalados (32 bits), se recomienda instalar [SDK de Azure para Python 2.7] o [SDK de Azure para Python 3.4] mediante el instalador de plataforma Web. Instala la versión de 32 bits de Python, setuptools, puntos, virtualenv, etcetera (32 bits Python es lo que está instalado en los equipos de host de Azure). Como alternativa, puede ir Python desde [python.org].

Para Git, se recomienda [Git para Windows] o [GitHub para Windows]. Si utiliza Visual Studio, puede utilizar la compatibilidad de Git integrada.

También se recomienda instalar [Python 2.2 de herramientas de Visual Studio]. Esto es opcional, pero si tiene [Visual Studio], incluido el gratuita de Visual Studio Comunidad 2013 o Visual Studio Express 2013 para Web, a continuación, le proporcionará un excelente IDE Python.

### <a name="maclinux"></a>Mac o Linux.

Debe tener Python y Git ya instalado, pero asegúrese de que tiene Python 2.7 o 3.4.


## <a name="web-app-creation-on-portal"></a>Creación de aplicaciones Web en el Portal

Es el primer paso para crear la aplicación crear la aplicación web a través del [Portal de Azure](https://portal.azure.com).

1. Inicie sesión en el Portal de Azure y haga clic en el botón de **nuevo** en la esquina inferior izquierda.
3. En el cuadro Buscar, escriba "python".
4. En los resultados de búsqueda, seleccione **Django** (publicado por PTVS), a continuación, haga clic en **crear**.
5. Configurar la nueva aplicación Django, como crear un nuevo plan de servicio de aplicaciones y un nuevo grupo de recursos para el mismo. A continuación, haga clic en **crear**.
6. Configurar la publicación de Git para la aplicación web recién creado siguiendo las instrucciones en la [Implementación Local de Git al servicio de la aplicación de Azure](app-service-deploy-local-git.md).

## <a name="application-overview"></a>Información general de la aplicación

### <a name="git-repository-contents"></a>Contenido del repositorio de GIT

Aquí es una descripción general de los archivos que encontrará en el repositorio de Git inicial, que se va a clonar en la siguiente sección.

    \app\__init__.py
    \app\forms.py
    \app\models.py
    \app\tests.py
    \app\views.py
    \app\static\content\
    \app\static\fonts\
    \app\static\scripts\
    \app\templates\about.html
    \app\templates\contact.html
    \app\templates\index.html
    \app\templates\layout.html
    \app\templates\login.html
    \app\templates\loginpartial.html
    \DjangoWebProject\__init__.py
    \DjangoWebProject\settings.py
    \DjangoWebProject\urls.py
    \DjangoWebProject\wsgi.py

Principales fuentes de la aplicación. Consta de 3 páginas (índice de contacto) con un diseño principal. Las secuencias de comandos y el contenido estático incluyen Inicio, jquery, modernizr y responder.

    \manage.py

Administración local y la compatibilidad con el servidor de desarrollo. Use esta opción para ejecutar la aplicación localmente, sincronizar la base de datos, etcetera.

    \db.sqlite3

Base de datos predeterminada. Incluye las tablas necesarias para la aplicación se ejecute, pero no contiene todos los usuarios (sincronizar la base de datos para crear un usuario).

    \DjangoWebProject.pyproj
    \DjangoWebProject.sln

Archivos de proyecto para su uso con [Python Tools para Visual Studio].

    \ptvs_virtualenv_proxy.py

Proxy IIS para entornos virtuales y PTVS depuración asistencia remota.

    \requirements.txt

Paquetes externos necesarios para esta aplicación. El script de implementación se pip instalar los paquetes incluidos en este archivo.

    \web.2.7.config
    \web.3.4.config

Archivos de configuración de IIS. El script de implementación utilizará la web.x.y.config adecuada y copiar como web.config.

### <a name="optional-files---customizing-deployment"></a>Archivos opcionales - implementación de personalización

[AZURE.INCLUDE [web-sites-python-django-customizing-deployment](../../includes/web-sites-python-django-customizing-deployment.md)]

### <a name="optional-files---python-runtime"></a>Archivos opcionales - tiempo de ejecución de Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### <a name="additional-files-on-server"></a>Archivos adicionales en el servidor

Algunos archivos existen en el servidor, pero no se agregan al repositorio git. Estos son creados por el script de implementación.

    \web.config

Archivo de configuración de IIS. Creado a partir de web.x.y.config en cada implementación.

    \env\

Entorno virtual de Python. Se crea durante la implementación si todavía no existe un entorno virtual compatible en la aplicación web. Paquetes enumerados en requirements.txt son puntos instalado, pero puntos omitirá instalación si ya están instalados los paquetes.

Las 3 secciones describen cómo proceder con el desarrollo de la aplicación web en diferentes entornos de 3:

- Windows, con Python Tools para Visual Studio
- Windows, con la línea de comandos
- Mac o Linux, con la línea de comandos


## <a name="web-app-development---windows---python-tools-for-visual-studio"></a>Desarrollo de aplicaciones Web - Windows - Python Tools para Visual Studio

### <a name="clone-the-repository"></a>Clonar al repositorio

En primer lugar, clonar el repositorio con la URL que se proporcionan en el Portal de Azure. Para obtener más información, vea [Implementación Local de Git al servicio de la aplicación de Azure](app-service-deploy-local-git.md).

Abra el archivo de solución (sln) que se incluye en la raíz del repositorio.

![](./media/web-sites-python-create-deploy-django-app/ptvs-solution-django.png)

### <a name="create-virtual-environment"></a>Crear un entorno virtual

Ahora crearemos un entorno virtual para el desarrollo local. Haga clic en **Entornos de Python** seleccione **Agregar entorno Virtual...**.

- Asegúrese de que es el nombre del entorno de `env`.

- Seleccione el intérprete base. Asegúrese de usar la misma versión de Python que se ha seleccionado para la aplicación web (en runtime.txt o el módulo de **Configuración de la aplicación** de la aplicación web en el Portal de Azure).

- Asegúrese de que está activada la opción para descargar e instalar paquetes.

![](./media/web-sites-python-create-deploy-django-app/ptvs-add-virtual-env-27.png)

Haga clic en **crear**. Esto creará el entorno virtual e instalar dependencias enumeradas en requirements.txt.

### <a name="create-a-superuser"></a>Crear un superusuario

La base de datos que se incluye con la aplicación no tiene superusuario definido. Para poder usar la funcionalidad de inicio de sesión en la aplicación o la interfaz de administración de Django (si decide habilitarlo), debe crear un superusuario.

Ejecutar esto desde la línea de comandos de la carpeta de proyecto:

    env\scripts\python manage.py createsuperuser

Siga las indicaciones para configurar el nombre de usuario, contraseña, etcetera.

### <a name="run-using-development-server"></a>Ejecutar con el servidor de desarrollo

Presione F5 para iniciar la depuración y el explorador web se abrirá automáticamente a la página que se ejecuta localmente.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

Puede establecer puntos de interrupción en las fuentes, utilice la ventanas Inspección etcetera. Vea [Python documentación Tools para Visual Studio] para obtener más información sobre las distintas características.

### <a name="make-changes"></a>Realizar cambios

Ahora puede experimentar al realizar cambios en los orígenes de la aplicación o plantillas.

Una vez que ha probado los cambios, confirmar al repositorio Git:

![](./media/web-sites-python-create-deploy-django-app/ptvs-commit-django.png)

### <a name="install-more-packages"></a>Instalar paquetes más

La aplicación puede tener dependencias más allá de Python y Django.

Puede instalar paquetes adicionales con puntos. Para instalar un paquete, haga clic en el entorno virtual y seleccione **Instalar el paquete de Python**.

Por ejemplo, para instalar el SDK de Azure para Python, que le proporciona acceso a almacenamiento de Azure, bus de servicio y otros servicios de Azure, escriba `azure`:

![](./media/web-sites-python-create-deploy-django-app/ptvs-install-package-dialog.png)

Haga clic en el entorno virtual y seleccione **Generar requirements.txt** actualizar requirements.txt.

A continuación, confirme los cambios a requirements.txt al repositorio Git.

### <a name="deploy-to-azure"></a>Implementar en Azure

Para desencadenar una implementación, haga clic en **sincronizar** o **de inserción**. Sincronización realiza una inserción y una extracción.

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-push.png)

La primera implementación llevará algún tiempo, ya que crea un entorno virtual, instalar paquetes, etcetera.

Visual Studio no muestra el progreso de la implementación. Si le gustaría revisar los resultados, consulte la sección sobre [solución de problemas - implementación](#troubleshooting-deployment).

Vaya a la dirección URL de Azure para ver los cambios.


## <a name="web-app-development---windows---command-line"></a>Línea de comandos de Web app desarrollo - Windows:

### <a name="clone-the-repository"></a>Clonar al repositorio

En primer lugar, clonar el repositorio con la URL que se proporcionan en el Portal de Azure y agregue el repositorio de Azure como un equipo remoto. Para obtener más información, vea [Implementación Local de Git al servicio de la aplicación de Azure](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url>

### <a name="create-virtual-environment"></a>Crear un entorno virtual

Se creará un nuevo entorno virtual para fines de desarrollo (no lo agregue al repositorio). Entornos virtuales en Python no son reubicables, por lo que todos los desarrolladores trabajando en la aplicación creará sus propios localmente.

Asegúrese de usar la misma versión de Python que se ha seleccionado para la aplicación web (en runtime.txt o el módulo de configuración de la aplicación de la aplicación web en el Portal de Azure).

Para Python 2.7:

    c:\python27\python.exe -m virtualenv env

Para Python 3.4:

    c:\python34\python.exe -m venv env

Instalar los paquetes externos necesarios para la aplicación. Puede usar el archivo requirements.txt en la raíz del repositorio para instalar los paquetes en su entorno virtual:

    env\scripts\pip install -r requirements.txt

### <a name="create-a-superuser"></a>Crear un superusuario

La base de datos que se incluye con la aplicación no tiene superusuario definido. Para poder usar la funcionalidad de inicio de sesión en la aplicación o la interfaz de administración de Django (si decide habilitarlo), debe crear un superusuario.

Ejecutar esto desde la línea de comandos de la carpeta de proyecto:

    env\scripts\python manage.py createsuperuser

Siga las indicaciones para configurar el nombre de usuario, contraseña, etcetera.

### <a name="run-using-development-server"></a>Ejecutar con el servidor de desarrollo

Puede iniciar la aplicación en un servidor de desarrollo con el siguiente comando:

    env\scripts\python manage.py runserver

La consola mostrará la dirección URL y escucha puerto del servidor:

![](./media/web-sites-python-create-deploy-django-app/windows-run-local-django.png)

A continuación, abra el explorador web a dicha dirección URL.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

### <a name="make-changes"></a>Realizar cambios

Ahora puede experimentar al realizar cambios en los orígenes de la aplicación o plantillas.

Una vez que ha probado los cambios, confirmar al repositorio Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Instalar paquetes más

La aplicación puede tener dependencias más allá de Python y Django.

Puede instalar paquetes adicionales con puntos. Por ejemplo, para instalar el SDK de Azure para Python, que le proporciona acceso a almacenamiento de Azure, bus de servicio y otros servicios de Azure, escriba:

    env\scripts\pip install azure

Asegúrese de actualizar requirements.txt:

    env\scripts\pip freeze > requirements.txt

Confirme los cambios:

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Implementar en Azure

Para desencadenar una implementación, aplicar los cambios a Azure:

    git push azure master

Verá el resultado de la secuencia de comandos de implementación, incluida la creación de un entorno virtual, instalación de paquetes, la creación de web.config.

Vaya a la dirección URL de Azure para ver los cambios.


## <a name="web-app-development---maclinux---command-line"></a>Línea de comandos de desarrollo - Mac o Linux - de aplicación Web

### <a name="clone-the-repository"></a>Clonar al repositorio

En primer lugar, clonar el repositorio con la URL que se proporcionan en el Portal de Azure y agregue el repositorio de Azure como un equipo remoto. Para obtener más información, vea [Implementación Local de Git al servicio de la aplicación de Azure](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url>

### <a name="create-virtual-environment"></a>Crear un entorno virtual

Se creará un nuevo entorno virtual para fines de desarrollo (no lo agregue al repositorio). Entornos virtuales en Python no son reubicables, por lo que todos los desarrolladores trabajando en la aplicación creará sus propios localmente.

Asegúrese de usar la misma versión de Python que se ha seleccionado para la aplicación web (en runtime.txt o el módulo de configuración de la aplicación de la aplicación web en el Portal de Azure).

Para Python 2.7:

    python -m virtualenv env

Para Python 3.4:

    python -m venv env

o

    pyvenv env

Instalar los paquetes externos necesarios para la aplicación. Puede usar el archivo requirements.txt en la raíz del repositorio para instalar los paquetes en su entorno virtual:

    env/bin/pip install -r requirements.txt

### <a name="create-a-superuser"></a>Crear un superusuario

La base de datos que se incluye con la aplicación no tiene superusuario definido. Para poder usar la funcionalidad de inicio de sesión en la aplicación o la interfaz de administración de Django (si decide habilitarlo), debe crear un superusuario.

Ejecutar esto desde la línea de comandos de la carpeta de proyecto:

    env/bin/python manage.py createsuperuser

Siga las indicaciones para configurar el nombre de usuario, contraseña, etcetera.

### <a name="run-using-development-server"></a>Ejecutar con el servidor de desarrollo

Puede iniciar la aplicación en un servidor de desarrollo con el siguiente comando:

    env/bin/python manage.py runserver

La consola mostrará la dirección URL y escucha puerto del servidor:

![](./media/web-sites-python-create-deploy-django-app/mac-run-local-django.png)

A continuación, abra el explorador web a dicha dirección URL.

![](./media/web-sites-python-create-deploy-django-app/mac-browser-django.png)

### <a name="make-changes"></a>Realizar cambios

Ahora puede experimentar al realizar cambios en los orígenes de la aplicación o plantillas.

Una vez que ha probado los cambios, confirmar al repositorio Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Instalar paquetes más

La aplicación puede tener dependencias más allá de Python y Django.

Puede instalar paquetes adicionales con puntos. Por ejemplo, para instalar el SDK de Azure para Python, que le proporciona acceso a almacenamiento de Azure, bus de servicio y otros servicios de Azure, escriba:

    env/bin/pip install azure

Asegúrese de actualizar requirements.txt:

    env/bin/pip freeze > requirements.txt

Confirme los cambios:

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Implementar en Azure

Para desencadenar una implementación, aplicar los cambios a Azure:

    git push azure master

Verá el resultado de la secuencia de comandos de implementación, incluida la creación de un entorno virtual, instalación de paquetes, la creación de web.config.

Vaya a la dirección URL de Azure para ver los cambios.


## <a name="troubleshooting---package-installation"></a>Solución de problemas - paquete de instalación

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## <a name="troubleshooting---virtual-environment"></a>Solución de problemas - entorno Virtual

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## <a name="troubleshooting---static-files"></a>Solución de problemas - archivos estáticos

Django tiene el concepto de recopilación de archivos estáticos. Se abrirá todos estático archivos desde su ubicación original y copia en una sola carpeta. Para esta aplicación, se copian a `/static`.

Esto ocurre porque los archivos estáticos pueden provenir de diferentes Django 'aplicaciones'. Por ejemplo, los archivos estáticos de las interfaces de administración de Django se encuentran en una subcarpeta de la biblioteca de Django en el entorno virtual. Archivos estáticos definidos por esta aplicación se encuentran en `/app/static`. Si usa más Django 'aplicaciones', deberá estáticos archivos ubicados en varios lugares.

Cuando ejecute la aplicación en modo de depuración, la aplicación sirve archivos estáticos desde su ubicación original.

Cuando ejecute la aplicación en modo de lanzamiento, hace la aplicación **no** servir archivos estáticos. Es responsabilidad del servidor web para servir los archivos. Para esta aplicación, IIS servirá archivos estáticos desde `/static`.

La colección de archivos estáticos se realiza automáticamente como parte del script de implementación, borrar previamente recopilado archivos. Esto significa que la colección se produce en cada implementación, ralentizar implementación un poco, pero garantiza que los archivos obsoletos no estarán disponibles, evitar un posible problema de seguridad.

Si desea omitir la colección de archivos estáticos de la aplicación de Django:

    \.skipDjango

A continuación, deberá hacer de la colección de forma manual en el equipo local:

    env\scripts\python manage.py collectstatic

A continuación, quite el `\static` carpeta de `.gitignore` y agregar al repositorio Git.


## <a name="troubleshooting---settings"></a>Solución de problemas - configuración

Varias configuraciones para la aplicación se pueden cambiar en `DjangoWebProject/settings.py`.

Para mayor comodidad del desarrollador, el modo de depuración está habilitado. Un buen efecto secundario es que podrá ver imágenes y otro contenido estático cuando se ejecuta localmente, sin tener que recopilar archivos estáticos.

Para deshabilitar el modo de depuración:

    DEBUG = False

Cuando se deshabilita la depuración, el valor de `ALLOWED_HOSTS` debe actualizarse para incluir el nombre de host de Azure. Por ejemplo:

    ALLOWED_HOSTS = (
        'pythonapp.azurewebsites.net',
    )

o habilitar cualquiera:

    ALLOWED_HOSTS = (
        '*',
    )

En la práctica, desea hacer algo más compleja para atender cambiar entre depuración y suelte modo y obtener el nombre de host.

Puede establecer las variables de entorno a través del portal de Azure **Configurar** página, en la sección **configuración de la aplicación** .  Esto puede ser útil para configurar los valores que no desee que aparezcan en los orígenes (cadenas de conexión, contraseñas, etcetera) o que desea configurar de forma diferente entre Azure y el equipo local. En `settings.py`, puede crear una consulta con las variables de entorno `os.getenv`.


## <a name="using-a-database"></a>Utilizar una base de datos

La base de datos que se incluye con la aplicación es una base de datos de sqlite. Se trata de una base de datos predeterminado de forma conveniente y útil para el desarrollo, ya que no requiere casi ninguna configuración. La base de datos se almacena en el archivo db.sqlite3 en la carpeta del proyecto.

Azure proporciona servicios de base de datos que son fáciles de usar desde una aplicación de Django. Tutoriales para usar la [Base de datos SQL] y [MySQL] desde una aplicación de Django mostrarán los pasos necesarios para crear el servicio de base de datos, cambie la configuración de la base de datos en `DjangoWebProject/settings.py`y las bibliotecas necesarias para instalar.

Por supuesto, si lo prefiere administrar sus propios servidores de base de datos, puede hacerlo mediante Windows o Linux máquinas virtuales ejecuta en Azure.


## <a name="django-admin-interface"></a>Interfaz de administración de Django

Una vez que empiece a crear los modelos, desea rellenar con algunos datos de la base de datos. Una forma sencilla de agregar y editar contenido de forma interactiva es usar la interfaz de administración de Django.

El código de la interfaz de administrador se explica en las fuentes de la aplicación, pero se marcará claramente por lo que puede habilitarlo fácilmente (Buscar 'admin').

Una vez habilitada, sincronizar la base de datos, ejecute la aplicación y vaya a `/admin`.


## <a name="next-steps"></a>Pasos siguientes

Siga estos vínculos para obtener más información sobre herramientas de Python y Django para Visual Studio:

- [Documentación de Django]
- [Python documentación Tools para Visual Studio]

Para obtener información sobre el uso de la base de datos SQL y MySQL:

- [Django y MySQL en Azure con Python Tools para Visual Studio]
- [Django y base de datos SQL Azure con Python Tools para Visual Studio]

Para obtener más información, consulte el [Centro para desarrolladores de Python](/develop/python/).


## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)


<!--Link references-->
[Django y MySQL en Azure con Python Tools para Visual Studio]: web-sites-python-ptvs-django-mysql.md
[Django y base de datos SQL Azure con Python Tools para Visual Studio]: web-sites-python-ptvs-django-sql.md
[Base de datos SQL]: web-sites-python-ptvs-django-sql.md
[MySQL]: web-sites-python-ptvs-django-mysql.md

<!--External Link references-->
[Azure SDK para Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK para Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[Python.org]: http://www.python.org/
[GIT para Windows]: http://msysgit.github.io/
[GitHub para Windows]: https://windows.github.com/
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Python 2.2 las herramientas de Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Visual Studio]: http://www.visualstudio.com/
[Python documentación Tools para Visual Studio]: http://aka.ms/ptvsdocs
[Documentación de Django]: https://www.djangoproject.com/
