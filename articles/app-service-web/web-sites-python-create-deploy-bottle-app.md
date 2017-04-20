<properties 
    pageTitle="Python web apps con botella de Azure" 
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
    ms.topic="article" 
    ms.date="02/19/2016"
    ms.author="huvalo"/>


# <a name="creating-web-apps-with-bottle-in-azure"></a>Crear aplicaciones web con botella de Azure

Este tutorial describe cómo empezar a trabajar ejecuta Python en Azure aplicación de servicio Web Apps. Web aplicaciones proporciona hospedaje gratuita limitada e implementación rápida, y puede utilizar Python! A medida que crece la aplicación, puede cambiar la opción de pago de hospedaje, y también se puede integrar con todos los otros servicios de Azure.

Va a crear una aplicación web mediante el marco de web botella (consulte versiones alternativas de este tutorial para [Django](web-sites-python-create-deploy-django-app.md) y [matraz](web-sites-python-create-deploy-flask-app.md)). Se crea la aplicación web de Azure Marketplace, configurar la implementación Git y clonar el repositorio localmente. A continuación, se ejecute la aplicación web localmente, realizar cambios, confirmar e insertarlos a [Azure aplicación de servicio Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). El tutorial muestra cómo hacerlo desde Windows o Mac o Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="prerequisites"></a>Requisitos previos

- Windows, Mac o Linux
- Python 2.7 o 3.4
- setuptools, puntos, virtualenv (solo Python 2.7)
- GIT
- [Herramientas de Python 2.2 para Visual Studio][] Tenga en cuenta (PTVS) -: opcional

**Nota**: la publicación TFS no es compatible actualmente para proyectos de Python.

### <a name="windows"></a>Windows

Si todavía no tiene Python 2.7 o 3.4 instalados (32 bits), se recomienda instalar [SDK de Azure para Python 2.7] o [SDK de Azure para Python 3.4] mediante el instalador de plataforma Web. Instala la versión de 32 bits de Python, setuptools, puntos, virtualenv, etcetera (32 bits Python es lo que está instalado en los equipos de host de Azure). Como alternativa, puede ir Python desde [python.org].

Para Git, se recomienda [Git para Windows] o [GitHub para Windows]. Si utiliza Visual Studio, puede utilizar la compatibilidad de Git integrada.

También se recomienda instalar [Python 2.2 de herramientas de Visual Studio]. Esto es opcional, pero si tiene [Visual Studio], incluido el gratuita de Visual Studio Comunidad 2013 o Visual Studio Express 2013 para Web, a continuación, le proporcionará un excelente IDE Python.

### <a name="maclinux"></a>Mac o Linux.

Debe tener Python y Git ya instalado, pero asegúrese de que tiene Python 2.7 o 3.4.


## <a name="web-app-creation-on-the-azure-portal"></a>Creación de aplicaciones Web en el Portal de Azure

Es el primer paso para crear la aplicación crear la aplicación web a través del [Portal de Azure](https://portal.azure.com).  

1. Inicie sesión en el Portal de Azure y haga clic en el botón de **nuevo** en la esquina inferior izquierda. 
3. En el cuadro Buscar, escriba "python".
4. En los resultados de búsqueda, seleccione **botella**, a continuación, haga clic en **crear**.
5. Configurar la nueva aplicación botella, como la creación de un nuevo plan de servicio de aplicaciones y un nuevo grupo de recursos para el mismo. A continuación, haga clic en **crear**.
6. Configurar la publicación de Git para la aplicación web recién creado siguiendo las instrucciones en la [Implementación Local de Git al servicio de la aplicación de Azure](app-service-deploy-local-git.md).
 
## <a name="application-overview"></a>Información general de la aplicación

### <a name="git-repository-contents"></a>Contenido del repositorio de GIT

Aquí es una descripción general de los archivos que encontrará en el repositorio de Git inicial, que se va a clonar en la siguiente sección.

    \routes.py
    \static\content\
    \static\fonts\
    \static\scripts\
    \views\about.tpl
    \views\contact.tpl
    \views\index.tpl
    \views\layout.tpl

Principales fuentes de la aplicación. Consta de 3 páginas (índice de contacto) con un diseño principal.  Las secuencias de comandos y el contenido estático incluyen Inicio, jquery, modernizr y responder.

    \app.py

Compatibilidad de servidor de desarrollo local. Use esta opción para ejecutar la aplicación localmente.

    \BottleWebProject.pyproj
    \BottleWebProject.sln

Archivos de proyecto para su uso con [Python Tools para Visual Studio].

    \ptvs_virtualenv_proxy.py

Proxy IIS para entornos virtuales y PTVS depuración asistencia remota.

    \requirements.txt

Paquetes externos necesarios para esta aplicación. El script de implementación se pip instalar los paquetes incluidos en este archivo.
 
    \web.2.7.config
    \web.3.4.config

Archivos de configuración de IIS. El script de implementación utilizará la web.x.y.config adecuada y copiar como web.config.

### <a name="optional-files---customizing-deployment"></a>Archivos opcionales - implementación de personalización

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../../includes/web-sites-python-customizing-deployment.md)]

