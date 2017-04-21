<properties
    pageTitle="Informática en Azure de código abierto y Linux | Microsoft Azure"
    description="Listas de artículos Linux e informática de código abierto en Azure, incluido el uso de Linux básico, algunos conceptos básicos sobre ejecuta o cargar imágenes de Linux en Azure y otro contenido sobre tecnologías específicas y las optimizaciones."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="06/27/2016"
    ms.author="rasquill"/>



# <a name="linux-and-open-source-computing-on-azure"></a>Informática en Azure de código abierto y Linux

Buscar toda la documentación que necesita para crear y administrar basados en Linux máquinas virtuales de Windows en el modelo de implementación clásico.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="get-started"></a>Introducción
- [Introducción para Linux en Azure](virtual-machines-linux-intro-on-azure.md)
- [Preguntas más frecuentes acerca de Azure máquinas virtuales de Windows creadas con el modelo de implementación clásica](virtual-machines-linux-classic-faq.md)
- [Acerca de las imágenes para máquinas virtuales](virtual-machines-linux-classic-about-images.md)
- [Cargar su propia imagen de Distro](virtual-machines-linux-classic-create-upload-vhd.md) (y también instrucciones usando una [Distribución de Azure-Endorsed](virtual-machines-linux-endorsed-distros.md))
- [Inicie sesión en una máquina virtual de Linux utilizando el portal de clásico de Azure](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="set-up"></a>Configurar

- [Instalar Azure de línea de comandos interfaz Azure](../xplat-cli-install.md)


## <a name="tutorials"></a>Tutoriales

- [Instalar la pila de luz en una máquina virtual de Linux en Azure](virtual-machines-linux-create-lamp-stack.md)
- [Ruby en la aplicación Web de guías en una máquina virtual de Azure](linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)
- [Cómo: instalar Apache protones-C Qpid para AMQP y Bus de servicio](../service-bus-messaging/service-bus-amqp-apache.md)

### <a name="databases"></a>Bases de datos
- [Optimizar el rendimiento de MySQL en Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [Clústeres MySQL](virtual-machines-linux-classic-mysql-cluster.md)
- [Ejecutar Casandra con Linux en Azure y tener acceso a él desde Node.js](virtual-machines-linux-classic-cassandra-nodejs.md)
- [Crear un clúster de varios maestro de MariaDbs](virtual-machines-linux-classic-mariadb-mysql-cluster.md)

### <a name="hpc"></a>HPC
- [Introducción a Linux nodos de cálculo en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
- [Ejecutar NAMD con Microsoft HPC Pack Linux nodos de cálculo en Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
- [Configurar un clúster de Linux RDMA para ejecutar aplicaciones de MPI](virtual-machines-linux-classic-rdma-cluster.md)

### <a name="docker"></a>Docker
- [Con la extensión de máquina virtual Docker desde la línea de comandos de Azure la interfaz (CLI Azure)](virtual-machines-linux-classic-cli-use-docker.md)
- [Uso de la extensión de máquina virtual Docker desde el portal de Azure](virtual-machines-linux-classic-portal-use-docker.md)
- [Cómo usar docker máquina en Azure](virtual-machines-linux-docker-machine.md)

### <a name="ubuntu"></a>Ubuntu
- [Cómo: clústeres MySQL](virtual-machines-linux-classic-mysql-cluster.md)
- [Cómo: Node.js y Casandra](virtual-machines-linux-classic-cassandra-nodejs.md)

### <a name="opensuse"></a>OpenSUSE
- [Cómo: instalar y ejecutar MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)

### <a name="coreos"></a>CoreOS
- [Cómo: usar CoreOS en Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)


## <a name="planning"></a>Planificación
- [Directrices para una implementación de servicios de infraestructura de Azure](virtual-machines-linux-infrastructure-subscription-accounts-guidelines.md)
- [Seleccionar nombres de usuario de Linux](virtual-machines-linux-usernames.md)
- [Cómo configurar una disponibilidad para máquinas virtuales en el modelo de implementación clásica](virtual-machines-linux-classic-configure-availability.md)
- [Cómo programar el mantenimiento previsto en máquinas virtuales de Azure](virtual-machines-linux-planned-maintenance-schedule.md)
- [Administrar la disponibilidad de máquinas virtuales](virtual-machines-linux-manage-availability.md)
- [Mantenimiento planeado para máquinas virtuales de Linux en Azure](virtual-machines-linux-planned-maintenance.md)


## <a name="deployment"></a>Implementación
- [Crear una máquina virtual personalizada con Linux](virtual-machines-linux-classic-createportal.md)
- [Conceptos básicos: capturar una VM Linux para hacer una plantilla](virtual-machines-linux-classic-capture-image.md)
- [Información de apoyo no distribuciones](virtual-machines-linux-create-upload-generic.md)


## <a name="management"></a>Administración

- [SSH](virtual-machines-linux-mac-create-ssh-keys.md)
- [Cómo restablecer una contraseña o SSH propiedades para Linux](virtual-machines-linux-classic-reset-access.md)
- [Uso de raíz](virtual-machines-linux-use-root-privileges.md)


## <a name="azure-resources"></a>Recursos de Azure

- [El agente de Linux Azure](virtual-machines-linux-agent-user-guide.md)
- [Características y extensiones de azure VM](virtual-machines-windows-extensions-features.md)
- [Insertar datos personalizados en una máquina virtual para usar con la nube al inicio](virtual-machines-windows-classic-inject-custom-data.md)


## <a name="storage"></a>Almacenamiento de información

- [Asociar un disco de datos a una VM Linux](virtual-machines-linux-classic-attach-disk.md)
- [Desasociar un disco de datos desde una máquina virtual de Linux](virtual-machines-linux-classic-detach-disk.md)
- [RAID](virtual-machines-linux-configure-raid.md)


## <a name="networking"></a>Redes
- [Cómo configurar extremos en una máquina virtual clásica en Azure](virtual-machines-linux-classic-setup-endpoints.md)


## <a name="troubleshooting"></a>Solución de problemas
- [Solucionar problemas de conexiones de Shell seguro (SSH) a una máquina virtual para Azure basados en Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)
- [Solucionar problemas de implementación clásica con la creación de una nueva máquina virtual de Linux en Azure](virtual-machines-linux-classic-troubleshoot-deployment-new-vm.md)  
- [Solucionar problemas de implementación clásica con reiniciar o cambiar el tamaño de una máquina Virtual de Linux existente en Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md) 


## <a name="reference"></a>Referencia

- [Comandos CLI Azure en modo de administración de servicios de Azure (asm)](../virtual-machines-command-line-tools.md)
- [Administración del servicio de Azure API de REST](https://msdn.microsoft.com/library/azure/ee460799.aspx)




## <a name="general-links"></a>Vínculos de general
Los siguientes vínculos son para Microsoft blogs, páginas de Technet y sitios externos en lugar de Azure.com documentación como se indicó anteriormente. Azure y en el mundo informático de código abierto se mueven con rapidez destinos, es casi seguro de que los vínculos siguientes son obsoleto *a pesar* el hecho de que haremos podremos lo posible continuamente agregar temas más recientes y quitar las actualizadas. Si nos hemos perdido uno, por favor, háganoslo saber en los comentarios o enviar una solicitud de extracción a nuestro [GitHub repo](https://github.com/Azure/azure-content/).

- [Ejecuta 5 ASP.NET en Linux con contenedores Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
- [Cómo implementar una imagen de la máquina virtual de CentOS desde OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
- [Infraestructura de actualización SUSE](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
- [SUSE Linux Enterprise Server para biblioteca de dispositivo de nube SAP](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)
- [Creación Linux altamente disponibles en Azure en 12 pasos](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
- [Automatizar aprovisionamiento Linux en Azure con Azure CLI, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
- [GlusterFS en Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

### <a name="freebsd"></a>FreeBSD
- [Ejecuta FreeBSD en Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
- [Fácil implementar FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
- [Implementar una imagen personalizada de FreeBSD](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
- [Kaspersky AV para servidor de archivos de Linux](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### <a name="nosql"></a>NoSQL

- [Bases de datos de código abierto NoSql 8 de Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
- [Slideshare (MSOpenTech): Experiencias con CouchDb en Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
- [Ejecutar CouchDB como-servicio con node.js, CORS y simplifique](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)

- [Redis en Windows en el servicio de caché Redis Azure](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
- [Anuncio de proveedor de estado de la sesión de ASP.NET para Redis versión de vista previa](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)

- [Blog: RavenHQ ahora disponible en Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### <a name="big-data"></a>Datos grandes
- [Instalar Hadoop en máquinas virtuales de Linux Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
- [HDInsight de Azure](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)

### <a name="relational-database"></a>Base de datos relacional
- [Arquitectura de disponibilidad de alta MySQL en Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
- [Instalación Postgres con corosync, pg_bouncer con ILB](https://github.com/chgeuer/postgres-azure)

### <a name="linux-high-performance-computing-hpc"></a>Linux de alto rendimiento informática (HPC)

- [Plantilla tutorial rápido: control de un clúster SLURM](https://github.com/Azure/azure-quickstart-templates/tree/master/slurm) (y [entrada de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
- [Plantilla de tutorial: crear un clúster HPC con Linux nodos de cálculo](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

### <a name="devops-management-and-optimization"></a>Devops, administración y optimización

Como el mundo de devops, administración y optimización es bastante amplio y cambiar muy rápidamente, debe considerar la lista situada debajo de un punto de partida.

- [Vídeo: Máquinas virtuales de Azure: usar Chef, posición libre y Docker para administrar máquinas virtuales de Linux](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)

- [Guía completa de la distribución automática de clúster Kubernetes con CoreOS y trama](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
- [Visualizador de Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)

- [Jenkins subordinado complemento para Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
- [GitHub repo: almacenamiento Jenkins complemento para Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)

- [Terceros: Complemento para Azure Hudson subordinado](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
- [Terceros: Almacenamiento Hudson complemento para Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)

- [Vídeo: ¿Qué es Chef y cómo funciona?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

- [Vídeo: Cómo usar la automatización Azure con máquinas virtuales de Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)

- [Blog: Cómo hacer DSC de Powershell para Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
- [GitHub: Docker cliente DSC](https://github.com/anweiss/DockerClientDSC)

- [Complemento de compresor de Azure](https://github.com/msopentech/packer-azure)
