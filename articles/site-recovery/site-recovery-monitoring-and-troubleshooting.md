<properties
    pageTitle="Supervisar y solucionar problemas de protección para máquinas virtuales y servidores físicos | Microsoft Auzre" 
    description="Recuperación de sitio de Azure coordenadas la replicación, la migración tras error y la recuperación de máquinas virtuales ubicados en servidores locales de Azure o un centro de datos secundario. Use este artículo para supervisar y solucionar problemas de protección de VMM o sitio Hyper-V." 
    services="site-recovery" 
    documentationCenter="" 
    authors="anbacker" 
    manager="mkjain" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/13/2016"    
    ms.author="rajanaki"/>
    
# <a name="monitor-and-troubleshoot-protection-for-virtual-machines-and-physical-servers"></a>Supervisar y solucionar problemas de protección para máquinas virtuales y servidores físicos

Esta guía de solución de problemas y supervisión le permite obtener el estado de la replicación de seguimiento y técnicas para solucionar problemas de recuperación de sitios de Azure.

## <a name="understanding-the-components"></a>Descripción de los componentes

### <a name="vmwarephysical-site-deployment-for-replication-between-on-premises-and-azure"></a>Implementación del sitio de VMware o físico para la replicación entre Azure y local.
Configurar DR entre el equipo de VMware o físico local; Servidor de configuración, patrón de destino y servidor de procesos debe configurado. Al habilitar la protección del servidor de origen Azure recuperación de sitio instalará servicio de movilidad. Publicar interrupción local una vez que el servidor de origen se produce un error sobre a Azure, necesidades de los clientes para configurar un servidor de procesos en Azure y un destino de patrón de servidor local para proteger el servidor de origen local reconstruirse. 

