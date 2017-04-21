<properties
    pageTitle="Replicar máquinas virtuales de VMware y servidores físicos en Azure con la recuperación de sitio de Azure en el portal de Azure | Microsoft Azure"
    description="Describe cómo implementar Azure recuperación de sitio para coordinar replicación, migración tras error y recuperación de máquinas virtuales de VMware local y Windows/Linux servidores físicos en Azure con el portal de Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-machines-to-azure-with-azure-site-recovery-using-the-azure-portal"></a>Replicar máquinas virtuales de VMware y equipos físicos en Azure con la recuperación de sitio de Azure con el portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-vmware-to-azure.md)
- [Clásico de Azure](site-recovery-vmware-to-azure-classic.md)
- [Azure clásica (heredado)](site-recovery-vmware-to-azure-classic-legacy.md)

Bienvenido a Azure sitio recuperación! Utilice este artículo si desea replicar máquinas virtuales de VMware local o servidores físicos de Windows/Linux en Azure con la recuperación de sitios de Azure en el portal de Azure.

> [AZURE.NOTE] Azure tiene dos distintos [modelos de implementación](../resource-manager-deployment-model.md) para crear y trabajar con recursos: Azure Resource Manager (ARM) y clásica. Azure también tiene dos portales – el portal clásico Azure que admita el modelo de implementación clásica y el portal de Azure con soporte para ambos modelos de implementación.

Recuperación de sitio en el portal de Azure proporciona varias características nuevas:

- Los servicios de copia de seguridad de Azure y Azure sitio recuperación se combinan en un único depósito de servicios de recuperación para que puedan configurar y administrar continuidad empresarial y recuperación (BCDR) desde una única ubicación. En el panel unificado puede supervisar y administrar operaciones en los sitios local y la nube pública de Azure.
- Los usuarios con suscripciones a Azure aprovisionados con el programa de proveedor de soluciones de nube (CSP) ahora pueden administrar operaciones de recuperación de sitio en el portal de Azure.
- Recuperación de sitio en el portal de Azure puede replicar máquinas a cuentas de almacenamiento ARM. Migración tras error, recuperación del sitio crea máquinas virtuales basado en ARM en Azure.
- Recuperación de sitio sigue admiten la replicación de cuentas de almacenamiento clásico. Migración tras error, recuperación del sitio crea máquinas virtuales con el modelo clásico.

Después de leer este artículo publicar los comentarios en la parte inferior de los comentarios de Disqus. Formular preguntas técnicas en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Información general

Las organizaciones necesitan una estrategia BCDR que determina cómo mantenerse ejecución y está disponible durante el tiempo de inactividad planificado y aplicaciones, cargas de trabajo y datos y recuperación tan pronto como sea posible a las condiciones de trabajo normal. La estrategia BCDR debe mantener datos empresariales seguros y recuperables y garantizar que las cargas de trabajo permanecen continuamente disponibles cuando se produce desastre.

Recuperación de sitio es un servicio de Azure que contribuye a la estrategia BCDR por coordinar la replicación de servidores físicos de local y máquinas virtuales en la nube (Azure) o a un centro de datos secundario. Cuando se produzcan interrupciones en su ubicación primaria, conmutar a la ubicación secundaria para mantener las aplicaciones y cargas de trabajo disponibles. No volver a su ubicación primaria cuando devuelve las operaciones normales. Más información en [¿Qué es la recuperación de sitio de Azure?](site-recovery-overview.md)

Este artículo proporciona toda la información que necesita replicar local máquinas virtuales de VMware y servidores físicos Linux o Windows Azure. Incluye una introducción a la arquitectura, planificación de la información y los pasos de implementación para configurar Azure, servidores locales, opciones de replicación y planificación de capacidad. Después de configurar la infraestructura que puede habilitar la replicación en equipos que desea proteger y comprobar que migración tras error funciona.

## <a name="business-advantages"></a>Ventajas de la empresa

- Recuperación de sitio proporciona protección fuera del sitio para las cargas de trabajo de la empresa y aplicaciones que se ejecutan en servidores físicos y máquinas virtuales de VMware.
- El portal de servicios de recuperación proporciona una única ubicación para configurar, administrar y supervisar la replicación, la migración tras error y la recuperación.
- Recuperación de sitio puede detectar automáticamente máquinas virtuales de VMware agregado a vSphere anfitriones.
- Fácilmente puede ejecutar migraciones tras error de la infraestructura local a Azure y recuperación (restaurar) de Azure a los servidores de VM VMware en su sitio local.
- Puede habilitar varios VM y crear grupos de replicación para que las cargas de trabajo de la aplicación en niveles a través de varias replicar equipos al mismo tiempo. Todos los equipos de un grupo de replicación tienen puntos de recuperación de bloqueo coherente y coherentes para la aplicación cuando no sobre. Para la migración tras error, puede recopilar varios equipos en los planes de recuperación para que las cargas de trabajo de la aplicación en niveles conmutan entre sí.

## <a name="supported-operating-systems"></a>Sistemas operativos compatibles

### <a name="windows64-bit-only"></a>Windows (solo 64 bits)
- Windows Server 2008 R2 SP1 +
- Windows Server 2012
- Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux (solo 64 bits)
- Rojos sombrero Enterprise Linux 6,7, 7.1, 7.2
- CentOS 6.5, 6.6, 6,7, 7.0, 7.1, 7.2
- Oracle Enterprise Linux 6.4, 6.5 ejecutando rojos sombrero compatible kernel o Unbreakable Enterprise Kernel versión 3 (UEK3)
- SUSE Linux Enterprise Server 11 SP3

## <a name="scenario-architecture"></a>Arquitectura de escenario

Estos son los componentes del escenario:

- **Servidor de configuración**: un equipo local que coordenadas comunicaciones y administra los procesos de replicación y la recuperación de datos. En este equipo deberá ejecutar un archivo de instalación único para instalar el servidor de configuración y estos componentes adicionales:
    - **Servidor de procesos**: actúa como una puerta de enlace de replicación. Recibe datos de replicación de equipos de origen protegida, optimiza con almacenamiento en caché, compresión y cifrado y envía al almacenamiento de Azure. También controla la instalación de inserción del servicio de movilidad para equipos protegidas y realiza la detección automática de máquinas virtuales de VMware. El servidor de procesos predeterminado está instalado en el servidor de configuración. Puede implementar servidores de procesos independientes adicionales para ajustar su implementación.
    - **Servidor de destino maestro**: controla los datos de replicación durante la recuperación de Azure.

- **Servicio de movilidad**: este componente se implementa en cada equipo (VMware VM o servidor físico) que desea replicar en Azure. Captura de escritura de datos en el equipo y reenvía al servidor de procesos.
- **Azure**: no es necesario crear cualquier máquinas virtuales de Azure para controlar la replicación y migración tras error en Azure.  Tiene una suscripción de Azure, una cuenta de almacenamiento de Azure para almacenar datos duplicados y a continuación, una red virtual Azure máquinas virtuales de Azure está conectadas a una red después de la migración. La cuenta de almacenamiento y la red deben estar en la misma región como depósito de servicios de recuperación.
- **Recuperación**: cuando esté listo para un error de Azure a su sitio local después de un error, tendrá que crear una máquina virtual de Azure como un servidor de procesos temporal. Puede eliminarlo una vez completada la recuperación. Para la recuperación, también deberá una conexión VPN (o Azure ExpressRoute) entre su sitio local y la red de Azure en la que se encuentran sus máquinas virtuales de Azure. Si el tráfico de recuperación es intenso también debe configurar un patrón dedicado servidor destino equipo local. Para aclarar tráfico puede utilizarse el servidor de destino principal predeterminado ejecuta en el servidor de configuración.


El gráfico muestra cómo interactúan estos componentes.