### <a name="optional-files---python-runtime"></a>Archivos opcionales - tiempo de ejecución de Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### <a name="additional-files-on-server"></a>Archivos adicionales en el servidor

Algunos archivos existen en el servidor, pero no se agregan al repositorio git. Estos son creados por el script de implementación.

    \web.config

Archivo de configuración de IIS. Creado a partir de web.x.y.config en cada implementación.

    \env\

Entorno virtual de Python. Se crea durante la implementación si todavía no existe un entorno virtual compatible en la aplicación web.  Paquetes enumerados en requirements.txt son puntos instalado, pero puntos omitirá instalación si ya están instalados los paquetes.

Las 3 secciones describen cómo proceder con el desarrollo de la aplicación web en diferentes entornos de 3:

- Windows, con Python Tools para Visual Studio
- Windows, con la línea de comandos
- Mac o Linux, con la línea de comandos


## <a name="web-app-development---windows---python-tools-for-visual-studio"></a>Web App desarrollo - Windows - Python Tools para Visual Studio

### <a name="clone-the-repository"></a>Clonar al repositorio

En primer lugar, clonar el repositorio con la url que se proporcionan en el Portal de Azure. Para obtener más información, vea [Implementación Local de Git al servicio de la aplicación de Azure](app-service-deploy-local-git.md).

Abra el archivo de solución (sln) que se incluye en la raíz del repositorio.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-solution-bottle.png)

### <a name="create-virtual-environment"></a>Crear un entorno virtual

Ahora crearemos un entorno virtual para el desarrollo local. Haga clic en **Entornos de Python** seleccione **Agregar entorno Virtual...**.

- Asegúrese de que es el nombre del entorno de `env`.

- Seleccione el intérprete base. Asegúrese de usar la misma versión de Python que se ha seleccionado para la aplicación web (en runtime.txt o el módulo de **Configuración de la aplicación** de la aplicación web en el Portal de Azure).

- Asegúrese de que está activada la opción para descargar e instalar paquetes.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-add-virtual-env-27.png)

Haga clic en **crear**. Esto creará el entorno virtual e instalar dependencias enumeradas en requirements.txt.

### <a name="run-using-development-server"></a>Ejecutar con el servidor de desarrollo

Presione F5 para iniciar la depuración y el explorador web se abrirá automáticamente a la página que se ejecuta localmente.

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

Puede establecer puntos de interrupción en las fuentes, utilice la ventanas Inspección etcetera. Vea [Python documentación Tools para Visual Studio] para obtener más información sobre las distintas características.

### <a name="make-changes"></a>Realizar cambios

Ahora puede experimentar al realizar cambios en los orígenes de la aplicación o plantillas.

Una vez que ha probado los cambios, confirmar al repositorio Git:

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-commit-bottle.png)

### <a name="install-more-packages"></a>Instalar paquetes más

La aplicación puede tener dependencias más allá de Python y botella.

Puede instalar paquetes adicionales con puntos. Para instalar un paquete, haga clic en el entorno virtual y seleccione **Instalar el paquete de Python**.

Por ejemplo, para instalar el SDK de Azure para Python, que le proporciona acceso a almacenamiento de Azure, bus de servicio y otros servicios de Azure, escriba `azure`:

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-install-package-dialog.png)

Haga clic en el entorno virtual y seleccione **Generar requirements.txt** actualizar requirements.txt.

A continuación, confirme los cambios a requirements.txt al repositorio Git.

### <a name="deploy-to-azure"></a>Implementar en Azure

