<properties
    pageTitle="Instalar Python y el SDK - Azure"
    description="Obtenga información sobre cómo instalar Python y el SDK con Azure."
    services=""
    documentationCenter="python"
    authors="lmazuel"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="lmazuel"/>

# <a name="installing-python-and-the-sdk"></a>Instalar Python y el SDK

Python es fácil para la instalación en Windows y viene preinstalado en Mac, Linux y [Bash para Windows](https://msdn.microsoft.com/commandline/wsl/about). Esta guía le guiará a través de la instalación o no consigue que su equipo listos para usarlos con Azure.

## <a name="whats-in-the-python-azure-sdk"></a>¿Novedades de la Python SDK Azure?

El SDK de Azure para Python incluye componentes que permiten desarrollar, implementar y administrar aplicaciones de Python para Azure. Más concretamente, el SDK de Azure para Python incluye lo siguiente:

* **Las bibliotecas de administración**. Estas bibliotecas de clase ofrecen una interfaz de administración de recursos de Azure, como las cuentas de almacenamiento, máquinas virtuales de Windows.

* **Bibliotecas de tiempo de ejecución**. Estas bibliotecas de clases ofrecen una interfaz para tener acceso a características de Azure, como servicio de almacenamiento y bus.

## <a name="which-python-and-which-version-to-use"></a>Qué Python y qué versión utilizar

Hay varias versiones de intérpretes de Python: algunos ejemplos:

* CPython - el intérprete de Python estándar y más utilizado
* PyPy - rápida y compatible con implementación alternativa CPython
* IronPython - intérprete de Python que se ejecuta en .net CLR
* Jython - intérprete de Python que se ejecuta en la máquina Virtual de Java

**CPython** v2.7 o v3.3 + y PyPy 5.4.0 son probado y compatibles para el SDK de Azure Python.

## <a name="where-to-get-python"></a>¿Dónde puede Python?

Hay varias maneras de obtener CPython:

* Directamente desde [www.python.org][]
* Desde una distro conocida como [www.continuum.io][], [www.enthought.com][] o [www.activestate.com][]
* ¡Crear origen!

A menos que tenga una necesidad específica, se recomienda que las dos primeras opciones.

## <a name="sdk-installation-on-windows-linux-and-macos-client-libraries-only"></a>Instalación de SDK en Windows, Linux y Mac OS (solo para bibliotecas de cliente)

Si ya tiene Python instalado, puede usar puntos para instalar un paquete de todas las bibliotecas de cliente en su entorno de Python 3.3 + o 2.7 de Python existente. Esto descargará los paquetes del [Índice de paquete Python][] (PyPI).

Es posible que tenga derechos de administrador:

- Linux y Mac OS, utilice la `sudo` comando: `sudo pip install azure-mgmt-compute`.
- Windows: abra el PowerShell o símbolo del sistema como administrador

Puede instalar individualmente cada biblioteca para cada servicio de Azure:

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

Pueden instalar paquetes de vista previa con la `--pre` marca:

```console
   $ pip install --pre azure-mgmt-compute # will install only the latest Compute Management library
```

También puede instalar un conjunto de bibliotecas de Azure en una sola línea con el `azure` paquete de metadatos. Puesto que no todos los paquetes de este paquete de metadatos se publican como estable todavía, el `azure` paquete meta aún está en vista previa. Sin embargo, los paquetes de core desde perspectivas de calidad/integridad de código pueden se considera "estables" en este momento
- lo estará oficialmente marcada como tales en sincronización con otros idiomas tan pronto como sea posible. No estamos planificando en los cambios más importantes hasta ese momento.

Puesto que es una versión preliminar, necesita usar la `--pre` marca:

```console
   $ pip install --pre azure
```
   
o directamente

```console
   $ pip install azure==2.0.0rc6
```

## <a name="getting-more-packages"></a>Obtener más paquetes

El [Índice de paquete Python][] (PyPI) tiene una selección enriquecida de las bibliotecas de Python.  Si decide instalar un Distro, ya tendrá la mayoría de los bits interesantes para varios escenarios de desarrollo de web a la informática técnica.


## <a name="python-tools-for-visual-studio"></a>Python Tools para Visual Studio

[Python Tools para Visual Studio][] (PTVS) es un complemento de libre/OSS de Microsoft, que VS se convierte en un IDE Python completas:

![métodos-a-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Uso PTVS es opcional, pero se recomienda que le proporciona soporte Python y solución de proyecto de Web, depuración, perfiles, ventana interactiva, edición de plantillas y Intellisense.

PTVS también facilita implementar Microsoft Azure, con el soporte técnico para su implementación [sitios][]y [Servicios en la nube][] .

PTVS funciona con su existente de Visual Studio 2013 o con la instalación de 2015.  Para documentación, descargas y discusiones, vea [Python Tools para Visual Studio].  

## <a name="python-azure-scenarios-for-linux-and-macos"></a>Python Azure escenarios para Linux y Mac OS

Para Linux o Mac OS, escenarios de Azure principales que son compatibles:

1. Consumo de servicios de Azure usando las bibliotecas de cliente para Python

2. Ejecute la aplicación en una máquina virtual de Linux

3. Desarrollar y publicar en sitios Web de Azure con Git

El primer escenario permite crear aplicaciones web enriquecidas que aprovechar las capacidades de Azure PaaS como [almacenamiento de blobs][], el [almacenamiento de cola][]de [almacenamiento de tablas][] etc. a través de contenedores Pythonic para las API de REST de Azure. Estas obras idénticos en Windows, Mac y Linux.  También puede usar estas bibliotecas cliente desde su equipo de desarrollo local o una VM de Linux que se ejecuta en Azure.

Para el escenario VM, inicie una VM Linux de su elección (Ubuntu, CentOS, Suse) y lo que desea ejecutar y administrar.  Por ejemplo, puede ejecutar [IPython][] respuesta o Bloc de notas en su equipo Windows, Mac o Linux y coloque el explorador Linux o Windows multiproceso VM ejecuta el motor de IPython en Azure. Consulte el tutorial de [Bloc de notas de IPython en Azure][] para obtener más información.

Para obtener información sobre cómo configurar una VM Linux, consulte el tutorial [crear Máquina Virtual ejecuta Linux][] .

Implementación de Git puede desarrollar una aplicación web de Python y publicarlo en un sitio Web de Azure desde cualquier sistema operativo.  Al actualizar su repositorio en Azure, creará automáticamente un entorno virtual y puntos instalar los paquetes necesarios.

Para obtener más información sobre el desarrollo y la publicación de sitios Web de Azure, consulte los tutoriales para [Crear sitios Web con Django][], [Crear sitios con botella][]y [Creación de sitios Web con matraz][]. Para obtener información general sobre el uso de cualquier marco WSGI compatibles, vea [Configurar Python con sitios Web de Azure][].


## <a name="additional-software-and-resources"></a>Software adicional y recursos:

* [Azure SDK para Python ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [Azure SDK para Python Github](https://github.com/Azure/azure-sdk-for-python)
* [Ejemplos de Azure oficiales de Python](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Distribución de Python análisis continuo][]
* [Distribución de Python Enthought][]
* [Distribución de ActiveState Python][]
* [SciPy - un conjunto de bibliotecas de Python científico][]
* [NumPy - una biblioteca de valores numéricos para Python][]
* [Proyecto Django - un marco web para adultos/CMS][]
* [IPython - un respuesta o portátil avanzado para Python][]
* [Bloc de notas de IPython de Azure][]
* [Herramientas de Python para Visual Studio en GitHub][]
* [Centro para desarrolladores de Python](/develop/python/)

[Distribución de Python análisis continuo]: http://continuum.io
[Distribución de Python Enthought]: http://www.enthought.com
[Distribución de ActiveState Python]: http://www.activestate.com
[www.Python.org]: http://www.python.org
[www.Continuum.IO]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - un conjunto de bibliotecas de Python científico]: http://www.scipy.org
[NumPy - una biblioteca de valores numéricos para Python]: http://www.numpy.org
[Proyecto Django - un marco web para adultos/CMS]: http://www.djangoproject.com
[IPython - un respuesta o portátil avanzado para Python]: http://ipython.org
[IPython]: http://ipython.org
[Bloc de notas de IPython de Azure]: virtual-machines-linux-jupyter-notebook.md
[Servicios de nube]: cloud-services-python-ptvs.md
[Sitios Web]: web-sites-python-ptvs-django-mysql.md
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Herramientas de Python para Visual Studio en GitHub]: https://github.com/microsoft/ptvs
[Índice de paquete Python]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Crear una máquina Virtual con Linux]: virtual-machines-linux-quick-create-cli.md
[Creación de sitios Web con Django]: web-sites-python-create-deploy-django-app.md
[Crear sitios Web con botella]: web-sites-python-create-deploy-bottle-app.md
[Creación de sitios Web con matraz]: web-sites-python-create-deploy-flask-app.md
[Configurar Python con sitios Web de Azure]: web-sites-python-configure.md
[almacenamiento de tablas]: storage-python-how-to-use-table-storage.md
[almacenamiento de cola]: storage-python-how-to-use-queue-storage.md
[almacenamiento de blobs]: storage-python-how-to-use-blob-storage.md
