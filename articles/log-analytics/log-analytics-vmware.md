<properties
    pageTitle="Solución de supervisión de VMware en análisis de registro | Microsoft Azure"
    description="Obtenga información sobre cómo la solución de supervisión de VMware puede ayudar a administrar registros y supervisar ESXi anfitriones."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="banders"/>

# <a name="vmware-monitoring-preview-solution-in-log-analytics"></a>Solución VMware supervisión (Preview) en el análisis de registro

La solución VMware supervisión en el análisis de registro es una solución que le ayuda a crear un registro centralizado y solución de supervisión de registros de VMware grandes. Este artículo describe cómo solucionar problemas, capturar y administrar los hosts ESXi en una única ubicación con la solución. Con la solución, puede ver datos detallados para todos los hosts ESXi en una única ubicación. Puede ver los recuentos de evento superior, estado y tendencias de hosts de máquina virtual y ESXi proporcionadas a través de los registros de host ESXi. Puede solucionar viendo y buscar registros de host ESXi centralizados. Y puede crear alertas basadas en consultas de búsqueda de registro.

La solución utiliza funcionalidad nativa de registro del sistema del host ESXi al introducir datos en un máquina virtual, que tiene el agente de OMS de destino. Sin embargo, la solución no escribe archivos en el registro del sistema dentro de la máquina virtual de destino. El agente OMS abre el puerto 1514 y escucha a este. Una vez que recibe los datos, el agente OMS inserta los datos en OMS.

## <a name="installing-and-configuring-the-solution"></a>Instalar y configurar la solución

Use la información siguiente para instalar y configurar la solución.

- Agregar la solución VMware supervisión al área de trabajo OMS con el proceso descrito en [soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md).

#### <a name="supported-vmware-esxi-hosts"></a>Hosts VMware ESXi admitidos
vSphere ESXi Host 5.5 y 6.0

