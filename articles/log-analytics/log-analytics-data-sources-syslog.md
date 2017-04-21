<properties 
   pageTitle="Mensajes de registro del sistema de análisis de registro | Microsoft Azure"
   description="Registro del sistema es un protocolo de registro de eventos común a Linux.   En este artículo se describe cómo configurar la colección de mensajes de registro del sistema de análisis de registro y detalles de los registros que se crean en el repositorio OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="bwren" />


# <a name="syslog-data-sources-in-log-analytics"></a>Orígenes de datos de registro del sistema de análisis de registro

Registro del sistema es un protocolo de registro de eventos común a Linux.  Aplicaciones enviará mensajes que se han almacenado en el equipo local o entrega en un selector de registro del sistema.  Cuando esté instalado el agente de OMS para Linux, configura el daemon de registro del sistema local para reenviar mensajes al agente.  El agente, a continuación, envía el mensaje a donde se crea un registro correspondiente en el repositorio OMS de análisis de registro.  

> [AZURE.NOTE]Análisis de registro es compatible con la colección de mensajes enviados por rsyslog o ng de registro del sistema. No se admite el daemon de registro del sistema de forma predeterminada en la versión 5 de Red sombrero Enterprise Linux, CentOS y Oracle Linux (sysklog) para la colección de eventos de registro del sistema. Para recopilar datos de registro del sistema de esta versión de estas distribuciones, el [daemon rsyslog](http://rsyslog.com) debe estar instalado y configurado para reemplazar sysklog.

![Colección de registro del sistema](media/log-analytics-data-sources-syslog/overview.png)


## <a name="configuring-syslog"></a>Configurar el registro del sistema
El agente de OMS para Linux sólo recopilará eventos con las instalaciones y gravedad que se especifica en su configuración.  Puede configurar el registro del sistema a través del portal OMS o administración de archivos de configuración de los agentes de Linux.


### <a name="configure-syslog-in-the-oms-portal"></a>Configurar el registro del sistema en el portal OMS

Configurar el registro del sistema en el [menú datos de configuración de análisis de registro](log-analytics-data-sources.md#configuring-data-sources).  Esta configuración se ha entregado al archivo de configuración en cada agente Linux.

Puede agregar una nueva función escribiendo su nombre y haciendo clic en **+**.  Para cada función, se recopilará solo los mensajes con la gravedad seleccionado.  Compruebe la gravedad para el equipamiento que se desea recopilar.  No puede proporcionar los criterios para filtrar los mensajes adicionales.

![Configurar el registro del sistema](media/log-analytics-data-sources-syslog/configure.png)


De forma predeterminada, todos los cambios de configuración se insertan automáticamente a todos los agentes.  Si desea configurar el registro del sistema de forma manual en cada agente Linux, a continuación, desactive la casilla *Aplicar debajo de configuración de mis equipos Linux*.


### <a name="configure-syslog-on-linux-agent"></a>Configurar el registro del sistema en el agente de Linux

[Agente OMS se instala en un cliente Linux](log-analytics-linux-agents.md), cuando se instala un archivo de configuración de registro del sistema predeterminado que define la gravedad de los mensajes que se recopilan y la instalación.  Puede modificar este archivo para cambiar la configuración.  El archivo de configuración es diferente según el daemon de registro del sistema que ha instalado el cliente.

> [AZURE.NOTE] Si modifica la configuración de registro del sistema, debe reiniciar el daemon de registro del sistema para que los cambios surtan efecto.

#### <a name="rsyslog"></a>rsyslog

El archivo de configuración para rsyslog se encuentra en **/etc/rsyslog.d/95-omsagent.conf**.  A continuación se muestra el contenido predeterminado.  Recopila los mensajes de registro del sistema enviados desde el agente local para todas las instalaciones con un nivel de advertencia o superior.

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

Puede quitar una instalación quitando la sección del archivo de configuración.  Puede limitar la gravedad que se recopila para un equipamiento modificando la entrada de esa función.  Por ejemplo, para limitar la función de usuario a los mensajes con una gravedad de error o superior puede modificar dicha línea del archivo de configuración a la siguiente:

    user.error  @127.0.0.1:25224


#### <a name="syslog-ng"></a>registro del sistema ng

El archivo de configuración para rsyslog es la ubicación en **/etc/syslog-ng/syslog-ng.conf**.  A continuación se muestra el contenido predeterminado.  Recopila los mensajes de registro del sistema enviados desde el agente local para todas las instalaciones y todos los niveles de gravedad.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };
    
    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };
    
    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };
    
    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };
    
    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };
    
    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };
    
    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

Puede quitar una instalación quitando la sección del archivo de configuración.  Puede limitar la gravedad que se recopila para un equipamiento quitándolas su lista.  Por ejemplo, para limitar la función de usuario a los mensajes solo alerta y críticas, puede modificar esa sección del archivo de configuración a la siguiente:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="changing-the-syslog-port"></a>Cambiar el puerto de registro del sistema

El agente OMS escucha los mensajes de registro del sistema en el cliente local en el puerto 25224.  Puede cambiar este puerto agregando la siguiente sección para el archivo de configuración de agente OMS ubicado en **/etc/opt/microsoft/omsagent/conf/omsagent.conf**.  Reemplace 25224 en la entrada de **puerto** con el número de puerto que desee.  Tenga en cuenta que también debe modificar el archivo de configuración para el daemon de registro del sistema enviar mensajes a este puerto.

    <source>
      type syslog
      port 25224
      bind 127.0.0.1
      protocol_type udp
      tag oms.syslog
    </source>


## <a name="data-collection"></a>Recopilación de datos

El agente OMS escucha los mensajes de registro del sistema en el cliente local en el puerto 25224. El archivo de configuración para el registro del sistema de daemon reenvía mensajes de registro del sistema de aplicación a este puerto donde se recopilan por el análisis de registro.


## <a name="syslog-record-properties"></a>Propiedades del registro de registro del sistema

Registros de registro del sistema tienen un tipo de **registro del sistema** y las propiedades en la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| Equipo | Equipo que se ha recopilado el evento. |
| Instalaciones | Define el elemento del sistema que generó el mensaje. |
| HostIP | Dirección IP del sistema a enviar el mensaje.  |
| Nombre de host | Nombre del sistema a enviar el mensaje. |
| NivelDeGravedad | Nivel de gravedad del evento. |
| SyslogMessage | Texto del mensaje. |
| ID de proceso | Id. del proceso que generó el mensaje. |
| EventTime | Fecha y hora en que se generó el evento.



## <a name="log-queries-with-syslog-records"></a>Consultas de registro con los registros del registro del sistema

La siguiente tabla proporciona ejemplos diferentes de consultas de registro que recuperar registros de registro del sistema.

| Consulta | Descripción |
|:--|:--|
| Tipo = registro del sistema | Todos los Syslogs. |
| Tipo = Syslog SeverityLevel = error | Todos los registros del registro del sistema con gravedad del error. |
| Tipo = registro del sistema & #124; medir count() por equipo | Número de registro del sistema los registros de equipo. |
| Tipo = registro del sistema & #124; medir count() por las instalaciones | Número de registro del sistema los registros de las instalaciones. |

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de las [búsquedas de registro](log-analytics-log-searches.md) analizar los datos recopilados desde orígenes de datos y soluciones. 
- Usar [Campos personalizados](log-analytics-custom-fields.md) para analizar datos de registros de registro del sistema en campos individuales.
- [Configurar Linux agentes](log-analytics-linux-agents.md) para recopilar otros tipos de datos. 