Para desencadenar una implementación, haga clic en **sincronizar** o **de inserción**. Sincronización realiza una inserción y una extracción.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-git-push.png)

La primera implementación llevará algún tiempo, ya que crea un entorno virtual, instalar paquetes, etcetera.

Visual Studio no muestra el progreso de la implementación. Si le gustaría revisar los resultados, consulte la sección sobre [solución de problemas - implementación](#troubleshooting-deployment).

Vaya a la dirección URL de Azure para ver los cambios.


## <a name="web-app-development---windows---command-line"></a>La línea de comandos de - Windows: desarrollo de aplicaciones Web

### <a name="clone-the-repository"></a>Clonar al repositorio

En primer lugar, clonar el repositorio con la URL que se proporcionan en el Portal de Azure y agregue el repositorio de Azure como un equipo remoto. Para obtener más información, vea [Implementación Local de Git al servicio de la aplicación de Azure](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### <a name="create-virtual-environment"></a>Crear un entorno virtual

Se creará un nuevo entorno virtual para fines de desarrollo (no lo agregue al repositorio). Entornos virtuales en Python no son reubicables, por lo que todos los desarrolladores trabajando en la aplicación creará sus propios localmente.

Asegúrese de usar la misma versión de Python que se ha seleccionado para la aplicación web (en runtime.txt o el módulo de configuración de la aplicación para la aplicación web en el Portal de Azure)

Para Python 2.7:

    c:\python27\python.exe -m virtualenv env

Para Python 3.4:

    c:\python34\python.exe -m venv env

Instalar los paquetes externos necesarios para la aplicación. Puede usar el archivo requirements.txt en la raíz del repositorio para instalar los paquetes en su entorno virtual:

    env\scripts\pip install -r requirements.txt

### <a name="run-using-development-server"></a>Ejecutar con el servidor de desarrollo

Puede iniciar la aplicación en un servidor de desarrollo con el siguiente comando:

    env\scripts\python app.py

La consola mostrará la dirección URL y escucha puerto del servidor:

![](./media/web-sites-python-create-deploy-bottle-app/windows-run-local-bottle.png)

A continuación, abra el explorador web a dicha dirección URL.

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

### <a name="make-changes"></a>Realizar cambios

Ahora puede experimentar al realizar cambios en los orígenes de la aplicación o plantillas.

Una vez que ha probado los cambios, confirmar al repositorio Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Instalar paquetes más

La aplicación puede tener dependencias más allá de Python y botella.

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
o ent pyvenv

Instalar los paquetes externos necesarios para la aplicación. Puede usar el archivo requirements.txt en la raíz del repositorio para instalar los paquetes en su entorno virtual:

    env/bin/pip install -r requirements.txt

### <a name="run-using-development-server"></a>Ejecutar con el servidor de desarrollo

Puede iniciar la aplicación en un servidor de desarrollo con el siguiente comando:

    env/bin/python app.py

La consola mostrará la dirección URL y escucha puerto del servidor:

![](./media/web-sites-python-create-deploy-bottle-app/mac-run-local-bottle.png)

A continuación, abra el explorador web a dicha dirección URL.

![](./media/web-sites-python-create-deploy-bottle-app/mac-browser-bottle.png)

### <a name="make-changes"></a>Realizar cambios

Ahora puede experimentar al realizar cambios en los orígenes de la aplicación o plantillas.

Una vez que ha probado los cambios, confirmar al repositorio Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Instalar paquetes más

La aplicación puede tener dependencias más allá de Python y botella.

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


## <a name="next-steps"></a>Pasos siguientes

Siga estos vínculos para obtener más información sobre herramientas de Python y botella para Visual Studio: 
 
- [Documentación de botella]
- [Python documentación Tools para Visual Studio]

Para obtener información sobre el uso de almacenamiento de tablas de Azure y MongoDB:

- [Botella y MongoDB en Azure con Python Tools para Visual Studio]
- [Botella y el almacenamiento de tablas de Azure en Azure con Python Tools para Visual Studio]

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)


<!--Link references-->
[Botella y MongoDB en Azure con Python Tools para Visual Studio]: web-sites-python-ptvs-bottle-table-storage.md
[Botella y el almacenamiento de tablas de Azure en Azure con Python Tools para Visual Studio]: web-sites-python-ptvs-bottle-table-storage.md

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
[Documentación de botella]: http://bottlepy.org/docs/dev/index.html
 
