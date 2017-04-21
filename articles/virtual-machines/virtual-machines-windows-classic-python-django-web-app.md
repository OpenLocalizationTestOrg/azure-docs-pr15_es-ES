<properties
    pageTitle="Python web app con Django | Microsoft Azure"
    description="Este tutorial le enseña a hospedar basado en Django de un sitio Web Azure utilizando una máquina virtual de centro de datos de Windows Server 2012 R2 utiliza el modelo de implementación clásico."
    services="virtual-machines-windows"
    documentationCenter="python"
    authors="huguesv"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>


<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="08/04/2015" 
    ms.author="huvalo"/>


# <a name="django-hello-world-web-application-on-a-windows-server-vm"></a>Aplicación web de Django Hola a todos en una máquina virtual de servidor de Windows

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac o Linux.](virtual-machines-linux-python-django-web-app.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Este tutorial describe cómo hospedar basado en Django de un sitio Web con una máquina virtual de Windows Server de Microsoft Azure. En este tutorial, se supone que no tiene experiencia previa con Azure. Después de completar este tutorial, tendrá una aplicación basada en Django hacia arriba y en la nube.

Obtendrá información sobre cómo:

* Configurar una máquina virtual Azure al host Django. Mientras que este tutorial explica cómo hacerlo en Windows Server, la misma también puede hacerse con una máquina virtual hospedada en Azure Linux.
* Crear una nueva aplicación Django de Windows.

Siguiendo este tutorial, va a crear una aplicación web de Hola a todos sencilla. La aplicación se alojarán en una máquina virtual Azure.

Captura de pantalla de la aplicación completada aparece siguiente.

![Una ventana del explorador que muestra la página del mundo Hola a todos en Azure][1]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Crear y configurar una máquina virtual Azure al host Django

1. Siga las instrucciones dada [aquí](virtual-machines-windows-classic-tutorial.md) para crear una máquina virtual Azure de la distribución de Windows Server 2012 R2 centro de datos.

1. Indicar a Azure para dirigir el tráfico de 80 puerto desde la web con el puerto 80 en la máquina virtual:
 - Vaya a su máquina virtual recién creado en el portal de clásico Azure y haga clic en los **EXTREMOS** .
 - Haga clic en el botón **Agregar** en la parte inferior de la pantalla.
    ![Agregar extremo](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-addendpoint.png)

 - Abierto el protocolo **TCP** del **público puertos 80** como **privado puertos 80**.
![][port80]
1. En la pestaña **panel** , haga clic en **Conectar** para usar **Escritorio remoto** para iniciar sesión remotamente en la máquina virtual de Azure recién creada.  

**Nota importante:** Las siguientes instrucciones se supone que ha iniciado sesión en la máquina virtual correctamente y emisión comandos allí en lugar de su equipo local.

## <a id="setup"> </a>Instalar Python, Django, WFastCGI

**Nota:** Para descargar con Internet Explorer, puede que tenga que establecer la configuración de ESC de Internet Explorer (servidor de inicio administrativas herramientas cliente-servidor Manager/Local, a continuación, haga clic en **Configuración de seguridad mejorada de Internet Explorer**, desactivada).

1. Instalar la última 2.7 Python o 3.4 desde [python.org][].
1. Instalar los paquetes de wfastcgi y django con puntos.

    Para Python 2.7, use el siguiente comando.

        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django

    Para Python 3.4, use el siguiente comando.

        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="installing-iis-with-fastcgi"></a>Instalar IIS con FastCGI

1. Instalar IIS con compatibilidad de FastCGI.  Esto puede tardar varios minutos en ejecutar.

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="creating-a-new-django-application"></a>Crear una nueva aplicación de Django

1.  Desde *C:\inetpub\wwwroot*, escriba el comando siguiente para crear un nuevo proyecto de Django:

    Para Python 2.7, use el siguiente comando.

        C:\Python27\Scripts\django-admin.exe startproject helloworld

    Para Python 3.4, use el siguiente comando.

        C:\Python34\Scripts\django-admin.exe startproject helloworld

    ![El resultado del comando nuevo AzureService](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-cmd-new-azure-service.png)

1.  El comando de **administración de django** genera una estructura básica de sitios Web basados en Django:

  -   **helloworld\manage.py** le ayuda a empezar el hospedaje y dejar su sitio Web de Django de hospedaje
  -   **helloworld\helloworld\settings.py** contiene la configuración de Django para la aplicación.
  -   **helloworld\helloworld\urls.py** contiene el código de asignación entre cada dirección url y su vista.

1.  Crear un nuevo archivo denominado **views.py** en el directorio *C:\inetpub\wwwroot\helloworld\helloworld* . Contendrá la vista que representa la página de "Hola a todos". Iniciar el editor y escriba lo siguiente:

        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  Reemplace el contenido del archivo urls.py con los siguientes elementos.

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )

## <a name="configuring-iis"></a>Configuración de IIS

1. Desbloquee la sección de controladores en el applicationhost.config global.  Esto le permitirá el uso del controlador de python en el archivo web.config.

        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers

1. Habilitar WFastCGI.  Esto agregará una aplicación a la applicationhost.config global que hace referencia a su intérprete de Python ejecutable y el script wfastcgi.py.

    Python 2.7:

        c:\python27\scripts\wfastcgi-enable

    Python 3.4:

        c:\python34\scripts\wfastcgi-enable

1. Crear un archivo web.config en *C:\inetpub\wwwroot\helloworld*.  El valor de la `scriptProcessor` atributo debe coincidir con el resultado del paso anterior.  Consulte la página de [wfastcgi][] en pypi más sobre configuración de wfastcgi.

    Python 2.7:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

    Python 3.4:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

1. Actualizar la ubicación del sitio Web predeterminado IIS para que apunten a la carpeta del proyecto django.

        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"

1. Por último, cargue la página web en el explorador.

![Una ventana del explorador que muestra la página del mundo Hola a todos en Azure][1]


## <a name="shutting-down-your-azure-virtual-machine"></a>Cerrar la máquina virtual Azure

Cuando haya terminado con este tutorial, cerrar o eliminar la máquina virtual Azure recién creado para liberar los recursos para otros tutoriales y evitar provocar cargos de uso de Azure.

[1]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
