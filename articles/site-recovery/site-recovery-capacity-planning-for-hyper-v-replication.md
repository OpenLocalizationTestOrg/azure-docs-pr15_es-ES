<properties
    pageTitle="Ejecutar la herramienta de planificador de capacidad de Hyper-V para el sitio de recuperación | Microsoft Azure"
    description="Este artículo contiene instrucciones para usar la herramienta de planificador de capacidad de Hyper-V para la recuperación de sitio de Azure"
    services="site-recovery"
    documentationCenter="na"
    authors="rayne-wiselman"
    manager="jwhit"
    editor="" />
<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/12/2016"
    ms.author="raynew" />

# <a name="run-the-hyper-v-capacity-planner-tool-for-site-recovery"></a>Ejecutar la herramienta de planificador de capacidad de Hyper-V para la recuperación de sitio

Como parte de la implementación de Azure sitio recuperación debe averiguar los requisitos de ancho de banda y replicación. La herramienta de planificador de capacidad de Hyper-V para el sitio de recuperación le ayuda a averiguar los requisitos de replicación y ancho de banda para la replicación de la máquina virtual de Hyper-V.


En este artículo se describe cómo ejecutar la herramienta de planificador de capacidad de Hyper-V. Esta herramienta se debe utilizar junto con otras herramientas de planificación de capacidad e información descrito en [Planear la capacidad de recuperación de sitios](site-recovery-capacity-planner.md).


## <a name="before-you-start"></a>Antes de empezar

Ejecutar la herramienta en un nodo de servidor o un clúster de Hyper-V en su sitio primario. Para ejecutar la herramienta de los servidores de host de Hyper-V necesita:

- Sistema operativo: Windows Server® 2012 o Windows Server® 2012 R2
- Memoria: 20 MB (mínimo)
- CPU: sobrecarga de la 5 por ciento (mínimo)
- Espacio en disco: 5 MB (mínimo)

Antes de ejecutar la herramienta debe preparar el sitio primario. Si va a replicar entre dos local sitios y que desea comprobar el ancho de banda, tendrá que preparar un servidor de réplica también.


## <a name="step-1-prepare-the-primary-site"></a>Paso 1: Preparar el sitio primario
1. Asegúrese de una lista de todas las máquinas virtuales de Hyper-V que desea replicar y Hyper-V hosts y clústeres en el que se encuentran en el sitio primario. La herramienta puede ejecutar cada vez para varios hosts independientes, o para un único clúster pero no en ambos juntos. También debe ejecutar por separado para cada sistema operativo, por lo que debería recopilar y tenga en cuenta los servidores Hyper-V como sigue:

  - Servidores de independientes de Windows Server® 2012
  - Windows Server® 2012 clústeres
  - Servidores independientes de Windows Server® 2012 R2
  - Windows Server® 2012 R2 clústeres

3. Habilitar el acceso remoto a WMI en todos los hosts Hyper-V y clústeres. Ejecutar este comando en cada servidor o clúster para que las reglas de firewall seguro y se establecen permisos de usuario:

        netsh firewall set service RemoteAdmin enable

5. Habilitar la supervisión de rendimiento en servidores y clústeres, como sigue:

  - Abra el Firewall de Windows con el complemento de **Seguridad avanzada** y, a continuación, habilitar las siguientes reglas de entrada: **Acceso de red COM + (DCOM-IN)** y todas las reglas en el **grupo de administración remota de registro de eventos**.

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>Paso 2: Preparar un servidor de réplica (local réplica local)

No es necesario hacer esto si va a duplicar en Azure.

Se recomienda que haya configurado un único host Hyper-V como un servidor de recuperación para que se pueden replicar una VM ficticia para comprobar el ancho de banda.  Puede omitir este pero no podrá medir el ancho de banda a menos que lo hace.

