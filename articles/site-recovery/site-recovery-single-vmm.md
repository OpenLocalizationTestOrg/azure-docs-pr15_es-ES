
<properties
    pageTitle="Azure recuperación de sitio: Máquinas virtuales replicar Hyper-V Windows en un solo servidor VMM | Microsoft Azure"
    description="Este artículo describe cómo replicar máquinas virtuales Hyper-V cuando sólo tenga un único servidor VMM."
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
    ms.workload="backup-recovery"
    ms.date="08/24/2016"
    ms.author="raynew"/>

#  <a name="replicate-hyper-v-virtual-machines-on-a-single-vmm-server"></a>Replicar máquinas virtuales de Hyper-V en un único servidor VMM

Lea este artículo para obtener información sobre cómo replicar máquinas virtuales de Hyper-V ubicados en un servidor de host de Hyper-V en una nube VMM cuando sólo tiene un único servidor VMM en su implementación.

Azure tiene dos distintos [modelos de implementación](../resource-manager-deployment-model.md) para crear y trabajar con recursos: Administrador de recursos de Azure y clásica. Azure también tiene dos portales – el portal clásico Azure que admita el modelo de implementación clásica y el portal de Azure con soporte para ambos modelos de implementación. Este artículo contiene instrucciones para configurar la replicación en el portal de Azure.


