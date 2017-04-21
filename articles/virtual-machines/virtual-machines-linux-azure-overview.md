 <properties
   pageTitle="Azure y Linux | Microsoft Azure"
   description="Describe los servicios de Azure calcular, almacenamiento y redes con máquinas virtuales de Linux."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/14/2016"
   ms.author="v-livech"/>

# <a name="azure-and-linux"></a>Azure y Linux

Microsoft Azure es una colección creciente de público integrado web y servicios de análisis, máquinas virtuales, las bases de datos, móviles, redes, almacenamiento, en la nube: ideal para hospedar sus soluciones.  Microsoft Azure proporciona una plataforma informática scalable que permite solo paga por lo que se usan, cuando lo desee - sin tener que invertir en local hardware.  Azure está listo cuando haya escalar sus soluciones y out a cualquier escala requieren para satisfacer las necesidades de sus clientes.

Si está familiarizado con las diferentes funciones de Amazon AWS, puede examinar vs Azure AWS [documento de definición de asignación](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).


## <a name="regions"></a>Regiones
Recursos de Microsoft Azure se distribuyen entre varias regiones geográficas todo el mundo.  Una "región" representa varios centros de datos en una sola zona geográfica.  A partir del 1 de enero de 2016 incluye: 8 en América, 2 en Europa, 6 en Asia Pacífico, 2 en continente China y 3 en la India.  Si desea obtener una lista completa de todas las áreas de Azure, se mantiene que una lista existente y recientemente anunciada regiones.

