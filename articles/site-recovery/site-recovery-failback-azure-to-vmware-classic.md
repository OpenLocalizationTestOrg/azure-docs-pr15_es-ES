<properties 
   pageTitle="Un error atrás máquinas virtuales de VMware y servidores físicos al sitio local | Microsoft Azure"
   description="Obtenga información sobre errores en el sitio local después de la migración de máquinas virtuales de VMware y servidores físicos en Azure." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="08/22/2016"
   ms.author="ruturajd"/>

# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>Un error atrás máquinas virtuales de VMware y servidores físicos al sitio local

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-failback-azure-to-vmware.md)
- [Portal de clásico de Azure](site-recovery-failback-azure-to-vmware-classic.md)
- [Portal clásica Azure (heredado)](site-recovery-failback-azure-to-vmware-classic-legacy.md)



En este artículo se describe cómo errores volver Azure máquinas virtuales de Azure en el sitio local. Cuando esté listo para conmutar las máquinas virtuales de VMware o servidores físicos de Windows/Linux después de que se ha producido algún error sobre la locales del sitio en Azure con este [tutorial](site-recovery-vmware-to-azure-classic.md), siga las instrucciones de este artículo.



## <a name="overview"></a>Información general

Este diagrama muestra la arquitectura de recuperación para este escenario.

Use esta arquitectura cuando el servidor de proceso local y usa un ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Use esta arquitectura cuando el servidor de procesos en Azure y tiene una VPN o una conexión de ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Para ver la lista completa de puertos y la recuperación arquitectura diagrama hacen referencia a la imagen siguiente