Si tiene alguna pregunta después de leer este artículo, publique en los comentarios de Disqus en la parte inferior de este artículo o en el [foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Información general

Si desea replicar VM Hyper-V ubicado en hosts Hyper-V VMM y solo tiene un único servidor VMM, puede [replicar en Azure](site-recovery-vmm-to-azure.md), o entre nubes en el servidor VMM único.

Se recomienda replicar en Azure porque migración y recuperación no transparente al replicar entre nubes y un número de pasos manuales es necesarias. Si desea replicar con el servidor VMM solo, puede hacer lo siguiente:

- Replicar con un solo VMM servidor independiente
- Replicar con un solo servidor VMM implementado en un clúster de Windows extendido


## <a name="replicate-across-sites-with-a-single-standalone-vmm-server"></a>Replicar en los sitios con un solo VMM servidor independiente

![Servidor virtual de VMM independiente](./media/site-recovery-single-vmm/single-vmm-standalone.png)

En este escenario implementar el servidor VMM solo como una máquina virtual en el sitio primario y replicar esta máquina virtual a un sitio secundario con la réplica de Hyper-V y recuperación de sitio.

1. Configurar VMM en una máquina virtual Hyper-V. Le recomendamos que instale la instancia de SQL Server usada VMM en la misma VM para ahorrar tiempo. Si desea usar una instancia remota de SQL Server y una interrupción se produce, necesita recuperar esa instancia antes de que se puede recuperar VMM.
2. Asegúrese de que el servidor VMM tiene al menos dos nubes configurados. Una nube contiene las máquinas virtuales que desea replicar y la nube otra sirve como la ubicación secundaria. Debe disponer de la nube que contiene las máquinas virtuales que desee proteger:

    - Uno o varios grupos host VMM que contiene uno o más servidores de host de Hyper-V en cada grupo de host.
    - Al menos una máquina virtual de Hyper-V en cada servidor de host de Hyper-V.

3. Crear un depósito de servicios de recuperación, generar y descargar una clave de registro de la cámara y registrar el servidor VMM en la cámara. Durante el registro de instalar el proveedor de servicios de recuperación de sitio de Azure en el servidor VMM.
4. Configurar una o más nubes en la VM VMM y agregue los hosts Hyper-V para estas nubes.
3. Establecer la configuración de protección de las nubes. Especifique el nombre del servidor VMM único como las ubicaciones de origen y destino. Para configurar la asignación de red, asigne la red de la máquina virtual para la nube con las máquinas virtuales que desee proteger a la red de la máquina virtual para la nube de replicación.
4. Habilitar la replicación inicial de máquinas virtuales que desea proteger en la red porque ambos nubes que se encuentran en el mismo servidor.
4. En la consola del Administrador de Hyper-V habilitar réplica Hyper-V en el host de Hyper-V que contiene la VM VMM y habilitar la replicación de la máquina virtual. Asegúrese de que no agrega la VM VMM a cualquier nubes que están protegidos por el sitio de recuperación. Así se garantiza que no reemplazar los valores de réplica de Hyper-V por sitio de recuperación.
5. Si desea crear planes de recuperación, especifique el mismo servidor VMM para origen y destino.

Siga las instrucciones de [este artículo](site-recovery-vmm-to-vmm.md) para crear un depósito, registrar el servidor y configurar la protección.

### <a name="what-to-do-in-an-outage"></a>Qué hacer en una interrupción

Si se produce una interrupción completa y necesita trabajar con desde el sitio secundario, haga lo siguiente:

1.  En la consola del Administrador de Hyper-V en el sitio secundario, ejecute una migración tras error planificado conmutar la VM VMM desde el principal sitio secundario.
2.  Compruebe que la VM VMM está listo y en funcionamiento en el sitio secundario.
3.  En la caja fuerte de servicios de recuperación, ejecute una migración tras error planificado conmutar la carga de trabajo máquinas virtuales de principal secundarios nubes. Para completar la migración tras error planificada de las máquinas virtuales, confirme la migración tras error o seleccione un punto de recuperación diferentes según sea necesario.
4.  Una vez completada la migración tras error no planeada, los usuarios pueden acceder a recursos de carga de trabajo en el sitio secundario.

Cuando el sitio primario está funcionando normalmente nuevamente, haga lo siguiente:

1.  En la consola del Administrador de Hyper-V Habilitar replicación inversa de la VM de VMM para iniciar la replicación de secundaria a principal.
2.  En la consola del Administrador de Hyper-V ejecute un error planeado para conmutar el VM VMM al sitio primario. Confirmar la migración tras error para completarla. A continuación, habilite la replicación inversa iniciar la replicación de VMM de principal a secundario.
3.  En la caja fuerte de servicios de recuperación, habilitar la replicación inversa de la carga de trabajo VM, para iniciar la replicación de secundaria a principal.
4.  En la cámara de servicios de recuperación, ejecute un error planeado para conmutar la carga de trabajo máquinas virtuales al sitio primario. Confirmar la migración tras error para completarla. A continuación, habilite la replicación inversa iniciar la replicación de la carga de trabajo máquinas virtuales de principal a secundario.



## <a name="replicate-across-sites-with-a-single-vmm-server-in-a-stretched-cluster"></a>Replicar en los sitios con un solo servidor VMM en un clúster extendido

![Servidor virtual de VMM agrupado](./media/site-recovery-single-vmm/single-vmm-cluster.png)

En lugar de implementar un servidor VMM independiente como una máquina virtual que se aplica a un sitio secundario, puede hacer que VMM altamente disponible mediante la implementación como una máquina virtual en un clúster de migración tras error de Windows. Esto proporciona resistencia de carga de trabajo y una protección contra fallos de hardware. Para implementar con el sitio de recuperación se debería implementar la VM VMM en un clúster extendido entre sitios geográfico independientes. Para hacer esto:

1. Instalar VMM en una máquina virtual en un clúster de migración tras error de Windows y seleccione la opción para ejecutar el servidor como altamente disponibles durante la instalación.
2. La instancia de SQL Server que utiliza VMM debe replicarse con grupos de disponibilidad de SQL Server AlwaysOn para que hay una réplica de la base de datos en el sitio secundario.
3. Siga las instrucciones de [este artículo](site-recovery-vmm-to-vmm.md) para crear un depósito, registrar el servidor y configurar la protección. Debe registrar cada servidor VMM en el clúster en depósito de servicios de recuperación. Para ello, instale al proveedor en un nodo activo y registrar el servidor VMM. A continuación, instale al proveedor en otros nodos.

### <a name="what-to-do-in-an-outage"></a>Qué hacer en una interrupción

Cuando se produce una interrupción, son error sobre el servidor VMM y su base de datos de SQL Server correspondiente y se tiene acceso desde el sitio secundario.


## <a name="next-steps"></a>Pasos siguientes

[Más información](site-recovery-vmm-to-vmm.md) acerca de la implementación detallada de recuperación de sitio para VMM replicación de VMM.