- [Áreas de Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Disponibilidad
En orden para su implementación optar a nuestro contrato de nivel de servicio de VM 99.95, debe implementar dos o más VM ejecuta la carga de trabajo dentro de la disponibilidad de una conjunto. Esto se asegurará sus máquinas virtuales se distribuye entre varios dominios de errores en nuestros centros de datos como implementados en hosts con ventanas de mantenimiento diferente. Completa [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) explica la disponibilidad de garantía de Azure como un todo.

## <a name="azure-virtual-machines--instances"></a>Máquinas virtuales de Windows Azure e instancias
Microsoft Azure es compatible con la ejecución de las distribuciones más populares de Linux proporciona y mantiene un número de socios.  Encontrará distribuciones como rojos sombrero Enterprise, CentOS, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD y mucho más en Azure Marketplace. Se trabaja con diversos Comunidades Linux para agregar tipos aún más a la lista de [que Azure apoyo Linux Distros](virtual-machines-linux-endorsed-distros.md) .

Si su distro Linux preferido de elección no está actualmente en la galería, puede "Traer su propia Linux" VM [creando](virtual-machines-linux-create-upload-generic.md)y cargar un disco duro virtual de Linux en Azure.

Máquinas virtuales de Windows Azure le permiten implementar una amplia gama de la informática soluciones en forma ágil. Puede implementar prácticamente cualquier carga de trabajo y cualquier idioma en casi cualquier sistema operativo - Windows, Linux, o un personalizado creado uno en uno de nuestro creciente lista de socios. ¿Sigue sin ver lo está buscando?  No se preocupe: también puede reunir sus propias imágenes desde local.

## <a name="vm-sizes"></a>Tamaños VM
Cuando se implementa una máquina virtual en Azure, va a seleccionar un tamaño de la máquina virtual dentro de una de nuestras series de tamaños que es adecuado para la carga de trabajo. El tamaño también afecta a la capacidad de procesamiento de energía, la memoria y el almacenamiento de la máquina virtual. Se cargarán en función de la cantidad de tiempo de la máquina virtual se está ejecutando y consumo de los recursos asignados. Una lista completa de [tamaños de máquinas virtuales de Windows](virtual-machines-linux-sizes.md).

Estas son algunas reglas básicas para seleccionar un tamaño VM desde una de nuestras series (A, D, DS, G y GS).

* Máquinas virtuales de serie son nuestro valor precio básicas máquinas virtuales de luz cargas y escenarios de desarrollo o prueba. Son esté disponibles en todas las regiones y puede conectar y utilizar todos los recursos estándares disponibles en máquinas virtuales.
* Tamaños de serie (A8: A11) son especiales cálculo intensivo configuraciones adecuadas para aplicaciones de clúster de cálculo de alto rendimiento.
* Máquinas virtuales de la serie D están diseñadas para ejecutar aplicaciones que requieren mayor potencia de cálculo y rendimiento de disco temporal. Máquinas virtuales de serie D proporcionan procesadores más rápidos, una relación de memoria a core mayor y una unidad de estado sólido (SSD) para el disco temporal.
* Serie de Dv2, es la última versión de nuestra serie D, características de una CPU más eficaz. La CPU Dv2 serie es 35% más rápido que la CPU D serie. Se basa en la última generación v3 de 2,4 GHz Intel Xeon® E5-2673 procesador (Haskell) y con el aumento de tecnología de Intel Turbo 2.0, puede ir hasta 3,2 GHz. La serie de Dv2 tiene las mismas configuraciones de memoria y disco como la serie D.
* Máquinas virtuales de serie G ofrecen más memoria y ejecutan en hosts que tengan procesadores de la familia Intel Xeon E5 V3.

Nota: La serie DS y máquinas virtuales de serie GS tienen acceso al almacenamiento de Premium: nuestro SSD copia almacenamiento de alto rendimiento, baja latencia para cargas de trabajo intensivo de i/OS. Almacenamiento de Premium está disponible en determinadas regiones. Para obtener información detallada, consulte:

- [Almacenamiento de Premium: Almacenamiento de alto rendimiento para cargas de trabajo de Azure máquina virtual](../storage/storage-premium-storage.md)

## <a name="automation"></a>Automatización
Para lograr una referencia cultural de DevOps adecuada, toda infraestructura debe ser código.  Cuando todos la infraestructura reside en código fácilmente se pueden crearse (Caracas servidores).  Azure funciona con todos los principal automatización herramientas como Ansible, Chef, SaltStack y posición libre.  Azure también tiene su propio conjunto de herramientas de automatización:

- [Plantillas de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

- [VMAccess de Azure](virtual-machines-linux-using-vmaccess-extension.md)

Soporte técnico para la [nube al inicio](http://cloud-init.io/) está implementando Azure a través de la mayoría Linux Distros compatibles.  Actualmente se implementan máquinas virtuales de Ubuntu de canónica con la nube al inicio habilitado de forma predeterminada.  RHEL RedHats, CentOS y Fedora admiten nube al inicio, pero las imágenes de Azure mantienen RedHat no tiene instalado nube al inicio.  Para usar la nube al inicio en una familia de RedHat OS, debe crear una imagen personalizada con la nube al inicio instalado.

- [Uso de nube al inicio en máquinas virtuales de Linux de Azure](virtual-machines-linux-using-cloud-init.md)

## <a name="quotas"></a>Cuotas
Cada suscripción de Azure tiene límites predeterminados de cuota en el lugar que puede afectar a la implementación de un gran número de máquinas virtuales de su proyecto. El límite actual por suscripción es 20 máquinas virtuales por región.  Límites de cuota pueden ser generados por una incidencia de soporte técnico solicitar un aumento del límite de archivos.  Para obtener más detalles sobre los límites de cuota:

- [Límites de servicio de suscripción de Azure](../azure-subscription-service-limits.md)


## <a name="partners"></a>Socios

Microsoft trabaja estrechamente con nuestros socios para garantizar las imágenes disponibles están actualizadas y optimizadas para un tiempo de ejecución de Azure.  Para obtener más información sobre nuestros socios comprobar sus páginas de catálogo de soluciones.

- [Linux en distribuciones de respaldo de Azure](virtual-machines-linux-endorsed-distros.md)

RedHat - [Azure Marketplace - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)

Canónico - [Azure Marketplace - Ubuntu Server 16.04 LTADOS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)

Debian - [Azure Marketplace - 8 Debian "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)

FreeBSD - [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)

CoreOS - [Azure Marketplace - CoreOS (estable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)

RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)

Bitnami - [Bitnami biblioteca de Azure](https://azure.bitnami.com/)

Mesosphere - [Azure Marketplace - Mesosphere DC/OS en Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)

Docker - [Azure Marketplace - servicio de contenedor Azure con conjunto de Docker](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)

Jenkins - [Azure Marketplace - CloudBees Jenkins plataforma](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)


## <a name="getting-setup-on-azure"></a>Obtener el programa de instalación en Azure
Para empezar a utilizar Azure necesita una cuenta de Azure, CLI Azure instalado y un par de claves públicas y privadas de SSH.

## <a name="sign-up-for-an-account"></a>Registrarse para una cuenta
El primer paso en el uso de la nube de Azure es registrarse para una cuenta de Azure.  Vaya a la página de [Registro de cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/) para empezar.

## <a name="install-the-cli"></a>Instalar la CLI
Con la nueva cuenta de Azure, puede empezar inmediatamente con el portal de Azure, que es un panel de administración basada en web.  Para administrar la nube de Azure a través de la línea de comandos, instale el `azure-cli`.  Instalar la [CLI de Azure ](../xplat-cli-install.md)en su estación de trabajo Mac o Linux.

## <a name="create-an-ssh-key-pair"></a>Crear un par de claves SSH
Ahora tiene una cuenta de Azure, el portal web de Azure y Azure CLI.  El siguiente paso es crear un par de claves SSH que se utiliza para SSH en Linux sin usar una contraseña.  [Crear SSH claves en Linux y Mac](virtual-machines-linux-mac-create-ssh-keys.md) para habilitar inicios de sesión de contraseña menor y una mejor seguridad.

## <a name="getting-started-with-linux-on-microsoft-azure"></a>Introducción a Linux en Microsoft Azure
Con la configuración de cuenta de Azure, la CLI de Azure instalado y claves SSH creadas ya está listo para comenzar a crear una infraestructura en la nube de Azure.  La primera tarea es crear un par de máquinas virtuales.

## <a name="create-a-vm-using-the-cli"></a>Crear una máquina virtual con la CLI
Creando una VM Linux mediante CLI es una forma rápida de implementar una máquina virtual sin salir de la terminal con que está trabajando.  Todo lo que puede especificar en el portal web está disponible a través de un indicador de línea de comandos o cambiar.  

- [Crear una VM Linux mediante CLI](virtual-machines-linux-quick-create-cli.md)

## <a name="create-a-vm-in-the-portal"></a>Crear una máquina virtual en el portal
Crear una VM Linux en el portal de Azure web es una forma fácil y haga clic en por las diversas opciones para obtener acceso a una implementación.  En lugar de usar indicadores de línea de comandos o modificadores, es posible ver un diseño web armonía de varias opciones y configuración.  Todo el contenido disponible a través de la interfaz de línea de comandos también está disponible en el portal.

- [Crear una VM Linux con el Portal](virtual-machines-linux-quick-create-portal.md)

## <a name="login-using-ssh-without-a-password"></a>Uso de SSH sin una contraseña de inicio de sesión
La máquina virtual ahora se ejecuta en Azure y esté listo para iniciar sesión.  Uso de contraseñas para iniciar sesión a través de SSH es inseguro y lleva mucho tiempo.  Uso de claves SSH es la manera más segura y también la forma más rápida de inicio de sesión.  Cuando se crea Linux VM a través del portal o CLI, tiene dos opciones de autenticación.  Si elige una contraseña para SSH, Azure configura la máquina virtual para permitir que los inicios de sesión a través de las contraseñas.  Si elige usar una clave pública de SSH, Azure configura la máquina virtual para permitir que solo los inicios de sesión a través de claves SSH y desactiva inicios de sesión de contraseña. Para proteger su VM Linux permitiendo únicamente SSH claves inicios de sesión, use la opción de clave pública SSH durante la creación de VM en el portal o CLI.

- [Deshabilitar las contraseñas SSH en su VM Linux configurando SSHD](virtual-machines-linux-mac-disable-ssh-password-usage.md)

## <a name="related-azure-components"></a>Componentes relacionados de Azure

## <a name="storage"></a>Almacenamiento de información

- [Introducción al almacenamiento de Azure](../storage/storage-introduction.md)

- [Agregar un disco a una VM Linux mediante la cli de azure](virtual-machines-linux-add-disk.md)

- [Cómo adjuntar un disco de datos a una VM Linux en el portal de Azure](virtual-machines-linux-attach-disk-portal.md)

## <a name="networking"></a>Redes

- [Información general de una red virtual](../virtual-network/virtual-networks-overview.md)

- [Direcciones IP de Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md)

- [Apertura de puertos a una VM Linux en Azure](virtual-machines-linux-nsg-quickstart.md)

- [Crear un nombre de dominio completo en el portal de Azure](virtual-machines-linux-portal-create-fqdn.md)


## <a name="containers"></a>Contenedores

- [Máquinas virtuales y contenedores en Azure](virtual-machines-linux-containers.md)

- [Introducción de servicio del contenedor de Azure](../container-service/container-service-intro.md)

- [Implementar un clúster de servicio del contenedor de Azure](../container-service/container-service-deployment.md)

## <a name="next-steps"></a>Pasos siguientes

Ahora tiene una descripción general de Linux en Azure.  El siguiente paso consiste en ir más allá y crear máquinas virtuales unas!

- [Crear una VM Linux en Azure con el Portal](virtual-machines-linux-quick-create-portal.md)

- [Crear una VM Linux en Azure mediante CLI](virtual-machines-linux-quick-create-cli.md)