![](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Le mostramos cómo funciona la recuperación:

- Una vez que ha podido sobre Azure, no volver a su sitio local en algunas fases:
    - **Fase 1**: proteger las máquinas virtuales de Azure para que comiencen replicar a máquinas virtuales de VMware que se ejecuta en su sitio local. Habilitar reprotection mueve la máquina virtual de un grupo de protección de recuperación creado automáticamente cuando creó el grupo de protección de migración tras error. Si ha agregado su grupo de protección de migración tras error a un plan de recuperación, a continuación, el grupo de protección de recuperación se agregó automáticamente al plan.  Durante proteger especifique cómo planificar conmutar.
    - **Fase 2**: después de que sus máquinas virtuales de Azure va a duplicar en su sitio local, ejecute un error un error de Azure.
    - **Fase 3**: después de que los datos no se ha atrás, proteger la VM local que no volver a para que comiencen replicar en Azure.

> [AZURE.VIDEO enhanced-vmware-to-azure-failback]


### <a name="failback-to-the-original-or-alternate-location"></a>Recuperación a la ubicación original o alternativa

Si no pudo sobre una VM VMware puede fallar al mismo origen VM si todavía existe local. En este escenario sólo los cambios delta error volver. Tenga en cuenta que:

- Si no pudo sobre servidores físicos, a continuación, recuperación es siempre una nueva VM VMware.
    - Antes de que no volver a un equipo físico tenga en cuenta que
    - Equipo físico protegido volverá como una máquina Virtual cuando un error sobre de Azure VMware
    - Asegúrese de que descubra al menos uno patrón de destino del servidor junto con los hosts ESX/ESXi necesarios al que necesita recuperación.
- Si no volver a la máquina virtual original se requiere lo siguiente:
    - Si administra la máquina virtual de un servidor de vCenter host ESX del destino patrón debe tener acceso al almacén de datos de máquinas virtuales.
    - Si la máquina virtual está en un host ESX pero no administrada por vCenter el disco duro de la máquina virtual debe estar en un almacén de datos accesible por host de MT.
    - Si su máquina virtual está en un host ESX y no usa vCenter debe completar detección del host ESX de MT antes de proteger. Esto se aplica si está experimentando problemas servidores físicos volver demasiado.
    - Otra opción (si existe el VM local) es eliminarla antes de realizar una recuperación. A continuación, recuperación creará una nueva máquina virtual en el mismo host como el host de ESX de destino principal.
    
- Cuando se recuperarán recuperación en una ubicación alternativa los datos en el mismo almacén de datos y el mismo host ESX que utiliza el servidor de destino principal local. 


## <a name="prerequisites"></a>Requisitos previos

- Tendrá un entorno de VMware con el fin de un error atrás máquinas virtuales de VMware y servidores físicos. Falta un servidor físico no es compatible.
- Para conmutar debe ha creado una red de Azure al configurar inicialmente protección. Recuperación necesita una conexión VPN o ExpressRoute de la red de Azure en la que se encuentran en el sitio local las máquinas virtuales de Azure.
- Si las máquinas virtuales que desee volver a administrado por un servidor de vCenter deberá asegurarse de que tiene los permisos necesarios para la detección de máquinas virtuales en servidores de vCenter. [Obtenga más información](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
- Si están presentes en una máquina virtual instantáneas reprotection dará un error. Puede eliminar las instantáneas o los discos. 
- Antes de que se produce de nuevo debe crear una serie de componentes:
    - **Crear un servidor de procesos en Azure**. Esta es una máquina virtual de Azure que necesitará para crear y mantener ejecutar durante la recuperación. Puede eliminar el equipo una vez completada la recuperación.
    - **Crear un servidor de destino principal**: el servidor de destino maestro envía y recibe datos de recuperación. El servidor de administración ha creado local tiene un servidor de destino maestro instalado de forma predeterminada. Sin embargo, según el volumen de tráfico atrás error debe crear un servidor de destino principal independiente para recuperación.
    - Si desea crear un servidor de destino principal adicional que se ejecuta en Linux, debe configurar la VM Linux antes de instalar al servidor de destino principal, como se describe a continuación.
- Servidor de configuración es necesario local al realizar una recuperación. Durante la recuperación, la máquina virtual debe existir en la base de datos de configuración, falla la recuperación no sea correcta. Por lo tanto, asegúrese de que realizar copia de seguridad programada normal de su servidor. En caso de desastre, necesitará restaurar con la dirección IP del misma modo que funcione recuperación.

## <a name="set-up-the-process-server-in-azure"></a>Configurar el servidor de procesos de Azure

Debe instalar a un servidor de procesos en Azure para que las máquinas virtuales de Azure puede enviar los datos al servidor de destino principal local. 

1.  En el portal de recuperación del sitio > **Servidores configuración** Seleccione esta opción para agregar un nuevo servidor de proceso.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)

2.  Especifique un nombre de servidor de proceso y escriba un nombre y una contraseña que usará para conectarse a la máquina virtual de Azure como administrador. En el **Servidor de configuración de** seleccionar el servidor de administración local y especificar la red de Azure en la que se debe implementar el servidor de procesos. Debe ser la red en la que se encuentran las máquinas virtuales de Azure. Especifique una dirección IP única desde la subred select y comenzar su implementación.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

    Se activará un trabajo para implementar el servidor de procesos

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

    Después de que el servidor de proceso se implementa en Azure puede iniciar sesión en él con las credenciales que especificó. La primera vez que inicie sesión en el cuadro de diálogo del servidor de proceso se ejecutará. Escriba la dirección IP del servidor de administración local y su contraseña. Deje el valor de puerto 443 predeterminado. También puede dejar el valor predeterminado en 9443 puerto para la replicación de datos a menos que específicamente modificado esta configuración al configurar el servidor de administración local. 

    >[AZURE.NOTE] El servidor no estará visible en **Propiedades de la máquina virtual**. Solo está visible en la pestaña **servidores** en el servidor de administración a la que se ha registrado. Este proceso puede demorarse sobre 10 y 15 minutos para que aparezca el servidor de procesos.


## <a name="set-up-the-master-target-server-on-premises"></a>Configurar el patrón de destino servidor local

El servidor de destino principal recibe los datos de recuperación. Un servidor de destino principal se instala automáticamente en el servidor de administración local, pero si está errores volver una gran cantidad de datos posible que necesite configurar un servidor de destino principal adicional. Haga lo siguiente como sigue:

>[AZURE.NOTE] Si desea instalar a un servidor de destino principal en Linux, siga las instrucciones en el procedimiento siguiente.

1. Si va a instalar al servidor de destino principal en Windows, abra la página de inicio rápido de la máquina virtual en la que va a instalar al servidor de destino principal y descargar el archivo de instalación para el Asistente de configuración de unificada de recuperación de sitio de Azure.
2. Ejecutar la configuración y en **antes de empezar** , seleccione **Agregar servidores de proceso adicional escalar implementación**.
3. Completar el Asistente de la misma manera que hizo al [Configurar el servidor de administración](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). En la página de **Detalles de configuración de servidor** , especifique la dirección IP de este servidor de destino principal y una contraseña para tener acceso a la máquina virtual.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Configurar una VM Linux como el servidor de destino principal
Para configurar el servidor de administración ejecuta el servidor de destino principal como una VM Linux tendrá que instalar el porcentaje) S 6.6 mínimas sistema operativo, recuperar los ID para cada disco duro de SCSI, instalar algunos paquetes adicionales y aplicar algunos cambios personalizados.

#### <a name="install-centos-66"></a>Instalar CentOS 6.6