1. Si desea usar un nodo de clúster como la réplica configurar agente de réplica de Hyper-V:

    - En el **Administrador del servidor**, abra el **Administrador de clúster de migración tras error**.
    - Conectar con el clúster, resalte el nombre de clúster y haga clic en **acciones** > **Rol configurar** para abrir el Asistente de alta disponibilidad.
    - **Seleccionar función** haga clic en **Agente de réplica de Hyper-V**. En el Asistente para proporcionar un **nombre de NetBIOS** y la **dirección IP** que se utilizará como el punto de conexión al clúster (llamado un punto de acceso de cliente). El **Agente de réplica de Hyper-V** se configurarán resultantes en un nombre de punto de acceso de cliente que debe tener en cuenta.
    - Compruebe que la función de agente de réplica de Hyper-V se suministra correctamente en línea y puede conmutar entre todos los nodos del clúster. Para ello, haga clic con el botón secundario del mouse en la función, seleccione **mover**y, a continuación, haga clic en **Seleccionar el nodo**. Seleccione un nodo > **Aceptar**.
    - Si está utilizando autenticación basada en certificado, asegúrese de que cada nodo del clúster y el cliente que tienen instalado el certificado de punto de acceso.
2.  Activar a un servidor de réplica:

    - Para un clúster de abrir el Administrador de clúster de error, conéctese al clúster y haga clic en **funciones** > seleccione rol > Opciones de **Replicación**> **Habilitar este clúster como un servidor de réplica**. Tenga en cuenta que si está usando un clúster como réplica deberá tener el rol de Hyper-V réplica agente presente en el clúster en el sitio primario.
    - Abrir Administrador de Hyper-V para un servidor independiente. En el panel de **acciones** , haga clic en **Configuración de Hyper-V** para el servidor que desea habilitar y en **Configuración de la replicación** , haga clic en **Habilitar este equipo como un servidor de réplica**.
3. Configurar la autenticación:

    - En **puertos de autenticación y** Seleccione cómo desea autenticar el servidor principal y los puertos de autenticación. Si está usando certificado haga clic en **Seleccionar certificado** para seleccionar uno. Usar Kerberos si los hosts de Hyper-V principal y recuperación se encuentran en el mismo dominio o dominios de confianza. Usar certificados para dominios diferentes o una implementación de grupo de trabajo.
    - En la sección **autorización y almacenamiento** , permitir **cualquier** servidor autenticado (principal) enviar datos de replicación a este servidor de réplica. Haga clic en **Aceptar** o en **Aplicar**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

    - Ejecutar **netsh http mostrar servicestate** para comprobar que el oyente está ejecutando para el protocolo o puerto especificado:  
4. Configure los servidores de seguridad. Durante la instalación de Hyper-V se crean las reglas de firewall para permitir el tráfico en el valor predeterminado de puertos (HTTPS en 443, Kerberos en 80). Habilitar estas reglas como sigue:

        - Certificate authentication on cluster (443): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}**
        - Kerberos authentication on cluster (80): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}**
        - Certificate authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"**
        - Kerberos authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"**

## <a name="step-3-run-the-capacity-planner-tool"></a>Paso 3: Ejecutar la herramienta de planificación de capacidad

Una vez que ha preparado su sitio principal y configurar un servidor de recuperación puede ejecutar la herramienta.

