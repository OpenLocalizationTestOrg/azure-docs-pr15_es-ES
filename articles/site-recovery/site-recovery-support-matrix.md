<properties
    pageTitle="Matriz de soporte de recuperación del sitio de Azure | Microsoft Azure"
    description="Resume los componentes y sistemas operativos compatibles para la recuperación de sitio de Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="azure-site-recovery-support-matrix"></a>Matriz de soporte de recuperación del sitio de Azure

Este artículo resumen los sistemas operativos compatibles y componentes para implementaciones de recuperación de sitios de Azure. Una lista de componentes compatibles y los requisitos previos está disponible para cada escenario de implementación de cada una el artículo correspondiente de la implementación y este documento resume ellos.

## <a name="supported-operating-systems-for-virtualization-servers"></a>Sistemas operativos compatibles para servidores de virtualización


**Origen** | **Destino** | **Sistema operativo host**
---|---|---|--- 
**Hosts Hyper-V (sin VMM)** | Azure | Windows Server 2012 R2 con las actualizaciones más recientes
**Hosts Hyper-V (con VMM)** | Azure | Windows Server 2012 R2 con las actualizaciones más recientes
**Hosts Hyper-V (con VMM)** | Sitio VMM secundario | Al menos Windows Server 2012 con las actualizaciones más recientes
**VMware hosts/vCenter** | Azure | vCenter 5.5 o 6.0 (compatibilidad con 5,5 características solo) <br/><br/> vSphere 6.0, 5.5 o 5.1 con las actualizaciones más recientes
**VMware hosts/vCenter** | Sitio de VMware secundario | vCenter 5.5 o 6.0 (compatibilidad con 5,5 características solo) <br/><br/> vSphere 6.0, 5.5 o 5.1 con las actualizaciones más recientes

## <a name="supported-requirements-for-replicated-machines"></a>Requisitos compatibles para equipos duplicados

