<properties
    pageTitle="Replicar máquinas virtuales de VMware y servidores físicos en Azure con la recuperación de sitio de Azure | Microsoft Azure"
    description="En este artículo se describe cómo implementar Azure recuperación de sitio para coordinar replicación, migración tras error y recuperación de máquinas virtuales de VMware local y servidores físicos Linux/Windows Azure."
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
    ms.date="09/29/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery"></a>Replicar máquinas virtuales de VMware y servidores físicos en Azure con la recuperación de sitio de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-vmware-to-azure.md)
- [Portal clásica](site-recovery-vmware-to-azure-classic.md)
- [Portal clásica (heredado)](site-recovery-vmware-to-azure-classic-legacy.md)


El servicio de recuperación de sitios de Azure contribuye a su estrategia de recuperación (BCDR) de desastres y continuidad empresarial por coordinar replicación, migración tras error y recuperación de máquinas virtuales y servidores físicos. Máquinas pueden replicarse en Azure o a un centro de datos secundario en local. Para obtener una introducción rápida leer [¿Qué es la recuperación de sitio de Azure?](site-recovery-overview.md).

## <a name="overview"></a>Información general

Este artículo se describe cómo:

- **Máquinas virtuales de VMware replicar en Azure**: implementar la recuperación de sitio para coordinar replicación, migración tras error y recuperación de máquinas virtuales de VMware local con el almacenamiento de Azure.
- **Replicar servidores físicos en Azure**: implementar recuperación de Azure sitio para coordinar replicación, migración tras error y recuperación de los servidores físicos de Windows y Linux de local a Azure.

>[AZURE.NOTE] Este artículo describe cómo replicar en Azure. Si desea replicar máquinas virtuales de VMware o servidores físicos de Windows/Linux en un centro de datos secundario, siga las instrucciones de [este artículo](site-recovery-vmware-to-vmware.md).

Publicar comentarios o preguntas en la parte inferior de este artículo o en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="enhanced-deployment"></a>Implementación mejorada

Este artículo incluye contiene instrucciones para una implementación mejorada en el portal de Azure clásico. Se recomienda que utilizar esta versión para todas las implementaciones nuevas. Si ya ha implementado utilizando la versión anterior de heredados se recomienda migrar a la nueva versión. Obtenga [más](site-recovery-vmware-to-azure-classic-legacy.md##migrate-to-the-enhanced-deployment) información sobre la migración.

La implementación mejorada es una actualización importante. Aquí es un resumen de las mejoras que hemos realizado:

- **Infraestructura de máquinas virtuales en Azure**: aplica datos directamente a una cuenta de almacenamiento de Azure. Además de replicación y migración tras error no es tener que configurar cualquier infraestructura máquinas virtuales (servidor de configuración, el servidor de destino principal) se necesarios en la implementación heredada.  
- **Instalación unificado**: una única instalación proporciona configuración sencilla y escalabilidad para componentes local.
- **Implementación segura**: todo el tráfico se cifra y comunicaciones de administración de replicación se envían a través de HTTPS 443.
- **Puntos de recuperación**: soporte para bloqueo y recuperación consistente con las aplicaciones de destino para los entornos de Windows y Linux y VM y VM múltiples configuraciones coherentes de un solo es compatible con ambos.
- **Probar la migración tras error**: compatibilidad de migración tras error de prueba sin interrupciones a Azure, sin afectar la producción o pausar la replicación.
- **No planeado migración tras error**: soporte técnico para failover planificado a Azure con la opción mejorada de cierre máquinas virtuales automáticamente antes de la migración tras error.
- **Recuperación**: recuperación integrada que replica únicamente los cambios de delta a su sitio local.
- **vSphere 6.0**: compatibilidad limitada para implementaciones VMware Vsphere 6.0.


## <a name="how-does-site-recovery-help-protect-virtual-machines-and-physical-servers"></a>¿Cómo ayuda sitio recuperación proteger máquinas virtuales y servidores físicos?


- Los administradores de VMware pueden configurar protección fuera del sitio a Azure de cargas de trabajo de empresa y aplicaciones que se ejecutan en máquinas virtuales VMware. Los administradores del servidor pueden replicar servidores de Windows y Linux físico local en Azure.
- La consola de recuperación de sitio de Azure proporciona una única ubicación para la instalación simple y administración de replicación, migración tras error y procesos de recuperación.
- Si replicar máquinas virtuales de VMware administradas por un servidor de vCenter, recuperación del sitio puedan detectar automáticamente esas máquinas virtuales. Si los equipos están en un host ESXi sitio recuperación descubre máquinas virtuales en el host.
- Ejecutar fácil migraciones tras error de la infraestructura local a Azure y recuperación (restaurar) de Azure a los servidores de VM VMware en el sitio local.
- Configurar los planes de recuperación agrupan cargas de trabajo que están en niveles en varios equipos. Puede conmutar los planes y recuperación de sitio ofrece coherencia entre varios VM para equipos que ejecutan el mismos cargas de trabajo pueden recuperarse juntos en un punto de datos coherentes.


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

Componentes del escenario:

- **Un servidor de administración local**: el servidor de administración ejecuta componentes de recuperación de sitios:
    - **Servidor de configuración**: coordenadas comunicaciones y administra los procesos de replicación y la recuperación de datos.
    - **Servidor de procesos**: actúa como una puerta de enlace de replicación. Recibe datos de equipos de origen protegida, optimiza con almacenamiento en caché, compresión y cifrado y envía los datos de replicación al almacenamiento de Azure. También se controla la instalación de inserción de servicio de movilidad para equipos protegidas y se realiza la detección automática de máquinas virtuales de VMware.
    - **Servidor de destino maestro**: controla los datos de replicación durante la recuperación de Azure.
    También puede implementar un servidor de administración que actúa como un servidor de procesos, para escalar su implementación.
- **Servicio de la movilidad**: este componente se implementa en cada equipo (VMware VM o servidor físico) que desea replicar en Azure. Captura de escritura de datos en el equipo y reenvía al servidor de procesos.
- **Azure**: no es necesario crear cualquier máquinas virtuales de Azure para controlar la replicación y migración tras error. El servicio de recuperación de sitio controla la administración de datos y, a continuación, aplica datos directamente al almacenamiento de Azure. Máquinas virtuales de Azure replicada está terminadas automáticamente solo cuando se produce error en Azure. Sin embargo, si desea un error de Azure al sitio local debe configurar una máquina virtual de Azure para que actúe como un servidor de procesos.


El gráfico muestra cómo interactúan estos componentes.

![arquitectura](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figura 1: VMware o físico a Azure** (creado por Henry Robalino)


## <a name="capacity-planning"></a>Planificación de capacidad

Cuando está pensando capacidad, aquí lo que debe pensar:

- **El entorno de origen**: planear la capacidad o los requisitos del equipo VMware infraestructura y origen.
- **El servidor de administración**, planificación de los servidores de administración local que se ejecutan componentes de recuperación del sitio.
- **Ancho de banda de origen a destino**-planificación de ancho de banda necesario para la replicación entre el origen y de Azure

### <a name="source-environment-considerations"></a>Consideraciones del entorno de origen

- **Tasa de cambio de máximo diariamente**: un equipo protegido solo puede usar un servidor de procesos y un servidor de un solo proceso puede controlar hasta 2 TB de cambio de datos al día. Así 2 TB es que los datos diarios máximos cambiarán tasa que se admite para un equipo protegido.
- **Rendimiento máximo**: un equipo replicado puede pertenecer a una cuenta de almacenamiento en Azure. Una cuenta de almacenamiento estándar puede administrar un máximo de 20.000 solicitudes por segundo y, a continuación, le recomendamos que mantenga el número de IOPS a través de un equipo de origen a 20.000. Por ejemplo si tiene un equipo de origen con 5 discos y cada genera 120 IOPS (tamaño de 8 KB) en el origen estará dentro de la Azure por límite de 500 IOPS en disco. El número de cuentas de almacenamiento necesarias = origen total IOPs/20000.


### <a name="management-server-considerations"></a>Consideraciones acerca del servidor de administración

El servidor de administración ejecuta los componentes de recuperación de sitios que controlan la optimización de datos, replicación y administración. Debería poder administrar la capacidad de tasa de cambio diaria para todas las cargas de trabajo en ejecución en equipos protegidos y tiene suficiente ancho de banda para replicar continuamente los datos a almacenamiento de Azure. Específicamente:

- El servidor de procesos recibe datos de replicación de equipos protegidas y lo optimiza con almacenamiento en caché, compresión y cifrado antes de enviar a Azure. El servidor de administración debe tener recursos suficientes para realizar estas tareas.
- El servidor de procesos utiliza la caché basada en disco. Se recomienda un disco de caché independiente de 600 GB o más para controlar los cambios en los datos almacenados botella en la red o una interrupción. Durante la implementación puede configurar la caché de cualquier unidad que tenga al menos 5 GB de almacenamiento disponible pero 600 GB es la recomendación mínima.
- Como práctica recomendada, se recomienda que el servidor de administración se encuentra en la misma red y el segmento de LAN como los equipos que desea proteger. Puede estar ubicado en una red diferente, pero los equipos que desee proteger deben tener visibilidad de red nivel 3.

En la tabla siguiente se resumen recomendaciones de tamaño para el servidor de administración.

**Servidor de administración de la CPU** | **Memoria** | **Tamaño de caché de disco** | **Tasa de cambio de datos** | **Máquinas protegidos**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz) | 16 GB | 300 GB | 500 GB o menos | Implementar un servidor de administración con esta configuración para replicar menos de 100 equipos.
12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz) | 18 GB | 600 GB | 500 GB hasta 1 TB | Implementar un servidor de administración con estos ajustes para replicar entre 100-150 equipos.
16 vCPUs (2 sockets * 8 núcleos @ 2,5 GHz) | 32 GB | 1 TB | 1 TB a 2 TB | Implementar un servidor de administración con estos ajustes para replicar entre equipos de 150-200.
Implementar otro servidor de proceso | | | > 2 TB | Implementar servidores de proceso adicional si está replicar más de 200 equipos, o si cambian los datos diarios tasa supera 2 TB.

