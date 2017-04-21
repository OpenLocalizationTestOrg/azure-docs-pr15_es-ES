<properties
    pageTitle="Con la extensión VM Docker para Linux | Microsoft Azure"
    description="Describe Docker y las extensiones de máquinas virtuales de Azure y cómo crear máquinas virtuales de Azure que son hosts docker mediante la CLI de Azure en el modelo de implementación clásica."
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
    ms.date="05/27/2016"
    ms.author="rasquill"/>


# <a name="using-the-docker-vm-extension-with-the-azure-classic-portal"></a>Con la extensión de máquina virtual de Docker con el portal de clásico de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


[Docker](https://www.docker.com/) es una de las soluciones de virtualización más populares que usa [Linux contenedores](http://en.wikipedia.org/wiki/LXC) en lugar de máquinas virtuales como una manera de aislar los datos y la informática en recursos compartidos. Puede usar la extensión Docker VM administrada por [Agente de Azure Linux] para crear una máquina virtual Docker que hospeda cualquier número de contenedores de las aplicaciones de Azure.

> [AZURE.NOTE] Este tema describe cómo crear una máquina virtual de Docker desde el portal de clásico de Azure. Para ver cómo crear una máquina virtual de Docker en la línea de comandos, vea [cómo usar la extensión de máquina virtual Docker de la interfaz de línea de comandos de Azure (Azure CLI)]. Para ver una discusión de alto nivel de contenedores y sus ventajas, vea la [Pizarra de nivel alto Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

## <a name="create-a-new-vm-from-the-image-gallery"></a>Crear una nueva máquina virtual desde la Galería de imágenes
El primer paso requiere una máquina virtual de Azure desde una imagen de Linux que admite la extensión de VM Docker, utilizando una imagen de Ubuntu 14.04 LTADOS desde la Galería de imágenes como una imagen de servidor de ejemplo y Ubuntu 14.04 escritorio como un cliente. En el portal, haga clic en **+ nuevo** en la esquina inferior izquierda para crear una nueva instancia de máquina virtual y seleccione una imagen de Ubuntu 14.04 LTADOS desde las opciones disponibles o desde la Galería de imágenes completa, como se muestra a continuación.

> [AZURE.NOTE] Actualmente, sólo Ubuntu 14.04 LTADOS las imágenes más recientes que julio de 2014 son compatibles con la extensión de VM Docker.

![Crear una nueva imagen Ubuntu](./media/virtual-machines-linux-classic-portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Crear certificados Docker

Después de crear la máquina virtual, asegúrese de que Docker está instalado en el equipo cliente. (Para obtener información detallada, consulte [las instrucciones de instalación de Docker](https://docs.docker.com/installation/#installation)).

Cree los archivos de certificado y la clave para la comunicación de Docker según [Ejecutando Docker con https] y colóquelos en el **`~/.docker`** directorio del equipo cliente.

> [AZURE.NOTE] La extensión de VM Docker en el portal de actualmente requiere credenciales de base 64 codificado.

En la línea de comandos, use **`base64`** u otra herramienta favorita de codificación para crear temas codificado base 64. Esta acción con un conjunto simple de archivos de certificado y la clave puede ser similar a este:

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Agregar la extensión VM Docker
Para agregar la extensión de VM Docker, busque la instancia de máquina virtual que creó y desplácese hacia abajo hasta **extensiones** y haga clic en ella para que aparezca VM extensiones, tal como se muestra a continuación.
> [AZURE.NOTE] Esta funcionalidad se admite en el portal de vista previa sólo: https://portal.azure.com/

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickExtensions.png)
### <a name="add-an-extension"></a>Agregar una extensión
Haga clic en el **+ Agregar** para mostrar las extensiones de VM posibles, puede agregar a esta máquina virtual.

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickAdd.png)
### <a name="select-the-docker-vm-extension"></a>Seleccione la extensión VM Docker
Elija la extensión de VM Docker, que muestra la descripción de Docker y vínculos importantes, y, a continuación, haga clic en **crear** en la parte inferior para comenzar el proceso de instalación.

![](./media/virtual-machines-linux-classic-portal-use-docker/ChooseDockerExtension.png)

![](./media/virtual-machines-linux-classic-portal-use-docker/CreateButtonFocus.png)
### <a name="add-your-certificate-and-key-files"></a>Agregar el certificado y archivos de claves:

En los campos de formulario, escriba las versiones codificado base 64 de su certificado de entidad emisora de certificados, el certificado de servidor y la clave del servidor, como se muestra en el gráfico siguiente.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddExtensionFormFilled.png)

> [AZURE.NOTE] Tenga en cuenta que (como en la imagen anterior) la 2376 se rellena de forma predeterminada. Puede introducir cualquier extremo aquí, pero el siguiente paso será abrir el extremo coincidente. Si cambia el valor predeterminado, asegúrese de abrir el extremo coincidente en el siguiente paso.

## <a name="add-the-docker-communication-endpoint"></a>Agregar el extremo de comunicación Docker
Al ver el grupo de recursos que haya creado, seleccione el grupo de seguridad de la red asociada a su máquina virtual y haga clic en **Reglas de seguridad de entrada** para ver las reglas, como se muestra aquí.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddingEndpoint.png)

Haga clic en **+ Agregar** para agregar otra regla y, en el caso predeterminado, escriba un nombre para el extremo (en este ejemplo, **Docker**) y 2376 'rango puerto de destino'. Establezca el valor de protocolo que muestra **TCP**y haga clic en **Aceptar** para crear la regla.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddEndpointFormFilledOut.png)


## <a name="test-your-docker-client-and-azure-docker-host"></a>Probar su cliente de Docker y el Host de Azure Docker
Busque y copie el nombre del dominio de la máquina virtual y, en la línea de comandos del equipo cliente, tipo `docker --tls -H tcp://` *dockerextension* `.cloudapp.net:2376 info` (donde *dockerextension* se reemplaza por el subdominio para su máquina virtual).

El resultado debería ser similar a este:

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

Cuando haya completado los pasos anteriores, tendrá un host Docker totalmente funcional que se ejecuta en una máquina virtual de Azure, configurado para estar conectado a remotamente desde otros clientes.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes

Ya está listo para ir a la [Guía de usuario de Docker] y usar su VM Docker. Si desea automatizar la creación hosts de Docker en máquinas virtuales de Azure a través de la interfaz de línea de comandos, vea [cómo usar la extensión de máquina virtual Docker de la interfaz de línea de comandos de Azure (Azure CLI)]

<!--Anchors-->
[Create a new VM from the Image Gallery]: #createvm
[Create Docker Certificates]: #dockercerts
[Add the Docker VM Extension]: #adddockerextension
[Test Docker Client and Azure Docker Host]: #testclientandserver
[Next steps]: #next-steps

<!--Image references-->
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Cómo usar la extensión de máquina virtual Docker de la interfaz de línea de comandos de Azure (Azure CLI)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Agente de Linux Azure]: virtual-machines-linux-agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

[Ejecutar Docker con https]: http://docs.docker.com/articles/https/
[Guía de usuario de docker]: https://docs.docker.com/userguide/
