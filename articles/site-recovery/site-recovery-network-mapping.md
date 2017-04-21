<properties
    pageTitle="Preparar la asignación de red para la protección de la máquina virtual de Hyper-V con VMM en Azure sitio recuperación | Microsoft Azure"
    description="Configurar la asignación de red para la replicación de máquina virtual de Hyper-V desde un centro de datos local en Azure o en un sitio secundario."
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


# <a name="prepare-network-mapping-for-hyper-v-virtual-machine-protection-with-vmm-in-azure-site-recovery"></a>Preparar la asignación de red para la protección de la máquina virtual de Hyper-V con VMM en recuperación de sitio de Azure

Recuperación de sitio de Azure contribuye a su estrategia de recuperación (BCDR) de desastres y continuidad empresarial por coordinar replicación, migración tras error y recuperación de máquinas virtuales y servidores físicos.

Este artículo describe la asignación de red, que le ayuda a óptimo configurar ajustes de red cuando usa recuperación de sitios para replicar máquinas virtuales Hyper-V ubicado en nubes de VMM entre dos centros de datos local, o entre un centro de datos local y Azure. Tenga en cuenta que si está replicar máquinas virtuales de Hyper-V sin una nube VMM o replicar máquinas virtuales de VMware o servidores físicos, en este artículo no es relevante.

Publicar comentarios o preguntas en la parte inferior de este artículo o en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Información general

Asignación de red se usa cuando se implementa Azure sitio recuperación para replicar máquinas virtuales Hyper-V a Azure o a un centro de datos secundario con replicación de réplica de Hyper-V o SAN.

- **Máquinas virtuales de Hyper-V replicar en nubes VMM entre dos centros de datos local**: red mapas de asignación entre redes de máquina virtual en un servidor VMM de origen y VM en un servidor VMM destino haga lo siguiente:

    - **Máquinas virtuales de conectar después de la migración**, se asegura de que máquinas virtuales se pueden conectar a redes apropiadas después de la migración. La máquina virtual de réplica se conectará a la red de destino que está asignada a la red de origen.
    - **Máquinas virtuales de lugar réplica Windows en servidores de host**: colocar óptima máquinas virtuales de réplica en servidores de host de Hyper-V. Máquinas virtuales de réplica se pondrán en hosts que pueden tener acceso a las redes VM asignadas.
    - **Ninguna asignación de red**: si no configurar asignación de red, replicadas máquinas virtuales no se pueden conectar a redes VM después de la migración.

- **Máquinas virtuales de Hyper-V replicar en un VMM local en la nube en Azure**: red mapas de asignación entre redes VM en el servidor VMM de origen y de destino Azure redes para hacer lo siguiente:
    - **Conectar máquinas virtuales después de la migración**, todos los equipos que migración tras error en la misma red puede conectar entre sí, con independencia de qué plan de recuperación se encuentran en.
    - **Puerta de enlace de red**: si una puerta de enlace de red está configurado en el destino de red Azure, máquinas virtuales puede conectarse a otras máquinas virtuales de local.
    - **Ninguna asignación de red**: si no configurar asignación de red, solamente máquinas virtuales esa migración tras error en el mismo plan de recuperación será puedan conectarse entre sí después de error sobre a Azure.


## <a name="network-mapping-example"></a>Ejemplo de asignación de red

Asignación de red puede configurarse entre redes VM en dos servidores VMM o en un único servidor VMM si dos sitios administrados por el mismo servidor. Cuando está correctamente configurada asignación y la replicación está habilitada, una máquina virtual en la ubicación principal se conectará a una red y su réplica en la ubicación de destino se conectará con su red asignada.

Si redes configuradas correctamente en VMM, al seleccionar una red de la máquina virtual de destino durante la asignación de red, se mostrarán las nubes de origen VMM que usan la red de la máquina virtual de origen, junto con las redes de máquina virtual de destino disponibles en las nubes de destino que se usan para la protección.

Aquí tenemos un ejemplo para ilustrar este mecanismo. Echemos una organización con dos ubicaciones de Nueva York y Chicago.