1.  Instalar el sistema operativo mínimo de CentOS 6.6 en el servidor de administración VM. Mantener la ISO en una unidad de DVD y arranque el sistema. Omitir las pruebas de medios, seleccione inglés en el idioma, seleccione **Dispositivos de almacenamiento de información básica**, compruebe que la unidad de disco duro no tiene datos importantes y haga clic en **Sí**, descartar todos los datos. Escriba el nombre de host del servidor de administración y seleccione el adaptador de red del servidor.  En el cuadro de diálogo **Editar sistema** seleccione** Conectar automáticamente** y agregar una dirección IP estática, la red y la configuración de DNS. Especificar una zona horaria y una contraseña de raíz para tener acceso al servidor de administración. 
2.  Cuando se solicita el tipo de instalación que le gustaría seleccione **Crear personalizado diseño** como la partición. Tras hacer clic en **siguiente** seleccione **libre** y haga clic en crear. Crear **/**, **/var/crash** **y/hogar particiones** con **FS tipo:** **ext4**. Crear la partición de intercambio como **FS tipo: intercambiar**.
3.  Si se encuentran dispositivos preexistentes que aparecerá un mensaje de advertencia. Haga clic en **formato** para dar formato a la unidad con la configuración de partición. Haga clic en **escribir cambio en disco** para aplicar los cambios de partición.
4.  Seleccione **cargador de inicio de instalación** > **siguiente** para instalar el cargador de inicio en la partición raíz.
5.  Cuando termine la instalación haga clic en **reiniciar**.


#### <a name="retrieve-the-scsi-ids"></a>Recuperar el Id.

1. Después de la instalación recuperar los ID para cada disco duro de SCSI en la máquina virtual. Para realizar este salir de la máquina virtual del servidor de administración, en las propiedades de la máquina virtual en VMware haga clic en la entrada VM > **Editar configuración** > **Opciones**.
2. Seleccione **Advanced** > **elemento General** y haga clic en **Parámetros de configuración**. Esta opción estará de-active cuando el equipo se está ejecutando. Para que se active el equipo se debe apagar.
3. Si la fila **disco. EnableUUID** existe Asegúrese de que el valor se establece en **True** (distingue entre mayúsculas y minúsculas). Si ya es puede cancelar y probar el comando SCSI dentro de un sistema operativo invitado después de arrancar. 
4.  Si la fila no existente haga clic en **Agregar fila** – y agregar con el valor **True** . No use comillas dobles.

#### <a name="install-additional-packages"></a>Instalar paquetes adicionales

Debe descargar e instalar algunos paquetes adicionales. 

1.  Asegúrese de que el servidor de destino principal está conectado a internet.
2.  Ejecutar este comando para descargar e instalar 15 paquetes del repositorio de CentOS: **# yum instalar – y xfsprogs perl lsscsi rsync wget kexec-herramientas**.
3.  Si los equipos de origen que proteger ejecutan Linux Set Reiser o XFS archivo sistema para el dispositivo de inicio o raíz, debe descargar e instalar paquetes adicionales como sigue:

    - # <a name="cd-usrlocal"></a>CD/usr/local
    - # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
    - # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
    - # <a name="rpm-ivh-kmod-reiserfs-00-1el6elrepox8664rpm-reiserfs-utils-3621-1el6elrepox8664rpm"></a>rpm-ivh 1.el6.elrepo.x86_64.rpm-kmod-reiserfs-0.0 reiserfs-utilidades-3.6.21-1.el6.elrepo.x86_64.rpm
    - # <a name="wget-httpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpmhttpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpm"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
    - # <a name="rpm-ivh-xfsprogs-311-16el6x8664rpm"></a>rpm-ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>Aplicar cambios personalizados

Los siguientes pasos para aplicar los cambios personalizados después de haber hacen completar los pasos posteriores a la instalación y los paquetes instalados:

1.  Copiar al binario RHEL 6-64 unificada agente en la máquina virtual. Ejecutar este comando para descomprima el binario: **alquitrán: zxvf <file name> **
2.  Ejecutar este comando para conceder permisos: **# chmod 755./ApplyCustomChanges.sh**
3.  Ejecutar la secuencia de comandos: **./ApplyCustomChanges.sh #**. Solo debe ejecutar la secuencia de comandos una vez. Reinicie el servidor después de la secuencia de comandos se ejecute correctamente.


## <a name="run-the-failback"></a>Ejecutar la recuperación

### <a name="reprotect-the-azure-vms"></a>Proteger las VM de Azure