![Implementación del sitio de VMware o físico para la replicación entre local y Azure](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises-site"></a>Implementación de VMM sitio para la replicación entre el sitio local.

Como parte de la configuración de DR entre dos local ubicación; Proveedor de recuperación de Azure sitio debe descargar e instalar en el servidor VMM. Proveedor necesita conexión a internet para asegurarse de que todas las operaciones activadas del Portal de Azure se traduce a las operaciones locales como activar la protección, máquinas virtuales de apagado lado principal Windows como parte de migraciones tras error etcetera.

![Implementación de VMM sitio para la replicación entre el sitio local](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises--azure"></a>Implementación de VMM sitio para la replicación entre local y Azure.

Como parte de la configuración de DR entre local y Azure; Proveedor de recuperación de Azure sitio debe descargar e instalar en el servidor VMM junto con el agente de servicios de recuperación de Azure que debe estar instalado en cada host de Hyper-V. Obtener más información, consulte el [Sitio de información sobre protección de Azure](./site-recovery-understanding-site-to-azure-protection.md) .

![Implementación de VMM sitio para la replicación entre local y Azure](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### <a name="hyper-v-site-deployment-for-replication-between-on-premises--azure"></a>Implementación del sitio de Hyper-V para la replicación entre local y Azure

Esta es la misma que la implementación de VMM: solo diferencia proveedor & agente obtiene instalada en el host de Hyper-V propiamente dicho. Obtener más información, consulte el [Sitio de información sobre protección de Azure](./site-recovery-understanding-site-to-azure-protection.md) .

## <a name="monitor-configuration-protection-and-recovery-operations"></a>Supervisar las operaciones de recuperación, protección y configuración

Cada operación de ASR obtiene auditoría y se controla en la ficha "Trabajos". En caso de cualquier configuración, protección o error de recuperación vaya a la ficha trabajos y ver si hay errores.

![Supervisar las operaciones de recuperación, protección y configuración](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Una vez que encuentre errores en la vista de tareas, seleccione la tarea y haga clic en detalles del ERROR para esa tarea.

![Supervisar las operaciones de recuperación, protección y configuración](media/site-recovery-monitoring-and-troubleshooting/image4.png)

Los detalles del error le sirva para identificar las causas posibles y recomendaciones para el problema.

![Supervisar las operaciones de recuperación, protección y configuración](media/site-recovery-monitoring-and-troubleshooting/image5.png)

En el caso anterior parece haber otra operación que está en curso falla debido a que la configuración de protección. Asegúrese de que resolver el problema según la recomendación: allí después haga clic en RESART para volver a iniciar la operación.

![Supervisar las operaciones de recuperación, protección y configuración](media/site-recovery-monitoring-and-troubleshooting/image6.png)

Opción de reiniciar no está disponible para todas las operaciones: para las que no tiene la opción de reiniciar vaya al objeto y rehacer la operación de nuevo. Todos los trabajos se pueden cancelar en cualquier punto de tiempo con el botón Cancelar en curso.

![Supervisar las operaciones de recuperación, protección y configuración](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## <a name="monitor-replication-health-for-virtual-machine"></a>Supervisar el mantenimiento de la replicación de máquina virtual

Proveedor de servicios de ASR central y supervisión a través del Portal de Azure para cada una de las entidades protegidas remota. Vaya a los elementos protegidos allí después seleccione VMM NUBES o grupos de protección. Ficha de NUBES de VMM solo está disponible para las implementaciones VMM según y todos los demás casos tienen las entidades protegidas en la pestaña de grupos de protección.

![Supervisar el mantenimiento de la replicación de máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Allí después seleccione la entidad protegida en la nube respectiva o el grupo de protección. Una vez que se selecciona la entidad protegida todo permite operaciones se muestran en el panel inferior.

![Supervisar el mantenimiento de la replicación de máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Como se indicó anteriormente en caso de la máquina virtual que estado es fundamental: puede hacer clic en el botón Detalles del ERROR en la parte inferior para ver el error. En función de las "posibles causas" y "Recomendación" mencionado resolver el problema.

![Supervisar el mantenimiento de la replicación de máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Supervisar el mantenimiento de la replicación de máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image11.png)

Nota: Si hay operaciones activas que están en curso o error, a continuación, vaya a la vista de tareas como se mencionó anteriormente para ver el error específico de trabajo.

## <a name="troubleshoot-on-premises-hyper-v-issues"></a>Solucionar problemas de Hyper-V local

Conectarse a la consola del Administrador de Hyper-V local, seleccione la máquina virtual y ver el estado de replicación.

![Solucionar problemas de Hyper-V local](media/site-recovery-monitoring-and-troubleshooting/image12.png)

En este caso se se indica *El estado de replicación* como críticas: *Estado de la replicación de la vista* para ver los detalles.

![Solucionar problemas de Hyper-V local](media/site-recovery-monitoring-and-troubleshooting/image13.png)

Para los casos donde se detiene la replicación de la máquina virtual, haga clic con el botón Seleccionar *replicación*->*replicación de currículum vítae*
![solucionar problemas de Hyper-V local](media/site-recovery-monitoring-and-troubleshooting/image19.png)

En caso de máquina virtual migra un nuevo host de Hyper-V (dentro del clúster o en un equipo independiente), que se ha configurado a través de ASR, la replicación de la máquina virtual no se ven afectada. Asegúrese de que el nuevo host de Hyper-V cumpla todos los por-requisitos y está configurado con ASR.

### <a name="event-log"></a>Registro de eventos

| Orígenes de eventos                | Detalles                                                                                                                                                                                           |
|-------------------------  |:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| **Aplicaciones y registros/Microsoft/VirtualMachineManager/servidor/Administrador de servicios** (Servidor VMM)   |  Proporciona un registro de información útil para solucionar muchos problemas VMM diferentes. |
| **Aplicaciones y registros, MicrosoftAzureRecoveryServices/replicación de servicio** (Host hyper-V)   | Proporciona un registro de información útil para solucionar muchos problemas de agente de servicios de recuperación de Microsoft Azure. <br/> ![Origen de evento para host Hyper-v.](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Aplicaciones y servicios registros, Microsoft, Azure sitio recuperación/proveedor/operativa** (Host hyper-V)   | Proporciona un registro de información útil para solucionar muchos problemas de servicio de recuperación de sitios de Microsoft Azure. <br/> ![Origen de evento para host Hyper-v.](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Aplicaciones y registros/Microsoft/Windows/Hyper-V-VMM/Administrador de servicios** (Host hyper-V) | Proporciona un registro de información útil para solucionar muchos problemas de administración de máquinas virtuales de Hyper-V. <br/> ![Origen de evento para host Hyper-v.](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |


### <a name="hyper-v-replication-logging-options"></a>Opciones de registro de replicación de Hyper-V

Todos los eventos relacionados con la réplica de Hyper-V se registran en Hyper-V-VMM\\registro de administrador que se encuentra debajo de **registros de aplicaciones y servicios\\Microsoft\\Windows**. Además, un registro analítico puede estar habilitado para Hyper-V-VMM. Para habilitar este registro, asegúrese primero de los registros analíticos y de depuración visible en el Visor de eventos. Abra el Visor de sucesos, a continuación, en el **menú Ver**, haga clic en **Mostrar analítico y registros de depuración**.

![Solucionar problemas de Hyper-V local](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Un registro analítico está visible en VMM de Hyper-V

![Solucionar problemas de Hyper-V local](media/site-recovery-monitoring-and-troubleshooting/image15.png)

En el panel de **acciones** , haga clic en **Habilitar registro**. Una vez habilitada, aparece como una sesión de seguimiento de eventos que se encuentra en el **Monitor** de rendimiento **conjuntos de selector de datos.**

![Solucionar problemas de Hyper-V local](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Para ver la información recopilada, primero detener la sesión de seguimiento deshabilitando el registro y, a continuación, guardar el registro y vuelva a abrirlo en el Visor de sucesos o utilizar otras herramientas para convertir como desee.



## <a name="reaching-out-for-microsoft-support"></a>Llegar para Microsoft Support

### <a name="log-collection"></a>Colección de registro

Protección del sitio de VMM, consulte [ASR registro colección mediante la herramienta de plataforma de diagnósticos de soporte técnico (SDP)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) para recopilar los registros necesarios.

Para la protección del sitio de Hyper-V, descargue la [herramienta](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) y ejecutar en el host de Hyper-V para recopilar los registros.

Escenarios de VMware o físico, consulte el [Conjunto de registro de recuperación de sitio de Azure para VMware y físico protección de sitio](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) para recopilar los registros necesarios.

Herramienta recopila los registros localmente en una subcarpeta nombre aleatorio en **%LocalAppData%\ElevatedDiagnostics**

![Pasos de ejemplo que se muestra de la protección del sitio de Hyper-V.](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### <a name="opening-a-support-ticket"></a>Abrir una incidencia de soporte técnico

Para elevar incidencia de soporte técnico para ASR, llegar a Azure admite el uso de la dirección URL en <http://aka.ms/getazuresupport>

## <a name="kb-articles"></a>Artículos de Knowledge Base

-   [Cómo mantener la letra de máquinas virtuales protegidos no se pudo sobre o migrar a Azure](http://support.microsoft.com/kb/3031135)
-   [Cómo administrar local para el uso de ancho de banda de red de protección de Azure](https://support.microsoft.com/kb/3056159)
-   [ASR: error de "no se encontró el recurso de clúster" cuando intenta activar la protección de una máquina virtual](http://support.microsoft.com/kb/3010979)
-   [Comprender y solucionar a Guía de réplica de Hyper-V](http://www.microsoft.com/en-in/download/details.aspx?id=29016) 

## <a name="common-asr-errors-and-their-resolutions"></a>Errores comunes de ASR y sus soluciones

A continuación se muestran los errores comunes que puede presionar y sus soluciones. Cada uno de lo error documentado en una página WIKI independiente.

### <a name="general"></a>General
-   <span style="color:green;">Nuevo</span> Trabajos [presenta el error "una operación está en curso". Error 505, 514, 532](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
-   <span style="color:green;">Nuevo</span> Trabajos de [presenta el error "El servidor no está conectado a Internet". Error 25018](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### <a name="setup"></a>Programa de instalación
-   [No se puede registrar el servidor VMM debido a un error interno. Consulte la vista de tareas en el Portal de recuperación de sitio para obtener más detalles sobre el error. Ejecutar el programa de instalación para registrar el servidor.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
-   [No puede establecer una conexión al administrador de recuperación de Hyper-V depósito. Comprobar la configuración de proxy o vuelva a intentarlo.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### <a name="configuration"></a>Configuración
-   [No se puede crear el grupo de protección: se produjo un error al recuperar la lista de servidores.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
-   [Clúster de host Hyper-V contiene al menos un adaptador de red estática o ningún adaptador conectada está configurados para usar DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
-   [VMM no tiene permisos para completar una acción](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
-   [No se puede seleccionar la cuenta de almacenamiento dentro de la suscripción durante la configuración de protección](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### <a name="protection"></a>Protección
- <span style="color:green;">Nuevo</span> No se puede habilitar la protección de [con error "protección no pudo configurarse para la máquina virtual". Error 60007, 40003](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
- <span style="color:green;">Nuevo</span> Error de [Habilitar protección con error "protección no pudo habilitarse para la máquina virtual." Error 70094](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
- <span style="color:green;">Nuevo</span> Error de migración en vivo [23848 - la máquina virtual se va a mover con tipo Live. Esto podría interrumpir el estado de protección de recuperación de la máquina virtual.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx) 
- [Habilitar la protección de error porque el agente no instalado en el equipo host](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
- [No se puede encontrar un host adecuado para la máquina virtual de réplica - debido a recursos de cálculo baja](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
- [No se puede encontrar un host adecuado para la máquina virtual de réplica - debido a ninguna red lógica adjuntada](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
- [No puede conectarse al equipo del host de réplica: no se pudo conectar](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)


### <a name="recovery"></a>Recuperación
- VMM no puede completar la operación de host:
    -   [Conmutar al punto de recuperación seleccionado para máquina virtual: acceso General denegado error.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
    -   [No se pudo conmutar al punto de recuperación seleccionado para máquina virtual Hyper-V: operación anulado pruebe un punto de recuperación más reciente. (0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
    -   Una conexión con el servidor no pudo establecidos (0x00002EFD)
        -   [Error al habilitar la replicación inversa de la máquina virtual Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
        -   [Error al habilitar la replicación de máquinas virtuales máquinas virtuales de Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
    -   [No se pudo confirmar la recuperación de errores para máquina virtual](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
-   [El plan de recuperación contiene máquinas virtuales que no están listos para failover planificado](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
-   [La máquina virtual no está lista para failover planificado](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   [Máquina virtual no está en ejecución y no está apagado](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
-   [Fuera de la operación de banda ocurrió en una máquina virtual y un error de migración tras error de confirmación](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   Probar la migración tras error
    -   [No se pudo iniciar migración tras error, ya que la migración tras error de prueba está en curso](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
-   <span style="color:green;">Nuevo</span>  Migración tras error agota el tiempo con 'PreFailoverWorkflow tarea WaitForScriptExecutionTaskTimeout' debido a la configuración en el grupo de seguridad de la red asociada a la máquina Virtual o la subred al que pertenece el equipo. Para obtener más información, consulte ['PreFailoverWorkflow tarea WaitForScriptExecutionTaskTimeout'](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery) .


### <a name="configuration-server-process-server-master-target"></a>Configuración Server, servidor de procesos, patrón de destino
Servidor de configuración (CS), el servidor de procesos (PS), patrón de destino (MT)
-   [Se produce un error en el host de ESXi en el que se hospeda el PS/SC como una máquina virtual con una pantalla de muerte púrpura.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### <a name="remote-desktop-troubleshooting-after-failover"></a>Solución de problemas después de la migración de escritorio remoto
-   Muchos clientes enfrentan problemas para conectarse a los errores sobre VM en Azure. [Usar el documento de solución de problemas para RDP en la máquina virtual](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)

#### <a name="adding-a-public-ip-on-a-resource-manager-virtual-machine"></a>Agregar una dirección IP pública en un equipo virtual de administrador de recursos
Si el botón **Conectar** en el portal está atenuado y no está conectado a Azure mediante una ruta de Express o una conexión VPN de sitio a otro, debe crear y asignar una dirección IP pública de la máquina virtual para poder usar RDP/SSH. Siga los pasos siguientes para agregar una dirección IP pública en la interfaz de red de la máquina virtual.  

![Agregar una dirección IP pública en la interfaz de red de error sobre la máquina virtual](media/site-recovery-monitoring-and-troubleshooting/createpublicip.gif)