![arquitectura](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figura 1: VMware o físico en Azure**

## <a name="azure-prerequisites"></a>Requisitos previos de Azure

Esto es lo tiene en Azure para implementar este escenario.

**Requisito previo** | **Detalles**
--- | ---
**Cuenta de Azure**| Tendrá una cuenta de [Microsoft Azure](http://azure.microsoft.com/) . Puede empezar con una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) acerca de precios de recuperación del sitio.
**Almacenamiento de Azure** | Los datos duplicados se almacenan en el almacenamiento de Azure y Azure VM se crean cuando se produce error. <br/><br/>Para almacenar datos tendrá una cuenta de almacenamiento estándar o premium en la misma región como depósito de servicios de recuperación.<br/><br/>Puede usar una cuenta de almacenamiento LRS o GRS. Se recomienda GRS para que los datos son resistentes si se produce una interrupción regional, o si no se puede recuperar el área principal. [Más información](../storage/storage-redundancy.md).<br/><br/> [Almacenamiento de Premium](../storage/storage-premium-storage.md) normalmente se usa para máquinas virtuales que necesitan un rendimiento de IO alto y baja latencia para cargas de trabajo intensivo IO de host.<br/><br/> Si desea usar una cuenta de premium para almacenar datos duplicados, también deberá una cuenta de almacenamiento estándar para almacenar registros de replicación que capturan los cambios en curso a datos locales.<br/><br/> Tenga en cuenta que las cuentas de almacenamiento creadas en el portal de Azure no pueden moverse entre los grupos de recursos. También protección a las cuentas de almacenamiento premium en la India Central y India sur no es compatible actualmente.<br/><br/> [Lea acerca de](../storage/storage-introduction.md) Almacenamiento de Azure.
**Red de Azure** | Necesitará una red virtual Azure que se conectará máquinas virtuales de Azure cuando se produce error. La red virtual Azure debe estar en la misma región como depósito de servicios de recuperación.
**Recuperación de Azure** | Necesitará un temporal servidor de proceso configurada como una máquina virtual de Azure. Se puede crear cuando esté listo para conmutar y eliminarlo una vez éxito volver completada.<br/><br/> Para un error volver tendrá una conexión VPN (o Azure ExpressRoute) desde la red de Azure que el sitio local.

## <a name="configuration-server--scale-out-process-prerequisites"></a>Servidor de configuración o cambiar la escala de los requisitos previos de proceso

Deberá configurar un equipo local que el servidor de configuración / escalado servidor de procesos.

**Requisito previo** | **Detalles**
--- | ---
**Servidor de configuración**| Necesita un local físico o máquina virtual que ejecuta Windows Server 2012 R2. Todos los componentes de recuperación de sitios local están instalados en este equipo.<br/><br/>Para la replicación de VMware VM, se recomienda que implementar el servidor como una VM VMware altamente disponibles. Si está replicar equipos físicos la máquina puede ser un servidor físico.<br/><br/> Recuperación al sitio local de Azure siempre es máquinas virtuales de VMware independientemente de si no se pudo sobre máquinas virtuales o servidores físicos. Si no implementa el servidor de configuración como una VM VMware deberá configurar un servidor de destino principal independiente como una VM VMware para recibir el tráfico de recuperación.<br/><br/>Si el servidor es una VM VMware, el tipo de adaptador de red debería VMXNET3. Si utiliza un adaptador de red distinto debe instalar una [actualización de VMware](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1) en el servidor vSphere 5.5.<br/><br/>El servidor debe tener una dirección IP estática.<br/><br/>El servidor no debe ser un controlador de dominio.<br/><br/>El nombre de host del servidor debe ser 15 caracteres o menos.<br/><br/>El sistema operativo debería inglés solo.<br/><br/> Debe instalar VMware vSphere PowerCLI 6.0. en el servidor de configuración.<br/><br/>El servidor de configuración necesita acceso a internet. El acceso de salida se requiere como sigue:<br/><br/>Acceso temporal en HTTP 80 durante la instalación de los componentes de recuperación del sitio (para descargar MySQL)<br/><br/>Acceso de salida en curso en HTTPS 443 para la administración de replicación<br/><br/>Acceso de salida en curso en HTTPS 9443 tráfico de replicación (puede modificarse este puerto)<br/><br/>El servidor también tendrán acceso a las siguientes direcciones URL para que se pueda conectar al Azure: *. hypervrecoverymanager.windowsazure.com; *. AccessControl.Windows.NET; *. backup.windowsazure.com; *. BLOB.Core.Windows.NET; *. store.core.windows.net<br/><br/>Si tiene reglas de firewall basado en la dirección IP del servidor, compruebe que las reglas de permitan la comunicación a Azure. Debe permitir el protocolo HTTPS (443) y los [Intervalos de IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) .<br/><br/>Permitir que los intervalos de direcciones IP para la región de su suscripción de Azure y oeste de Estados Unidos.<br/><br/>Permitir que esta dirección URL para la descarga de MySQL:.http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi


## <a name="vmware-vcentervsphere-host-prerequisites"></a>Requisitos previos de vCenter/vSphere host VMware

**Requisito previo** | **Detalles**
--- | ---
**vSphere**| Necesita uno o varios hipervisores de vSphere VMware.<br/><br/>Hipervisores deben estar ejecutándose vSphere versión 6.0, 5.5 o 5.1 con las últimas actualizaciones.<br/><br/>Se recomienda que su vSphere hosts y servidores vCenter se encuentran en la misma red que el servidor de procesos (se trata de la red en la que el servidor de configuración se encuentra a menos que se ha configurado un servidor de proceso dedicado).
**vCenter** | Se recomienda implementar un servidor de vCenter VMware para administrar sus hosts vSphere. Debe estar ejecutándose vCenter versión 6.0 o 5.5 con las últimas actualizaciones.<br/><br/>Observe que el sitio recuperación no admite vCenter nuevo y vSphere 6.0 características como cruzada vCenter vMotion, volúmenes virtuales y almacenamiento DRS. Soporte de recuperación de sitio se limita a las características que también estaban disponibles en la versión 5.5.


## <a name="protected-machine-prerequisites"></a>Requisitos previos del equipo protegido

**Requisito previo** | **Detalles**
--- | ---
**Local (máquinas virtuales de VMware)** | Máquinas virtuales de VMware que desee proteger debe tener instalado y ejecuta las herramientas de VMware.<br/><br/> Equipos que desee proteger deben cumplir con los [requisitos previos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) para crear máquinas virtuales de Azure.<br/><br/>Capacidad de disco individuales en equipos protegidas no debería ser más de 1023 GB. Una máquina virtual puede tener hasta 64 discos (así hasta 64 TB). <br/><br/>Mínimo 2 GB de espacio disponible en la unidad de instalación para la instalación de componente.<br/><br/>No se admite la protección de máquinas virtuales con los discos cifrados.<br/><br/>Compartir invitado disco clústeres no se admiten.<br/><br/>**Puerto 20004** se debe abrir en firewall local de la máquina virtual protegida, si desea habilitar la **coherencia de la aplicación**.<br/><br/>Equipos que tienen Unified Extensible Firmware interfaz (UEFI) / inicio Extensible Interface(EFI) Firmware no es compatible.<br/><br/>Nombres de equipo deben contener entre 1 y 63 caracteres (letras, números y guiones). El nombre debe comenzar con una letra o un número y termine con una letra o un número. Después de que se ha habilitado la replicación de un equipo puede modificar el nombre de Azure.<br/><br/>Si el origen de la máquina virtual tiene el equipo NIC se convierte a una única NIC después de la migración a Azure.<br/><br/>Si protegidas máquinas virtuales tiene un disco iSCSI sitio recuperación convierte el disco de iSCSI VM protegido en un archivo de disco duro virtual cuando la máquina virtual por error a Azure. Si el destino iSCSI se puede llegar con la máquina virtual de Azure se conectarse a él y se vea esencialmente dos discos: el disco de disco duro virtual en la máquina virtual de Azure y el disco de iSCSI de origen. En este caso deberá desconectar el destino iSCSI que aparece en la máquina virtual de Azure.
**Equipos con Windows (físico o sus)** | El equipo debe ejecutar un sistema operativo de 64 bits: Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 con al menos SP1.<br/><br/> El sistema operativo debe instalarse en la unidad C:\. El disco de sistema operativo debe ser un disco básico de Windows y no dinámicos. El disco de datos puede ser dinámico.<br/><br/>Recuperación de sitio es compatible con máquinas virtuales con un disco RDM. Durante la recuperación, sitio recuperación volverá a utilizar el disco RDM si está disponible el disco de VM y RDM origen original. Si no está disponibles, durante la recuperación recuperación del sitio creará un nuevo archivo VMDK para cada disco.
**Equipos Linux** (phyical o sus)|  Necesitará un sistema operativo de 64 bits: 6.7,7.1,7.2 rojos sombrero Enterprise Linux; CentOS 6.5, 6.6,6.7,7.0,7.1,7.2; Oracle Enterprise Linux 6.4, 6.5 ejecutando rojos sombrero compatible kernel o Unbreakable Enterprise Kernel versión 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>archivos / etc/hosts en equipos protegidos deben contener entradas que asignan el nombre de host local a direcciones IP asociadas con todos los adaptadores de red.<br/><br/>Si desea conectarse a una máquina virtual Azure ejecutan Linux después de la migración con un Secure Shell cliente (ssh), asegúrese de que el servicio de Shell seguro en el equipo protegido está establecido para iniciar automáticamente en el inicio del sistema, y que las reglas de firewall permiten una ssh conexión a ella.<br/><br/>El nombre de host, puntos de montaje, nombres de dispositivo y rutas de acceso de sistema de Linux y nombres de archivo (por ejemplo, / etcetera /, / usr) solo deben estar en inglés.<br/><br/>Protección solo puede activarse para equipos Linux con el almacenamiento siguiente: archivos de sistema (EXT3, ETX4, ReiserFS, XFS); Múltiples rutas software el dispositivo asignador (múltiples rutas)) Administrador de volumen: (LVM2). Servidores físicos con almacenamiento del controlador HP CCISS no son compatibles. El sistema de archivos ReiserFS sólo se admite en SUSE Linux Enterprise Server 11 SP3.<br/><br/>Recuperación de sitio es compatible con máquinas virtuales con un disco RDM.  Durante la recuperación para Linux, recuperación de sitios no volver a usar el disco RDM. En cambio, crea un nuevo archivo VMDK para cada disco RDM correspondiente.<br/><br/>Asegúrese de establecer la configuración de disk.enableUUID=true en los parámetros de configuración de la máquina virtual en VMware. Si no existe, cree la entrada. Es necesaria para proporcionar un UUID coherente para el VMDK para que lo montajes correctamente. Agregar esta configuración también se garantiza que los cambios delta solo se transfieren a la implementación local durante la recuperación y no una réplica completa.
**Servicio de movilidad** |  **Windows**: deberá para actualizar automáticamente el servicio de movilidad en máquinas virtuales de Windows, tendrá que proporcionar una cuenta de administrador (Administrador local en el equipo de Windows) para que el servidor de procesos puede realizar una instalación remota.<br/><br/>**Linux**: insertar automáticamente el servicio de movilidad en máquinas virtuales con Linux, deberá crear una cuenta que puede ser usada por el servidor de proceso para realizar una instalación remota.<br/><br/> De forma predeterminada se duplican todos los discos en un equipo. Para [excluir un disco de la replicación](#exclude-disks-from-replication), el servicio de movilidad debe instalarse manualmente en el equipo antes de habilitar la replicación.<br/>

## <a name="prepare-for-deployment"></a>Prepararse para su implementación

Prepararse para la implementación, que tendrá que:

1. [Configurar una red de Azure](#set-up-an-azure-network) en la que máquinas virtuales de Azure se encuentran cuando haya terminado después de la migración. Además, para recuperación debe configurar una conexión VPN (o Azure ExpressRoute) desde la red de Azure a su sitio local.
2. [Configurar una cuenta de Azure almacenamiento](#set-up-an-azure-storage-account) de datos duplicados.
3. [Preparar una cuenta](#prepare-an-account-for-automatic-discovery) en el servidor de vCenter o vSphere aloja para que el sitio de recuperación puede detectar automáticamente las máquinas virtuales de VMware que se agregan.
4. [Preparar el servidor de configuración](#prepare-the-configuration-server) para garantizar que puede tener acceso a las direcciones URL necesarias e instalar vSphere PowerCLI 6.0.


### <a name="set-up-an-azure-network"></a>Configurar una red de Azure

- La red debe estar en la misma región Azure que en el que podrá implementar el depósito de servicios de recuperación.
- Dependiendo del modelo de recursos que desea usar para error sobre máquinas virtuales de Azure, deberá configurar la red de Azure en [modo de BRAZO](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o en [modo clásico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Para un error de Azure a su sitio de VMware local se necesita una conexión VPN (o una conexión de Azure ExpressRoute) desde la red de Azure en la que se encuentra a la red local en el que se encuentra el servidor de configuración las máquinas virtuales de Azure replicada.
- [Obtenga más información acerca de](../vpn-gateway/vpn-gateway-site-to-site-create.md) la implementación compatible modelos para conexiones de sitio a sitio VPN y cómo [Configurar una conexión](../vpn-gateway/vpn-gateway-site-to-site-create.md#create-your-virtual-network).
- También puede configurar [Azure ExpressRoute](../expressroute/expressroute-introduction.md). [Más información](../expressroute/expressroute-howto-vnet-portal-classic.md) sobre cómo configurar una red Azure ExpressRoute.

> [AZURE.NOTE] [Migración de redes](../resource-group-move-resources.md) entre los grupos de recursos en la misma suscripción o a través de suscripciones no es compatible con redes usadas para implementar el sitio de recuperación.

### <a name="set-up-an-azure-storage-account"></a>Configurar una cuenta de almacenamiento de Azure

- Necesitará una estándar o a una cuenta de almacenamiento de Azure premium para almacenar datos duplicados en Azure. La cuenta debe estar en la misma región como depósito de servicios de recuperación. Dependiendo del modelo de recursos que desea usar para error sobre máquinas virtuales de Azure, deberá configurar una cuenta en [modo de BRAZO](../storage/storage-create-storage-account.md) o en [modo clásico](../storage/storage-create-storage-account-classic-portal.md).
- Si usa una cuenta de premium para datos duplicados, que deberá crear una cuenta adicional estándar para almacenar registros de replicación que capturan los cambios en curso a datos locales.  

> [AZURE.NOTE] No se admite la [migración de cuentas de almacenamiento](../resource-group-move-resources.md) en grupos de recursos en la misma suscripción o en suscripciones para las cuentas de almacenamiento usadas para implementar el sitio de recuperación.

### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar una cuenta para la detección automática

El servidor de procesos de recuperación del sitio puedan detectar automáticamente máquinas virtuales de VMware vSphere hosts o en un servidor de vCenter que administra hosts. Para llevar a cabo automática detección sitio recuperación de credenciales que puede tener acceso al servidor de VMware. Esto no es relevante si está replicar sólo equipos físicos.

1. Para usar una cuenta dedicada para la detección automática de crear una función (por ejemplo, Azure_Site_Recovery) en el nivel de vCenter con los [permisos necesarios](#vmware-account-permissions).
2. Crear un nuevo usuario en el servidor de host o vCenter vSphere y asignar el rol al usuario. Más adelante le permiten sitio recuperación conocer estas credenciales para que pueda realizar la detección automática.

    >[AZURE.NOTE] Una cuenta de usuario de vCenter con una función de sólo lectura puede ejecutar la migración tras error, pero no se puede cerrar equipos de origen protegida. Si desea cerrar esos equipos tendrá la función [Azure_Site_Recovery](#vmware-account-permissions) . Si solo migra máquinas virtuales de VMware a Azure y recuperación no necesita la función de sólo lectura es suficiente.

### <a name="prepare-the-configuration-server"></a>Preparar el servidor de configuración

1.  Asegúrese de que el equipo que usa para el servidor de configuración cumple los [requisitos previos](#configuration-server-prerequisites). En particular, asegúrese de que el equipo está conectado a internet con estas opciones:

    - Permitir el acceso a estas direcciones URL: *. hypervrecoverymanager.windowsazure.com; *. AccessControl.Windows.NET; *. backup.windowsazure.com; *. BLOB.Core.Windows.NET; *. store.core.windows.net
    - Permitir el acceso a [http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi) para descargar MySQL.
    - Permitir la comunicación de firewall para Azure con los [intervalos de IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el protocolo HTTPS (443).

2.  Descargar e instalar [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) en el servidor de configuración. (Actualmente otras versiones de PowerCLI no son compatibles, incluidas las versiones de R de la versión 6.0.)


## <a name="create-a-recovery-services-vault"></a>Crear un depósito de servicios de recuperación

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
2. Haga clic en **nuevo** > **administración** > **copia de seguridad y recuperación de sitios (OMS)**. También puede hacer clic en **Examinar** > **Depósito de servicios de recuperación** > **Agregar**.

    ![Nuevo depósito](./media/site-recovery-vmware-to-azure/new-vault3.png)

3. En **nombre** , especifique un nombre descriptivo para identificar la cámara. Si tiene más de una suscripción, seleccione uno de ellos.
4. [Crear un nuevo grupo de recursos](../resource-group-template-deploy-portal.md) o seleccione uno existente. Especificar una región de Azure. Máquinas se reflejarán en esta área. Tenga en cuenta que almacenamiento de Azure y redes utilizadas para la recuperación de sitios tendrán que estar en la misma región. Para comprobar regiones admitidas vea disponibilidad geográfica en [Detalles de precios de Azure sitio recuperación](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Si desea obtener acceso rápidamente a la cámara del panel haga clic en **Anclar al panel** y, a continuación, haga clic en **crear**.

    ![Nuevo depósito](./media/site-recovery-vmware-to-azure/new-vault-settings.png)

El nuevo depósito aparecerá en el **panel** > **todos los recursos**y en los principales módulos de **Servicios de recuperación de depósitos** .

## <a name="getting-started"></a>Introducción

Recuperación de sitio proporciona una experiencia de introducción diseñado para poder empezar a y ejecutar tan rápido como sea posible. Comprueba los requisitos previos y le guiará por los pasos que necesarios para obtener la recuperación de sitio implementado.

Seleccione el tipo de equipos desea replicar y, a continuación, en la que desee replicar en. Configurar la infraestructura, incluidos los servidores locales de, configuración de Azure, las políticas de replicación y planificación de capacidad. Después de la infraestructura de habilitar la replicación de máquinas virtuales y servidores físicos. A continuación, puede ejecutar migraciones tras error para equipos específicos o crear planes de recuperación para conmutar varios equipos.

Comenzar introducción, elija cómo desea implementar el sitio de recuperación. El flujo de introducción cambia ligeramente dependiendo de los requisitos de replicación.


## <a name="step-1-choose-your-protection-goals"></a>Paso 1: Seleccionar los objetivos de protección

Seleccione lo que desea replicar y donde desea replicar en.

1. En el módulo de **Servicios de recuperación de depósitos** seleccione su cámara y haga clic en **configuración**.
2. En la **configuración de** > **Introducción** haga clic en el **Sitio de recuperación** > **paso 1: preparar la infraestructura** > **objetivo de protección**.

    ![Elija los objetivos](./media/site-recovery-vmware-to-azure/choose-goals.png)

3. En el **objetivo de protección** seleccione **A Azure**y seleccione **Sí, con VMware vSphere hipervisor**. A continuación, haga clic en **Aceptar**.

    ![Elija los objetivos](./media/site-recovery-vmware-to-azure/choose-goals2.png)


## <a name="step-2-set-up-the-source-environment"></a>Paso 2: Configurar el entorno de origen

Configurar el servidor de configuración y registrar en el almacén de servicios de recuperación. Si va a replicar máquinas virtuales de VMware especificar la cuenta de VMware que se usa para la detección automática.

1. Haga clic en **paso 1: preparar la infraestructura de** > **origen**. En **Preparar origen** si no tiene un servidor de configuración haga clic en **+ servidor de configuración** para agregar una.

    ![Configurar origen](./media/site-recovery-vmware-to-azure/set-source1.png)

2. En la comprobación del módulo de **Servidor agregar** ese **Servidor configuración** aparece en **tipo de servidor**.
3. Antes de configurar el servidor de configuración, compruebe [los requisitos previos](#configuration-server-prerequisites). En concreto, compruebe que el equipo puede tener acceso a las direcciones URL necesarias.
4.  Descargar el archivo de instalación de la configuración del sitio recuperación unificada.
5.  Descargue la clave de registro de la cámara. Necesitará esta al ejecutar la configuración unificada. La clave es válida durante 5 días después de que genere.

    ![Configurar origen](./media/site-recovery-vmware-to-azure/set-source2.png)

6.  En el equipo que se usa como el servidor de configuración, ejecute la instalación unificada para instalar el servidor de configuración, el servidor de procesos y el servidor de destino principal.


### <a name="run-site-recovery-unified-setup"></a>Ejecución de sitio recuperación unificada configuración

1.  Ejecute el archivo de instalación de instalación unificada.
2.  Antes de **empezar** , seleccione **instalar el servidor de configuración y proceso**.

    ![Antes de empezar](./media/site-recovery-vmware-to-azure/combined-wiz1.png)

3. **Licencia del Software de terceros** haga clic en **** para descargar e instalar MySQL.

    ![Tercero = software de terceros](./media/site-recovery-vmware-to-azure/combined-wiz105.PNG)

4. **Registro** en Examinar y seleccione la clave de registro que ha descargado desde la cámara.

    ![Registro](./media/site-recovery-vmware-to-azure/combined-wiz3.png)

5. En la **Configuración de Internet** , especifique cómo se conectará el proveedor se ejecuta en el servidor de configuración para la recuperación de sitios de Azure a través de internet.

    - Si desea conectar con el servidor proxy que está actualmente configurado en el equipo, seleccione **Conectar con la configuración de proxy existente**.
    - Si desea que el proveedor para conectar directamente, seleccione **Conectar directamente sin un servidor proxy**.
    - Si el servidor proxy existente requiere autenticación o que desea usar a un proxy personalizado para la conexión del proveedor, seleccione **Conectar con la configuración de proxy personalizada**.
        - Si usa a un proxy personalizado, que deberá especificar la dirección y puerto credenciales
        - Si usa a un servidor proxy debe han permitido ya las direcciones URL que se describe en [los requisitos previos](#configuration-server-prerequisites).

    ![Firewall](./media/site-recovery-vmware-to-azure/combined-wiz4.png)

6. En **Comprobación de requisitos previos** instalación ejecuta una comprobación para asegurarse de que puede ejecutar la instalación. Si aparece una advertencia sobre la **Active la sincronización de tiempo Global** Compruebe que la hora en el reloj del sistema (configuración de**fecha y hora** ) es igual a la zona horaria.

    ![Requisitos previos](./media/site-recovery-vmware-to-azure/combined-wiz5.png)

7. En **Configuración de MySQL** crear credenciales para iniciar sesión en la instancia de servidor MySQL que se van a instalar.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)

8. En seleccionar **Detalles del entorno** de si va a replicar VM VMware. Si no, la instalación comprueba que PowerCLI 6.0 está instalado.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz7.png)

9. En **Ubicación de instalación** , seleccione dónde desea instalar los archivos binarios y almacenar la memoria caché. Puede seleccionar una unidad que tenga al menos 5 GB de espacio de almacenamiento, pero se recomienda una unidad de caché con al menos 600 GB de espacio libre.

    ![Ubicación de instalación](./media/site-recovery-vmware-to-azure/combined-wiz8.png)

10. En **Selección de red** , especifique la escucha (adaptador de red y puerto SSL) en el que el servidor de configuración enviar y recibir datos de replicación. Puede modificar el valor predeterminado puerto (9443). Además de este puerto, se usará el puerto 443 de un servidor web que dirige las operaciones de replicación. 443 no debe usarse para recibir el tráfico de replicación.


    ![Selección de red](./media/site-recovery-vmware-to-azure/combined-wiz9.png)



11.  En **Resumen** , revise la información y haga clic en **instalar**. Cuando finaliza la instalación se genera una frase de contraseña. Deberá cuando se habilita la replicación copiarla y mantener en una ubicación segura.

    ![Resumen](./media/site-recovery-vmware-to-azure/combined-wiz10.png)

12.  Después de que finalice el registro del servidor se muestra en la **configuración de** > módulo de**servidores** de la cámara.



#### <a name="run-setup-from-the-command-line"></a>Ejecutar el programa de instalación desde la línea de comandos

Puede configurar el servidor de configuración de la línea de comandos:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Parámetros:

- / ServerMode: obligatorio. Especifica si se deben instalar servidores de la configuración y el proceso o solo el servidor de procesos. Valores de entrada: CS, PS.
- InstallLocation: obligatorio. La carpeta donde se instalan los componentes.
- / MySQLCredsFilePath. Obligatorio. La ruta de acceso de archivo en el que se almacenan las credenciales del servidor MySQL. El archivo debe estar en este formato:
    - [MySQLCredentials]
    - MySQLRootPassword = "<Password>"
    - MySQLUserPassword = "<Password>"
- / VaultCredsFilePath. Obligatorio. La ubicación del archivo de credenciales de cámara
- / EnvType. Obligatorio. El tipo de instalación. Valores: VMware, NonVMware
- / PSIP y /CSIP. Obligatorio. La dirección IP del servidor de procesos y del servidor de configuración.
- / PassphraseFilePath. Obligatorio. La ubicación del archivo de frase de contraseña.
- / BypassProxy. Opcional. Especifica que el servidor de configuración se conecta a Azure sin un servidor proxy.
- / ProxySettingsFilePath. Opcional. Configuración de proxy (el proxy predeterminado requiere autenticación o un proxy personalizado). El archivo debe estar en este formato:
    - [ProxySettings]
    - ProxyAuthentication = "Sí/No"
    - Proxy IP = "dirección IP >"
    - ProxyPort = "<Port>"
    - ProxyUserName = "<User Name>"
    - ProxyPassword = "<Password>"
- DataTransferSecurePort. Opcional. Número de puerto que se usará para los datos de replicación.
- SkipSpaceCheck. Opcional. Omitir comprobación de espacio para la caché.
- AcceptThirdpartyEULA. Obligatorio. Marca implica la aceptación de CLUF de terceros.
- ShowThirdpartyEULA. Obligatorio. Muestra el CLUF de terceros. Si se proporciona como entrada se omiten todos los demás parámetros.

### <a name="add-the-vmware-account-used-for-automatic-discovery"></a>Agregue la cuenta de VMware utilizada para la detección automática

 Al preparar para su implementación debería tener [creado una cuenta de VMware](#prepare-an-account-for-automatic-discovery) que recuperación de sitios pueden utilizar la detección automática. Agregar esta cuenta como sigue:

1. Abra **CSPSConfigtool.exe**. Está disponible como un acceso directo en el escritorio y se encuentra en la carpeta de \home\svsystems\bin [ubicación de instalación].
2. Haga clic en **Administrar cuentas** > **Agregar cuenta**.

    ![Agregar cuenta](./media/site-recovery-vmware-to-azure/credentials1.png)

3. En **Detalles de la cuenta** , agregue la cuenta que se usará para la detección automática. Tenga en cuenta que puede tardar 15 minutos o más para el nombre de cuenta que aparezca en el portal. Para actualizar inmediatamente, haga clic en **Configuración de servidores** > nombre de servidor > **Actualizar el servidor**.

    ![Detalles](./media/site-recovery-vmware-to-azure/credentials2.png)

### <a name="connect-to-vsphere-hosts-and-vcenter-servers"></a>Conectarse a servidores de vCenter y vSphere hosts

Si va a replicar máquinas virtuales de VMware conectarse a los servidores de vCenter y vSphere hosts.

1. Compruebe que el servidor de configuración tiene acceso a la red al vSphere anfitriones y los servidores de vCenter.
2. Haga clic en **Preparar infraestructura** > **origen**. **Origen de la preparación** de seleccionar el servidor de configuración y haga clic en **+ vCenter** para agregar un servidor de host o vCenter vSphere.
3. En **Agregar vCenter** , especifique un nombre descriptivo para el servidor de host o vCenter vSphere y especifique la dirección IP o FQDN del servidor. Deje el puerto 443 a menos que los servidores de VMware están configurados para escuchar las solicitudes en un puerto diferente. A continuación, seleccione la cuenta que se usará para conectarse al servidor de VMware. Haga clic en **Aceptar**.

    ![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

    >[AZURE.NOTE] Si va a agregar el servidor de vCenter o host de vSphere con una cuenta que no tiene privilegios de administrador en el servidor vCenter o host, a continuación, asegúrese de que la cuenta tiene estos privilegios habilitados: Centro de datos, almacén de datos, carpeta, Host, red, recursos, Virtual de máquina, vSphere distribuido cambiar. Además, el servidor de vCenter tiene los privilegios de vistas de almacenamiento.

Recuperación de sitio se conecta a los servidores de VMware con la configuración que haya especificado y descubre máquinas virtuales.

## <a name="step-3-set-up-the-target-environment"></a>Paso 3: Configurar el entorno de destino

Compruebe que tiene una cuenta de almacenamiento para la replicación y una red de Azure al que se conectará máquinas virtuales de Azure después de la migración.

1.  Haga clic en **Preparar infraestructura** > **destino** y seleccione la suscripción de Azure que desea usar.
2.  Especifique el modelo de implementación que desea usar para máquinas virtuales después de la migración.
3.  Recuperación de sitio comprueba que tiene una o varias cuentas de almacenamiento de Azure compatible y redes.

    ![Destino](./media/site-recovery-vmware-to-azure/gs-target.png)

4.  Si no ha creado una cuenta de almacenamiento y desea crear uno utilizando ARM haga clic en **+ cuenta de almacenamiento** para hacer que en línea.  En el módulo de **crear cuenta de almacenamiento** , especifique un nombre de cuenta, el tipo, la suscripción y la ubicación. La cuenta debe estar en la misma región como depósito de servicios de recuperación.

    ![Almacenamiento de información](./media/site-recovery-vmware-to-azure/gs-createstorage.png)

    Tenga en cuenta que:

    - Si desea crear una cuenta de almacenamiento mediante el modelo clásico puede hacer en el portal de Azure. [Aprende más](../storage/storage-create-storage-account-classic-portal.md)
    - Si usa una cuenta de almacenamiento premium para datos duplicados, a que tendrá que configurar una cuenta de almacenamiento adicional de estándar replicación de almacén de registros que captura cambios realizados a datos locales.

    > [AZURE.NOTE] Protección de cuentas de almacenamiento premium en la India Central y India sur no es compatible actualmente.

4.  Seleccione una red de Azure. Si no ha creado una red y desea hacerlo usando ARM haga clic en **+ red** para hacer que en línea. En el módulo de **crear una red virtual** especifique un nombre de red, intervalo de direcciones, detalles de subred, suscripción y ubicación. La red debe estar en la misma ubicación que la cámara de servicios de recuperación.

    ![Red](./media/site-recovery-vmware-to-azure/gs-createnetwork.png)

    Si desea crear una red de uso del modelo clásico puede hacer en el portal de Azure. [Más información](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

## <a name="step-4-set-up-replication-settings"></a>Paso 4: Configurar las opciones de replicación

1. Para crear una nueva réplica directiva, haga clic en **Preparar infraestructura** > **Opciones de replicación** > **+ crear y asociar**.
2. En **crear y asociar directiva** especifique un nombre de la directiva.
3. En el **umbral RPO**: especifique el límite RPO. Cuando la replicación continua supera este límite, se generará alertas.
5. En **el punto de recuperación retención**, especificar en horas cuánto que será la ventana de retención para cada punto de recuperación. Equipos protegidas se pueden recuperar a cualquier punto dentro de una ventana. Es compatible hasta retención de 24 horas para equipos replicados en almacenamiento premium.
6. En la **frecuencia de instantáneas coherentes para la aplicación**, especifique la frecuencia (en minutos) se crearán puntos de recuperación que contiene Dr.
7. Cuando se crea una directiva de replicación, de forma predeterminada una directiva coincidente se crea automáticamente para la recuperación. Por ejemplo, si la directiva de replicación es **representante de directiva** , la directiva de recuperación se **Directiva de representante de recuperación**. No se usa esta directiva hasta que se inicie una recuperación.  
8. Haga clic en **Aceptar** para crear la directiva.

    ![Directiva de replicación](./media/site-recovery-vmware-to-azure/gs-replication2.png)

9. Cuando se crea una nueva directiva automáticamente está asociada con el servidor de configuración. Haga clic en **Aceptar**.

    ![Directiva de replicación](./media/site-recovery-vmware-to-azure/gs-replication3.png)


## <a name="step-5-capacity-planning"></a>Paso 5: Planear la capacidad

Ahora que ya tiene su basic infraestructura configurada por el usuario puede pensar planeamiento de capacidad y averiguar si necesita recursos adicionales.

Recuperación de sitio proporciona un planificador de capacidad para ayudarle a asignar los recursos adecuados para su entorno de origen, los componentes de recuperación de sitio, redes y almacenamiento. Puede ejecutar el organizador en el modo rápido para realizar cálculos en función de un número medio de máquinas virtuales, discos y almacenamiento o en el modo detallado en la que deberá introduce las cifras en el nivel de carga de trabajo. Antes de empezar necesitará:

- Recopilar información acerca de su entorno de replicación, incluidos VM, discos por máquinas virtuales y almacenamiento por disco.
- Estimar la tasa cambio (renovación) diarias que tendrá datos duplicados. Puede usar el [dispositivo de planificación de la capacidad de vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) para ayudarle a hacerlo.

1.  Haga clic en **Descargar** para descargar la herramienta y ejecútelo. [Lea el artículo](site-recovery-capacity-planner.md) que acompaña a la herramienta.
2.  Cuando haya terminado seleccionar **Sí** **haya completado el planeamiento de capacidad?**

    ![Planificación de capacidad](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)

La tabla siguiente recoge a un número de puntos para ayudarle con la capacidad de planificación de este escenario.


**Componente** | **Detalles**
--- | --- | ---
**Replicación** | **Tasa de cambio de máximo diariamente**: un equipo protegido solo puede usar un servidor de procesos y un servidor de un solo proceso puede controlar diaria velocidad de cambio de 2 TB. Así 2 TB es que los datos diarios máximos cambiarán tasa que se admite para un equipo protegido.<br/><br/> **Rendimiento máximo**: un equipo replicado puede pertenecer a una cuenta de almacenamiento en Azure. Una cuenta de almacenamiento estándar puede administrar un máximo de 20.000 solicitudes por segundo y, a continuación, le recomendamos que mantenga el número de IOPS a través de un equipo de origen a 20.000. Por ejemplo si tiene un equipo de origen con 5 discos y cada genera 120 IOPS (tamaño de 8 KB) en el origen estará dentro de la Azure por límite de 500 IOPS en disco. El número de cuentas de almacenamiento necesarias = origen total IOPs/20000.
**Servidor de configuración** | El servidor de configuración debería poder administrar la capacidad de tasa de cambio diaria para todas las cargas de trabajo en ejecución en equipos protegidos y necesita suficiente ancho de banda para replicar continuamente los datos a almacenamiento de Azure.<br/><br/> Como práctica recomendada, se recomienda que el servidor de configuración se encuentra en la misma red y el segmento de LAN como los equipos que desea proteger. Puede estar ubicado en una red diferente, pero los equipos que desee proteger deben tener visibilidad de red nivel 3.<br/><br/> Recomendaciones de tamaño para el servidor de configuración se resumen en la tabla siguiente.
**Servidor de procesos** | El primer servidor de proceso está instalado en el servidor de configuración de forma predeterminada. Puede implementar servidores de procesos adicionales para ajustar el tamaño de su entorno. Tenga en cuenta que:<br/><br/> El servidor de procesos recibe datos de replicación de equipos protegidas y lo optimiza con almacenamiento en caché, compresión y cifrado antes de enviar a Azure. El equipo de servidor de proceso debe tener recursos suficientes para realizar estas tareas.<br/><br/> El servidor de procesos utiliza la caché basada en disco. Se recomienda un disco de caché independiente de 600 GB o más para controlar los cambios en los datos almacenados botella en la red o una interrupción.

### <a name="size-recommendations-for-the-configuration-server"></a>Recomendaciones de tamaño para el servidor de configuración

**CPU** | **Memoria** | **Tamaño de caché de disco** | **Tasa de cambio de datos** | **Máquinas protegidos**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz) | 16 GB | 300 GB | 500 GB o menos | Replicar menos de 100 equipos.
12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz) | 18 GB | 600 GB | 500 GB hasta 1 TB | Replicar entre 100-150 equipos.
16 vCPUs (2 sockets * 8 núcleos @ 2,5 GHz) | 32 GB | 1 TB | 1 TB a 2 TB | Replicar entre equipos de 150-200.
Implementar otro servidor de proceso | | | > 2 TB | Implementar servidores de proceso adicional si está replicar más de 200 equipos, o si cambian los datos diarios tasa supera 2 TB.

Donde:

- Cada equipo de origen está configurado con 3 discos de 100 GB.
- Hemos usado el almacenamiento de pruebas comparativas de 8 unidades SA de 10 KB RPM con RAID 10 para las medidas de disco de caché.

### <a name="size-recommendations-for-the-process-server"></a>Recomendaciones de tamaño para el servidor de proceso

Si necesita proteger máquinas más de 200 o tasa de cambio diaria es mayor que 2 TB puede agregar servidores de proceso adicional para controlar la carga de replicación. Para escalar hacer lo siguiente:

- Aumentar el número de servidores de configuración. Por ejemplo, si desea proteger hasta 400 máquinas con dos servidores de configuración.
- Agregar servidores de proceso adicional y usarlos para controlar el tráfico en lugar de (o además de) el servidor de configuración.

Esta tabla describe un escenario en el que:

- No va a usar el servidor de configuración como un servidor de procesos.
- Ha configurado un servidor de proceso adicional.
- Configurar la protegida máquinas virtuales de Windows para usar el servidor de procesos adicionales.
- Cada equipo de la fuente de protección está configurado con tres discos de 100 GB.

**Servidor de configuración** | **Servidor de procesos adicionales**| **Tamaño de caché de disco** | **Tasa de cambio de datos** | **Máquinas protegidos**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz), 16 GB de memoria | 4 vCPUs (2 sockets * 2 núcleos @ 2,5 GHz), 8 GB de memoria | 300 GB | 250 GB o menos | Replicar 85 o menos equipos.
8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz), 16 GB de memoria | 8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz), 12 GB de memoria | 600 GB | 250 GB hasta 1 TB | Replicar entre máquinas 85-150.
12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz), 18 GB de memoria | 12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz) 24 GB de memoria | 1 TB | 1 TB a 2 TB | Replicar entre equipos de 150-225.


La manera en que escalar los servidores dependen de su preferencia de una escala de o ajustar el modelo.  Escalar implementando unos servidores de proceso y configuración de gama alta o escalar implementando más servidores con menos recursos. Por ejemplo: si necesita proteger 220 máquinas podría hacer lo siguiente:

- Configurar el servidor de configuración con 12vCPU, 18 GB de memoria, un servidor de proceso adicionales con 12vCPU, 24 GB de memoria y configurar equipos protegidas para usar solo el servidor de procesos adicionales.
- También podría configurar dos servidores de configuración (8vCPU de 2 x, 16 GB de RAM) y dos servidores de proceso adicional (1 x 8vCPU) y 4vCPU x 1 manejar 135 + 85 máquinas (220) y configurar los equipos protegidos para usar los servidores de proceso adicional solo.

[Siga estas instrucciones](#deploy-additional-process-servers) para configurar un servidor de proceso adicional.

### <a name="network-bandwidth-considerations"></a>Consideraciones de ancho de banda de red

Puede usar la herramienta de planificación de capacidad para calcular el ancho de banda que necesario para la replicación (replicación inicial y, a continuación, delta). Para controlar la cantidad de uso de ancho de banda de replicación tiene varias opciones:

- **Limitar ancho de banda**: tráfico de VMware que se aplica a Azure pasa a través de un servidor de proceso específico. También puede limitar el ancho de banda en los equipos que ejecutan como servidores de procesos.
- **Influir en ancho de banda**: puede influir en el ancho de banda que se utiliza para la replicación con un par de claves de registro:
    - El valor del registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** especifica el número de subprocesos que se usan para la transferencia de datos (replicación inicial o delta) de un disco. Un valor más alto aumenta el ancho de banda de red que utiliza para la replicación.
    - La **Backup\DownloadThreadsPerVM de Azure HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows** especifica el número de subprocesos utilizados para transferir datos durante la recuperación.

#### <a name="throttle-bandwidth"></a>Limitar ancho de banda

1. Abra el complemento MMC de copias de seguridad de Microsoft Azure en el equipo que actúe como el servidor de procesos. De forma predeterminada, un método abreviado para la copia de seguridad de Microsoft Azure está disponible en el escritorio o en Agent\bin\wabadmin de servicios de recuperación de C:\Program Files\Microsoft Azure.
2. En el complemento, haga clic en **Cambiar las propiedades**.

    ![Limitar ancho de banda](./media/site-recovery-vmware-to-azure/throttle1.png)

3. En la pestaña **regulación** seleccione **Habilitar límite para operaciones de copia de seguridad de uso de ancho de banda de internet**y establecer los límites de trabajo y no-trabajo de horas. Rangos válidos son entre 512 a 102 Mbps por segundo.

    ![Limitar ancho de banda](./media/site-recovery-vmware-to-azure/throttle2.png)

También puede usar el cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para establecer el límite. Este es un ejemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Conjunto OBMachineSetting NoThrottle** indica que no se requiere ninguna limitación.


#### <a name="influence-network-bandwidth"></a>Influir en el ancho de banda de red

1. En el registro navegue **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Backup\Replication de Azure**.
    - Para influir en el tráfico de ancho de banda en un disco replicación, modifique el valor de la **UploadThreadsPerVM**o cree la clave, si no existe.
    - Para influir en el ancho de banda para el tráfico de recuperación de Azure, modifique el valor **DownloadThreadsPerVM**.
2. El valor predeterminado es 4. En una red "overprovisioned", se deben cambiar estas claves de registro de los valores predeterminados. El máximo es 32. Supervisar el tráfico para optimizar el valor.

## <a name="step-6-replicate-applications"></a>Paso 6: Replicar aplicaciones

Asegúrese de que están preparados para la instalación del servicio de movilidad máquinas que desea replicar y habilitar la replicación.

### <a name="install-the-mobility-service"></a>Instalar el servicio de movilidad

Es el primer paso para habilitar la protección de máquinas virtuales y servidores físicos instalar el servicio de movilidad. Puede hacerlo de dos maneras:

- **Inserción de servidor de proceso**: al habilitar la replicación en un equipo, notificaciones e instale el componente de servicio de movilidad desde el servidor de procesos. Tenga en cuenta que la instalación remota no se producirá si máquinas ya están ejecutando una versión de todate arriba del componente.
- **Inserción de Enterprise**: instalar automáticamente el componente mediante el proceso de inserción de enterprise como WSUS o administrador de configuración de System Center o [automatización de Azure y configuración de estado deseado](./site-recovery-automate-mobility-service-install.md). Configurar el servidor de configuración antes de hacerlo.
- **Instalación manual**: instale el componente de forma manual en cada equipo que desea duplicar. Configurar el servidor de configuración antes de hacerlo.


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Prepararse para la inserción automática en equipos con Windows

Aquí le mostramos cómo preparar los equipos de Windows para que el servidor de procesos puede instalarse automáticamente el servicio de movilidad.

1.  Crear una cuenta que puede ser usada por el servidor de procesos para acceder al equipo. La cuenta debe tener privilegios de administrador (local o dominio) y solo se utiliza para la instalación de inserción.

    >[AZURE.NOTE] Si no usa una cuenta de dominio debe deshabilitar el control de acceso remoto al usuario en el equipo local. Para ello, en el registro en HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System agregar la entrada DWORD LocalAccountTokenFilterPolicy con un valor de 1. Para agregar la entrada del registro de un tipo CLI **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  En el Firewall de Windows del equipo que desea proteger, seleccione **Permitir una aplicación o una característica a través del Firewall**. Habilitar el **uso compartido de impresoras y archivos** e **Instrumentación de administración de Windows**. Puede configurar la configuración del firewall con un GPO de equipos que pertenecen a un dominio.

    ![Configuración del Firewall](./media/site-recovery-vmware-to-azure/mobility1.png)

2. Agregue la cuenta que creó:

    - Abra **cspsconfigtool**. Está disponible como un acceso directo en el escritorio y se encuentra en la carpeta de \home\svsystems\bin [ubicación de instalación].
    - En la pestaña **Administrar cuentas** , haga clic en **Agregar cuenta**.
    - Agregue la cuenta que creó. Después de agregar la cuenta debe proporcionar las credenciales al habilitar la replicación de un equipo.


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Prepararse para la inserción automática en servidores Linux

1.  Asegúrese de que el equipo de Linux que desea proteger se admite como se describe en [los requisitos previos del equipo protegido](#protected-machine-prerequisites). Asegúrese de que haya conectividad entre el equipo de Linux y el servidor de procesos de red.

2.  Crear una cuenta que puede ser usada por el servidor de procesos para acceder al equipo. La cuenta debe ser un usuario de raíz del servidor de origen Linux y solo se utiliza para la instalación de inserción.

    - Abra **cspsconfigtool**. Está disponible como un acceso directo en el escritorio y se encuentra en la carpeta de \home\svsystems\bin [ubicación de instalación].
    - En la pestaña **Administrar cuentas** , haga clic en **Agregar cuenta**.
    - Agregue la cuenta que creó. Después de agregar la cuenta debe proporcionar las credenciales al habilitar la replicación de un equipo.

3.  Compruebe que el archivo/etc/hosts del servidor de origen Linux contiene entradas que asignan el nombre de host local a direcciones IP asociadas con todos los adaptadores de red.
4.  Instalar la última openssh, servidor openssh, openssl paquetes en el equipo que desea duplicar.
5.  Asegúrese de que SSH está habilitado y en ejecución en el puerto 22.
6.  Habilitar la autenticación de SFTP subsistema y la contraseña del archivo sshd_config como sigue:

    - Inicie sesión como raíz.
    - En el archivo de /etc/ssh/sshd_config archivo, busque la línea que comienza con **PasswordAuthentication**.
    - Elimine la línea y cambie el valor de **no** a **Sí**.
    - Busque la línea que comienza con **subsistema** y elimine la línea.

        ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)


### <a name="install-the-mobility-service-manually"></a>Instalar manualmente el servicio de movilidad

Los instaladores están disponibles en el servidor de configuración de **\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository C:\Program Files (x86)**.

Sistema operativo de origen | Archivo de instalación del servicio de movilidad
--- | ---
Windows Server (64 bits sólo) | ASR_UA_9 de Microsoft. *.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6 (solo 64 bits) | ASR_UA_9 de Microsoft. *.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (solo 64 bits) | ASR_UA_9 de Microsoft. *.0.0_SLES11-SP3-64_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (solo 64 bits) | ASR_UA_9 de Microsoft. *.0.0_OL6-64_*release.tar.gz


#### <a name="install-mobility-service-on-a-windows-server"></a>Instalar el servicio de movilidad en un servidor de Windows


1. Descargar y ejecutar el programa de instalación correspondiente.
2. Antes de **empezar** , seleccione **servicio de movilidad**.

    ![Servicio de movilidad](./media/site-recovery-vmware-to-azure/mobility3.png)

3. En **Detalles de configuración de servidor** , especifique la dirección IP del servidor de configuración y la contraseña que se generó cuando ejecutó el programa de instalación unificada. Puede recuperar la frase de contraseña ejecutando: ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – v** en el servidor de configuración.

    ![Servicio de movilidad](./media/site-recovery-vmware-to-azure/mobility6.png)

4. En la **Ubicación de instalación** , deje el valor predeterminado y haga clic en **siguiente** para comenzar la instalación.
5. En el **Progreso de la instalación** supervisar la instalación y reinicie el equipo si se le solicita. Después de instalar el servicio puede tardar unos 15 minutos para estado de actualización en el portal.

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>Instalar el servicio de movilidad en un servidor de Windows mediante el símbolo del sistema

1. Copie al instalador en una carpeta local (por ejemplo, C:\Temp) en el servidor que desea proteger. El instalador puede encontrarse en el servidor de configuración en el **\home\svsystems\pushinstallsvc\repository [ubicación de instalación]**. El paquete para sistemas operativos Windows tendrá un nombre similar a Microsoft ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe
2. **Cambiar el nombre de** archivo para MobilitySvcInstaller.exe
3. Ejecute el comando siguiente para extraer el programa de instalación MSI </br>

        C:\> cd C:\Temp
        C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted
        C:\Temp> cd Extracted
        C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>

#####<a name="full-command-line-syntax"></a>Sintaxis de línea de comandos

    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**Parámetros**

- **/Role:** Obligatorio. Especifica si se debe instalar el servicio de movilidad. Agente de valores de entrada | MasterTarget
- **/InstallLocation:** Obligatorio. Especifica dónde instalar el servicio.
- **/PassphraseFilePath:** Obligatorio. La frase de contraseña del servidor de configuración.
- **/LogFilePath:** Obligatorio. Ubicación donde se deben crear los archivos de registro de instalación.



#### <a name="uninstall-mobility-service-manually"></a>Desinstalar el servicio de movilidad manualmente

Servicio de movilidad se puede desinstalar mediante el programa quitar agregar desde el Panel de Control o línea de comandos.

El comando para desinstalar el servicio de movilidad con línea de comandos

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}


#### <a name="install-mobility-service-on-a-linux-server-using-command-line"></a>Instalar el servicio de movilidad en un servidor Linux con línea de comandos

1. Copie el archivo tar adecuado basado en la tabla anterior en el equipo de Linux que desea duplicar.
2. Abra un programa de consola y extraer el archivo tar en zip a una ruta de acceso local ejecutando:`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Crear un archivo passphrase.txt en el directorio local a la que extrae el contenido del archivo tar. Para hacer esto copie la frase de contraseña de C:\ProgramData\Microsoft Azure sitio Recovery\private\connection.passphrase en el servidor de configuración y guárdelo en passphrase.txt ejecutando *`echo <passphrase> >passphrase.txt`* en el shell.
4. Instalar el servicio de movilidad ejecutando *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especificar la dirección IP interna del servidor de configuración y asegúrese de que está seleccionado el puerto 443. Después de instalar el servicio puede tardar unos 15 minutos para estado de actualización en el portal.

**También puede instalar desde la línea de comandos**:

1. Copie la frase de contraseña de \InMage Systems\private\connection C:\Program Files (x86) en el servidor de configuración y guardarlo como "passphrase.txt" en el servidor de configuración. A continuación, ejecute estos comandos. En nuestro ejemplo, la dirección IP del servidor configuración es 104.40.75.37 y el puerto HTTPS debe estar 443:

Instalar en un servidor de producción:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Instalar en el servidor de destino principal:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


### <a name="enable-replication"></a>Habilitar la replicación

#### <a name="before-you-start"></a>Antes de empezar

Si va a replicar virtual VMware máquinas tenga en cuenta lo siguiente:

- Máquinas virtuales de VMware se detectan cada 15 minutos y puede tardar 15 minutos o más para que aparezcan en el portal después de detección. Del mismo modo detección puede tardar 15 minutos o más, al agregar un nuevo host de servidor o vSphere vCenter.
- Cambios en el entorno de la máquina virtual (como la instalación de herramientas de VMware) pueden tardar 15 minutos o más se actualice en el portal.
- Puede comprobar la última vez descubierta máquinas virtuales de VMware en el **último contacto** campo para el host de servidor/vSphere vCenter en el módulo de **Servidores de configuración** .
- Para agregar equipos para la replicación sin esperar la detección programada, resalte el servidor de configuración (no haga clic en él) y haga clic en el botón **Actualizar** .
- Al habilitar la replicación, si el equipo está preparado el servidor de procesos automáticamente, instala el servicio de movilidad en él.

#### <a name="exclude-disks-from-replication"></a>Excluir discos de replicación

Al habilitar la replicación, de forma predeterminada se duplican todos los discos en un equipo. Puede excluir discos de replicación. Por ejemplo, no es recomendable replicar discos con datos temporales o los datos que se actualizan cada vez que un equipo o reinicia la aplicación (por ejemplo, pagefile.sys o tempdb de SQL Server). Si desea excluir discos tenga en cuenta que:

- Solo puede excluir discos que ya tienen instalado el servicio de movilidad. Deberá instalar [manualmente el servicio de movilidad](#install-the-mobility-service-manually) porque el servicio de movilidad sólo se instala con el sistema de inserción después de la replicación está habilitada.
- Sólo los discos básicos pueden excluir de la replicación. No puede excluir OS o discos dinámicos.
- Una vez habilitada la replicación no puede agregar o quitar discos para la replicación. Si desea agregar o excluir un disco que deberá desactivar la protección para el equipo y, a continuación, volver a habilitar.
- Si excluir un disco que se necesita para que una aplicación para que funcione, después de la migración a Azure debe crearla manualmente en Azure para que pueda ejecutar la aplicación duplicada. O bien puede integrar automatización Azure en un plan de recuperación para crear el disco durante la migración tras error del equipo.
- Error discos que crear manualmente en Azure volver. Por ejemplo si un error en tres discos y crear dos directamente en Azure, las cinco error volver. No puede excluir discos creados manualmente desde la recuperación.

**Habilitar replicación como sigue**:

1. Haga clic en **paso 2: replicar aplicación** > **origen**. Después de que se ha habilitado la replicación por primera vez deberá haga clic en **+ replicar** en el depósito al habilitar la replicación de equipos adicionales.
2. En el módulo de **origen** > **origen** seleccione el servidor de configuración.
3. En **tipo de máquina** seleccione **máquinas virtuales** o **Equipos físicos**.
4. En **vCenter/vSphere hipervisor** seleccionar el servidor vCenter que administra el host vSphere o seleccione el host. Esta configuración no es relevante si está replicar equipos físicos.
5. Seleccione el servidor de procesos. Si todavía no lo ha creado todos los servidores adicionales de proceso se trata del nombre del servidor de configuración. A continuación, haga clic en **Aceptar**.

    ![Habilitar la replicación](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. En **destino** seleccione la suscripción de la cámara y, en el **modelo de implementación de migración tras error de publicación** , seleccione el modelo (administración de recursos o clásico) que desea usar en Azure después de la migración.
7. Seleccione la cuenta de almacenamiento de Azure que usará para replicar los datos. Tenga en cuenta que:

    - Puede seleccionar una cuenta de almacenamiento estándar o premium. Si selecciona una cuenta de premium que deberá especificar una cuenta de almacenamiento adicional de estándar para los registros de replicación en curso. Cuentas deben estar en la misma región como depósito de servicios de recuperación.
    - Si desea usar una cuenta de almacenamiento diferentes a los tiene, puede [crear uno](#set-up-an-azure-storage-account). Para crear un almacenamiento cuenta mediante el modelo ARM, haga clic en **Crear nuevo**. Si desea crear una cuenta de almacenamiento mediante el modelo clásico puede hacer que [en el portal de Azure](../storage/storage-create-storage-account-classic-portal.md).

8. Seleccione la red Azure y subred al que se conectará máquinas virtuales de Azure cuando haya terminado después de la migración. La red debe estar en la misma región como depósito de servicios de recuperación. Seleccione **Configurar ahora para equipos seleccionados** para aplicar la configuración de red a todos los equipos que seleccione para la protección. Seleccione **configurar más adelante** para seleccionar la red Azure por máquina. Si no tiene una red deberá [crear uno](#set-up-an-azure-network). Para crear una red con el modelo de ARM, haga clic en **Crear nuevo**. Si desea crear una red de uso del modelo clásico puede hacer que [en el portal de Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Seleccione una subred si procede. A continuación, haga clic en **Aceptar**.

    ![Habilitar la replicación](./media/site-recovery-vmware-to-azure/enable-replication3.png)

9. En **máquinas virtuales** > **Seleccione máquinas virtuales de Windows** , haga clic en y seleccione cada equipo que desea replicar. Solo puede seleccionar equipos para el que se puede habilitar la replicación. A continuación, haga clic en **Aceptar**.

    ![Habilitar la replicación](./media/site-recovery-vmware-to-azure/enable-replication5.png)

10. En las **Propiedades** > **configurar las propiedades**, seleccione la cuenta que se utilizará automáticamente el servidor de proceso para instalar el servicio de movilidad en el equipo. De forma predeterminada se duplican todos los discos. Haga clic en **Todos los discos** y desactive los discos que no desea replicar. A continuación, haga clic en **Aceptar**. Puede establecer propiedades adicionales más adelante.

    ![Habilitar la replicación](./media/site-recovery-vmware-to-azure/enable-replication6.png)

11. En **Opciones de replicación** > **establecer la configuración de replicación** Compruebe que está seleccionada la directiva de replicación correcto. Puede modificar la configuración de la directiva de replicación de **configuración de** > **las políticas de replicación** > nombre de la directiva > **Editar configuración**. Los cambios que se aplica a una directiva se aplicarán a nuevos y replicación máquinas.

12. Habilitar la **coherencia de múltiples VM** si desea recopilar equipos en un grupo de replicación y a continuación, especifique un nombre para el grupo. A continuación, haga clic en **Aceptar**. Tenga en cuenta que:

    - Máquinas en replicación agrupan replicar y comparten los puntos de recuperación de bloqueo coherente y coherentes para la aplicación cuando no sobre.
    - Se recomienda que recopile máquinas virtuales y servidores físicos juntos para que reflejen las cargas de trabajo. Habilitar coherencia entre varios VM puede afectar al rendimiento de la carga de trabajo y solo se utiliza si máquinas están ejecutando la misma carga de trabajo y necesita coherencia.

    ![Habilitar la replicación](./media/site-recovery-vmware-to-azure/enable-replication7.png)

13. Haga clic en **Habilitar la replicación**. Puede seguir el progreso de la tarea de **Habilitar protección** en la **configuración de** > **trabajos** > **Trabajos de recuperación de sitios**. Después de que el trabajo de **Protección finalizar** ejecuta el equipo está listo para migración tras error.

> [AZURE.NOTE] Si el equipo está preparado para la instalación de inserción que se instalará el componente de servicio de movilidad cuando está habilitada la protección. Después del componente está instalado en el equipo que se inicia un trabajo de protección y se produce un error. Después del error debe reiniciar manualmente cada equipo. Después de reiniciar el trabajo de protección comienza de nuevo y se produce la replicación inicial.

### <a name="view-and-manage-vm-properties"></a>Ver y administrar propiedades VM

Le recomendamos que compruebe las propiedades de la máquina de origen. Recuerde que el nombre de Azure VM debe cumplir con los [requisitos de la máquina virtual de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Haga clic en **configuración** > **elementos replicada** > y seleccione el equipo. El módulo de **Essentials** muestra información sobre la configuración de equipos y estado.

2. En las **Propiedades** puede ver información de replicación y migración tras error de la máquina virtual.

    ![Habilitar la replicación](./media/site-recovery-vmware-to-azure/test-failover2.png)

3. En la **red y calcular** > **calcular propiedades** que puede especificar el tamaño de nombre y de destino de la máquina virtual de Azure. Modifique el nombre para cumplir con los requisitos de Azure si necesita.
También puede ver y agregar información acerca de la red de destino, la subred y la dirección IP que se asignará a la máquina virtual de Azure. Tenga en cuenta lo siguiente:

    - Puede establecer la dirección IP de destino. Si no proporciona una dirección, el error sobre equipo usará DHCP. Si establece una dirección que no está disponible en la migración tras error, no funcionará la migración tras error. La misma dirección IP de destino puede utilizarse para probar migración tras error si la dirección está disponible en la red de migración tras error de prueba.
    - El número de adaptadores de red se indica mediante el tamaño que especifique para la máquina virtual de destino, como sigue:

        - Si el número de adaptadores de red en el equipo de origen es menor o igual que el número de adaptadores permitida para el tamaño del equipo de destino, el destino tendrá el mismo número de adaptadores como el origen.
        - Si el número de adaptadores para la máquina virtual de origen supera el número permitido para que se utilizará el tamaño de destino, a continuación, el tamaño máximo de destino.
        - Por ejemplo, si un equipo de origen tiene dos adaptadores de red y el tamaño del equipo de destino admite cuatro, el equipo de destino tendrá dos adaptadores. Si el equipo de origen tiene dos adaptadores, pero el tamaño de destino compatibles solo es compatible con una el equipo de destino tendrá sólo un adaptador.     
    - Si la máquina virtual tiene varios adaptadores de red se conectará a la misma red.

    ![Habilitar la replicación](./media/site-recovery-vmware-to-azure/test-failover4.png)

4. En **discos** puede ver el sistema operativo y los discos de datos en la máquina virtual que se van a replicar.


## <a name="step-7-test-the-deployment"></a>Paso 7: Comprobar la implementación

Para probar la implementación puede ejecutar un error de prueba de una sola máquina virtual o un plan de recuperación que contiene una o más máquinas virtuales.


### <a name="prepare-for-failover"></a>Prepararse para la migración tras error

- Para ejecutar un error de prueba, le recomendamos que cree una nueva red Azure que ha aislado de la red de producción Azure (es el comportamiento predeterminado cuando se crea una nueva red en Azure). [Más información](site-recovery-failover.md#run-a-test-failover) acerca de cómo ejecutar migraciones tras error de prueba.
- Para obtener el mejor rendimiento al conmutar a Azure, instale al agente de Azure en el equipo protegido. Hace arrancar de forma más rápida y ayuda para solucionar problemas. Instalar al agente [Linux](https://github.com/Azure/WALinuxAgent) o [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) .
- Para probar la implementación por completo tendrá una infraestructura de la máquina duplicada para que funcione como se esperaba. Si desea probar DNS y Active Directory puede crear una máquina virtual como un controlador de dominio con el DNS y replicar esto en Azure mediante la recuperación de sitio de Azure. Lea más en [Consideraciones de migración tras error de prueba de Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Asegúrese de que se está ejecutando el servidor de configuración. En caso contrario, se producirá un error de migración tras error.
- Si ha excluido discos de replicación debe crear esos discos manualmente en Azure después de la migración para que la aplicación se ejecuta según lo esperado.
- Si desea ejecutar una migración tras error planificado en lugar de un error de prueba tenga en cuenta lo siguiente:

    - Si es posible debe apagar equipos principales antes de ejecutar una migración tras error no planeado. Esto garantiza que no tiene el origen y la réplica máquinas que se ejecutan al mismo tiempo. Si va a replicar máquinas virtuales de VMware puede especificar que sitio recuperación debe realizar un mayor esfuerzo para apagar los equipos de origen. Según el estado del sitio primario Esto puede o no funcionen. Si va a replicar servidores físicos sitio recuperación no ofrece esta opción.
    - Al ejecutar una migración tras error planificado detiene la replicación de datos de equipos principales para cualquier delta de datos no se transferirán después de que comience una migración tras error no planeado. Además si ejecuta una migración tras error planificado en un plan de recuperación se ejecutará hasta que haya terminado, incluso si se produce un error.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Prepararse para conectarse a máquinas virtuales de Azure después de la migración

Si desea conectarse a máquinas virtuales de Azure con RDP después de la migración, asegúrese de que hacer lo siguiente:

**En el equipo local antes de la migración tras error**:

- Acceso a través de internet habilitar RDP, asegúrese de que se agregan las reglas TCP y UDP para el **público**y asegúrese de que está permitido RDP en el **Firewall de Windows** -> **características y aplicaciones permitidos** para todos los perfiles.
- Acceso a través de una conexión de sitio a sitio habilitar RDP en el equipo y asegúrese de que está permitido RDP en el **Firewall de Windows** -> **permitidos aplicaciones y características** de redes **privadas** y **dominio** .
- Instalar el [agente de Azure VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) en el equipo local.
- [Instalar manualmente el servicio de movilidad](#install-the-mobility-service-manually) en equipos en lugar de usar el servidor de procesos para insertar el servicio de forma automática. Esto es porque la instalación de inserción pasa solo después de que el equipo está habilitado para la replicación.
- Asegúrese de que la directiva de SAN del sistema operativo se establece en OnlineAll. [Aprende más]( https://support.microsoft.com/kb/3031135)
- Desactivar el servicio IPSec antes de ejecutar la migración tras error.

**En la VM Azure después de la migración**:

- Agregar un extremo público para el protocolo RDP (puerto 3389) y especifique las credenciales de inicio de sesión.
- Asegúrese de que no tiene las directivas de dominio que impiden la conexión a una máquina virtual usando una dirección pública.
- Intente conectarse. Si no puede conectarse Compruebe que se está ejecutando la máquina virtual. Para obtener más sugerencias de solución de problemas, lea este [artículo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


Si desea tener acceso a una máquina virtual de Azure ejecutan Linux después de la migración con un Secure Shell cliente (ssh), haga lo siguiente:

**En el equipo local antes de la migración tras error**:

- Asegúrese de que el servicio de Secure Shell en la máquina virtual de Azure está establecido para iniciarse automáticamente en el inicio del sistema.
- Compruebe que las reglas de firewall permiten una conexión SSH a él.

**En la VM Azure después de la migración**:

- Las reglas de grupo de seguridad de red de error sobre VM y la subred Azure al que está conectado debe permitir las conexiones entrantes al puerto SSH.
- Debe crear un extremo del público para permitir las conexiones entrantes en el SSH (puerto TCP 22 de forma predeterminada).
- Si la máquina virtual se accede a través de una conexión VPN (ruta Express o VPN de sitio a sitio) el cliente puede utilizarse para conectarse directamente a la máquina virtual sobre SSH.

**En el Windows/Linux VM Azure después de la migración**:

Si tiene un grupo de seguridad de la red asociada a la máquina Virtual o la subred al que pertenece el equipo, asegúrese de que el grupo de seguridad de la red tiene una regla de salida para permitir HTTP/HTTPS. Además, asegúrese de que el DNS de la red de la máquina virtual está recibiendo podido más está configurado correctamente. Else la migración tras error podría el tiempo de espera con el error-'WaitForScriptExecutionTask agotado el tiempo de espera de PreFailoverWorkflow tarea'. Para comprender esto en detalle, consulte sección de recuperación de la [supervisión y la Guía de solución de problemas](site-recovery-monitoring-and-troubleshooting.md#recovery).

## <a name="run-a-test-failover"></a>Ejecutar un error de prueba

1. Conmutar un único equipo, en **configuración de** > **Replicar elementos**, haga clic en la máquina virtual > icono **+ migración tras error de prueba** .

    ![Migración tras error de prueba](./media/site-recovery-vmware-to-azure/test-failover1.png)

2. Conmutar un plan de recuperación, en la **configuración de** > de**Planes de recuperación**, haga clic en el plan > **Migración tras error de prueba**. Para crear una recuperación plan [siga estas instrucciones](site-recovery-create-recovery-plans.md).

3. **Prueba de migración tras error** de seleccionar la red Azure al que se conectará máquinas virtuales de Azure después de que se produce error.
4. Haga clic en **Aceptar** para comenzar la migración tras error. Puede realizar el seguimiento del progreso haciendo clic en la máquina virtual para abrir sus propiedades o en el trabajo de **Migración tras error de prueba** en nombre de depósito > **configuración de** > **trabajos** > **trabajos de recuperación de sitios**.
5. Cuando la migración tras error alcanza el estado **completado pruebas** , haga lo siguiente:

    1. Ver la máquina virtual de réplica en el portal de Azure. Compruebe que la máquina virtual se inicia correctamente.
    2. Si está configurado para máquinas virtuales de acceso de su red local, puede iniciar una conexión a Escritorio remoto en la máquina virtual.
    3. Haga clic en **probar completado** para finalizarlo.

        ![Migración tras error de prueba](./media/site-recovery-vmware-to-azure/test-failover6.png)


    4. Haga clic en **notas** para grabar y guardar las observaciones asociadas con la migración tras error de prueba.
    5. Haga clic en **la prueba de migración tras error esté completa** automáticamente limpiar el entorno de prueba. Después de esto se realiza la migración tras error de prueba mostrará un estado **completado** .
    6.  En esta fase se eliminan todos los elementos o máquinas virtuales creadas automáticamente por sitio de recuperación durante la migración tras error de prueba. No se eliminan los elementos adicionales que haya creado para la migración tras error de prueba.

    > [AZURE.NOTE] Si un error de prueba continúa más de dos semanas, se completa en vigor.


6. Una vez completada la migración tras error también debería poder ver la réplica Azure equipo aparezca en el portal de Azure > **máquinas virtuales de Windows**. Debe asegurarse de que la máquina virtual es el tamaño apropiado, que ha conectado a la red apropiada, y que se está ejecutando.
7. Si se [preparado para conexiones después de la migración](#prepare-to-connect-to-azure-vms-after-failover) debería poder conectarse a la máquina virtual de Azure.

## <a name="monitor-your-deployment"></a>Supervisar la implementación

Esto es cómo puede supervisar el estado, estado y opciones de configuración para la implementación de recuperación de sitios:

1. Haga clic en el nombre de la cámara para tener acceso al panel de **Essentials** . En este panel puede trabajos, estado de la replicación, planes de recuperación, el estado del servidor y eventos de recuperación del sitio.  Puede personalizar Essentials para mostrar los mosaicos y los diseños más útiles, que incluye el estado de otros depósitos de copia de seguridad y recuperación del sitio.<br>
![Essentials](./media/site-recovery-vmware-to-azure/essentials.png)

2. En el icono de **estado** que puede supervisar servidores de sitio (servidores VMM o configuración) que se producen problemas y los eventos generados por el sitio de recuperación en las últimas 24 horas.
3. Puede administrar y supervisar la replicación en los **Elementos replicar**, **Planes de recuperación**, y mosaicos de **Trabajos de recuperación de sitios** . Puede desglosar en trabajos en la **configuración de** -> **trabajos** -> **Trabajos de recuperación de sitios**.


## <a name="deploy-additional-process-servers"></a>Implementar servidores de procesos adicionales

Si tiene para ampliar la implementación más allá de 200 equipos de origen o de una tasa de renovación diaria total de más de 2 TB, necesitará servidores de procesos adicionales para controlar el volumen de tráfico.

Compruebe las [recomendaciones de tamaño para servidores de proceso](#size-recommendations-for-the-process-server) y, a continuación, siga estas instrucciones para configurar el servidor de procesos. Después de configurar el servidor deberá migrar equipos de origen para usarlo.

### <a name="install-an-additional-process-server"></a>Instalar a un servidor de proceso adicional

1. En la **configuración de** > **servidores de recuperación de sitios** , haga clic en el servidor de configuración > **servidor de procesos**.

    ![Agregar el servidor de procesos](./media/site-recovery-vmware-to-azure/migrate-ps1.png)

2. En **Tipo de servidor** , haga clic en **el servidor de procesos (local)**.

    ![Agregar el servidor de procesos](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

3. Descargar el archivo de configuración del sitio recuperación unificada y ejecútelo para instalar al servidor de procesos y registrar en la cámara.
4. En **antes de comenzar** seleccione **Agregar servidores de proceso adicional escalar implementación**.
5. Completar el Asistente de la misma manera que hizo al [Configurar](#step-2-set-up-the-source-environment) el servidor de configuración.

    ![Agregar el servidor de procesos](./media/site-recovery-vmware-to-azure/add-ps1.png)

6. En **Detalles de configuración de servidor** , especifique la dirección IP del servidor de configuración y la frase de contraseña. Para obtener la frase de contraseña ejecutar ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** en el servidor de configuración.

    ![Agregar el servidor de procesos](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar máquinas para usar el nuevo servidor de proceso

1. En la **configuración de** > **servidores de recuperación de sitio** haga clic en el servidor de configuración y, a continuación, expanda **servidores de proceso**.

    ![Actualizar el servidor de proceso](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

2. Haga clic en el servidor de procesos en uso actualmente y haga clic en **cambiar**.

    ![Actualizar el servidor de proceso](./media/site-recovery-vmware-to-azure/migrate-ps3.png)

3. En **Seleccionar el servidor de procesos de destino**, seleccione el nuevo servidor de proceso que desea usar y, a continuación, seleccione las máquinas virtuales que va a controlar el servidor de procesos de nuevo. Haga clic en el icono de información para obtener información acerca del servidor. Para ayudarle a tomar decisiones de carga, se muestra el espacio medio necesario para replicar cada máquina virtual seleccionada en el servidor de proceso de nuevo. Haga clic en la marca de verificación para empezar a replicar en el servidor de proceso de nuevo.

## <a name="vmware-account-permissions"></a>Permisos de la cuenta de VMware

El servidor de procesos puede detectar automáticamente las máquinas virtuales en un servidor de vCenter. Para llevar a cabo la detección automática deberá [definir una función (Azure_Site_Recovery)](#prepare-an-account-for-automatic-discovery) permitir la recuperación de sitio tener acceso al servidor de VMware. Tenga en cuenta que, si solo necesita migrar máquinas VMware a Azure y no necesita recuperación de Azure, puede definir una función de solo lectura es suficiente. En la tabla siguiente se resumen los permisos de la función necesaria.

**Función** | **Detalles** | **Permisos**
--- | --- | ---
Función Azure_Site_Recovery | Detección de VMware VM |Asignar estos privilegios para el servidor de centro de v:<br/><br/>Almacén de datos -> asignar espacio, examinar almacén de datos, las operaciones de bajo nivel de un archivo., quitar archivo, archivos de máquina virtual de actualización<br/><br/>Red -> asignar de red<br/><br/>Recursos -> máquina virtual de asignar al grupo de recursos, migrar apagado de máquina virtual, migrar encendido máquina virtual<br/><br/>Tareas -> Crear tarea, tarea de actualización<br/><br/>Máquina virtual -> configuración<br/><br/>Máquina virtual -> interactuar -> pregunta de respuesta, dispositivo conexión, configurar CD media, configurar medios de disco, apagado, Power en, instalación las herramientas de VMware<br/><br/>Máquina virtual -> Inventario -> crear, registrar, eliminar del registro<br/><br/>Máquina virtual -> aprovisionamiento -> Permitir descarga de máquina virtual, archivos de máquina virtual de permitir cargar<br/><br/>Máquina virtual -> instantáneas -> Quitar instantáneas
rol de usuario de vCenter | VMware VM detección y recuperación tras errores sin apagado de VM de origen | Asignar estos privilegios para el servidor de centro de v:<br/><br/>Objeto de centro de datos –> propagar al objeto secundario, función = sólo lectura <br/><br/>El usuario está asignado a nivel de centro de datos y, por tanto, tiene acceso a todos los objetos en el centro de datos.  Si desea restringir el acceso, asignar la función **sin acceso** con el objeto de **propagar hijo** a los objetos secundarios (hosts vSphere, almacenes de datos, máquinas virtuales y redes).
rol de usuario de vCenter | Conmutación y | Asignar estos privilegios para el servidor de centro de v:<br/><br/>Objeto de centro de datos: propagar al objeto secundario, función = Azure_Site_Recovery<br/><br/>El usuario está asignado a nivel de centro de datos y, por tanto, tiene acceso a todos los objetos en el centro de datos.  Si desea restringir el acceso, asigne la función de **ningún acceso** con la **propagar a objeto secundario** al objeto secundario (hosts vSphere, almacenes de datos, máquinas virtuales y redes).  
## <a name="next-steps"></a>Pasos siguientes

- [Más información](site-recovery-failover.md) sobre los distintos tipos de migración tras error.
- [Más información sobre la recuperación](site-recovery-failback-azure-to-vmware.md) para que aparezca el error sobre los equipos que se ejecuta en Azure a su entorno local.

## <a name="third-party-software-notices-and-information"></a>Avisos de software de terceros e información

No traducir texto o localizar

El software y firmware que se ejecuta en el producto de Microsoft o un servicio basado en o incorpora material de los proyectos que se enumeran a continuación (colectivamente, "código de terceros").  Microsoft es el autor del código de terceros no original.  El aviso de copyright original y la licencia, que Microsoft ha recibido este código de terceros y se establecen presentado más adelante.

La información en la sección A está relacionado con componentes de terceros de código de los proyectos que se muestran a continuación. Tales licencias y la información se proporcionan solo con fines informativos.  Este código de terceros que se está relicensed a usted Microsoft en términos de licencia de software de Microsoft para el producto de Microsoft o un servicio.  

La información en la sección B está relacionado con los componentes de código de terceros que se están disponibles para Microsoft en términos de la licencia originales.

Puede encontrar el archivo completo en el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserva todos los derechos que no se conceden expresamente, ya sea de forma implícita, desestimación o de otro tipo.
