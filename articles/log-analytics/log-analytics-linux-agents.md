<properties
    pageTitle="Conectar equipos Linux para el análisis de registro | Microsoft Azure"
    description="Análisis de registro puede recopilar y actuar sobre los datos generados de equipos Linux."
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
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="connect-linux-computers-to-log-analytics"></a>Conectar equipos Linux para el análisis de registro

Análisis de registro puede recopilar y actuar sobre los datos generados de equipos Linux. Agregar datos recopilados desde Linux a OMS le permite administrar soluciones de contenedor como Docker independientemente de dónde se encuentran sus equipos y sistemas Linux: prácticamente cualquier lugar. Por lo tanto, los orígenes de datos podrían residen en el centro de datos local como servidores físicos, equipos virtuales en un servicio hospedado de nube como servicios Web de Amazon (AWS) o Microsoft Azure o incluso el portátil en su escritorio. Además, OMS también recopila datos de equipos con Windows del mismo modo, por lo que es compatible con un híbrido consiguen entorno de TI.

Puede ver y administrar los datos de todos los orígenes de registro de análisis de OMS con un portal de administración única. Esto reduce la necesidad de supervisar con muchos sistemas diferentes, hace fácil de consumir y puede exportar cualquier dato que desee para la solución de análisis de negocio o sistema que ya tiene.

En este artículo es rápida iniciar a guía que le ayudará a recopilar y administrar los datos de los equipos de Linux mediante el agente de OMS para Linux. Para obtener más información, como la configuración del servidor proxy, información sobre cómo métricas CollectD y orígenes de datos JSON personalizados, encontrará esta información en [Agente de OMS para información general de Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) y [OMS para Linux completa documentación](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md) en Github.


Actualmente, puede recopilar los siguientes tipos de datos de equipos Linux:

- Indicadores de rendimiento
- Eventos de registro del sistema
- Alertas de Nagios y Zabbix
- Registros, inventario y medidas de rendimiento del contenedor de docker

## <a name="supported-linux-versions"></a>Versiones de Linux compatibles

Las versiones x86 y x64 oficialmente son compatibles con una amplia variedad de las distribuciones de Linux. Sin embargo, el agente de OMS para Linux también pueden ejecutar en otras distribuciones no aparece.

- Amazon Linux 2012.09 a través de 2015.09
- CentOS Linux 5, 6 y 7
- Oracle Linux 5, 6 y 7
- Servidor de rojo sombrero Enterprise Linux 5, 6 y 7
- Debian GNU/Linux 6, 7 y 8
- Ubuntu 12.04 LTADOS, 14.04 LTADOS, 15.04, 15.10
- SUSE Linux Enterprise Server 11 y 12

## <a name="oms-agent-for-linux"></a>Agente OMS para Linux
El agente de conjunto de aplicaciones de administración de operaciones para Linux consta de varios paquetes. El archivo de versión contiene los siguientes paquetes disponibles, ejecute el paquete de shell con `--extract`.

**Paquete** | **Versión** | **Descripción**
----------- | ----------- | --------------
omsagent | 1.1.0 | El agente de conjunto de aplicaciones de administración de operaciones para Linux
omsconfig | 1.1.1 | Agente de configuración para el agente de OMS
OMI | 1.0.8.3 | Infraestructura de administración abierta (OMI), un servidor de CIM ligero
SCX | 1.6.2 | Proveedores de CIM OMI para los indicadores de rendimiento de sistema operativo
Apache cimprov | 1.0.0 | Proveedor para OMI de supervisión de rendimiento de servidor HTTP Apache. Sólo se instalan si se detecta servidor HTTP Apache.
MySQL cimprov | 1.0.0 | Proveedor para OMI de supervisión de rendimiento de servidor MySQL. Sólo se instalan si se detecta servidor MySQL/MariaDB.
docker cimprov | 0.1.0 | Proveedor de docker para OMI. Sólo se instalan si se detecta Docker.

### <a name="additional-installation-artifacts"></a>Artefactos de instalación adicionales
Después de instalar al agente OMS para paquetes Linux, se aplican los siguientes cambios de configuración adicionales de todo el sistema. Estos artefactos se quitan cuando se desinstala el paquete omsagent.
- El nombre de usuario sin privilegios: `omsagent` se crea. Esta es la cuenta que se ejecuta el daemon omsagent como
- Se crea un archivo de "incluir" sudoers en /etc/sudoers.d/omsagent este campo autoriza omsagent reiniciar el registro del sistema y omsagent daemons. Si no se admiten directivas de sudo "incluir" en la versión instalada de sudo, estas entradas se escribirá en /etc/sudoers.
- Se modifica la configuración de registro del sistema para reenviar un subconjunto de eventos con el agente. Para obtener más información, consulte la sección **Configuración de recopilación de datos**

### <a name="linux-data-collection-details"></a>Detalles del conjunto de datos de Linux

La siguiente tabla muestra los métodos de conjunto de datos y otros detalles acerca de cómo se recopilan los datos.