1. [Descargue](https://www.microsoft.com/download/details.aspx?id=39057) la herramienta desde Microsoft Download Center.
2. Ejecutar la herramienta de uno de los servidores principales (o uno de los nodos del clúster principal). Haga clic en el archivo .exe y, a continuación, elija **Ejecutar como administrador**.
3. Antes de **comenzar** especifique cuánto tiempo desean recopilar datos. Se recomienda que ejecutar la herramienta durante el horario de producción para asegurarse de que los datos son representante. Si solo intenta validar la conectividad de red, puede recopilar sólo un minuto.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. En **Detalles del sitio primario** especifique el nombre del servidor o el FQDN de un host independiente, o para un clúster especifique el FQDN del cliente Aceptar punto, nombre de clúster o cualquier nodo en el clúster y, a continuación, haga clic en **siguiente**. La herramienta detecta automáticamente el nombre del servidor que se ejecuta en. La herramienta recoge máquinas virtuales que pueden supervisar para los servidores especificados.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. En **Detalles del sitio de réplica** si va a duplicar en Azure o si está replicar en un centro de datos secundario y no ha configurado un servidor de réplica, seleccione **Omitir pruebas de sitio de réplica**. Si va a duplicar en un centro de datos secundario y ha configurado un tipo de réplica en el FQDN del servidor independiente o el punto de acceso de cliente para el clúster en **nombre del servidor (o) Hyper-V réplica agente de capital**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. En **Detalles de réplica extendido** habilita **omitir las pruebas de sitio de réplica extendido**. Que no se admiten por sitio de recuperación.
7. En **Máquinas virtuales Elija replicar** las herramientas se conecta al servidor o clúster y muestra máquinas virtuales y discos que se ejecutan en el servidor principal, según la configuración especificada en la página de **Detalles del sitio primario** . Tenga en cuenta que no ejecutan máquinas virtuales que ya están habilitadas para replicación o que no se muestra. Seleccione las máquinas virtuales para el que desean recopilar métricas. Seleccionar el VHD automáticamente recopila los datos para las VM demasiado.
9. Si ha configurado un servidor de réplica o clúster, en **información de la red** especifique el ancho de banda WAN aproximada que cree se utilizará entre los sitios primarios y réplica y seleccione los certificados si ha configurado la autenticación de certificado.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. En **Resumen** Compruebe la configuración y haga clic en **siguiente** para comenzar la recopilación de métricas. Estado y progreso de la herramienta se muestra en la página de la **Capacidad de calcular** . Cuando finalice el herramienta ejecutando haga clic en **Ver informe** sobre el resultado. De forma predeterminada los informes y registros se almacenan en **%systemdrive%\Users\Public\Documents\Capacity organizador**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)


## <a name="step-4-interpret-the-results"></a>Paso 4: Interpretar los resultados
Estas son las mediciones importantes. Puede pasar por alto métricas que no se enumeran aquí. No son relevantes para el sitio de recuperación.

### <a name="on-premises-to-on-premises-replication"></a>Local a la replicación local
  - Impacto de la replicación en el cálculo del host principal, memoria
  - Impacto de la replicación en el principal, el espacio de almacenamiento de hosts de recuperación, IOPS
  - Ancho de banda total necesaria para la replicación de delta (Mbps)
  - Ancho de banda de red observado entre el host de principal y el host de recuperación (Mbps)
  - Sugerencia para el número ideal de activos transferencias paralelo entre los dos hosts/clústeres

### <a name="on-premises-to-azure-replication"></a>Local a la replicación de Azure
  - Impacto de la replicación en el cálculo del host principal, memoria
  - Impacto de la replicación almacenamiento espacio del host principal en disco, IOPS
  - Ancho de banda total necesaria para la replicación de delta (Mbps)

## <a name="more-resources"></a>Más recursos

- Para obtener información detallada acerca de la herramienta leer el documento que acompaña a la descarga de la herramienta.
- Ver un tutorial acerca de la herramienta de de Keith Mayer [blog de TechNet](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx).
- [Obtener los resultados](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) de nuestra pruebas de rendimiento para local a la replicación de Hyper-V local



## <a name="next-steps"></a>Pasos siguientes

Después de acabar el planeamiento de capacidad puede iniciar la implementación de recuperación del sitio:

- [Replicar máquinas virtuales de Hyper-V en nubes de VMM en Azure](site-recovery-vmm-to-azure.md)
- [Replicar VM de Hyper-V (sin VMM) en Azure](site-recovery-hyper-v-site-to-azure.md)
- [Replicar VM de Hyper-V entre sitios VMM](site-recovery-vmm-to-vmm.md)
- [Replicar VM de Hyper-V entre sitios VMM con SAN](site-recovery-vmm-san.md)
- [Replicar hyper-V máquinas virtuales con en solo servidor VMM](site-recovery-single-vmm.md)