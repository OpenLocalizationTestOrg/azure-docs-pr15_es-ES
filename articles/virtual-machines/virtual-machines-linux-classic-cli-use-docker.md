<properties
    pageTitle="Uso de la extensión VM Docker para Linux en Azure"
    description="Describen Docker y las extensiones de máquinas virtuales de Azure y se muestra cómo crear mediante programación máquinas virtuales en Azure que son hosts docker desde la línea de comandos mediante la CLI de Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="08/29/2016"
    ms.author="rasquill"/>

# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>Con la extensión de máquina virtual Docker desde la línea de comandos de Azure la interfaz (CLI Azure)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]



Este tema describe cómo crear una máquina virtual con la extensión de máquina virtual Docker desde el modo de administración (asm) de servicio en Azure CLI en cualquier plataforma. [Docker](https://www.docker.com/) es una de las soluciones de virtualización más populares que usa [Linux contenedores](http://en.wikipedia.org/wiki/LXC) en lugar de máquinas virtuales como una manera de aislar los datos y la informática en recursos compartidos. Puede usar la extensión Docker VM y el [Agente de Azure Linux](virtual-machines-linux-agent-user-guide.md) para crear una máquina virtual Docker que hospeda cualquier número de contenedores de las aplicaciones de Azure. Para ver una discusión de alto nivel de contenedores y sus ventajas, vea la [Pizarra de nivel alto Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).


##<a name="how-to-use-the-docker-vm-extension-with-azure"></a>Cómo usar la extensión de VM Docker con Azure
Para usar la extensión Docker VM con Azure, debe instalar una versión de la [interfaz de comandos de Azure](https://github.com/Azure/azure-sdk-tools-xplat) Azure superiores a 0.8.6 (tal como se escribe este artículo, la versión actual es 0.10.0). Puede instalar la CLI de Azure en Mac, Linux y Windows.


El proceso completo usar Docker en Azure es sencillo:

+ Instalar la CLI de Azure y sus dependencias en el equipo desde el que desea controlar Azure (en Windows, se trata de una distribución de Linux que se ejecuta como una máquina virtual)
+ Use los comandos de Azure CLI Docker para crear un host de máquina virtual Docker en Azure
+ Use los comandos de Docker locales para administrar los contenedores Docker en la máquina virtual de Docker en Azure.


### <a name="install-the-azure-command-line-interface-azure-cli"></a>Instalar la Azure de línea de comandos interfaz Azure

Instalar y configurar la CLI de Azure, consulte [cómo instalar la interfaz de línea de comandos de Azure](../xplat-cli-install.md). Para confirmar la instalación, escriba `azure` en el símbolo del sistema y después de un breve momento debería ver las imágenes de Azure CLI ASCII, que muestra los comandos básicos disponibles para usted. Si la instalación ha funcionado correctamente, debe escribir `azure help vm` y compruebe que uno de los comandos de la lista es "docker".

> [AZURE.NOTE] Docker tiene herramientas para Windows, [Docker máquina](https://docs.docker.com/installation/windows/), que también puede usar para automatizar la creación de un cliente de docker que puede usar para trabajar con máquinas virtuales de Azure como hosts docker.

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Conectar CLI Azure a su cuenta de Azure
Antes de poder usar CLI Azure debe asociar las credenciales de cuenta de Azure con CLI Azure en su plataforma. La sección [cómo conectarse a su suscripción de Azure](../xplat-cli-connect.md) explica cómo descargar e importar el archivo **.publishsettings** o asociar la CLI Azure con un identificador de organización.

> [AZURE.NOTE] Existen algunas diferencias en el comportamiento cuando se usa uno o a los otros métodos de autenticación, por lo que debe leer el documento anterior para conocer las diferentes funciones.

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Instalar a Docker y usar la extensión de VM Docker de Azure
Siga las [instrucciones de instalación de Docker](https://docs.docker.com/installation/#installation) para instalar a Docker localmente en el equipo.

Para usar Docker con una máquina Virtual de Azure, la imagen de Linux usada para la máquina virtual debe tener instalado el [Agente de máquina virtual de Azure Linux](virtual-machines-linux-agent-user-guide.md) . Actualmente, hay sólo dos tipos de imágenes que proporcionan esto:

+ Una imagen de Ubuntu desde la Galería de imágenes de Azure o

+ Una imagen de Linux personalizada que ha creado con el agente de Azure Linux VM instalado y configurado. Consulte [Agente de Azure Linux VM](virtual-machines-linux-agent-user-guide.md) para obtener más información acerca de cómo crear una VM Linux personalizado con el agente de máquina virtual de Azure.

### <a name="using-the-azure-image-gallery"></a>Uso de la Galería de imágenes de Azure

Desde una fiesta o sesión de Terminal, use el siguiente comando CLI Azure para localizar la imagen Ubuntu más reciente de la Galería de máquina virtual para usar escribiendo

`azure vm image list | grep Ubuntu-14_04`

y seleccione uno de los nombres de imagen, como `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`y use el comando siguiente para crear una nueva máquina virtual con la imagen.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

donde:

+ * &lt;nombre de la máquina virtual cloudservice&gt; * es el nombre de la máquina virtual que se convertirá en el equipo del host de contenedor Docker en Azure

+  * &lt;nombre de usuario&gt; * es el nombre de usuario del usuario raíz predeterminada de la máquina virtual

+ * &lt;contraseña&gt; * es la contraseña de la cuenta de *usuario* que cumpla las normas de complejidad de Azure

> [AZURE.NOTE] Actualmente, una contraseña debe tener al menos 8 caracteres, contienen una minúscula y una mayúscula, un número y un carácter especial como uno de los siguientes caracteres: `!@#$%^&+=`. No, el período al final de la frase anterior no es un carácter especial.

Si el comando se realizó correctamente, verá algo parecido al siguiente, dependiendo de los argumentos precisos y opciones que utiliza:

![](./media/virtual-machines-linux-classic-cli-use-docker/dockercreateresults.png)

> [AZURE.NOTE] Crear una máquina virtual puede tardar unos minutos, pero cuando se ha configurado (el valor de estado es `ReadyRole`) se inicia el daemon Docker (el servicio de Docker) y puede conectar con el host de contenedor Docker.

Para probar la máquina virtual de Docker que haya creado en Azure, escriba

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

donde * &lt;vm nombre--usadas&gt; * es el nombre de la máquina virtual que ha usado en la llamada a `azure vm docker create`. Debería ver algo parecido al siguiente, lo que indica que su máquinas virtuales Docker hacia arriba y ejecuta en Azure y esperando sus comandos. 

Ahora puede intentar conectarse mediante el cliente de docker para obtener información (en algunas configuraciones de cliente Docker, tales como los de Mac, puede que deba usar `sudo`):

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

Para asegurarse de que es todo en marcha, puede examinar la máquina virtual para la extensión Docker:

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Autenticación de docker Host VM

Además de crear la máquina virtual Docker, la `azure vm docker create` comando también crea automáticamente los certificados necesarios para permitir que el equipo del cliente Docker conectar con el host de Azure contenedor mediante HTTPS y los certificados se almacenan en el cliente y el host de equipos, según corresponda. En los intentos posteriores, los certificados existentes reutilizar y compartir con el nuevo host.

De forma predeterminada, los certificados se colocan en `~/.docker`, y Docker se configurarán para ejecutar en puerto **2376**. Si desea usar un puerto diferente o directorio, puede usar uno de los siguientes `azure vm docker create` opciones de línea de comandos para configurar el contenedor Docker hospedar máquina virtual para usar un puerto diferente o certificados diferentes para conectar clientes:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

El daemon Docker en el host está configurado para escuchar y autenticar las conexiones de cliente en el puerto especificado mediante los certificados generados por el `azure vm docker create` comando. El equipo cliente debe tener estos certificados para obtener acceso al host Docker.

> [AZURE.NOTE] Un host de red que se ejecuta sin estos certificados será vulnerable a cualquier persona que puede conectarse al equipo. Antes de modificar la configuración predeterminada, asegúrese de que comprende los riesgos a los equipos y aplicaciones.

## <a name="next-steps"></a>Pasos siguientes

* Ya está listo para ir a la [Guía de usuario de Docker] y usar su VM Docker. Para crear una máquina virtual Docker habilitado en el nuevo portal, vea [cómo usar la extensión de VM Docker con el Portal].

* La extensión de Azure Docker VM también admite redactar Docker, que utiliza un archivo YAML descriptiva para realizar una aplicación de modelado programador en cualquier entorno y generar una implementación coherente. Consulte [Introducción a Docker y redactar definir y ejecutar una aplicación de contenedor múltiples en una máquina virtual Azure].  

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

[How to use the Docker VM Extension with Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]: #Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
[Cómo utilizar la extensión de VM Docker con el Portal]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Guía de usuario de docker]: https://docs.docker.com/userguide/
 
[Introducción a Docker y redactar definir y ejecutar una aplicación de contenedor múltiples en una máquina virtual Azure]:virtual-machines-linux-docker-compose-quickstart.md