**Ubicación** | **Servidor VMM** | **Redes VM** | **Asignado a**
---|---|---|---
Nueva York | VMM de Nueva York| VMNetwork1 de Nueva York | Asignado a Chicago VMNetwork1
 |  | VMNetwork2 de Nueva York | No asignado
Chicago | VMM Chicago| Chicago VMNetwork1 | Asignado a VMNetwork1 de Nueva York
 | | Chicago VMNetwork2 | No asignado

Con este ejemplo:

- Cuando se crea una máquina virtual de réplica para cualquier máquina virtual que está conectado a VMNetwork1 de Nueva York, se le conectará al VMNetwork1 Chicago.
- Cuando se crea una máquina virtual de réplica para VMNetwork2 de Nueva York o Chicago VMNetwork2, no se pueden conectar a una red.

Aquí es cómo se configuran nubes de VMM en nuestra organización de ejemplo y las redes lógicas asociadas a las nubes.

### <a name="cloud-protection-settings"></a>Configuración de protección de la nube

**Nube protegido** | **Protección de la nube** | **Red lógica (Nueva York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NOD</p><p></p> | <p>LogicalNetwork1 de Nueva York</p><p>Chicago LogicalNetwork1</p>
SilverCloud2 | <p>NOD</p><p></p> | <p>LogicalNetwork1 de Nueva York</p><p>Chicago LogicalNetwork1</p>

### <a name="logical-and-vm-network-settings"></a>Configuración de red lógica y VM

**Ubicación** | **Red lógica** | **Red VM asociada**
---|---|---
Nueva York | LogicalNetwork1 de Nueva York | VMNetwork1 de Nueva York
Chicago | Chicago LogicalNetwork1 | Chicago VMNetwork1
 | LogicalNetwork2Chicago | Chicago VMNetwork2

### <a name="target-networks"></a>Redes de destino

En función de esta configuración, cuando se selecciona la red de la máquina virtual de destino, la siguiente tabla muestra las opciones que estarán disponibles.

**Seleccione** | **Nube protegido** | **Protección de la nube** | **Red de destino disponible**
---|---|---|---
Chicago VMNetwork1 | SilverCloud1 | SilverCloud2 | Disponible
 | GoldCloud1 | GoldCloud2 | Disponible
Chicago VMNetwork2 | SilverCloud1 | SilverCloud2 | No disponible
 | GoldCloud1 | GoldCloud2 | Disponible



## <a name="multiple-subnets"></a>Varias subredes

Si la red de destino tiene varias subredes y una de esas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, a continuación, la máquina virtual de réplica se conectará a esa subred de destino después de la migración. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.


### <a name="failback"></a>Recuperación

Para ver qué ocurre en el caso de recuperación (replicación inversa), supongamos que VMNetwork1 de Nueva York está asignado a VMNetwork1-Chicago, con la siguiente configuración.


**Máquina virtual** | **Conectado a la red VM**
---|---
VM1 | Red VMNetwork1
VM2 (réplica de VM1) | Chicago VMNetwork1

Con esta configuración, vamos a revisar lo que sucede en un par de escenarios posibles.

**Escenario** | **Resultado**
---|---
Sin cambios en las propiedades de la red de VM 2 después de la migración. | 1 VM permanece conectado a la red de origen.
Propiedades de la red de VM 2 se cambia después de la migración y se desconecta. | Máquina virtual 1 está desconectado.
Propiedades de la red de VM 2 se cambia después de la migración y está conectado a VMNetwork2 Chicago. | Si no se ha asignado VMNetwork2 Chicago, se desconectarán VM-1.
Asignación de red de Chicago VMNetwork1 cambia. | Máquina virtual 1 se conectará a la red que ahora se asignan a VMNetwork1 Chicago.


## <a name="next-steps"></a>Pasos siguientes

Ahora que ya tiene una mejor comprensión de asignación de red, [Introducción a la implementación de recuperación de sitios](site-recovery-best-practices.md).
