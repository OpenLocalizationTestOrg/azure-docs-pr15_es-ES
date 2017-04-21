<properties 
    pageTitle="Python web app con Django en Linux | Microsoft Azure" 
    description="Aprenda a hospedar una aplicación web basada en Django en Azure con un equipo virtual Linux." 
    services="virtual-machines-linux" 
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="11/17/2015" 
    ms.author="huvalo"/>
    
# <a name="django-hello-world-web-application-on-a-linux-vm"></a>Aplicación web de Django Hola a todos en una VM Linux

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac o Linux.](virtual-machines-linux-python-django-web-app.md)

<br>

Este tutorial describe cómo hospedar basado en Django de un sitio Web con un equipo virtual Linux de Microsoft Azure. En este tutorial, se supone que no tiene experiencia previa con Azure. Tras completar a esta guía, tendrá una aplicación basada en Django hacia arriba y en la nube.

Obtendrá información sobre cómo:

* Configurar una máquina virtual Azure al host Django. Mientras que este tutorial explica cómo hacerlo en **Linux**, el mismo también puede hacerse con una máquina virtual de servidor de Windows alojado en Azure. 
* Crear una nueva aplicación Django de Linux.

Siguiendo este tutorial, va a crear una aplicación web de Hola a todos sencilla. La aplicación se alojarán en una máquina virtual Azure.

Captura de pantalla de la aplicación completada está por debajo de:

![Una ventana del explorador que muestra la página del mundo Hola a todos en Azure](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Crear y configurar una máquina virtual Azure al host Django

1. Siga las instrucciones dada [aquí](virtual-machines-linux-quick-create-portal.md) para crear una máquina virtual Azure de la distribución de *Ubuntu Server 14.04 LTADOS* .  Si lo prefiere, puede elegir la autenticación de contraseña en lugar de la clave pública de SSH.

1. Editar el grupo de seguridad de la red para permitir el tráfico de http entrante al puerto 80 siguiendo las instrucciones [aquí](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).

1. De forma predeterminada, la nueva máquina virtual no tiene un nombre de dominio completo (FQDN).  Puede crear una siguiendo las instrucciones [aquí](virtual-machines-linux-portal-create-fqdn.md).  Este paso es opcional para completar este tutorial.

## <a id="setup"> </a>Configurar el entorno de desarrollo

**Nota:** Si necesita instalar Python o le gustaría usar las bibliotecas de cliente, vea la [Guía de instalación de Python](../python-how-to-install.md).

La máquina virtual Linux de Ubuntu ya incluye 2.7 Python previamente instalado, pero no dispone de Apache o django instalado.  Siga estos pasos para conectarse a su máquina virtual e instalar Apache y django.

1.  Iniciar una nueva ventana de **Terminal** .
    
1.  Escriba el comando siguiente para conectarse a la máquina virtual de Azure.  Si no ha creado un nombre de dominio completo, puede conectarse usando la dirección IP pública que se muestra en la máquina virtual de resumen en el portal de clásico de Azure.

        $ ssh yourusername@yourVmUrl

1.  Escriba los comandos siguientes para instalar django:

        $ sudo apt-get install python-setuptools python-pip
        $ sudo pip install django

1.  Escriba el comando siguiente para instalar a Apache con wsgi mod:

        $ sudo apt-get install apache2 libapache2-mod-wsgi


## <a name="creating-a-new-django-application"></a>Crear una nueva aplicación de Django

1.  Abra la ventana de **Terminal** que utilizados en la sección anterior para ssh en su máquina virtual.
    
1.  Escriba los siguientes comandos para crear un nuevo proyecto de Django:

        $ cd /var/www
        $ sudo django-admin.py startproject helloworld

    La secuencia de comandos de **django admin.py** genera una estructura básica de sitios Web basados en Django:
    -   **HelloWorld/Manage.py** le ayuda a empezar el hospedaje y dejar su sitio Web de Django de hospedaje
    -   **HelloWorld/HelloWorld/Settings.py** contiene la configuración de Django para la aplicación.
    -   **HelloWorld/HelloWorld/URLs.py** contiene el código de asignación entre cada dirección url y su vista.

1.  Crear un nuevo archivo denominado **views.py** en el directorio **/var/www/helloworld/helloworld** . Contendrá la vista que representa la página de "Hola a todos". Iniciar el editor y escriba lo siguiente:
        
        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  Ahora, reemplace el contenido del archivo **urls.py** con los siguientes elementos:

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )


## <a name="setting-up-apache"></a>Configuración de Apache

1.  Crear un archivo de configuración de host virtual Apache **/etc/apache2/sites-available/helloworld.conf**. Establecer el contenido al siguiente y reemplace *yourVmName* con el nombre real del equipo que está utilizando (por ejemplo *pyubuntu*).

        <VirtualHost *:80>
        ServerName yourVmName
        </VirtualHost>
        WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
        WSGIPythonPath /var/www/helloworld

1.  Habilitar el sitio con el siguiente comando:

        $ sudo a2ensite helloworld

1.  Reinicie Apache con el siguiente comando:

        $ sudo service apache2 reload

1.  Por último, cargue la página web en el explorador:

    ![Una ventana del explorador que muestra la página del mundo Hola a todos en Azure](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)


## <a name="shutting-down-your-azure-virtual-machine"></a>Cerrar la máquina virtual Azure

Cuando haya terminado con este tutorial, apagado o quitar su máquina virtual Azure recién creado para liberar los recursos para otros tutoriales y evitar provocar cargos de uso de Azure.
