

[Docker](https://www.docker.com/) es una de las soluciones de virtualización más populares que usa [Linux contenedores](http://en.wikipedia.org/wiki/LXC) en lugar de máquinas virtuales como una manera de aislar los datos de la aplicación y la informática en recursos compartidos. Puede usar la [extensión de Azure Docker VM](https://github.com/Azure/azure-docker-extension/blob/master/README.md) al [Agente de Azure Linux](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md) para crear una máquina virtual Docker que hospeda cualquier número de contenedores de las aplicaciones de Azure.

Este tema describe:

+ [Docker y contenedores de Linux]
+ [Cómo usar la extensión de VM Docker con Azure]
+ [Extensiones de máquina virtual para Linux y Windows]

Para crear las máquinas virtuales habilitadas Docker ahora mismo, consulte:

+ [Cómo usar la extensión de máquina virtual Docker de la interfaz de línea de comandos de Azure (Azure CLI)]
+ [Cómo utilizar la extensión de VM Docker con el portal de clásico de Azure]
+ [Cómo empezar a trabajar rápidamente con Docker en Azure Marketplace]

Para obtener más información sobre la extensión y cómo funciona, consulte la [Guía del usuario de extensión Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md).

## <a name="docker-and-linux-containers"></a>Docker y contenedores de Linux
[Docker](https://www.docker.com/) es una de las soluciones de virtualización más populares que utiliza [Linux contenedores](http://en.wikipedia.org/wiki/LXC) en lugar de máquinas virtuales como una manera de aislar los datos y la informática en recursos compartidos y proporciona otros servicios que le permiten crear o ensamblar aplicaciones rápidamente y les distribuir entre otros contenedores Docker.

Docker y contenedores Linux no son [hipervisores](http://en.wikipedia.org/wiki/Hypervisor) como Windows Hyper-V y [KVM](http://www.linux-kvm.org/page/Main_Page) en Linux (hay muchos otros ejemplos). Hipervisores virtualización del sistema operativo subyacente para habilitar sistemas operativos completos (denominados *máquinas virtuales*) para ejecutar dentro de hipervisor como si fueran una aplicación.

Docker y otros enfoques *contenedor* completamente han disminuido el tiempo de inicio consumida y procesamiento y almacenamiento sobrecarga necesario mediante el proceso y características de aislamiento del sistema del núcleo de Linux para exponer solo habían aislado de características de kernel a un contenedor de archivos.

La siguiente tabla describe en un nivel muy alto el tipo de diferencias de características que existen entre hipervisores y contenedores de Linux. Tenga en cuenta que algunas características que quizás más o menos conveniente según sus necesidades de la aplicación.

|   Característica      | Hipervisores | Contenedores  |
| :------------- |-------------| ----------- |
| Aislamiento de proceso | Más o menos completa | Si se obtiene la raíz, podría ser comprometido host contenedor |
| Memoria en disco obligatorio | Sistema operativo completo más aplicaciones | Requisitos de la aplicación solo |
| Tiempo necesario para comenzar a | Mucho más tiempo: Inicio de carga de la aplicación y sistemas operativos | Mucho más corta: solo aplicaciones necesitan iniciar porque ya se está ejecutando núcleo  |
| Automatización de contenedor | Ampliamente varía según el sistema operativo y aplicaciones | [Galería de imágenes de docker](https://registry.hub.docker.com/); otras personas

Para ver una discusión de alto nivel de contenedores y sus ventajas, vea la [Pizarra de nivel alto Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

Para obtener más información sobre qué es Docker y cómo funciona realmente, consulte [¿Qué es Docker?](https://www.docker.com/whatisdocker/)

#### <a name="docker-and-linux-container-security-best-practices"></a>Prácticas recomendadas docker y Linux contenedor seguridad

Contenedores comparten acceso a núcleo del equipo host, si código malintencionado es tener raíz también puede estar puede tener acceso no sólo en el equipo host, sino también los otros contenedores. Para proteger su sistema de contenedor más seguro que la configuración predeterminada, [Docker recomienda](https://docs.docker.com/articles/security/) usar Directiva de grupo de adición o [seguridad basada en roles](http://en.wikipedia.org/wiki/Role-based_access_control) , como [SELinux](http://selinuxproject.org/page/Main_Page) o [AppArmor](http://wiki.apparmor.net/index.php/Main_Page), por ejemplo, así como reducir tanto como sea posible las capacidades de kernel que se le ha concedido los contenedores. Además, hay muchos otros documentos de Internet que describen enfoques de seguridad con contenedores como Docker.

## <a name="how-to-use-the-docker-vm-extension-with-azure"></a>Cómo usar la extensión de VM Docker con Azure

La extensión de VM Docker es un componente que se instala en la instancia VM que cree que instala el motor de Docker y administra la comunicación remota con la máquina virtual. Hay dos formas para instalar la extensión de VM: puede crear su máquina virtual con el portal de administración o puede crear desde la interfaz de línea de comandos de Azure (Azure CLI).

Puede usar el portal para agregar la extensión de VM Docker a cualquier VM Linux compatible (actualmente, la única imagen que lo admita es la Ubuntu 14.04 LTADOS más reciente que julio). Usando la línea de comandos de CLI de Azure, sin embargo, puede instalar la extensión de VM Docker y crear y cargar sus certificados de comunicación Docker todas al mismo tiempo al crear la instancia de máquina virtual.

Para crear las máquinas virtuales habilitadas Docker ahora mismo, consulte:

+ [Cómo usar la extensión de máquina virtual Docker de la interfaz de línea de comandos de Azure (Azure CLI)]
+ [Cómo utilizar la extensión de VM Docker con el portal de clásico de Azure]

## <a name="virtual-machine-extensions-for-linux-and-windows"></a>Extensiones de máquina virtual para Linux y Windows
La [extensión Docker VM para Azure](https://github.com/Azure/azure-docker-extension/blob/master/README.md) es solo una de las varias extensiones VM que proporcionan un comportamiento especial y más están en desarrollo. Por ejemplo, algunas de las características de [extensión de Linux VM agente](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md) permiten modificar y administrar la máquina Virtual, incluidas las características de seguridad y características de kernel y redes. Por ejemplo, la extensión VMAccess le permite restablecer la contraseña de administrador o SSH clave.

Para obtener una lista completa, vea [Azure VM extensiones](../articles/virtual-machines/virtual-machines-windows-extensions-features.md).

<!--Anchors-->
[Cómo usar la extensión de máquina virtual Docker de la interfaz de línea de comandos de Azure (Azure CLI)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Cómo utilizar la extensión de VM Docker con el portal de clásico de Azure]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/
[Cómo empezar a trabajar rápidamente con Docker en Azure Marketplace]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-ubuntu-quickstart/
[Docker y contenedores de Linux]: #Docker-and-Linux-Containers
[Cómo usar la extensión de VM Docker con Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Extensiones de máquina virtual para Linux y Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