Donde:

- Cada equipo de origen está configurado con 3 discos de 100 GB.
- Hemos usado el almacenamiento de pruebas comparativas de 8 unidades SA de 10 KB RPM con RAID 10 para las medidas de disco de caché.

### <a name="network-bandwidth-from-source-to-target"></a>Ancho de banda de origen a destino
Asegúrese de que calcular el ancho de banda que sería necesario para duplicación inicial y delta mediante la [herramienta de planificación de capacidad](site-recovery-capacity-planner.md)

#### <a name="throttling-bandwidth-used-for-replication"></a>Limitar ancho de banda que se utiliza para la replicación

Tráfico de VMware replicado en Azure pasa por un servidor de proceso específico. Puede limitar el ancho de banda que está disponible para la replicación de recuperación de sitio en el servidor como sigue:

1. Abrir la copia de seguridad de Microsoft Azure MMC complemento en el servidor de administración principal o en un servidor de administración ejecuta adicionales aprovisione servidores de procesos. De forma predeterminada un método abreviado de copia de seguridad de Microsoft Azure se crean en el escritorio o pueda encontrarlo en: Agent\bin\wabadmin de servicios de recuperación de C:\Program Files\Microsoft Azure.
2. En el complemento, haga clic en **Cambiar las propiedades**.

    ![Limitar ancho de banda](./media/site-recovery-vmware-to-azure-classic/throttle1.png)

3. En la pestaña **regulación** especifique el ancho de banda que se pueden usar para la programación aplicable y replicación de recuperación del sitio.

    ![Limitar ancho de banda](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

Opcionalmente, también puede establecer límite con PowerShell. Aquí tenemos un ejemplo:

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

#### <a name="maximizing-bandwidth-usage"></a>Maximizar el uso de ancho de banda
Para aumentar el ancho de banda que se utiliza para la replicación de recuperación de sitios de Azure necesarias para cambiar una clave del registro.

La siguiente clave controla el número de subprocesos por replicación de disco que se usan al replicar

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 En una red "overprovisioned", esta clave del registro es necesario cambiar sus valores predeterminados. Se admite un máximo de 32.  


[Obtenga más](site-recovery-capacity-planner.md) información sobre la planeación de capacidad detallada.

### <a name="additional-process-servers"></a>Servidores de proceso adicional

Si necesita proteger máquinas más de 200 o tasa de cambio diaria es mayor que 2 TB puede agregar servidores adicionales para controlar la carga. Para escalar hacer lo siguiente:

- Aumentar el número de servidores de administración. Por ejemplo, si desea proteger hasta 400 máquinas con dos servidores de administración.
- Agregar servidores de proceso adicional y usarlos para controlar el tráfico en lugar de (o además de) el servidor de administración.

Esta tabla describe un escenario en el que:

- Configurar el servidor de administración original como un servidor de configuración solo.
- Configurar un servidor de proceso adicional.
- Configurar protegida máquinas virtuales de Windows para usar el servidor de procesos adicionales.
- Cada equipo de la fuente de protección está configurado con tres discos de 100 GB.

**Servidor de administración de original**<br/><br/>(servidor de configuración) | **Servidor de procesos adicionales**| **Tamaño de caché de disco** | **Tasa de cambio de datos** | **Máquinas protegidos**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz), 16 GB de memoria | 4 vCPUs (2 sockets * 2 núcleos @ 2,5 GHz), 8 GB de memoria | 300 GB | 250 GB o menos | Puede replicar 85 o menos equipos.
8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz), 16 GB de memoria | 8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz), 12 GB de memoria | 600 GB | 250 GB hasta 1 TB | Puede replicar entre máquinas 85-150.
12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz), 18 GB de memoria | 12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz) 24 GB de memoria | 1 TB | 1 TB a 2 TB | Puede replicar entre máquinas de 150-225.


La manera en que escalar los servidores dependen de su preferencia de una escala de o ajustar el modelo.  Escalar por implementar algunos servidores de proceso y administración de gama alta o escalar implementando más servidores con menos recursos. Por ejemplo: si necesita proteger 220 máquinas podría hacer lo siguiente:

- Configure el servidor de administración original con 12vCPU, 18 GB de memoria, un servidor de proceso adicionales con 12vCPU, 24 GB de memoria y equipos protegidas para usar solo el servidor de procesos adicionales.
- O bien, también podría configurar dos servidores de administración (8vCPU de 2 x, 16 GB de RAM) y dos servidores de proceso adicional (1 x 8vCPU) y 4vCPU x 1 manejar 135 + 85 máquinas (220) y configurar los equipos protegidos para usar los servidores de proceso adicional solo.