1.  En el portal de recuperación del sitio > ficha de **equipos** , seleccione la máquina virtual que se ha fallado sobre y haga clic en **volver a proteger**.
2.  En el **Servidor de destino de patrón** y **Servidor de procesos** , seleccione el servidor de destino principal local y a continuación, el servidor de procesos de Azure VM.
3.  Seleccione la cuenta que ha configurado para conectarse a la máquina virtual.
4.  Seleccione la versión de recuperación del grupo de protección. Por ejemplo, si la máquina virtual está protegida en GP1, a continuación, se deberá seleccionar PG1_Failback.
5.  Si desea recuperar en una ubicación alternativa, seleccione la unidad de retención y el almacén de datos configurado para el servidor de destino principal. Cuando no a su sitio local las máquinas virtuales de VMware en el plan de protección de recuperación utiliza el mismo almacén de datos como el servidor de destino principal. Si desea recuperar la réplica Azure VM hasta el mismo local VM, a continuación, la VM local debe estar en el mismo almacén de datos como el servidor de destino principal. Si no hay ninguna máquina virtual local se creará una nueva durante reprotection.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)

6.  Tras hacer clic en **Aceptar** para empezar a reprotection un trabajo comienza a replicar la máquina virtual de Azure en el sitio local. Puede realizar un seguimiento del progreso en la ficha **trabajos** .

    ![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### <a name="run-a-failover-to-the-on-premises-site"></a>Ejecutar un error al sitio local

Después de reprotection la máquina virtual se mueve a la versión de recuperación de su grupo de protección y se agrega automáticamente al plan de recuperación que ha usado para la migración tras error a Azure si hay alguno.

1.  En la página de **Planes de recuperación** , seleccione el plan de recuperación que contiene el grupo de recuperación y haga clic en **migración tras error** > **Planificado migración tras error**.
2.  En **Confirme de migración tras error** , compruebe la dirección de migración tras error (en Azure) y seleccione el punto de recuperación que desea usar para la migración tras error (más reciente). Si habilita **Múltiples VM** cuando configura las propiedades de replicación puede recuperar a la aplicación más reciente o el punto de recuperación consistente de bloqueo. Haga clic en la marca de verificación para iniciar la migración tras error.
3.  Durante la migración tras error sitio recuperación se cerrará las máquinas virtuales de Azure. Después de comprobar que recuperación ha completado como se esperaba puede puede comprobar que se ha cerrado la máquinas virtuales de Azure según lo esperado.

### <a name="reprotect-the--on-premises-site"></a>Proteger el sitio local

Después de que finalice la recuperación los datos estarán en el sitio local, pero no estará protegidos. Para iniciar la replicación de Azure vuelva a hacer lo siguiente:

1.  En el portal de recuperación del sitio > **máquinas** ficha, seleccione las máquinas virtuales que han fallado atrás y haga clic en **volver a proteger**. 
2.  Después de comprobar que la replicación a Azure funciona como se esperaba en Azure puede eliminar las máquinas virtuales de Azure (actualmente no se ejecuta) que se migraron volver.


### <a name="common-issues-in-failback"></a>Problemas comunes de recuperación

1. Si realiza la detección de vCenter de usuario de sólo lectura y proteger máquinas virtuales de Windows, se realiza correctamente y funciona la migración tras error. En el momento de proteger, se producirá ya que no se puede detectar los almacenes de datos. Como un síntoma no podrá ver los almacenes de datos que aparece al volver a proteger. Para resolver este problema, puede actualizar la credencial vCenter con la cuenta que tenga permisos y vuelva a intentar el trabajo. [Obtener más información](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. Cuando recuperación una VM Linux y ejecutar local, verá que se haya desinstalado el paquete de administrador de red del equipo. Esto es porque cuando se recupera la máquina virtual en Azure, se quita el paquete de administrador de red.
3. Cuando una máquina virtual está configurada con la dirección IP estática y no pudo sobre Azure, la dirección IP se adquiere a través de DHCP. Cuando conmutar atrás a local, la máquina virtual continuará utilizar DHCP para obtener la dirección IP. Necesitará manualmente en el inicio de sesión en el equipo y establecer la dirección IP volver a dirección estática si es necesario.
4. Si está utilizando la edición gratuita de ESXi 5.5 o edición gratuita de hipervisor vSphere 6, ¿correctamente migración tras error, pero recuperación no se realizará correctamente. Le ned actualizar a cualquier licencia de evaluación para habilitar la recuperación.

## <a name="failing-back-with-expressroute"></a>Errores atrás con ExpressRoute

Puede fallar volver a través de una conexión VPN o Azure ExpressRoute. Si desea usar ExpressRoute nota lo siguiente:

- ExpressRoute se debe configurar en la red virtual Azure a qué origen de éxito de equipos por y en qué máquinas virtuales de Azure se encuentran después de que se produce el error.
- Datos se duplica en una cuenta de almacenamiento de Azure en un extremo del público. Debe configurar interconexión pública en ExpressRoute con el centro de datos de destino para la replicación de recuperación de sitio utilizar ExpressRoute.