**Origen** | **¿Qué se duplica** | **Destino** | **Sistema operativo host**
---|---|---|--- 
**Máquinas virtuales de Hyper-V** | Cualquier carga de trabajo | Azure | Cualquier sistema operativo de invitado [compatible con Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> Máquinas virtuales deben cumplir los [requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Máquinas virtuales de Hyper-V (con VMM)** | Cualquier carga de trabajo | Azure | Cualquier sistema operativo de invitado [compatible con Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> Máquinas virtuales deben cumplir los [requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Máquinas virtuales de Hyper-V (con VMM)** | Cualquier carga de trabajo | Sitio VMM secundario | Cualquier sistema operativo de invitado [compatible con Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)
**Máquinas virtuales de VMware** | Cualquier carga de trabajo que se ejecuta en la máquina virtual de Windows | Azure | 64 bits de Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 con al menos SP1<br/><br/> Máquinas virtuales deben cumplir los [requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Máquinas virtuales de VMware** | Cualquier carga de trabajo que se ejecutan en Linux VM | Azure | Rojos sombrero Enterprise Linux 6,7, 7.1, 7.2 <br/><br/> CentOS 6.5, 6.6, 6,7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 ejecutando rojos sombrero compatible kernel o Unbreakable Enterprise Kernel versión 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Almacenamiento necesario: (EXT3, ETX4, ReiserFS, XFS); de sistema de archivos Múltiples rutas software el dispositivo asignador (múltiples rutas)) Administrador de volumen: (LVM2). Servidores físicos con almacenamiento del controlador HP CCISS no son compatibles. El sistema de archivos ReiserFS sólo se admite en SUSE Linux Enterprise Server 11 SP3.<br/><br/> Máquinas virtuales deben cumplir los [requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Máquinas virtuales de VMware** | Cualquier carga de trabajo que se ejecuta en la máquina virtual de Windows | Sitio de VMware secundario | 64 bits de Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 con al menos SP1
**Máquinas virtuales de VMware** | Cualquier carga de trabajo que se ejecutan en Linux VM | Sitio de VMware secundario | Rojos sombrero Enterprise Linux 6,7, 7.1, 7.2 <br/><br/> CentOS 6.5, 6.6, 6,7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 ejecutando rojos sombrero compatible kernel o Unbreakable Enterprise Kernel versión 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Almacenamiento necesario: (EXT3, ETX4, ReiserFS, XFS); de sistema de archivos Múltiples rutas software el dispositivo asignador (múltiples rutas)) Administrador de volumen: (LVM2). Servidores físicos con almacenamiento del controlador HP CCISS no son compatibles. El sistema de archivos ReiserFS sólo se admite en SUSE Linux Enterprise Server 11 SP3.
**Servidores físicos** | Cualquier carga de trabajo que se ejecutan en Windows | Azure | 64 bits de Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 con al menos SP1
**Servidores físicos** | Cualquier carga de trabajo que se ejecutan en Linux | Azure | Rojos sombrero Enterprise Linux 6.7,7.1,7.2 <br/><br/> CentOS 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 ejecutando rojos sombrero compatible kernel o Unbreakable Enterprise Kernel versión 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Almacenamiento necesario: (EXT3, ETX4, ReiserFS, XFS); de sistema de archivos Múltiples rutas software el dispositivo asignador (múltiples rutas)) Administrador de volumen: (LVM2). Servidores físicos con almacenamiento del controlador HP CCISS no son compatibles. El sistema de archivos ReiserFS sólo se admite en SUSE Linux Enterprise Server 11 SP3.
**Servidores físicos** | Cualquier carga de trabajo que se ejecutan en Windows | Sitio secundario | 64 bits de Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 con al menos SP1
**Servidores físicos** | Cualquier carga de trabajo que se ejecutan en Linux | Sitio secundario | Rojos sombrero Enterprise Linux 6.7,7.1,7.2 <br/><br/> CentOS 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 ejecutando rojos sombrero compatible kernel o Unbreakable Enterprise Kernel versión 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Almacenamiento necesario: (EXT3, ETX4, ReiserFS, XFS); de sistema de archivos Múltiples rutas software el dispositivo asignador (múltiples rutas)) Administrador de volumen: (LVM2). Servidores físicos con almacenamiento del controlador HP CCISS no son compatibles. El sistema de archivos ReiserFS sólo se admite en SUSE Linux Enterprise Server 11 SP3.


## <a name="provider-versions"></a>Versiones de proveedor

**Nombre** | **Descripción** | **Versión más reciente** | **Soporte técnico** | **Detalles**
---|---|---|---| ---
**Proveedor de servicios de recuperación de sitio de Azure** | Comunicaciones de coordenadas entre servidores local y sitios de Azure y secundario <br/><br/> Instalado en servidores VMM local, o Hyper-V si no hay ningún servidor VMM | 5.1.1700 (disponible desde el portal) | [Características y soluciones más recientes](https://support.microsoft.com/kb/3155002)
**Recuperación de sitio de Azure unificada configuración (VMware a Azure)** | Comunicaciones de coordenadas entre servidores de VMware local y Azure <br/><br/> Instalar en servidores de VMware local | 9.3.4246.1 (disponible desde el portal) | [Características y soluciones más recientes](https://support.microsoft.com/kb/3155002)
**Servicio de movilidad** | Replicación de coordenadas entre servidores de servidores o físico VMware local y sitios de Azure y secundario | NOD (disponible desde el portal) | Instalar en cada VM VMware o servidor físico que desea duplicar. **Agente de servicios de recuperación de Microsoft Azure (MARS)** | Replicación de coordenadas entre máquinas virtuales de Hyper-V y Azure<br/><br/> Instalar en servidores de Hyper-V local (con o sin un servidor VMM) | 2.0.8689.0 (disponible desde el portal) | Este agente utiliza copia de seguridad de Azure y Azure sitio recuperación). [Más] (https://support.microsoft.com/en-us/kb/2997692)

## <a name="next-steps"></a>Pasos siguientes

[Prepararse para su implementación](site-recovery-best-practices.md)