| origen | Agente directo | Agente SCOM | Almacenamiento de Azure | ¿SCOM obligatorio? | Datos de agente SCOM enviadas por grupo de administración | frecuencia de la colección |
|---|---|---|---|---|---|---|
|Zabbix|![Sí](./media/log-analytics-linux-agents/oms-bullet-green.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|            ![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|1 minuto|
|Nagios|![Sí](./media/log-analytics-linux-agents/oms-bullet-green.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|            ![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|en llegada|
|registro del sistema|![Sí](./media/log-analytics-linux-agents/oms-bullet-green.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|            ![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|desde el almacenamiento de Azure: 10 minutos; agente de: en llegada|
|Contadores de rendimiento de Linux|![Sí](./media/log-analytics-linux-agents/oms-bullet-green.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|            ![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|Según lo programado, mínimo de 10 segundos|
|seguimiento de cambios|![Sí](./media/log-analytics-linux-agents/oms-bullet-green.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|            ![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|cada hora|



### <a name="package-requirements"></a>Requisitos de paquete
| **Paquete requerido**  | **Descripción**   | **Versión mínima**|
|--------------------- | --------------------- | -------------------|
|Glibc |    Biblioteca GNU C   | 2,5-12|
|OpenSSL    | Bibliotecas de OpenSSL | 0.9.8E o 1.0|
|Doblez | cliente de web doblez | 7.15.5
|Python ctypes |bibliotecas de funciones | n/a.|
|PAM | Módulos de autenticación acoplable  |n/a. |

>[AZURE.NOTE] Rsyslog o ng de registro del sistema se requieren para recopilar mensajes de registro del sistema. No se admite el daemon de registro del sistema de forma predeterminada en la versión 5 de Red sombrero Enterprise Linux, CentOS y Oracle Linux (sysklog) para la colección de eventos de registro del sistema. Para recopilar datos de registro del sistema de esta versión de estas distribuciones, el daemon rsyslog debe estar instalado y configurado para reemplazar sysklog.

## <a name="quick-install"></a>Instalación rápida

Ejecute los comandos siguientes para descargar el omsagent, validar la suma de comprobación, vuelva a instalar e incorporado el agente. Comandos son de 64 bits. El identificador de área de trabajo y la clave principal se encuentran en el portal OMS en **configuración** en la pestaña **Orígenes conectados** .

![detalles de área de trabajo](./media/log-analytics-linux-agents/oms-direct-agent-primary-key.png)

```
wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.1.0-28/omsagent-1.1.0-28.universal.x64.sh
sha256sum ./omsagent-1.1.0-28.universal.x64.sh
sudo sh ./omsagent-1.1.0-28.universal.x64.sh --upgrade -w <YOUR OMS WORKSPACE ID> -s <YOUR OMS WORKSPACE PRIMARY KEY>
```

Hay una gran variedad de otros métodos para instalar al agente y actualizarla. Puede obtener más información acerca de ellos en [los pasos para instalar el agente de OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux).

También puede ver el [Tutorial de vídeo de Azure](https://www.youtube.com/watch?v=mF1wtHPEzT0).

## <a name="choose-your-linux-data-collection-method"></a>Elija el método de la colección de datos de Linux

¿Cómo elegir los tipos de datos que desea recopilar depende de si desea usar el portal de OMS o si desea editar varios archivos de configuración directamente en los clientes de Linux. Si decide usar el portal, la configuración se envía automáticamente a todos los clientes de Linux. Si necesita configuraciones diferentes para diferentes clientes Linux, necesitará editar archivos de cliente de forma individual, o use una alternativa como DSC de PowerShell, Chef o posición libre.

Puede especificar los eventos de registro del sistema y contadores de rendimiento que se desean recopilar mediante archivos de configuración en los equipos de Linux. *Si opta por configurar la recopilación de datos mediante la edición de archivos de configuración de agente, debe deshabilitar la configuración centralizada.*  Continuación se proporcionan instrucciones para configurar la recopilación de datos en archivos de configuración del agente así como para deshabilitar la configuración para todos los agentes de OMS central para Linux o equipos individuales.

### <a name="disable-oms-management-for-an-individual-linux-computer"></a>Deshabilitar la administración de OMS de un equipo Linux individual

Recopilación de datos centralizada de datos de configuración está deshabilitado para un equipo con la secuencia de comandos de OMS_MetaConfigHelper.py Linux. Esto puede ser útil si un subconjunto de equipos debe tener una configuración especializada.

Para deshabilitar la configuración centralizada:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```

Para volver a habilitar configuración centralizada:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py –enable
```

## <a name="linux-performance-counters"></a>Contadores de rendimiento de Linux

Contadores de rendimiento de Linux son similares a los registros de rendimiento de Windows, funcionan de forma similar. Puede usar los siguientes procedimientos para agregar y configurarlos. Después de que se hayan agregado a OMS, se recopilan datos para ellos cada 30 segundos.

### <a name="to-add-a-linux-performance-counter-in-oms"></a>Para agregar un contador de rendimiento de Linux de OMS

1. Para configurar agentes de OMS para Linux con el portal OMS, puede agregar contadores de rendimiento de Linux en la página Configuración, haga clic en **datos**.  
2. En la página de **configuración** en **datos** , haga clic en **Linux contadores** y, a continuación, seleccione o escriba el nombre del contador que desea agregar.  
    ![datos](./media/log-analytics-linux-agents/oms-settings-data01.png)
3. Si no conoce el nombre completo del contador, puede empezar a escribir un nombre parcial y aparecerá una lista de contadores disponibles. Cuando encuentre el contador que desea agregar, haga clic en el nombre de la lista y, a continuación, haga clic en el icono de signo más para agregar el contador.
4. Después de agregar el contador, aparece en la lista de contadores resaltada con una barra de color.
5. De forma predeterminada, está seleccionada la opción **Aplicar debajo de configuración de mis equipos** . Si desea deshabilitar el envío de datos de configuración, desactive la selección.
6. Cuando haya terminado de modificar contadores de rendimiento, en la parte inferior de la página haga clic en **Guardar** para finalizar los cambios. Los cambios de configuración que ha realizado se envían a todos los agentes de OMS para Linux que están registrados con OMS, normalmente dentro de 5 minutos.

### <a name="configure-linux-performance-counters-in-oms"></a>Configurar Linux contadores de OMS

Contadores de rendimiento de Windows, puede elegir una instancia específica para cada contador de rendimiento. Sin embargo, Linux contadores de rendimiento, cualquier instancia de un contador que elija se aplica a todos los contadores secundarios del contador principal. La siguiente tabla muestra las instancias comunes disponibles a registros de rendimiento de Windows y Linux.

| **Nombre de instancia** | **Significado** |
| --- | --- |
| \_Total | Total de todas las instancias |
| \* | Todas las instancias |
| (/ & #124; / var) | Coincide con las instancias con nombre: / o/var |


Asimismo, el intervalo de muestra que elija un contador principal se aplica a todos sus contadores secundarios. En otras palabras, todos los intervalos de muestra de contador secundarios y casos en los que están entre sí.

### <a name="add-and-configure-performance-metrics-with-linux"></a>Agregar y configurar los indicadores de rendimiento con Linux

Indicadores de rendimiento para recopilar están controlados por la configuración de /etc/opt/microsoft/omsagent/conf/omsagent.conf. Vea [indicadores de rendimiento disponibles](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#appendix-available-performance-metrics) para las clases disponibles y métrica para el agente de OMS para Linux.

Cada objeto o categoría de indicadores de rendimiento para recopilar debe definirse en el archivo de configuración como un único `<source>` elemento. La sintaxis de la sigue el modelo siguiente.

```
<source>
  type oms_omi  
  object_name "Processor"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

Los parámetros de este elemento configurables son:

- **Objeto\_nombre**: el nombre de objeto de la colección.
- **Instancia\_regex**: una *expresión regular* que se define qué instancias recopilar. El valor: `.*` especifica todas las instancias. Recopilar métricas del procesador para solo la \_instancia Total, puede especificar `_Total`. Para recopilar métricas del proceso para las instancias crond o sshd, puede especificar: `(crond|sshd)`.
- **Contador\_nombre\_regex**: una *expresión regular* que se define alguno (para el objeto) para recopilar. Para recopilar todos los contadores para el objeto, especifique: `.*`. Para recopilar sólo intercambiar espacio contadores para el objeto de memoria, puede especificar:`.+Swap.+`
- **Intervalo:**: la frecuencia a la que se recopilan contadores del objeto.

La configuración predeterminada de los indicadores de rendimiento es:

```
<source>
  type oms_omi
  object_name "Physical Disk"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Logical Disk"
  instance_regex ".*
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Processor"
  instance_regex ".*
  counter_name_regex ".*"
  interval 30s
</source>

<source>
  type oms_omi
  object_name "Memory"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

### <a name="enable-mysql-performance-counters-using-linux-commands"></a>Habilitar contadores de rendimiento de MySQL con los comandos de Linux

Si el servidor MySQL o MariaDB se detecta en el equipo cuando está instalado el paquete de omsagent, un proveedor de servidor MySQL de supervisión de rendimiento se instala automáticamente. Este proveedor se conecta al servidor MySQL/MariaDB local para exponer estadísticas de rendimiento. Debe configurar las credenciales de usuario de MySQL para que el proveedor de acceso el servidor MySQL.

Para definir una cuenta de usuario predeterminada para el servidor MySQL en el host local, utilice el siguiente ejemplo de comando.

>[AZURE.NOTE] El archivo de credenciales debe leerse con la cuenta omsagent. Se recomienda ejecutar el comando mycimprovauth como omsgent.


```
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'

sudo service omiserverd restart
```


Como alternativa, puede especificar las credenciales necesarias de MySQL en un archivo, crear el archivo: /var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth. Para obtener más información acerca de cómo administrar las credenciales de MySQL para supervisión a través del archivo auth mysql, vea [Administrar MySQL credenciales supervisión en el archivo de autenticación](#manage-mysql-monitoring-credentials-in-the-authentication-file).

Para obtener más información acerca de los permisos de objeto requeridos por el usuario de MySQL para recopilar datos de rendimiento de servidor MySQL, vea [requieren permisos de base de datos MySQL contadores de rendimiento](#database-permissions-required-for-mysql-performance-counters) .

### <a name="enable-apache-http-server-performance-counters-using-linux-commands"></a>Habilitar contadores de rendimiento de servidor HTTP Apache con los comandos de Linux

Si se detecta servidor HTTP Apache en el equipo cuando está instalado el paquete de omsagent, un proveedor de servidor HTTP Apache de supervisión de rendimiento se instala automáticamente. Este proveedor se basa en un Apache "módulo" que debe cargarse en el servidor Apache HTTP para tener acceso a datos de rendimiento.

Puede cargar el módulo con el siguiente comando:

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Para descargar el módulo de supervisión de Apache, ejecute el siguiente comando:

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```
### <a name="to-view-performance-data-with-log-analytics"></a>Para ver los datos de rendimiento con el análisis de registro

1. En el portal de la serie de administración de operaciones, haga clic en el icono de búsqueda de registros.
2. En la barra de búsqueda, escriba `* (Type=Perf)` para ver todos los contadores de rendimiento.


Porque OMS también recopila datos de contador de rendimiento de Windows, debe abajo para el ámbito de la búsqueda a datos específicos de Linux. Por lo tanto, en el ejemplo siguiente se podría mostrar que datos de rendimiento específicas de un servidor de Linux de ejemplo denominado Chorizo21.

```
Type=Perf Computer=chorizo*
```

![servidor de ejemplo que se muestra en los resultados de búsqueda](./media/log-analytics-linux-agents/oms-perfsearch01.png)

En los resultados, puede hacer clic en **métricas** para ver los contadores que se recopilan los datos de. Datos en tiempo real se muestran como gráficos para cada contador.

![métricas](./media/log-analytics-linux-agents/oms-perfmetrics01.png)


## <a name="syslog"></a>Registro del sistema

Registro del sistema es un protocolo de registro de eventos similar a los registros de eventos de Windows, funcionan de forma similar cuando se muestran en OMS.

### <a name="to-add-a-new-linux-syslog-facility-in-oms"></a>Para agregar una nueva facilidad de registro del sistema de Linux de OMS

1. En la página de **configuración** en **datos** , haga clic en el **registro del sistema** y a la izquierda del icono de signo más, escriba el nombre de la función de registro del sistema que desea agregar.
    ![Registro del sistema de Linux](./media/log-analytics-linux-agents/oms-linuxsyslog01.png)
2.  Si no conoce el nombre completo de la instalación, puede empezar a escribir un nombre parcial y aparecerá una lista de instalaciones disponibles de registro del sistema. Cuando encuentre la instalación de registro del sistema que desea agregar, haga clic en el nombre de la lista y, a continuación, haga clic en el icono de signo más para agregar la función de registro del sistema.
3.  Después de agregar aparece en la lista de las instalaciones, se resalta con una barra de color. A continuación, elija la gravedad (categorías de la información de las instalaciones de registro del sistema) que se desea recopilar.
4.  En la parte inferior de la página, haga clic en **Guardar** para finalizar los cambios. Los cambios de configuración que ha realizado se envían a todos los agentes de OMS para Linux que están registrados con OMS, normalmente dentro de 5 minutos.


### <a name="configure-linux-syslog-facilities-in-linux"></a>Configurar las instalaciones de registro del sistema Linux en Linux

Eventos de registro del sistema se envían demonio del registro del sistema, por ejemplo rsyslog o ng de registro del sistema, a un puerto local que está escuchando el agente. De forma predeterminada, el puerto 25224. Cuando esté instalado el agente, se aplica una configuración de registro del sistema de forma predeterminada. Esto se encuentra en:


Rsyslog: /etc/rsyslog.d/rsyslog-oms.conf

Registro del sistema ng: /etc/syslog-ng/syslog-ng.conf


La configuración de registro del sistema del agente OMS predeterminado carga los eventos de registro del sistema de todas las instalaciones con una gravedad de advertencia o superior.

>[AZURE.NOTE] Si modifica la configuración de registro del sistema, debe reiniciar el daemon de registro del sistema para que los cambios surtan efecto.

El registro del sistema configuración predeterminada para el agente de OMS para Linux para OMS es:

#### <a name="rsyslog"></a>Rsyslog

```
kern.warning       @127.0.0.1:25224
user.warning       @127.0.0.1:25224
daemon.warning     @127.0.0.1:25224
auth.warning       @127.0.0.1:25224
syslog.warning     @127.0.0.1:25224
uucp.warning       @127.0.0.1:25224
authpriv.warning   @127.0.0.1:25224
ftp.warning        @127.0.0.1:25224
cron.warning       @127.0.0.1:25224
local0.warning     @127.0.0.1:25224
local1.warning     @127.0.0.1:25224
local2.warning     @127.0.0.1:25224
local3.warning     @127.0.0.1:25224
local4.warning     @127.0.0.1:25224
local5.warning     @127.0.0.1:25224
local6.warning     @127.0.0.1:25224
local7.warning     @127.0.0.1:25224
```

#### <a name="syslog-ng"></a>Registro del sistema ng

```
#OMS_facility = all
filter f_warning_oms { level(warning); };
destination warning_oms { tcp("127.0.0.1" port(25224)); };
log { source(src); filter(f_warning_oms); destination(warning_oms); };
```

### <a name="to-view-all-syslog-events-with-log-analytics"></a>Para ver todos los eventos de registro del sistema con el análisis de registro

1. En el portal de la serie de administración de operaciones, haga clic en el icono de **Búsqueda de registros** .
2. En la agrupación de **Administración de registros** , elija una búsqueda predefinida registro del sistema y, a continuación, seleccione uno para ejecutarlo.

Este ejemplo muestra todos los eventos de registro del sistema.

![Se muestra en la búsqueda de registro de eventos de registro del sistema](./media/log-analytics-linux-agents/oms-linux-syslog.png)

Ahora puede explorar en profundidad los resultados de búsqueda.

## <a name="linux-alerts"></a>Alertas de Linux

Si usa Nagios o Zabbix para administrar los equipos Linux, OMS puede recibir las alertas generadas desde estas herramientas. Sin embargo, hay actualmente ningún método para configurar los datos de alerta entrantes con el portal OMS. En su lugar, debe modificar un archivo de configuración para empezar a enviar alertas a OMS.



### <a name="collect-alerts-from-nagios"></a>Recopilar las alertas de Nagios

Para recopilar las alertas de un servidor de Nagios, debe realizar los siguientes cambios de configuración.

1. Conceder acceso de lectura de **omsagent** de usuario para el archivo de registro de Nagios (es decir, /var/log/nagios/nagios.log/var/log/nagios/nagios.log). Suponiendo que el archivo nagios.log pertenece a la **nagios** de grupo, puede agregar el usuario **omsagent** al grupo **nagios** .

    ```
    sudo usermod –a -G nagios omsagent
    ```

2. Modificar el archivo omsagent.confconfiguration (o etc/opt/microsoft/omsagent/conf/omsagent.conf). Asegúrese de que las entradas siguientes están presentes y no comentados:

    ```
    <source>
    type tail
    #Update path to point to your nagios.log
    path /var/log/nagios/nagios.log
    format none
    tag oms.nagios
    </source>

    <filter oms.nagios>
    type filter_nagios_log
    </filter>
    ```

3. Reinicie el daemon omsagent:

    ```
    sudo service omsagent restart
    ```

### <a name="collect-alerts-from-zabbix"></a>Recopilar las alertas de Zabbix

Para recopilar las alertas de un servidor de Zabbix, que puede realizar pasos similares a los Nagios anterior, excepto deberá especificar un usuario y contraseña en *texto simple*. No es ideal, pero es probable que cambiarán pronto. Para solucionar este problema, le recomendamos que cree el usuario y conceder permiso para supervisar solo.

Una sección de ejemplo del archivo de configuración de omsagent.conf (o etc/opt/microsoft/omsagent/conf/omsagent.conf) para Zabbix debe ser similar a lo siguiente:

```
<source>
  type zabbix_alerts
  run_interval 1m
  tag oms.zabbix
  zabbix_url http://localhost/zabbix/api_jsonrpc.php
  zabbix_username Admin
  zabbix_password zabbix
</source>

```

### <a name="view-alerts-in-log-analytics-search"></a>Ver los avisos en búsqueda de análisis de registro

Una vez que haya configurado sus equipos Linux para enviar alertas a OMS, puede usar algunas consultas de búsqueda de registro simple para ver las alertas. El siguiente ejemplo de consulta de búsqueda devuelve todas las alertas registradas generados. Por ejemplo, si se produce algún tipo de problema en la infraestructura de TI, a continuación, los resultados de la siguiente consulta de ejemplo pueden indicar procedencia del problema. Y puede fácilmente profundizar a las alertas de sistema de origen para que restrinja la investigación. La ventaja es que no tiene necesariamente ir a distintos sistemas de administración, desde el principio, siempre que se envían las alertas de OMS, puede iniciar allí.

```
Type=Alert
```

#### <a name="to-view-all-nagios-alerts-with-log-analytics"></a>Para ver todas las alertas de Nagios con el análisis de registro
1. En el portal de la serie de administración de operaciones, haga clic en el icono de **Búsqueda de registros** .
2. En la barra de la consulta, escriba la siguiente consulta de búsqueda

    ```
    Type=Alert SourceSystem=Nagios
    ```
![Alertas de Nagios que se muestra en la búsqueda de registros](./media/log-analytics-linux-agents/oms-linux-nagios-alerts.png)

Después de ver los resultados de búsqueda, puede explorar en profundidad los detalles adicionales como *AlertState*.

### <a name="to-view-all-zabbix-alerts-with-log-analytics"></a>Para ver todas las alertas de Zabbix con el análisis de registro
1. En el portal de la serie de administración de operaciones, haga clic en el icono de **Búsqueda de registros** .
2. En la barra de la consulta, escriba la siguiente consulta de búsqueda

    ```
    Type=Alert SourceSystem=Zabbix
    ```
![Alertas de Zabbix que se muestra en la búsqueda de registros](./media/log-analytics-linux-agents/oms-linux-zabbix-alerts.png)

Después de ver los resultados de búsqueda, puede explorar en profundidad los detalles adicionales como *NombreAlerta*.


## <a name="compatibility-with-system-center-operations-manager"></a>Compatibilidad con System Center Operations Manager

El agente de OMS para Linux comparte archivos binarios de agente con el agente de System Center Operations Manager. Instalar al agente de OMS para Linux en un sistema administrado actualmente por Operations Manager actualiza los paquetes OMI y SCX en el equipo a una versión más reciente. El agente de OMS para Linux y System Center 2012 R2 son compatibles. Sin embargo, **System Center 2012 SP1 y versiones anteriores actualmente no son compatibles o es compatible con el agente de OMS para Linux.**

>[AZURE.NOTE] Si está instalado el agente de OMS para Linux en un equipo que no está gestionado actualmente por Operations Manager y más tarde desea administrar el equipo con Operations Manager, debe modificar la configuración de OMI antes de detectar el equipo. **Este paso no es necesario si está instalado el agente de Operations Manager antes que el agente de OMS para Linux.**

### <a name="to-enable-the-oms-agent-for-linux-to-communicate-with-operations-manager"></a>Para habilitar al agente de OMS para Linux para comunicarse con Operations Manager

1. Editar el archivo /etc/opt/omi/conf/omiserver.conf
2. Asegúrese de que la línea que comienza con **httpsport =** define el puerto 1270. Por ejemplo`httpsport=1270`
3. Reiniciar el servidor OMI:

    ```
    service omiserver restart or systemctl restart omiserver
    ```




## <a name="database-permissions-required-for-mysql-performance-counters"></a>Permisos de base de datos necesarios para contadores de rendimiento de MySQL

Para conceder permisos a un usuario de supervisión de MySQL, debe tener el usuario conceder el privilegio 'GRANT option', así como el privilegio que se le ha concedido.

Para el usuario de MySQL devolver datos de rendimiento que el usuario tendrá acceso a las consultas siguientes:

```
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

Además de estas consultas MySQL usuario requiere seleccione acceso a las siguientes tablas de forma predeterminada:

- INFORMATION_SCHEMA
- MySQL

Se pueden conceder estos privilegios, ejecute los siguientes comandos conceder.

```
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

## <a name="manage-mysql-monitoring-credentials-in-the-authentication-file"></a>Administrar MySQL supervisión credenciales en el archivo de autenticación

Las secciones siguientes le permiten administrar credenciales de MySQL.

### <a name="configure-the-mysql-omi-provider"></a>Configurar el proveedor de MySQL OMI

El proveedor de OMI MySQL requiere un usuario MySQL preconfigurado e instalado MySQL bibliotecas de cliente para consultar la información de rendimiento y mantenimiento de la instancia de MySQL.

### <a name="mysql-omi-authentication-file"></a>Archivo de autenticación OMI MySQL

Proveedor de MySQL OMI utiliza un archivo de autenticación para determinar qué dirección de enlace y puerto MySQL escucha instancia y sobre qué credenciales usar para recopilar métricas. Durante la instalación de la OMI MySQL proveedor analizar los archivos de configuración de my.cnf MySQL (ubicaciones predeterminadas) para la dirección de enlace y puerto y establecer parcialmente la OMI MySQL archivo de autenticación.

Para completar la supervisión de una instancia del servidor MySQL, agregue un archivo de autenticación MySQL OMI generado previamente en el directorio correcto.

### <a name="authentication-file-format"></a>Formato de archivo de autenticación

El archivo de autenticación MySQL OMI es un archivo de texto que contiene información acerca de:

- Puerto
- Dirección de enlace
- Nombre de usuario de MySQL
- Contraseña de base 64 codificado

El archivo de autenticación MySQL OMI sólo concede privilegios de lectura y escritura para el usuario de Linux que lo generó.

```
[Port]=[Bind-Address], [username], [Base64 encoded Password]
(Port)=(Bind-Address), (username), (Base64 encoded Password)
(Port)=(Bind-Address), (username), (Base64 encoded Password)
AutoUpdate=[true|false]
```

Un archivo de autenticación MySQL OMI predeterminado contiene una instancia predeterminada y un número de puerto según la información que está disponible y analizada desde el archivo de configuración de MySQL encontrado.

La instancia predeterminada es un medio para gestionar varias instancias de MySQL en un host Linux sea más fácil y se denota con la instancia con puerto 0. Todas las instancias agregadas heredan propiedades de la instancia predeterminada. Por ejemplo, si se agrega la instancia de MySQL en el puerto '3308', dirección de enlace, nombre de usuario y contraseña de base 64 codificado de la instancia predeterminada se utilizará para probar y supervisar la instancia atendiendo 3308. Si la instancia en 3308 está enlazada a otra dirección y utiliza el mismo par de nombre de usuario y contraseña de MySQL solo la respecification de la dirección de enlace es necesario y heredarán las otras propiedades.

Ejemplos del archivo de autenticación similar a la siguiente.

Instancia predeterminada e instancia con puerto 3308:

```
0=127.0.0.1, myuser, cnBwdA==3308=, ,AutoUpdate=true
```

Instancia predeterminada e instancia con puerto 3308 + diferentes Base 64 codifican contraseña:

```
0=127.0.0.1, myuser, cnBwdA==3308=127.0.1.1, , AutoUpdate=true
```


| **(Propiedad)** | **Descripción** |
| --- | --- |
| Puerto | Puerto representa el puerto actual que está escuchando la instancia de MySQL.  El puerto 0 implica que las propiedades siguientes se usan para la instancia predeterminada. |
| Dirección de enlace | la dirección de enlazar es la actual MySQL-dirección de enlace |
| nombre de usuario | Este nombre de usuario del usuario de MySQL que desea usar para supervisar la instancia del servidor MySQL. |
| Contraseña de codificado base 64 | Esta es la contraseña del usuario supervisión MySQL codificado en base 64. |
| AutoUpdate | Cuando se actualiza el proveedor de OMI MySQL el proveedor volver a examinar los cambios en el archivo my.cnf y sobrescribir el archivo MySQL OMI autenticación. Establecer este indicador como verdadero o falso dependiendo de las actualizaciones necesarias para el archivo de autenticación OMI MySQL. |

#### <a name="authentication-file-location"></a>Ubicación del archivo de autenticación

El archivo de autenticación MySQL OMI se debe había ubicado en la siguiente ubicación y con el nombre "mysql auth":

/var/opt/Microsoft/MySQL-cimprov/AUTH/omsagent/MySQL-AUTH

El archivo (y directorio de auth/omsagent) deben pertenecer por el usuario omsagent.

## <a name="agent-logs"></a>Registros de agente

Los registros para el agente de OMS para Linux está en:

/ var/participar/microsoft/omsagent/registro /

Los registros para el agente de OMS para Linux omsconfig (configuración de agente) programa está en:

/ var/participar/microsoft/omsconfig/registro /

Registros de los componentes OMI y SCX (que proporcionan datos de rendimiento métricas) está en:

/ var/participar/omi/registro/y /var/opt/microsoft/scx/log

## <a name="troubleshooting-the-oms-agent-for-linux"></a>El agente OMS para Linux de solución de problemas

Use la información siguiente para diagnosticar y solucionar problemas comunes.

Si ninguno de los datos de solución de problemas de esta sección le ayuda a, también puede usar los siguientes recursos para ayudar a resolver el problema.

- Los clientes con Premier soporte puede iniciar un caso de soporte a través de [Premier](https://premier.microsoft.com/)
- Clientes con contratos de soporte técnico de Azure pueden iniciar casos de soporte en el [portal de Azure](https://manage.windowsazure.com/?getsupport=true)
- Un [Problema de GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) de archivo
- Foro de comentarios para obtener ideas y para crear un error de informe [http://aka.ms/opinsightsfeedback](http://aka.ms/opinsightsfeedback)

### <a name="important-log-locations"></a>Ubicaciones del registro importantes

Archivo | Ruta de acceso
---- | -----
Agente OMS para archivo de registro de Linux | `/var/opt/microsoft/omsagent/log/omsagent.log `
Archivo de registro de configuración de agente OMS | `/var/opt/microsoft/omsconfig/omsconfig.log`

### <a name="important-configuration-files"></a>Archivos de configuración importantes

Catergory | Ubicación del archivo
----- | -----
Registro del sistema | `/etc/syslog-ng/syslog-ng.conf`o `/etc/rsyslog.conf` o`/etc/rsyslog.d/95-omsagent.conf`
Rendimiento, Nagios, Zabbix, salida OMS y agente general | `/etc/opt/microsoft/omsagent/conf/omsagent.conf`
Configuraciones adicionales | `/etc/opt/microsoft/omsagent/conf.d/*.conf`

>[AZURE.NOTE] Edición de archivos de configuración para contadores de rendimiento y registro del sistema se sobrescriben si se habilita la configuración del Portal de OMS. Puede deshabilitar la configuración en el Portal de OMS (para todos los nodos) o de nodos únicos ejecutando lo siguiente:

```
sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```


### <a name="enable-debug-logging"></a>Habilitar el registro de depuración

Para habilitar el registro de depuración, puede usar el complemento de salida OMS y el resultado detallado.

#### <a name="oms-output-plugin"></a>Complemento de salida OMS

FluentD permite que el complemento especificar los niveles de registro para los niveles de registro diferentes para entradas y salidas. Para especificar un nivel de registro diferentes para los resultados OMS, editar la configuración de agente general en el `/etc/opt/microsoft/omsagent/conf/omsagent.conf` archivo.

En la parte inferior del archivo de configuración, cambie la `log_level` propiedad de `info` a `debug`.

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

Registro de depuración le permite ver cargas por lotes al servicio de OMS separados por tipo, el número de elementos de datos y el tiempo necesario para enviar.

*Registro de depuración habilitada de ejemplo:*
```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

#### <a name="verbose-output"></a>Resultado detallado
En lugar de usar el complemento de salida OMS, puede también enviar elementos de datos directamente a `stdout`, que está visible en el agente de OMS para archivo de registro de Linux.

En el archivo de configuración de agente general de OMS en `/etc/opt/microsoft/omsagent/conf/omsagent.conf`, comentario de salida de complemento de salida de la OMS agregando una `#` delante de cada línea.

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Debajo de complemento de salida, quite el comentario en la siguiente sección eliminando la `#` símbolo al principio de cada línea.

```
<match **>
  type stdout
</match>
```

### <a name="forwarded-syslog-messages-do-not-appear-in-the-log"></a>Los mensajes reenviados de registro del sistema no aparecen en el registro

#### <a name="probable-causes"></a>Causas probables

- La configuración de servidor de Linux no admite la colección de las instalaciones de enviados o los niveles de registro
- Registro del sistema no se reenvíen correctamente en el servidor Linux
- El número de mensajes que se desvíen por segundo es demasiado grande para la configuración básica del agente OMS para Linux manejar

#### <a name="resolutions"></a>Resoluciones

- Compruebe que la configuración en el Portal de OMS de registro del sistema tiene todas las funciones y los niveles de registro correcto
  - **Portal OMS > Configuración > datos > registro del sistema**
-  Compruebe que registro del sistema remoto nativo mensajería daemons (`rsyslog`, `syslog-ng`) pueden recibir los mensajes reenviados
- Comprobar la configuración del firewall en el servidor de registro del sistema para asegurarse de que los mensajes no se están bloqueados
-  Simular un mensaje de registro del sistema para OMS utilizando la `logger` comando: por ejemplo:
  - `logger -p local0.err "This is my test message"`

### <a name="problems-connecting-to-oms-when-using-a-proxy"></a>Problemas al conectarse a OMS cuando se usa un servidor proxy

#### <a name="probable-causes"></a>Causas probables

- El servidor proxy especificado al instalar y configurar al agente de son incorrecta
- Los extremos de servicio OMS no están whitelistested en el centro de datos

#### <a name="resolutions"></a>Resoluciones

- Vuelva a instalar el agente de OMS para Linux mediante el comando siguiente con la opción `-v` habilitado. Esto permite la salida detallada del agente de conexión a través del proxy al servicio de OMS.
  - `/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`
  - Revise la documentación del proxy OMS en [Configuración del agente para su uso con un servidor proxy HTTP](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#configuring-the-agent-for-use-with-an-http-proxy-server)
- Compruebe que los extremos de servicio OMS siguientes están en la lista blanca

Recurso Agente | Puertos
---- | ----
& #42;. ODS.opinsights.Azure.com | Puerto 443
& #42;. OMS.opinsights.Azure.com | Puerto 443
ODS.systemcenteradvisor.com | Puerto 443
& #42;.blob.core.windows.net/ | Puerto 443

### <a name="a-403-error-is-displayed-when-onboarding"></a>Se muestra un error 403 cuando integrado

#### <a name="probable-causes"></a>Causas probables

- La fecha y hora son incorrectas en el servidor Linux
- El identificador del área de trabajo y la clave de área de trabajo que se utiliza son incorrectos

#### <a name="resolution"></a>Resolución

- Compruebe la hora en el servidor Linux con la `date` comando. Si los datos están mayores o menor que 15 minutos de la hora actual, integrado produce un error. Para corregir esto, actualice la fecha y la zona horaria del servidor Linux.
- La versión más reciente del agente OMS para Linux le notifica si una diferencia de tiempo está causando error integrado
- Re-integrado con el identificador de área de trabajo y la clave de área de trabajo correctos. Para obtener más información, vea [integrado con la línea de comandos](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) .

### <a name="a-500-error-or-404-error-appears-in-the-log-file-after-onboarding"></a>Un error 500 o error 404 aparece en el archivo de registro tras integrado

Se trata de un problema conocido que se produce durante la primera carga de datos de Linux en un área de trabajo OMS. Esto no afecta a los datos que se envían u otros problemas. Puede pasar por alto los errores cuando inicialmente integrado.

### <a name="nagios-data-does-not-appear-in-the-oms-portal"></a>Datos de Nagios no aparece en el Portal de OMS

#### <a name="probable-causes"></a>Causas probables
- El usuario omsagent no tiene permisos para leer desde el archivo de registro de Nagios
- Las secciones de origen y filtro de Nagios todavía tienen comentarios en el archivo omsagent.conf

#### <a name="resolutions"></a>Resoluciones

- Agregar al usuario omsagent para leer el archivo Nagios. Para obtener más información, vea [Nagios alertas](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts) .
- En el agente de OMS para el archivo de configuración general de Linux en `/etc/opt/microsoft/omsagent/conf/omsagent.conf`, asegúrese de que **tanto** el origen de Nagios y las secciones del filtro tienen comentarios quitados, al igual que en el ejemplo siguiente se.

```
<source>
  type tail
  path /var/log/nagios/nagios.log
  format none
  tag oms.nagios
</source>

<filter oms.nagios>
  type filter_nagios_log
</filter>
```


### <a name="linux-data-doesnt-appear-in-the-oms-portal"></a>Datos de Linux no aparecen en el Portal de OMS

#### <a name="probable-causes"></a>Causas probables

- No se pudo integrado al servicio de OMS
- Conexión al servicio de OMS está bloqueada
- El agente de OMS para Linux datos es la copia de seguridad

#### <a name="resolutions"></a>Resoluciones

- Compruebe que integrado al servicio de OMS fue correcta, compruebe que la `/etc/opt/microsoft/omsagent/conf/omsadmin.conf` existe.
- Re-integrado con la línea de comandos omsadmin.sh. Para obtener más información, vea [integrado con la línea de comandos](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) .
- Si usa a un servidor proxy, use el servidor proxy de solución de problemas de pasos anteriores
- En algunos casos, cuando el agente de OMS para Linux no puede comunicarse con el servicio OMS, datos en el agente se copia de seguridad para el tamaño del búfer completa de 50 MB. Reinicie el agente de OMS para Linux ejecutando el el `service omsagent restart` o `systemctl restart omsagent` comandos.
  >[AZURE.NOTE] Este problema se corrige en agente versión 1.1.0-28 y versiones posteriores.

### <a name="syslog-linux-performance-counter-configuration-is-not-applied-in-the-oms-portal"></a>Configuración de contador de rendimiento de Syslog Linux no se aplica en el portal OMS

#### <a name="probable-causes"></a>Causas probables

- El agente de configuración en el agente de OMS para Linux no recuperado la configuración más reciente desde el portal OMS.
- No se ha aplicado la configuración modificada en el portal

#### <a name="resolutions"></a>Resoluciones

`omsconfig`es el agente de configuración en el agente de OMS para Linux que recupera los cambios de configuración del portal de OMS cada 5 minutos. Esta configuración se aplica al agente OMS para archivos de configuración que se encuentran en `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

- En algunos casos, es posible que el agente de OMS para agente de configuración de Linux no que pueda comunicarse con el servicio de configuración del portal de última configuración no se aplican.
- Compruebe que la `omsconfig` agente se ha instalado con las siguientes acciones:
  - `dpkg --list omsconfig`o`rpm -qi omsconfig`
  - Si no está instalado, vuelva a instalar la versión más reciente del agente OMS para Linux

- Compruebe que la `omsconfig` agente puede comunicarse con el servicio OMS
  - Ejecutar la `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` comando
    - El comando anterior devuelve la configuración que el agente recupera desde el portal, incluida la configuración de registro del sistema, contadores de rendimiento de Linux y registros personalizados
    - Si se produce un error en el comando anterior, ejecute el `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py` comando. Este comando fuerza el agente omsconfig para comunicarse con el servicio OMS para recuperar la configuración más reciente.


### <a name="custom-linux-log-data-does-not-appear-in-the-oms-portal"></a>Datos de registro personalizados Linux no aparece en el Portal de OMS

#### <a name="probable-causes"></a>Causas probables

- No se pudo integrado al servicio de OMS
- No se ha seleccionado la opción de **aplicar la siguiente configuración para los servidores Linux**
- no se recogen omsconfig el registro personalizado más reciente desde el portal
- La `omsagent` uso no logra tener acceso el registro personalizado debido a un problema de permisos o `omsagent` no se encontró. En este caso, verá el siguiente resultado:
  - `[DATETIME] [warn]: file not found. Continuing without tailing it.`
  - `[DATETIME] [error]: file not accessible by omsagent.`
- Este es un problema conocido con la condición que se haya fijado en el agente de OMS para Linux versión 1.1.0-217

#### <a name="resolutions"></a>Resoluciones
- Compruebe que ha onboarded, correctamente, determine si la `/etc/opt/microsoft/omsagent/conf/omsadmin.conf` archivo existe.
  - Si fuera necesario, incorporado de nuevo con la línea de comandos omsadmin.sh. Para obtener más información, vea [integrado con la línea de comandos](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) .
- En el Portal de OMS, en **configuración** en la pestaña **datos** , asegúrese de que está seleccionada la opción **aplicar la siguiente configuración para los servidores Linux**  
  ![Aplicar configuración](./media/log-analytics-linux-agents/customloglinuxenabled.png)

- Compruebe que la `omsconfig` agente puede comunicarse con el servicio OMS
  - Ejecutar la `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` comando
  - El comando anterior devuelve la configuración que el agente recupera desde el Portal, incluida la configuración de registro del sistema, contadores de rendimiento de Linux y registros personalizados
  - Si se produce un error en el comando anterior, ejecute el `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py` comando. Este comando fuerza el agente omsconfig para comunicarse con el servicio OMS y recuperar la configuración más reciente.


En lugar del agente de OMS en ejecución como un usuario con privilegios de usuario de Linux `root`, se ejecuta el agente de OMS para Linux como el `omsagent` usuario. En la mayoría de los casos, se debe concedido permiso explícito al usuario para leer determinados archivos.

Para conceder permiso a `omsagent` usuario, ejecute los comandos siguientes:

1. Agregar la `omsagent` usuario a un grupo específico con`sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Conceder acceso universal de lectura para el archivo requerido con`sudo chmod -R ugo+rw <FILE DIRECTORY>`

Hay un problema conocido con la condición que se haya fijado en el agente de OMS para Linux versión 1.1.0-217. Después de actualizar a la última agente, ejecute el comando siguiente para obtener la última versión del complemento de salida:

```
sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/conf/omsagent.conf
```

## <a name="known-limitations"></a>Limitaciones conocidas
Revise las secciones siguientes para obtener información sobre las limitaciones actuales del agente OMS para Linux.

### <a name="azure-diagnostics"></a>Diagnóstico de Azure

Para máquinas virtuales de Linux ejecuta en Azure, pueden ser necesario para permitir la recopilación de datos pasos adicionales diagnósticos de Azure y conjunto de aplicaciones de administración de operaciones. **Versión 2.2** de la extensión de diagnóstico para Linux es necesario para la compatibilidad con el agente de OMS para Linux.

Para obtener más información sobre cómo instalar y configurar la extensión de diagnóstico para Linux, consulte [usar el comando de Azure CLI para habilitar la extensión de diagnóstico Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md#use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension).

**Actualizar la extensión Linux diagnósticos de 2.0 a 2.2 ASM CLI Azure:**

```
azure vm extension set -u <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.0
azure vm extension set <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

**ARM**

```
azure vm extension set -u <resource-group> <vm-name> Microsoft.Insights.VMDiagnosticsSettings Microsoft.OSTCExtensions 2.0
azure vm extension set <resource-group> <vm-name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

Estos ejemplos de comando hacer referencia a un archivo llamado PrivateConfig.json. El formato de archivo debería ser similar en el ejemplo siguiente.

```
    {
    "storageAccountName":"the storage account to receive data",
    "storageAccountKey":"the key of the account"
    }
```

### <a name="sysklog-is-not-supported"></a>No se admite Sysklog

Rsyslog o ng de registro del sistema se requieren para recopilar mensajes de registro del sistema. No se admite el daemon de registro del sistema de forma predeterminada en la versión 5 de Red sombrero Enterprise Linux, CentOS y Oracle Linux (sysklog) para la colección de eventos de registro del sistema. Para recopilar datos de registro del sistema de esta versión de estas distribuciones, el daemon rsyslog debe estar instalado y configurado para reemplazar sysklog. Para obtener más información acerca de cómo sustituir sysklog con rsyslog, consulte [instalar el rsyslog recién creado RPM](http://wiki.rsyslog.com/index.php/Rsyslog_on_CentOS_success_story#Install_the_newly_built_rsyslog_RPM).

## <a name="next-steps"></a>Pasos siguientes

- [Soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md) para agregar funcionalidad y recopilación de datos.
- Familiarizarse con [las búsquedas de registro](log-analytics-log-searches.md) ver información detallada recopilada por soluciones.
- Use los [paneles](log-analytics-dashboards.md) para guardar y mostrar sus propias búsquedas personalizadas.