#### <a name="prepare-a-linux-server"></a>Preparar un servidor Linux
Crear un sistema operativo de Linux VM para recibir todos los datos del registro del sistema de los hosts ESXi. El [Agente de Linux OMS](log-analytics-linux-agents.md) es el punto de la colección de todos los datos del registro del sistema de host ESXi. Puede usar varios hosts ESXi para reenviar registros a un solo servidor Linux, como en el ejemplo siguiente.  

   ![flujo de registro del sistema](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Configurar la colección de registro del sistema

1. Configurar el reenvío de registro del sistema para VSphere. Para que obtener información detallada ayudar a configurar el reenvío de registro del sistema, consulte [configuración de registro del sistema en ESXi 5.x y 6.0 (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Vaya a **configuración de Host de ESXi** > **Software** > **Configuración avanzada** > **registro del sistema**.
  ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  

2. En el campo *Syslog.global.logHost* , agregue el número de puerto *1514*y su servidor de Linux. Por ejemplo, `tcp://hostname:1514` o`tcp://123.456.789.101:1514`

3. Abra el firewall de host ESXi para el registro del sistema. **Configuración de Host de ESXi** > **Software** > **Perfil de seguridad** > **Firewall** y abrir las **Propiedades**.  

    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  

    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  

4. Compruebe la vSphere consola para verificar que dicho registro del sistema está configurado correctamente. Confirmar en el host ESXI ese puerto **1514** está configurado.

5. Probar la conectividad entre el servidor Linux y el host de ESXi mediante la `nc` comando en el ESXi Host. Por ejemplo:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

6. Descargue e instale al agente de OMS para Linux en el servidor Linux. Para obtener más información, consulte la [documentación de agente de OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).

7. Una vez instalado el agente de OMS para Linux, vaya al directorio /etc/opt/microsoft/omsagent/sysconf/omsagent.d y copie el archivo de vmware_esxi.conf al directorio /etc/opt/microsoft/omsagent/conf/omsagent.d y el cambio del propietario o grupo y los permisos del archivo. Por ejemplo:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```

8.  Reinicie el agente de OMS para Linux ejecutando `sudo /opt/microsoft/omsagent/bin/service_control restart`.

9. En el Portal de OMS, realizar una búsqueda de registro de `Type=VMware_CL`. Cuando OMS recopila los datos del registro del sistema, conserva el formato de registro del sistema. En el portal se capturan algunos campos específicos, como el *nombre de host* y *nombre de proceso*.  

    ![tipo](./media/log-analytics-vmware/type.png)  

    Si los resultados de búsqueda de registro de vista son similares a la imagen anterior, está listo para usar el panel de solución OMS VMware supervisión.  

## <a name="vmware-data-collection-details"></a>Detalles del conjunto de datos de VMware

La solución VMware supervisión recopila varias métricas y registro de información de rendimiento hosts ESXi con los agentes de OMS para Linux ha habilitado.

La siguiente tabla muestra los métodos de conjunto de datos y otros detalles acerca de cómo se recopilan los datos.

| plataforma | Agente OMS para Linux | Agente SCOM | Almacenamiento de Azure | ¿SCOM obligatorio? | Datos de agente SCOM enviadas por grupo de administración | frecuencia de la colección |
|---|---|---|---|---|---|---|
|Linux|![Sí](./media/log-analytics-vmware/oms-bullet-green.png)|![No](./media/log-analytics-vmware/oms-bullet-red.png)|![No](./media/log-analytics-vmware/oms-bullet-red.png)|            ![No](./media/log-analytics-containers/oms-bullet-red.png)|![No](./media/log-analytics-vmware/oms-bullet-red.png)| cada 3 minutos|


La siguiente tabla se muestran ejemplos de campos de datos recopilados por la solución de supervisión de VMware:

| nombre de campo | Descripción |
| --- | --- |
| Device_s| Dispositivos de almacenamiento de VMware |
| ESXIFailure_s | tipos de errores |
| EventTime_t | Cuando se produjo el evento |
| HostName_s | Nombre de host de ESXi |
| Operation_s | crear VM o eliminar VM |
| ProcessName_s | nombre del evento |
| ResourceId_s | nombre del host VMware |
| ResourceLocation_s | VMware |
| ResourceName_s | VMware |
| ResourceType_s | Hyper-V |
| SCSIStatus_s | Estado de SCSI VMware |
| SyslogMessage_s | Datos de registro del sistema |
| UserName_s | usuario que creó o eliminados VM |
| VMName_s | Nombre de VM |
| Equipo | equipo host |
| TimeGenerated | se ha generado los datos de tiempo |
| DataCenter_s | Centro de datos de VMware |
| StorageLatency_s | latencia de almacenamiento (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Información general de solución de supervisión de VMware

El mosaico VMware aparece en el portal OMS. Proporciona una vista de alto nivel de todos los errores. Al hacer clic en el mosaico, vaya a una vista de paneles.

![Mosaico](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Desplazarse por la vista de paneles

En la vista de paneles de **VMware** aspas organizados por:

- Recuento de estado de error
- Cuenta de Host superior por evento
- Recuentos de eventos principales
- Actividades de máquina virtual
- Eventos de disco ESXi Host


![Solution1](./media/log-analytics-vmware/solutionview1-1.png)

![2](./media/log-analytics-vmware/solutionview1-2.png)

Haga clic en cualquier módulo para abrir el panel de búsqueda de análisis de registro que muestra información detallada específica de la hoja.

Desde aquí, puede editar la consulta de búsqueda para modificar algo específico. Para obtener un tutorial sobre los conceptos básicos de búsqueda OMS, consulte la [tutorial de búsqueda de registro OMS.](log-analytics-log-searches.md)

#### <a name="find-esxi-host-events"></a>Buscar ESXi eventos de host

Un único host ESXi genera varios registros, en función de los procesos. La solución VMware supervisión centraliza ellos y resume los recuentos de evento. Esta vista centralizada le ayuda a comprender qué host ESXi tiene un gran volumen de eventos y qué ocurre con más frecuencia en su entorno.

![evento](./media/log-analytics-vmware/events.png)

Es posible agregar detalles adicionales haciendo clic en un host ESXi o un tipo de evento.

Al hacer clic en un nombre de host ESXi, ver información de ese host ESXi. Si desea restringir los resultados con el tipo de evento, agregue `“ProcessName_s=EVENT TYPE”` en la consulta de búsqueda. Puede seleccionar el **nombre de proceso** en el filtro de búsqueda. Limita la información.

![obtención de detalles](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Buscar actividades de alto VM

Una máquina virtual puede crear y eliminar en cualquier host ESXi. Resulta útil para identificar cuántas máquinas virtuales de un host ESXi crea un administrador. Que a su vez, ayuda a comprender el planeamiento de capacidad y rendimiento. Realizar el seguimiento de eventos de la actividad VM es fundamental al administrar su entorno.

![obtención de detalles](./media/log-analytics-vmware/vmactivities1.png)

Si desea ver los datos de creación de máquina virtual de host ESXi adicionales, haga clic en un nombre de host ESXi.

![obtención de detalles](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>Consultas de búsqueda comunes

La solución incluye otras consultas útiles que pueden ayudarle a administrar su hosts ESXi, como espacio de almacenamiento alta, latencia de almacenamiento y error en la ruta de acceso.

![consultas](./media/log-analytics-vmware/queries.png)

#### <a name="save-queries"></a>Guardar consultas

Guardar las consultas de búsqueda es una característica estándar de OMS y puede ayudarle a mantener todas las consultas que haya encontrado útil. Después de crear una consulta que resulte útil, guárdelo haciendo clic en los **Favoritos**. Una consulta guardada le permite fácilmente volver a usarla más tarde desde la página de [Mi panel](log-analytics-dashboards.md) donde puede crear sus propios escritorios personalizados.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Crear alertas de consultas

Una vez que haya creado las consultas, es recomendable usar las consultas para alertarle cuando se producen eventos concretos. Vea [las alertas de análisis de registro](log-analytics-alerts.md) para obtener información acerca de cómo crear alertas. Para obtener ejemplos de alerta consultas y otros ejemplos de consultas, consulte el [Monitor VMware mediante el análisis del registro de OMS](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) blog.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>¿Qué es necesario para hospedar en el ESXi configuración? ¿Qué impacto tendrá ésta en mi entorno?
La solución utiliza nativa ESXi Host de registro del sistema mecanismo de reenvío. No necesita ningún software adicional de Microsoft en el ESXi Host para capturar los registros. Debe tener un impacto bajo su entorno existente. Sin embargo, debe configurar el reenvío de registro del sistema, que es la funcionalidad ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>¿Es necesario reiniciar el host de mi ESXi?
No. Este proceso no requiere reiniciar el equipo. A veces, vSphere no actualiza correctamente el registro del sistema. En este caso, inicie sesión en el host ESXi y volver a cargar el registro del sistema. De nuevo, no tiene reinicie el host, para que este proceso no resulte perjudicial para su entorno.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-oms"></a>¿Puedo aumentar o disminuir el volumen de datos de registro que se envían a OMS?
Sí puede. Puede usar la configuración de nivel de registro de Host ESXi en vSphere. Colección de registro se basa en el nivel de *información* . Por lo tanto, si desea auditar VM creación o eliminación, debe mantener el nivel de *información* en Hostd. Para obtener más información, consulte la [Base de conocimientos de VMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-oms-my-log-setting-is-set-to-info"></a>¿Por qué Hostd no está proporcionando datos a OMS? Mi configuración de registro se establece en información.
Se ha producido un error de host ESXi para la marca de hora de registro del sistema. Para obtener más información, consulte la [Base de conocimientos de VMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Después de aplicar la solución alternativa, Hostd debería funcionar normalmente.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>¿Puedo tener varios hosts de ESXi datos del registro del sistema de reenvío para una única VM con omsagent?
Sí. Puede tener varios hosts de ESXi reenvío para una única VM con omsagent.

### <a name="why-dont-i-see-data-flowing-into-oms"></a>¿Por qué no puedo ver los datos que fluye en OMS?

Puede haber varias razones:

- El host ESXi no está correctamente e inserta datos VM ejecutando omsagent. Para probar, realice los pasos siguientes:
    1. Para confirmar, inicie sesión en el host de ESXi mediante ssh y ejecute el siguiente comando:`nc -z ipaddressofVM 1514`

        Si no es correcta, es probable que la configuración de vSphere en la configuración avanzada no corregir. Vea [Configurar colección de registro del sistema](#configure-syslog-collection) para obtener información sobre cómo configurar el host ESXi para el reenvío de registro del sistema.

    2. Si la conectividad de puerto de registro del sistema es correcta, pero aún no ve los datos, a continuación, volver a cargar el registro del sistema en el host ESXi mediante ssh para ejecutar el siguiente comando:` esxcli system syslog reload`

- La máquina virtual con el agente de OMS no está configurada correctamente. Para comprobarlo, siga estos pasos:
    1. OMS escucha el puerto 1514 e inserta los datos en OMS. Para comprobar que está abierto, ejecute el siguiente comando:`netstat -a | grep 1514`
    2. Debería ver puerto `1514/tcp` abrir. Si no lo hace, compruebe que la omsagent está instalado correctamente. Si no ve la información de puerto, el puerto de registro del sistema no está abierto en la máquina virtual.
        1. Compruebe que está ejecutando el agente de OMS usando `ps -ef | grep oms`. Si no se está ejecutando, inicie el proceso, ejecute el comando` sudo /opt/microsoft/omsagent/bin/service_control start`
        2. Abrir la `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` archivo.

            Compruebe que la configuración de grupo y usuario adecuado es válido, al igual que:`-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

            Si el archivo no existe o el usuario y la configuración de grupo es incorrecta, tomar medidas correctivas Preparando [un servidor Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Pasos siguientes

- Realice [Búsquedas de registro](log-analytics-log-searches.md) de análisis de registro para ver datos detallados de host de VMware.
- [Crear sus propio paneles](log-analytics-dashboards.md) que muestra datos de host de VMware.
- [Crear alertas](log-analytics-alerts.md) cuando se producen eventos específicos de host de VMware.