[Siga estas instrucciones](#deploy-additional-process-servers) para configurar un servidor de proceso adicional.

## <a name="before-you-start-deployment"></a>Antes de comenzar la implementación

Las tablas resumen los requisitos previos para implementar este escenario.


### <a name="azure-prerequisites"></a>Requisitos previos de Azure

**Requisito previo** | **Detalles**
--- | ---
**Cuenta de Azure**| Tendrá una cuenta de [Microsoft Azure](https://azure.microsoft.com/) . Puede empezar con una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) acerca de precios de recuperación del sitio.
**Almacenamiento de Azure** | Tendrá una cuenta de almacenamiento de Azure para almacenar datos duplicados. Los datos duplicados se almacenan en el almacenamiento de Azure y Azure VM está terminadas cuando se produce error. <br/><br/>Se necesita una [cuenta de almacenamiento geo redundantes estándar](../storage/storage-redundancy.md#geo-redundant-storage). La cuenta debe estar en la misma región como el servicio de recuperación de sitio y estar asociadas a la misma suscripción. Tenga en cuenta que la replicación a cuentas de almacenamiento premium no es compatible actualmente y no debe usarse.<br/><br/>No se admite el movimiento de las cuentas de almacenamiento creados con el [nuevo portal de Azure](../storage/storage-create-storage-account.md) a través de grupos de recursos. [Lea acerca de](../storage/storage-introduction.md) Almacenamiento de Azure.<br/><br/>
**Red de Azure** | Necesitará una red virtual Azure que se conectará máquinas virtuales de Azure cuando se produce error. La red virtual Azure debe estar en la misma región como depósito de recuperación del sitio.<br/><br/>Nota que no se realice correctamente después de migración tras error a Azure tendrá una VPN conexión (o Azure ExpressRoute) configurar desde la red de Azure en el sitio local.


### <a name="on-premises-prerequisites"></a>Requisitos previos de local

**Requisito previo** | **Detalles**
--- | ---
**Servidor de administración** | Necesita un servidor de Windows 2012 R2 local ejecuta en una máquina virtual o el servidor físico. Todos los componentes de recuperación de sitios local están instalados en este servidor de administración<br/><br/> Se recomienda que implementar el servidor como una VM VMware altamente disponibles. Recuperación al sitio local de Azure es siempre se en máquinas virtuales de VMware independientemente de si un error sobre máquinas virtuales o servidores físicos. Si no configura el servidor de administración como una VM VMware deberá configurar un servidor de destino principal independiente como una VM VMware para recibir el tráfico de recuperación.<br/><br/>El servidor no debe ser un controlador de dominio.<br/><br/>El servidor debe tener una dirección IP estática.<br/><br/>El nombre de host del servidor debe ser 15 caracteres o menos.<br/><br/>La configuración regional del sistema operativo sólo debería inglés.<br/><br/>El servidor de administración requiere acceso a internet.<br/><br/>Necesita acceso saliente desde el servidor como sigue: acceso temporal en HTTP 80 durante la instalación de los componentes de recuperación del sitio (para descargar MySQL); Acceso de salida en curso en HTTPS 443 para la administración de replicación; Acceso de salida en curso en HTTPS 9443 tráfico de replicación (puede modificarse este puerto)<br/><br/> Asegúrese de que estas direcciones URL están accesibles desde el servidor de administración: <br/>- \*. hypervrecoverymanager.windowsazure.com<br/>- \*. accesscontrol.windows.net<br/>- \*. backup.windowsazure.com<br/>- \*. blob.core.windows.net<br/>- \*. store.core.windows.net<br/>-https://www.msftncsi.com/ncsi.txt<br/>- [https://dev.MySQL.com/Get/Archives/MySQL-5.5/MySQL-5.5.37-Win32.msi]( https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>Si tiene reglas de firewall basado en la dirección IP del servidor, compruebe que las reglas de permitan la comunicación a Azure. Debe permitir que los [Intervalos de IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) y el puerto HTTPS (443). También necesitará lista blanca intervalos de direcciones IP para la región de su suscripción de Azure y oeste de Estados Unidos. Es la dirección URL [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi") para descargar MySQL.
**VMware vCenter/ESXi host**: | Necesita uno o más vMware vSphere ESX/ESXi hipervisores administrar las máquinas virtuales de VMware, ejecuta ESX/ESXi versión 6.0, 5.5 o 5.1 con las últimas actualizaciones.<br/><br/> Se recomienda que implementar un servidor de vCenter VMware para administrar sus hosts ESXi. Debe estar ejecutándose vCenter versión 6.0 o 5.5 con las últimas actualizaciones.<br/><br/>Observe que el sitio recuperación no admite vCenter nuevo y vSphere 6.0 características como cruzada vCenter vMotion, volúmenes virtuales y almacenamiento DRS. Soporte de recuperación de sitio se limita a las características que también estaban disponibles en la versión 5.5.
**Máquinas protegida**: | **AZURE**<br/><br/>Equipos que desee proteger deben cumplir con los [requisitos previos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) para crear máquinas virtuales de Azure.<br><br/>Si desea conectarse a las máquinas virtuales de Azure después de la migración necesitará habilitar las conexiones de escritorio remoto en el firewall local.<br/><br/>Capacidad de disco individuales en equipos protegidas no debería ser más de 1023 GB. Una máquina virtual puede tener hasta 64 discos (así hasta 64 TB). Si tiene discos mayores de 1 TB considere la posibilidad de uso de la replicación de base de datos como SQL Server siempre en o protección de datos de Oracle.<br/><br/>Mínimo 2 GB de espacio disponible en la unidad de instalación para la instalación de componente.<br/><br/>Compartir invitado disco clústeres no se admiten. Si dispone de una implementación agrupada piense en usar la replicación de base de datos como SQL Server siempre en o protección de datos de Oracle.<br/><br/>Unificada de interfaz de Firmware Extensible (UEFI) / inicio Extensible Interface(EFI) Firmware no es compatible.<br/><br/>Nombres de equipo deben contener entre 1 y 63 caracteres (letras, números y guiones). El nombre debe comenzar con una letra o un número y termine con una letra o un número. Después de protege un equipo puede modificar el nombre de Azure.<br/><br/>**Máquinas virtuales de VMware**<br/><br>Debe instalar VMware vSphere PowerCLI 6.0. en el servidor de administración (servidor de configuración).<br/><br/>Máquinas virtuales de VMware que desee proteger debe tener instalado y ejecuta las herramientas de VMware.<br/><br/>Si el origen de la máquina virtual tiene el equipo NIC se convierte a una única NIC después de la migración a Azure.<br/><br/>Si VM protegidas tienen un disco iSCSI sitio recuperación convierte el disco de iSCSI VM protegido en un archivo de disco duro virtual cuando la máquina virtual por error a Azure. Si el destino iSCSI se puede llegar con la máquina virtual de Azure se conecte al destino iSCSI y se esencialmente ver dos discos: el disco duro virtual en la máquina virtual de Azure y el disco de iSCSI de origen. En este caso deberá desconectar el destino iSCSI que aparece en el error sobre VM de Azure.<br/><br/>[Más información](#vmware-permissions-for-vcenter-access) acerca de los permisos de usuario de VMware son necesarios para el sitio de recuperación.<br/><br/> **EQUIPOS con WINDOWS SERVER (en VMware VM o servidor físico)**<br/><br/>El servidor debe ejecutar un sistema operativo de 64 bits: Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 con al menos SP1.<br/><br/>El sistema operativo debe instalarse en unidad C:\ y el disco de sistema operativo debe ser un disco básico de Windows (OS no debe instalarse en un disco dinámico de Windows).<br/><br/>Para equipos de Windows Server 2008 R2 debe tener .NET Framework 3.5.1 instalado.<br/><br/>Deberá proporcionar una cuenta de administrador (debe ser un administrador local en el equipo de Windows) para la instalación de inserción el servicio de movilidad en servidores de Windows. Si la cuenta proporcionada es una cuenta de dominio no debe deshabilitar el control de acceso remoto al usuario en el equipo local. [Más información](#install-the-mobility-service-with-push-installation).<br/><br/>Recuperación de sitio es compatible con máquinas virtuales con disco RDM.  Durante la recuperación sitio recuperación volverá a utilizar el disco RDM si está disponible el disco de VM y RDM origen original. Si no está disponibles, durante la recuperación recuperación del sitio creará un nuevo archivo VMDK para cada disco.<br/><br/>**EQUIPOS LINUX**<br/><br/>Necesitará un sistema operativo de 64 bits compatibles: rojo sombrero Enterprise Linux 6,7; CentOS 6.5, 6.6,6.7; Oracle Enterprise Linux 6.4, 6.5 ejecutando rojos sombrero compatible kernel o Unbreakable Enterprise Kernel versión 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>archivos / etc/hosts en equipos protegidos deben contener entradas que asignan el nombre de host local a direcciones IP asociadas con todos los adaptadores de red. <br/><br/>Si desea conectarse a una máquina virtual Azure ejecutan Linux después de la migración con un Secure Shell cliente (ssh), asegúrese de que el servicio de Shell seguro en el equipo protegido está establecido para iniciar automáticamente en el inicio del sistema, y que las reglas de firewall permiten una ssh conexión a ella.<br/><br/>Protección solo puede activarse para equipos Linux con el almacenamiento siguiente: archivos de sistema (EXT3, ETX4, ReiserFS, XFS); Múltiples rutas software el dispositivo asignador (múltiples rutas)) Administrador de volumen: (LVM2). Servidores físicos con almacenamiento del controlador HP CCISS no son compatibles. El sistema de archivos ReiserFS sólo se admite en SUSE Linux Enterprise Server 11 SP3.<br/><br/>Recuperación de sitio es compatible con máquinas virtuales con disco RDM.  Durante la recuperación para Linux, recuperación de sitios no volver a usar el disco RDM. En cambio, crea un nuevo archivo VMDK para cada disco RDM correspondiente.

Solo para Linux VM - Asegúrese de que establezca la configuración de disk.enableUUID=true en los parámetros de configuración de la máquina virtual de VMware. Si esta fila no existe, debe agregarlo. Esto es necesario para proporcionar un UUID coherente para el VMDK para que lo montajes correctamente. Observe también que sin esta configuración, recuperación provocará una descarga completa incluso si la máquina virtual está disponible en prem. Agregar esta configuración se asegurará de que solo los cambios delta se transfieren durante la recuperación.

## <a name="step-1-create-a-vault"></a>Paso 1: Crear un depósito

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com/).
2. Expanda **Servicios de datos** > **Servicios de recuperación** y haga clic en **Depósito de recuperación del sitio**.
3. Haga clic en **Crear nuevo** > **crear rápido**.
4. En **nombre**, escriba un nombre descriptivo para identificar la cámara.
5. En la **región**, seleccione la región geográfica de la cámara. Para comprobar regiones admitidas vea disponibilidad geográfica en [Detalles de precios de Azure sitio recuperación](https://azure.microsoft.com/pricing/details/site-recovery/)
6. Haga clic en **Crear depósito**.
    ![Nuevo depósito](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

Compruebe la barra de estado para confirmar que la cámara se creó correctamente. La cámara se mostrarán como **activo** en la página principal de **Servicios de recuperación** .

## <a name="step-2-set-up-an-azure-network"></a>Paso 2: Configurar una red de Azure

Configurar una red de Azure para que las máquinas virtuales de Azure se conectará a una red después de la migración y para que puedan trabajar recuperación al sitio local según lo esperado.

1. En el portal de Azure > **crear red virtual** especifique el nombre de red. Nombre de rango y subred de dirección IP.
2. Debe agregar VPN/ExpressRoute a la red si debe realizar la recuperación. VPN/ExpressRoute se pueden agregar a la red, incluso después de la migración tras error.

[Obtenga más información](../virtual-network/virtual-networks-overview.md) acerca de las redes de Azure.

> [AZURE.NOTE] [Migración de redes](../resource-group-move-resources.md) entre los grupos de recursos en la misma suscripción o a través de suscripciones no es compatible con redes usadas para implementar el sitio de recuperación.

## <a name="step-3-install-the-vmware-components"></a>Paso 3: Instalar los componentes de VMware

Si desea replicar VMware virtual máquinas instalación los siguientes componentes de VMware en el servidor de administración:

1. [Descargar](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) e instalar VMware vSphere PowerCLI 6.0.
2. Reinicie el servidor.


## <a name="step-4-download-a-vault-registration-key"></a>Paso 4: Descargar una clave de registro de cámara

1. En la administración de servidor abra la consola de recuperación de sitio en Azure. En la página de **Servicios de recuperación** , haga clic en la cámara para abrir la página de inicio rápido. También se pueden abrir en cualquier momento mediante el icono de inicio rápido.

    ![Icono de inicio rápido](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)

2. En la página de **Inicio rápido** , haga clic en **Recursos de destino preparar** > **descargar una clave de registro**. El archivo de registro se genera automáticamente. Es válido durante 5 días después de que se genera.


## <a name="step-5-install-the-management-server"></a>Paso 5: Instalar al servidor de administración
> [AZURE.TIP] Asegúrese de que estas direcciones URL están accesibles desde el servidor de administración:
>
- *. hypervrecoverymanager.windowsazure.com
- *. accesscontrol.windows.net
- *. backup.windowsazure.com
- *. blob.core.windows.net
- *. store.core.windows.net
- https://dev.MySQL.com/Get/Archives/MySQL-5.5/MySQL-5.5.37-Win32.msi
- https://www.msftncsi.com/ncsi.txt




[AZURE.VIDEO enhanced-vmware-to-azure-setup-registration]

1. En la página de **Inicio rápido** descargar el archivo de instalación unificada en el servidor.

2. Ejecute el archivo de instalación para iniciar la instalación en el Asistente de configuración de sitio recuperación unificada.

3.  Antes de **empezar** , seleccione **instalar el servidor de configuración y proceso**.

    ![Antes de empezar](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)
4. **Licencia del Software de terceros** haga clic en **** para descargar e instalar MySQL. 

    ![Tercero = software de terceros](./media/site-recovery-vmware-to-azure-classic/combined-wiz105.PNG)

5. **Registro** en Examinar y seleccione la clave de registro que ha descargado desde la cámara.

    ![Registro](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)

6. En la **Configuración de Internet** , especifique cómo se conectará el proveedor se ejecuta en el servidor de configuración para la recuperación de sitios de Azure a través de internet.

    - Si desea conectar con el servidor proxy que está actualmente configurado en el equipo, seleccione **Conectar con la configuración de proxy existente**.
    - Si desea que el proveedor para conectar directamente, seleccione **Conectar directamente sin un servidor proxy**.
    - Si el servidor proxy existente requiere autenticación o que desea usar a un proxy personalizado para la conexión del proveedor, seleccione **Conectar con la configuración de proxy personalizada**.
        - Si usa a un proxy personalizado, que deberá especificar la dirección y puerto credenciales
        - Si usa a un servidor proxy debe ha permitido ya las siguientes direcciones URL:
            - *. hypervrecoverymanager.windowsazure.com;    
            - *. accesscontrol.windows.net; 
            - *. backup.windowsazure.com; 
            - *. blob.core.windows.net; 
            - *. store.core.windows.net
            

    ![Firewall](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

7. En **Comprobación de requisitos previos** instalación ejecuta una comprobación para asegurarse de que puede ejecutar la instalación. 

    
    ![Requisitos previos](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

     Si aparece una advertencia sobre la **Active la sincronización de tiempo Global** Compruebe que la hora en el reloj del sistema (configuración de**fecha y hora** ) es igual a la zona horaria.

    ![TimeSyncIssue](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

8. En **Configuración de MySQL** crear credenciales para iniciar sesión en la instancia de servidor MySQL que se van a instalar.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)

9. En seleccionar **Detalles del entorno** de si va a replicar VM VMware. Si no, la instalación comprueba que PowerCLI 6.0 está instalado.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)

10. En **Ubicación de instalación** , seleccione dónde desea instalar los archivos binarios y almacenar la memoria caché. Puede seleccionar una unidad que tenga al menos 5 GB de espacio de almacenamiento, pero se recomienda una unidad de caché con al menos 600 GB de espacio libre.

    ![Ubicación de instalación](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)

11. En **Selección de red** , especifique la escucha (adaptador de red y puerto SSL) en el que el servidor de configuración enviar y recibir datos de replicación. Puede modificar el valor predeterminado puerto (9443). Además de este puerto, se usará el puerto 443 de un servidor web que dirige las operaciones de replicación. 443 no debe usarse para recibir el tráfico de replicación.


    ![Selección de red](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)



12.  En **Resumen** , revise la información y haga clic en **instalar**. Cuando finaliza la instalación se genera una frase de contraseña. Deberá cuando se habilita la replicación copiarla y mantener en una ubicación segura.

    ![Resumen](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)



13.  En **Resumen** , revise la información.

    ![Resumen](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)

>[AZURE.WARNING]Proxy del agente de servicio de Microsoft Azure recuperación debe ser el programa de instalación.
>Una vez finalizada la instalación, inicie una aplicación denominada "Shell de servicios de recuperación de Microsoft Azure" en el menú Inicio de Windows. En la ventana de comandos que abre ejecute el siguiente conjunto de comandos para definir la configuración del servidor proxy.
>
    $pwd = ConvertTo-SecureString -String ProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine



### <a name="run-setup-from-the-command-line"></a>Ejecutar el programa de instalación desde la línea de comandos

También puede ejecutar al Asistente unificado desde la línea de comandos, como sigue:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Donde:

- / ServerMode: obligatorio. Especifica si debe instalar la instalación de los servidores de configuración y proceso o solo el servidor de procesos (usado para instalar los servidores de proceso adicional). Valores de entrada: CS, PS.
- UnidadDeInstalación: obligatorio. Especifica la carpeta donde se instalan los componentes.
- / MySQLCredFilePath. Obligatorio. Especifica la ruta de acceso a un archivo donde las credenciales del servidor MySQL son historia. Obtener la plantilla para crear el archivo.
- / VaultCredFilePath. Obligatorio. Ubicación del archivo de credenciales de cámara
- / EnvType. Obligatorio. Tipo de instalación. Valores: VMware, NonVMware
- / PSIP y /CSIP. Obligatorio. Dirección IP del servidor de procesos y del servidor de configuración.
- / PassphraseFilePath. Obligatorio. Ubicación del archivo de frase de contraseña.
- / ByPassProxy. Opcional. Especifica que el servidor de administración se conecta a Azure sin un servidor proxy.
- / ProxySettingsFilePath. Opcional. Especifica la configuración de un proxy personalizado (proxy de forma predeterminada en el servidor que requiere autenticación), o bien proxy personalizado




## <a name="step-6-set-up-credentials-for-the-vcenter-server"></a>Paso 6: Configurar las credenciales para el servidor vCenter

> [AZURE.VIDEO enhanced-vmware-to-azure-discovery]

El servidor de procesos puede detectar automáticamente las máquinas virtuales de VMware administrado por un servidor de vCenter. La detección automática de recuperación de sitios necesita una cuenta y credenciales que pueden tener acceso al servidor de vCenter. Esto no es relevante si está replicar sólo servidores físicos.

Haga lo siguiente como sigue:

1. En la vCenter server crea una función (**Azure_Site_Recovery**) en el nivel de vCenter con los [permisos necesarios](#vmware-permissions-for-vcenter-access).
2. Asignar el rol de **Azure_Site_Recovery** a un usuario vCenter.

    >[AZURE.NOTE] Una cuenta de usuario de vCenter que tiene la función de sólo lectura puede ejecutar migración tras error sin apagar equipos de origen protegida. Si desea cerrar esos equipos tendrá la función Azure_Site_Recovery. Tenga en cuenta que si sólo migra máquinas virtuales de VMware a Azure y recuperación no es necesario, a continuación, la función de sólo lectura es suficiente.

3. Para agregar la cuenta abra **cspsconfigtool**. Está disponible como un acceso directo en el escritorio y se encuentra en la carpeta de \home\svsystems\bin [ubicación de instalación].
2. en la pestaña **Administrar cuentas** , haga clic en **Agregar cuenta**.

    ![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic/credentials1.png)

3. En **Detalles de cuenta** agregar credenciales que se pueden usar para tener acceso al servidor de vCenter. Tenga en cuenta que podría tardar más de 15 minutos para que el nombre de cuenta que aparezca en el portal. Para actualizar inmediatamente, haga clic en actualizar en la pestaña **Configuración de servidores** .

    ![Detalles](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## <a name="step-7-add-vcenter-servers-and-esxi-hosts"></a>Paso 7: Agregar servidores vCenter y ESXi hosts

Si va a replicar máquinas virtuales de VMware debe agregar un servidor de vCenter (o ESXi host).

1. En los **servidores** > **Servidores de configuración** , seleccione el servidor de configuración > **Agregar vCenter servidor**.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)

2. Agregar el servidor vCenter o ESXi detalles del host, el nombre de la cuenta especificada para tener acceso al servidor vCenter en el paso anterior y a continuación, el servidor de procesos que se usará para descubrir máquinas virtuales de VMware administrados por el servidor vCenter. Tenga en cuenta que el servidor de vCenter o host de ESXi debe encontrarse en la misma red que el servidor en el que está instalado el servidor de procesos.

    >[AZURE.NOTE] Si va a agregar el servidor de vCenter o host de ESXi con una cuenta que no tiene privilegios de administrador en el servidor vCenter o host, asegúrese de que la vCenter o cuentas ESXi tienen estos privilegios habilitados: Centro de datos, almacén de datos, carpeta, Jost, red, recursos, Virtual de máquina, vSphere distribuido cambiar. Además, el servidor de vCenter tiene los privilegios de vistas de almacenamiento.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)

3. Una vez completada la detección del servidor de vCenter se mostrarán en la pestaña **Configuración de servidores** .

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)


## <a name="step-8-create-a-protection-group"></a>Paso 8: Crear un grupo de protección

> [AZURE.VIDEO enhanced-vmware-to-azure-protection]


Grupos de protección son agrupaciones lógicas de máquinas virtuales o servidores físicos que desee proteger con la misma configuración de protección. Aplicar configuración de protección a un grupo de protección y esos valores se aplican a todos los equipos de máquinas virtuales o físicas agregar al grupo.

1. Abrir **Elementos protegidos** > **Grupo de protección** y haga clic en Agregar un grupo de protección.

    ![Crear grupo de protección](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)

2. En la página **Especificar la configuración de grupo de protección** especifique un nombre para el grupo y en el cuadro **de** , seleccione el servidor de configuración en la que desea crear el grupo. **Destino** es Azure.

    ![Configuración de protección de grupo](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)

3. En la página **Especificar la configuración de replicación de** configurar las opciones de replicación que se usará para todos los equipos en el grupo.

    ![Replicación de grupo de protección](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

    - **Coherencia entre varios VM**: si activar esta función crea puntos de recuperación consistente con las aplicaciones compartidas en el grupo de protección los equipos. Esta opción es más relevante cuando todos los equipos en el grupo de protección están ejecutando la misma carga de trabajo. En el mismo punto de datos se recuperarán todos los equipos. Está disponible si está replicar máquinas virtuales de VMware o servidores físicos de Windows o Linux.
    - **Umbral RPO**: establece el RPO. Cuando la replicación de protección de datos continua supera el valor de umbral RPO configurado, se generará alertas.
    - **Retención de punto de recuperación**: especifica la ventana de retención. Equipos protegidas se pueden recuperar a cualquier punto dentro de esta ventana.
    - **Frecuencia de instantáneas coherentes de aplicación**: especifica la frecuencia con la que se creará puntos de recuperación que contiene Dr.

Al hacer clic en la marca de verificación se creará un grupo de protección con el nombre especificado. In Addition se crea un segundo grupo de protección con el nombre < protección-grupo-nombre-recuperación). Si se producen errores en el sitio local después de la migración a Azure, se utiliza este grupo de protección. Puede supervisar los grupos de protección como se crean en la página de **Elementos protegidos** .

## <a name="step-9-install-the-mobility-service"></a>Paso 9: Instalar el servicio de movilidad

Es el primer paso para habilitar la protección de máquinas virtuales y servidores físicos instalar el servicio de movilidad. Puede hacerlo de dos maneras:

- Push e instale automáticamente el servicio de cada equipo desde el servidor de procesos. Tenga en cuenta que al agregar equipos a un grupo de protección y si está ejecutando una versión adecuada de la instalación de inserción del servicio de movilidad no se produce.
- Instalar automáticamente el servicio con el método de inserción de enterprise como WSUS o administrador de configuración de System Center. Asegúrese de que ha configurado el servidor de administración antes de hacerlo.
- Instalar manualmente en cada equipo que desea proteger. crear seguro de que ha configurado el servidor de administración antes de hacerlo.


### <a name="install-the-mobility-service-with-push-installation"></a>Instalar el servicio de movilidad con la instalación de inserción

Al agregar equipos a un grupo de protección es inserta automáticamente el servicio de movilidad y se instalado en cada equipo por el servidor de procesos.


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Prepararse para la inserción automática en equipos con Windows

Aquí le mostramos cómo preparar los equipos de Windows para que el servidor de procesos puede instalarse automáticamente el servicio de movilidad.

1.  Crear una cuenta que puede ser usada por el servidor de procesos para acceder al equipo. La cuenta debe tener privilegios de administrador (local o dominio). Tenga en cuenta que estas credenciales solo se usan para la instalación de inserción de servicio de movilidad.

    >[AZURE.NOTE] Si no usa una cuenta de dominio debe deshabilitar el control de acceso remoto al usuario en el equipo local. Para ello, en el registro en HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System agregar la entrada DWORD LocalAccountTokenFilterPolicy con un valor de 1 en. Para agregar el registro de entrada desde una CLI el comando Abrir o con PowerShell escriba **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  En el Firewall de Windows del equipo que desea proteger, seleccione **Permitir una aplicación o una característica a través del Firewall** y habilitar el **uso compartido de impresoras y archivos** e **Instrumentación de administración de Windows**. Puede configurar la directiva de firewall con un GPO de equipos que pertenecen a un dominio.

    ![Configuración del Firewall](./media/site-recovery-vmware-to-azure-classic/mobility1.png)

2. Agregue la cuenta que creó:

    - Abra **cspsconfigtool**. Está disponible como un acceso directo en el escritorio y se encuentra en la carpeta de \home\svsystems\bin [ubicación de instalación].
    - En la pestaña **Administrar cuentas** , haga clic en **Agregar cuenta**.
    - Agregue la cuenta que creó. Después de agregar la cuenta debe proporcionar las credenciales al agregar un equipo a un grupo de protección.


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Prepararse para la inserción automática en servidores Linux

1.  Asegúrese de que el equipo de Linux que desea proteger se admite como se describe en [los requisitos previos de local](#on-premises-prerequisites). Asegúrese de que hay conectividad de red entre el equipo que desea proteger y el servidor de administración que ejecuta el servidor de procesos.

2.  Crear una cuenta que puede ser usada por el servidor de procesos para acceder al equipo. La cuenta debe ser un usuario de raíz del servidor de origen Linux. Tenga en cuenta que estas credenciales solo se usan para la instalación de inserción de servicio de movilidad.

    - Abra **cspsconfigtool**. Está disponible como un acceso directo en el escritorio y se encuentra en la carpeta de \home\svsystems\bin [ubicación de instalación].
    - En la pestaña **Administrar cuentas** , haga clic en **Agregar cuenta**.
    - Agregue la cuenta que creó. Después de agregar la cuenta debe proporcionar las credenciales al agregar un equipo a un grupo de protección.

3.  Compruebe que el archivo/etc/hosts del servidor de origen Linux contiene entradas que asignan el nombre de host local a direcciones IP asociadas con todos los adaptadores de red.
4.  Instalar la última openssh, servidor openssh, openssl paquetes en el equipo que desea proteger.
5.  Asegúrese de que SSH está habilitado y en ejecución en el puerto 22.
6.  Habilitar la autenticación de SFTP subsistema y la contraseña del archivo sshd_config como sigue:

    - Inicie sesión como raíz.
    - En el archivo de /etc/ssh/sshd_config archivo, busque la línea que comienza con PasswordAuthentication.
    - Elimine la línea y cambie el valor de **no** a **Sí**.
    - Busque la línea que comienza con **subsistema** y elimine la línea.

        ![Linux](./media/site-recovery-vmware-to-azure-classic/mobility2.png)


### <a name="install-the-mobility-service-manually"></a>Instalar manualmente el servicio de movilidad

Los instaladores están disponibles en C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository.

Sistema operativo de origen | Archivo de instalación del servicio de movilidad
--- | ---
Windows Server (64 bits sólo) | ASR_UA_9 de Microsoft. *.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6 (solo 64 bits) | ASR_UA_9 de Microsoft. *.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (solo 64 bits) | ASR_UA_9 de Microsoft. *.0.0_SLES11-SP3-64_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (solo 64 bits) | ASR_UA_9 de Microsoft. *.0.0_OL6-64_*release.tar.gz


#### <a name="install-manually-on-a-windows-server"></a>Instalar manualmente en un servidor de Windows


1. Descargar y ejecutar el programa de instalación correspondiente.
2. Antes de **empezar **, seleccione **servicio de movilidad**.

    ![Servicio de movilidad](./media/site-recovery-vmware-to-azure-classic/mobility3.png)

3. En **Detalles de configuración de servidor** , especifique la dirección IP del servidor de administración y la contraseña que se ha generado al instalar los componentes de servidor de administración. Puede recuperar la frase de contraseña ejecutando: ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** en el servidor de administración.

    ![Servicio de movilidad](./media/site-recovery-vmware-to-azure-classic/mobility6.png)

4. En la **Ubicación de instalación** dejar la ubicación predeterminada y haga clic en **siguiente** para comenzar la instalación.
5. En el **Progreso de la instalación** supervisar la instalación y reinicie el equipo si se le solicita.

También puede instalar desde la línea de comandos:

UnifiedAgent.exe [/ función < agente/MasterTarget >] [/ InstallLocation <Installation Directory>] [/ CSIP <IP address of CS to be registered with>] [/ PassphraseFilePath <Passphrase file path>] [/ LogFilePath <Log File Path>]

Donde:

- / Función: obligatorio. Especifica si se debe instalar el servicio de movilidad.
- / InstallLocation: obligatorio. Especifica dónde instalar el servicio.
- / PassphraseFilePath: obligatorio. Especifica la frase de contraseña del servidor de configuración.
- / LogFilePath: obligatorio. Especifica la ubicación de archivos de configuración de registro

#### <a name="uninstall-mobility-service-manually"></a>Desinstalar el servicio de movilidad manualmente

Servicio de movilidad se puede desinstalar mediante el programa quitar agregar desde el Panel de Control o línea de comandos.

El comando para desinstalar el servicio de movilidad con línea de comandos

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="modify-the-ip-address-of-the-management-server"></a>Modifique la dirección IP del servidor de administración

Después de ejecutar al asistente puede modificar la dirección IP del servidor de administración como sigue:

1. Abra la hostconfig.exe archivo (que se encuentra en el escritorio).
2. En la pestaña **Global** puede cambiar la dirección IP del servidor de administración.

    >[AZURE.NOTE] Sólo debe cambiar la dirección IP del servidor de administración. El número de puerto para las comunicaciones del servidor de administración debe ser 443 y usar HTTPS debe estar habilitado hacia la izquierda. No se puede modificar la frase de contraseña.

    ![Dirección IP de servidor](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### <a name="install-manually-on-a-linux-server"></a>Instalar manualmente en un servidor Linux:

1. Copie el archivo tar adecuado basado en la tabla anterior en el equipo de Linux que desea proteger.
2. Abra un programa de consola y extraer el archivo tar en zip a una ruta de acceso local ejecutando:`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Crear un archivo passphrase.txt en el directorio local a la que extrae el contenido del archivo tar. Para hacer esto copie la frase de contraseña de C:\ProgramData\Microsoft Azure sitio Recovery\private\connection.passphrase en el servidor de administración y guárdelo en passphrase.txt ejecutando *`echo <passphrase> >passphrase.txt`* en el shell.
4. Instalar el servicio de movilidad especificando *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especificar la dirección IP interna del servidor de administración y asegúrese de que está seleccionado el puerto 443.

**También puede instalar desde la línea de comandos**:

1. Copie la frase de contraseña de \InMage Systems\private\connection C:\Program Files (x86) en el servidor de administración y guardarlo como "passphrase.txt" en el servidor de administración. A continuación, ejecute estos comandos. En nuestro ejemplo, la dirección IP del servidor es 104.40.75.37 y el puerto HTTPS debe estar 443:

Instalar en un servidor de producción:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Instalar en el servidor de destino principal:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## <a name="step-10-enable-protection-for-a-machine"></a>Paso 10: Habilitar la protección de un equipo

Para habilitar la protección agregar máquinas virtuales y servidores físicos a un grupo de protección. Antes de empezar, tenga en cuenta lo siguiente si está proteger máquinas virtuales de VMware:

- Máquinas virtuales de VMware se detectan cada 15 minutos y podría tardar más de 15 minutos para que aparezcan en el portal de recuperación de sitio después de la detección.
- Cambios en el entorno de la máquina virtual (como la instalación de herramientas de VMware) pueden tardar más de 15 minutos en actualizarse en el sitio de recuperación.
- Puede comprobar la última vez descubierta máquinas virtuales de VMware en el **último contacto** campo para el host de servidor/ESXi vCenter en la pestaña **Configuración de servidores** .
- Si tiene un grupo de protección que ya ha creado y agregar un host de servidor o ESXi vCenter después de eso, puede tardar más de 15 minutos para el portal de Azure sitio recuperación actualizar y máquinas virtuales que se mostrarán en el cuadro de diálogo **Agregar equipos a un grupo de protección** .
- Si desea continuar inmediatamente con agregar equipos al grupo de protección sin esperar la detección programada, resalte el servidor de configuración (no haga clic en él) y haga clic en el botón **Actualizar** .

Además, observe:

- Se recomienda diseñar los grupos de protección para que reflejen las cargas de trabajo. Por ejemplo, agregar equipos que ejecutan una aplicación específica al mismo grupo.
- Al agregar equipos a un grupo de protección, el servidor de procesos inserta automáticamente e instala el servicio de movilidad si no esté instalado. Tenga en cuenta que necesitará tener el mecanismo de inserción preparar como se describe en el paso anterior.


Agregar equipos a un grupo de protección:

1. Haga clic en **elementos protegidos** > **Grupo protección** > **máquinas** > Agregar equipos. \As un procedimiento recomendado
2. En **Seleccionar máquinas virtuales** si está proteger máquinas virtuales VMware, seleccione un servidor de vCenter que está administrando su máquinas virtuales de Windows (o el host de EXSi en el que está ejecutando) y, a continuación, seleccione los equipos.

    ![Habilitar protección](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)

3.  En **Seleccionar máquinas virtuales** si está proteger servidores físicos, en el asistente **Agregar máquinas físicas** proporcione la dirección IP y un nombre descriptivo. A continuación, seleccione la familia de sistemas operativos.

    ![Habilitar protección](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)

4. En **Especificar recursos de destino** , seleccione la cuenta de almacenamiento que usa para la replicación y seleccione si se debe usar la configuración para todas las cargas de trabajo. Tenga en cuenta que actualmente no se admiten cuentas de almacenamiento premium.

    >[AZURE.NOTE] 1 se admite el movimiento de las cuentas de almacenamiento creados con el [nuevo portal de Azure](../storage/storage-create-storage-account.md) a través de grupos de recursos.                           2.[migración de cuentas de almacenamiento](../resource-group-move-resources.md) en grupos de recursos en la misma suscripción o a través de suscripciones no es compatible con cuentas de almacenamiento usadas para implementar el sitio de recuperación.

    ![Habilitar protección](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)

5. En **Especificar cuentas** , seleccione la cuenta [configurado](#install-the-mobility-service-with-push-installation) para usar para la instalación automática del servicio de movilidad.

    ![Habilitar protección](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)

6. Haga clic en la marca de verificación para terminar de agregar equipos al grupo de protección y para comenzar la replicación inicial para cada equipo.

    >[AZURE.NOTE] Si la instalación de inserción se ha preparado el servicio de movilidad se instala automáticamente en equipos que no tengan como que se haya agregado al grupo de protección. Después de que el servicio de instalación un trabajo de protección se inicia y se produce un error. Después del error debe reiniciar manualmente cada equipo que tiene instalado el servicio de movilidad. Después de reiniciar el trabajo de protección comienza de nuevo y se produce la replicación inicial.

Puede supervisar su estado en la página de **trabajos** .

![Habilitar protección](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

Además, se puede supervisar el estado de protección de **Los elementos protegidos** > <protection group name> > **máquinas virtuales de Windows**. Después de que finalice la replicación inicial y se sincronizan los datos, estado de equipo cambia a** protegido**.

![Habilitar protección](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)


## <a name="step-11-set-protected-machine-properties"></a>Paso 11: Conjunto de propiedades del equipo protegido

1. Después de un equipo no tiene un estado **protegido** puede configurar sus propiedades de migración tras error. En los detalles del grupo de protección seleccione el equipo y abra la ficha **Configurar** .
2. Recuperación de sitio automáticamente sugiere propiedades para la máquina virtual de Azure y detecta que local en la configuración de red.

    ![Establecer las propiedades de la máquina virtual](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)

3. Puede modificar esta configuración:

    -  **Nombre de Azure VM**: este es el nombre que se asignará a la máquina en Azure después de la migración. El nombre debe cumplir los requisitos de Azure.

    -  **Tamaño de Azure VM**: el número de adaptadores de red se indica mediante el tamaño que especifique para la máquina virtual de destino. [Obtenga más información](../virtual-machines/virtual-machines-linux-sizes.md/#size-tables) acerca de los tamaños y adaptadores. Tenga en cuenta que:

        - Cuando se modifica el tamaño de una máquina virtual y guardar la configuración, cambiará el número de adaptador de red cuando se abre la ficha **Configurar** tiempo siguiente. El número de adaptadores de red de máquinas virtuales de destino es mínimo de la cantidad de adaptadores de red en la máquina virtual de origen y el número máximo de adaptadores de red compatibles con el tamaño de la máquina virtual elegido.
            - Si el número de adaptadores de red en el equipo de origen es menor o igual que el número de adaptadores permitida para el tamaño del equipo de destino, el destino tendrá el mismo número de adaptadores como el origen.
            - Si el número de adaptadores para la máquina virtual de origen supera el número permitido para que se utilizará el tamaño de destino, a continuación, el tamaño máximo de destino.
            - Por ejemplo, si un equipo de origen tiene dos adaptadores de red y el tamaño del equipo de destino admite cuatro, el equipo de destino tendrá dos adaptadores. Si el equipo de origen tiene dos adaptadores, pero el tamaño de destino compatibles solo es compatible con una el equipo de destino tendrá sólo un adaptador.
        - Si la máquina virtual tiene varios adaptadores de red, todos los adaptadores deben conectado a la misma red de Azure.
    - **Red de Azure**: debe especificar una red de Azure máquinas virtuales de Azure se conectará con después de la migración. Si no se especifica las máquinas virtuales de Azure no se pueden conectar a una red. Además deberá especificar una red Azure si desea recuperación de Azure en el sitio local. Recuperación requiere una conexión VPN entre una red de Azure y una red local.
    - **IP de Azure dirección o subred**: seleccione la subred al que debe conectarse la máquina virtual de Azure para cada adaptador de red. Tenga en cuenta que:
        - Si el adaptador de red del equipo de origen está configurado para usar una dirección IP estática puede especificar una dirección IP estática de la máquina virtual de Azure. Si no proporciona una dirección IP estática asignará cualquier dirección IP disponible. Si se especifica la dirección IP de destino pero aún está en uso por otra VM en Azure provocarán migración tras error. Si el adaptador de red del equipo de origen está configurado para utilizar DHCP tendrá DHCP como la configuración de Azure.

## <a name="step-12-create-a-recovery-plan-and-run-a-failover"></a>Paso 12: Crear un plan de recuperación y ejecutar una migración tras error

> [AZURE.VIDEO enhanced-vmware-to-azure-failover]

Puede ejecutar una migración tras error para un único equipo o conmutar múltiples máquinas virtuales que realizar la misma tarea o ejecutar la misma carga de trabajo. Conmutar varios equipos al mismo tiempo que agregarlas a un plan de recuperación.

### <a name="create-a-recovery-plan"></a>Crear un plan de recuperación

1. En la página de **Planes de recuperación** haga clic en **Agregar el Plan de recuperación** y agregue un plan de recuperación. Especificar los detalles del plan y seleccione **Azure** como destino.

    ![Configurar el plan de recuperación](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)

2. En **Seleccione una máquina Virtual** seleccione un grupo de protección y, a continuación, seleccione equipos en el grupo al que agregar el plan de recuperación.

    ![Agregar máquinas virtuales de Windows](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

Puede personalizar el plan para crear grupos y secuencia el orden en que se error máquinas en el plan de recuperación sobre. También puede agregar secuencias de comandos y las indicaciones para acciones manuales. Pueden crear secuencias de comandos manualmente o mediante el uso de [Azure automatización Runbooks](site-recovery-runbook-automation.md). [Obtenga más](site-recovery-create-recovery-plans.md) información sobre cómo personalizar los planes de recuperación.

## <a name="run-a-failover"></a>Ejecutar una migración tras error

Antes de ejecutar una migración tras error tenga en cuenta que:


- Asegúrese de que está ejecutando el servidor de administración y disponible - en caso contrario, migración tras error se producirá un error.
- Si ejecuta una nota planificado migración tras error que:

    - Si es posible debe apagar equipos principales antes de ejecutar una migración tras error no planeado. Esto garantiza que no tiene el origen y la réplica máquinas que se ejecutan al mismo tiempo. Si va a replicar máquinas virtuales de VMware, a continuación, cuando se ejecuta una migración tras error planificado puede especificar que sitio recuperación deberían ser mejor esfuerzo para apagar los equipos de origen. Según el estado del sitio primario Esto puede o no funcionen. Si va a replicar servidores físicos sitio recuperación no ofrece esta opción.
    - Al realizar una migración tras error planificado detiene la replicación de datos de equipos principales para cualquier delta de datos no se transferirán después de que comience una migración tras error no planeado.

- Si desea conectarse a la máquina virtual de réplica en Azure después de la migración, Habilitar conexión a Escritorio remoto en el equipo de origen antes de ejecutar la migración tras error y permitir la conexión RDP a través del firewall. También deberá permitir RDP en el extremo de la máquina virtual Azure público después de la migración. Siga estos [procedimientos recomendados](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) para asegurarse de que RDP funciona después de un error.

>[AZURE.NOTE] Para obtener el mejor rendimiento al realizar una migración tras error en Azure, asegúrese de que ha instalado al agente de Azure en el equipo protegido. Esto ayuda de inicio de forma más rápida y también ayuda en diagnóstico en caso de problemas. Puede ser agente de Linux se encuentra [aquí](https://github.com/Azure/WALinuxAgent) - y Windows agente puede encontrarse [aquí](http://go.microsoft.com/fwlink/?LinkID=394789)

### <a name="run-a-test-failover"></a>Ejecutar un error de prueba

Ejecutar un error de prueba para simular los procesos de migración y recuperación en una red aislado que no afecta a su entorno de producción y continúa con normalidad replicación normal. Inicia la migración tras error de prueba en el origen y se puede ejecutar en un par de maneras:

- **No se especifica una red de Azure**: si ejecuta un error de prueba sin una red la prueba simplemente comprueba que máquinas virtuales inicio y se muestran correctamente en Azure. Máquinas virtuales no se pueden conectar a una red de Azure después de la migración.
- **Especificar una red Azure**: este tipo de migración tras error comprueba que el entorno de replicación completa procede como se esperaba y que máquinas virtuales de Windows Azure están conectados a la red especificada.


1. En la página de **Planes de recuperación** seleccione el plan y haga clic en **Prueba de migración tras error**.

    ![Agregar máquinas virtuales de Windows](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)

2. **Confirmar probar migración tras error** de seleccione **Ninguno** para indicar que no desea usar una red Azure para la migración tras error de prueba, o seleccionar la red a la que se conectará la prueba máquinas virtuales después de la migración. Haga clic en la marca de verificación para iniciar la migración tras error.

    ![Agregar máquinas virtuales de Windows](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)

3. Supervisar el progreso de la migración tras error en la ficha **trabajos** .

    ![Agregar máquinas virtuales de Windows](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)

4. Una vez completada la migración tras error también debería poder ver la réplica Azure equipo aparezca en el portal de Azure > **máquinas virtuales de Windows**. Si desea iniciar una conexión RDP Azure VM debe abrir el puerto 3389 en el extremo de la máquina virtual.

5. Cuando haya terminado, en la migración tras error alcanza la completar pruebas fase, haga clic en prueba completa para finalizar. En notas grabar y guardar las observaciones asociadas con la migración tras error de prueba.

6. Haga clic en **la prueba de migración tras error esté completa** automáticamente limpiar el entorno de prueba. Después de esto se realiza la migración tras error de prueba mostrará un estado **completado** . Se eliminan todos los elementos o máquinas virtuales que se crean automáticamente durante la migración tras error de prueba. Tenga en cuenta que si un error de prueba continúa más de dos semanas se completó por forzar.



### <a name="run-an-unplanned-failover"></a>Ejecutar una migración tras error planificado

No planeado migración tras error se inicia desde Azure y se puede realizar incluso si no está disponible el sitio primario.


1. En la página de **Planes de recuperación** , seleccione el plan y haga clic en **migración tras error** > **Planificado migración tras error**.

    ![Agregar máquinas virtuales de Windows](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)

2. Si va a replicar máquinas virtuales de VMware puede seleccionar para probar y apagar VM local. Esto es mejor esfuerzo y migración tras error seguirá el esfuerzo para ver si se realiza correctamente o no. Si no tiene la éxito detalles del error aparecerá en la ficha **trabajos **> **Trabajos de migración tras error no planeado**.

    ![Agregar máquinas virtuales de Windows](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

    >[AZURE.NOTE] Esta opción no está disponible si está replicar servidores físicos. Deberá probar y cerrarlos manualmente si es posible.

3. En **Confirme de migración tras error** , compruebe la dirección de migración tras error (en Azure) y seleccione el punto de recuperación que desea usar para la migración tras error. Si habilita múltiples VM cuando configura las propiedades de replicación puede recuperar el punto de recuperación de aplicaciones o bloqueo coherente más reciente. También puede seleccionar **el punto de recuperación de personalizada** recuperar a un momento anterior en el tiempo. Haga clic en la marca de verificación para iniciar la migración tras error.

    ![Agregar máquinas virtuales de Windows](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)

3. Espere a que el trabajo no planeado migración tras error se complete. Puede supervisar el progreso de la migración tras error en la ficha **trabajos** . Tenga en cuenta que incluso si se producen errores durante la migración de tras error planificado el plan de recuperación se ejecuta hasta que se complete. También debería poder ver la réplica Azure máquina aparecen en máquinas virtuales en el portal de Azure.

### <a name="connect-to-replicated-azure-virtual-machines-after-failover"></a>Conectarse a replicar máquinas virtuales de Windows Azure después de la migración

Para conectarse a replica máquinas virtuales de Azure después de migración tras error aquí es lo que necesita:

1. Una conexión a Escritorio remoto debe estar habilitada en el equipo principal.
2. Firewall de Windows en el equipo principal para permitir RDP.
3. Después de la migración debe agregar RDP al extremo público de Azure máquina virtual.

[Obtenga más información](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) sobre cómo configurar esto.


## <a name="deploy-additional-process-servers"></a>Implementar servidores de procesos adicionales

Si tiene escalar supera la implementación más allá de 200 equipos de origen o de la tasa de renovación total diaria de salida de 2 TB, necesitará servidores de procesos adicionales para controlar el volumen de tráfico. Para configurar un servidor de proceso adicional Compruebe los requisitos de [servidores de procesos adicionales](#additional-process-servers) y, a continuación, siga las instrucciones aquí para configurar el servidor de procesos. Después de configurar el servidor puede configurar los equipos de origen para usarlo.

### <a name="set-up-an-additional-process-server"></a>Configurar un servidor de proceso adicional

Configurar un servidor de proceso adicional como sigue:

- Ejecutar el Asistente para configurar un servidor de administración como un servidor de procesos solo unificado.
- Si desea administrar la replicación de datos con el nuevo servidor de proceso deberá migrar sus equipos protegidos para hacerlo.

### <a name="install-the-process-server"></a>Instalar al servidor de procesos

1. En la página de inicio rápido descargar el archivo de instalación unificado para la instalación del componente de recuperación del sitio. Ejecutar el programa de instalación.
2. En **antes de comenzar** seleccione **Agregar servidores de proceso adicional escalar implementación**.

    ![Agregar el servidor de procesos](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)

3. Completar el Asistente de la misma manera que hizo al [Configurar](#step-5-install-the-management-server) el primer servidor de administración.

4. En **Detalles de configuración de servidor** , especifique la dirección IP del servidor de administración original en el que instaló el servidor de configuración y la frase de contraseña. En el servidor de administración original ejecutar ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** para obtener la frase de contraseña.

    ![Agregar el servidor de procesos](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar máquinas para usar el nuevo servidor de proceso

1. Abrir **Configuración servidores** > **servidor** > nombre del servidor de administración original > **Detalles del servidor**.

    ![Actualizar el servidor de proceso](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)

2. En la lista de **Servidores de procesos** haga clic en **Cambiar el servidor de proceso** junto al servidor que desea modificar.

    ![Actualizar el servidor de proceso](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)

3. En el **Servidor de procesos de cambio** > **Servidor del proceso de destino** seleccionar el servidor de administración de nuevo y, a continuación, seleccione las máquinas virtuales que va a controlar el nuevo servidor de proceso. Haga clic en el icono de información para obtener información acerca del servidor. El espacio medio necesario para replicar cada máquina virtual seleccionada en el servidor de proceso nueva se muestra para ayudarle a tomar decisiones de carga. Haga clic en la marca de verificación para empezar a replicar en el servidor de proceso de nuevo.

    ![Actualizar el servidor de proceso](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)




## <a name="vmware-permissions-for-vcenter-access"></a>Permisos de VMware vCenter acceso

El servidor de procesos puede detectar automáticamente las máquinas virtuales en un servidor de vCenter. Para llevar a cabo la detección automática debe definir una función (Azure_Site_Recovery) en el nivel de vCenter para permitir la recuperación de sitio tener acceso al servidor de vCenter. Tenga en cuenta que, si solo necesita migrar máquinas VMware a Azure y no necesita recuperación de Azure, puede definir una función de solo lectura es suficiente. Configurar los permisos, como se describe en [paso 6: configurar las credenciales para el servidor vCenter](#step-6-set-up-credentials-for-the-vcenter-server) se resumen los permisos de la función en la tabla siguiente.

**Función** | **Detalles** | **Permisos**
--- | --- | ---
Función Azure_Site_Recovery | Detección de VMware VM |Asignar estos privilegios para el servidor de centro de v:<br/><br/>Almacén de datos -> asignar espacio, examinar almacén de datos, las operaciones de bajo nivel de un archivo., quitar archivo, archivos de máquina virtual de actualización<br/><br/>Red -> asignar de red<br/><br/>Recursos -> máquina virtual de asignar al grupo de recursos, migrar apagado de máquina virtual, migrar encendido máquina virtual<br/><br/>Tareas -> Crear tarea, tarea de actualización<br/><br/>Máquina virtual -> configuración<br/><br/>Máquina virtual -> interactuar -> pregunta de respuesta, dispositivo conexión, configurar CD media, configurar medios de disco, apagado, Power en, instalación las herramientas de VMware<br/><br/>Máquina virtual -> Inventario -> crear, registrar, eliminar del registro<br/><br/>Máquina virtual -> aprovisionamiento -> Permitir descarga de máquina virtual, archivos de máquina virtual de permitir cargar<br/><br/>Máquina virtual -> instantáneas -> Quitar instantáneas
rol de usuario de vCenter | VMware VM detección y recuperación tras errores sin apagado de VM de origen | Asignar estos privilegios para el servidor de centro de v:<br/><br/>Objeto de centro de datos –> propagar al objeto secundario, función = sólo lectura <br/><br/>El usuario está asignado a nivel de centro de datos y, por tanto, tiene acceso a todos los objetos en el centro de datos.  Si desea restringir el acceso, asignar la función **sin acceso** con el objeto de **propagar hijo** a los objetos secundarios (ESX hosts, almacenes de datos, máquinas virtuales y redes).
rol de usuario de vCenter | Conmutación y | Asignar estos privilegios para el servidor de centro de v:<br/><br/>Objeto de centro de datos: propagar al objeto secundario, función = Azure_Site_Recovery<br/><br/>El usuario está asignado a nivel de centro de datos y, por tanto, tiene acceso a todos los objetos en el centro de datos.  Si desea restringir el acceso, asigne la función de **ningún acceso **con la **propagar a objeto secundario** al objeto secundario (ESX hosts, almacenes de datos, máquinas virtuales y redes).  



## <a name="third-party-software-notices-and-information"></a>Avisos de software de terceros e información

No traducir texto o localizar

El software y el firmware que se ejecuta en el producto de Microsoft o un servicio basado en o incorpora material de los proyectos que se enumeran a continuación (colectivamente, "código de terceros").  Microsoft es el autor del código de terceros no original.  El aviso de copyright original y la licencia, que Microsoft ha recibido este código de terceros y se establecen presentado más adelante.

La información en la sección A está relacionado con componentes de terceros de código de los proyectos que se muestran a continuación. Tales licencias y la información se proporcionan solo con fines informativos.  Este código de terceros que se está relicensed a usted Microsoft en términos de licencia de software de Microsoft para el producto de Microsoft o un servicio.  

La información en la sección B está relacionado con los componentes de código de terceros que se están disponibles para Microsoft en términos de la licencia originales.

Puede encontrar el archivo completo en el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserva todos los derechos que no se conceden expresamente, ya sea de forma implícita, desestimación o de otro tipo.

## <a name="next-steps"></a>Pasos siguientes

[Más información sobre la recuperación](site-recovery-failback-azure-to-vmware-classic.md) para que aparezca el error sobre los equipos que se ejecuta en Azure a su entorno local.
