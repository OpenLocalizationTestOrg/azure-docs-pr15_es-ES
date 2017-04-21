<properties
   pageTitle="Configurar el Monitor de dependencia de aplicaciones (ADM) en el conjunto de aplicaciones de administración de operaciones (OMS) | Microsoft Azure"
   description="Monitor de dependencia de aplicación (ADM) es una solución de conjunto de aplicaciones de administración de operaciones (OMS) que automáticamente descubre los componentes de la aplicación en los sistemas de Windows y Linux y le asigna la comunicación entre los servicios.  Este artículo proporciona detalles para implementar ADM en su entorno y utilizarlo en una gran variedad de escenarios."
   services="operations-management-suite"
   documentationCenter=""
   authors="daseidma"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/28/2016"
   ms.author="daseidma;bwren" />

# <a name="configuring-application-dependency-monitor-solution-in-operations-management-suite-oms"></a>Configuración de solución de aplicación Monitor de dependencia en conjunto de aplicaciones de administración de operaciones (OMS)
![Icono de alerta de administración](media/operations-management-suite-application-dependency-monitor/icon.png) Monitor de dependencia de aplicación (ADM) automáticamente descubre los componentes de la aplicación en los sistemas de Windows y Linux y asigna la comunicación entre los servicios. Permite ver los servidores como pensar que sistemas interconectados que ofrecen servicios críticos de:.  Monitor de dependencia de aplicaciones muestra las conexiones entre servidores, procesos, y puertos a través de cualquier arquitectura TCP conectado con ninguna configuración necesarios excepto la instalación de un agente.

En este artículo se describe los detalles de configuración de agentes de Monitor de dependencia de aplicaciones e integrado.  Para obtener información sobre el uso de ADM, consulte [solución de usar el Monitor de dependencia de aplicación en conjunto de aplicaciones de administración de operaciones (OMS)](operations-management-suite-application-dependency-monitor.md)

>[AZURE.NOTE]Monitor de dependencia de la aplicación está en vista previa privado.  Puede solicitar acceso a la vista previa privada de ADM en [https://aka.ms/getadm](https://aka.ms/getadm).
>
>Durante la vista previa de privado, todas las cuentas OMS tienen acceso ilimitado a ADM  Nodos ADM son gratuitos, pero los datos de análisis de registro para los tipos de AdmComputer_CL y AdmProcess_CL se va a medir como cualquier otra solución.
>
>Después de ADM entra en versión preliminar pública, estará disponible únicamente para clientes gratuitos y de pagados de conocimientos y análisis en el Plan de precios de OMS.  Cuentas de nivel gratuita se limitarán a 5 nodos ADM.  Si está participando en la vista previa privada y no inscrito en el Plan de precios de OMS cuando ADM entra en versión preliminar pública, ADM deshabilitará en ese momento. 



## <a name="connected-sources"></a>Orígenes conectados
La siguiente tabla describe las fuentes conectadas que son compatibles con la solución ADM.

| Origen conectada | Compatibles | Descripción |
|:--|:--|:--|
| [Agentes de Windows](../log-analytics/log-analytics-windows-agents.md) | Sí | ADM analiza y recopila los datos de equipos de agente de Windows.  <br><br>Observe que el agente OMS, además de agentes de Windows requieren al agente de dependencia de Microsoft.  Consulte los [sistemas operativos compatibles](#supported-operating-systems) para obtener una lista completa de versiones de sistema operativo. |
| [Agentes Linux](../log-analytics/log-analytics-linux-agents.md) | Sí | ADM analiza y recopila los datos de equipos de agente de Linux.  <br><br>Observe que el agente OMS, además de agentes Linux requieren al agente de dependencia de Microsoft.  Consulte los [sistemas operativos compatibles](#supported-operating-systems) para obtener una lista completa de versiones de sistema operativo. |
| [Grupo de administración de SCOM](../log-analytics/log-analytics-om-agents.md) | Sí | ADM analiza y recopila datos de Windows y Linux agentes de un grupo de administración de SCOM conectado. <br><br>Se requiere una conexión directa desde el equipo de agente SCOM a OMS. Datos se envían directamente desde reenviados desde el grupo de administración en el repositorio OMS.|
| [Cuenta de almacenamiento de Azure](../log-analytics/log-analytics-azure-storage.md) | No | ADM recopila los datos de equipos agente, lo que no hay ningún dato de recopilar de almacenamiento de Azure. |

Tenga en cuenta que ADM solo es compatible con plataformas de 64 bits.

En Windows, se utiliza el agente de supervisión de Microsoft (MMA) SCOM y OMS para recopilar y enviar datos de supervisión.  (Este agente se denomina el agente SCOM, OMS, MMA o agente directo, según el contexto.)  Proporcionar diferentes fuera de las versiones de cuadro de MMA SCOM y OMS, pero estas versiones pueden cada informe a SCOM, OMS o a ambas.  En Linux, el agente de OMS para Linux recopila y envía datos a OMS de supervisión.  Puede usar ADM en servidores con agentes de OMS directa o en los servidores que se adjuntan a OMS a través de grupos de administración de SCOM.  Con el fin de esta documentación se hará referencia a todos estos agentes, ya sea en Linux o Windows, si se conecta a un MG SCOM o directamente a OMS: como "Agente de OMS", a menos que el nombre de la implementación específica del agente es necesaria para el contexto.

El agente ADM no transmita los datos y no requiere los cambios en los firewalls o puertos.  Datos de ADM siempre se transmiten por el agente de OMS OMS, directamente o a través de la puerta de enlace de OMS.

![ADM agentes](media/operations-management-suite-application-dependency-monitor/agents.png)

Si es un cliente SCOM con un grupo de administración conectado a OMS:

- Si los agentes SCOM pueden tener acceso a internet para conectarse a OMS, no se requiere ninguna configuración adicional.  
- Si los agentes SCOM no pueden acceder a OMS por internet, debe configurar la puerta de enlace de OMS para trabajar con SCOM.
  
Si está utilizando al agente de directa de OMS, debe configurar el agente de OMS para conectarse a OMS o a la puerta de enlace de OMS.  La puerta de enlace de OMS puede descargarse desde [https://www.microsoft.com/en-us/download/details.aspx?id=52666](https://www.microsoft.com/en-us/download/details.aspx?id=52666)


### <a name="avoiding-duplicate-data"></a>Evitar el duplicado de datos

Si es un cliente SCOM, no debe configurar los agentes SCOM comunicarse directamente a OMS o datos se notificará dos veces.  En ADM, esto se traducirán en equipos que aparece dos veces en la lista de equipos.

Configuración de OMS ocurrirá en sólo una de las siguientes ubicaciones:

- El panel de la serie de administración de las operaciones de consola SCOM para equipos administrados
- Configuración de Azure perspectivas operativas en las propiedades MMA

Usar ambas configuraciones con la misma área de trabajo en cada provocará duplicación de datos. Usar ambas con diferentes áreas de trabajo puede dar lugar a configuración en conflicto (uno con solución ADM habilitado y la otra sin) que puede impedir que los datos fluya adm completamente.  

Tenga en cuenta que, incluso si no se especifica el propio equipo en una configuración de la consola de SCOM OMS, si un grupo de instancia como "Grupo de instancias de servidor de Windows" está activo, aún puede traducirse en la configuración de OMS receptora del equipo a través de SCOM.



## <a name="management-packs"></a>Paquetes de administración
Cuando se activa ADM en un área de trabajo OMS, un 300KB módulo de administración se envía a todos los Microsoft supervisión agentes de dicha área.  Si está utilizando a agentes SCOM en un [grupo de administración conectado](../log-analytics/log-analytics-om-agents.md), se implementará el módulo de administración de ADM de SCOM.  Si los agentes están conectados directamente, el panel de administración se entregarán por OMS.

El panel de administración se denomina Microsoft.IntelligencePacks.ApplicationDependencyMonitor*.  Se escribe en *%Programfiles%\Microsoft supervisión Agent\Agent\Health Service State\Management Packs\*.  El origen de datos usado por el módulo de administración es *% Program files%\Microsoft supervisión Agent\Agent\Health servicio State\Resources\<AutoGeneratedID > \Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*.



## <a name="configuration"></a>Configuración
Además de Windows y Linux equipos tienen un agente instalado y conectado a OMS, el programa de instalación de agente de dependencia debe descargarse de la solución ADM y, a continuación, se instala como raíz o administrador en cada servidor administrado.  Una vez instalado el agente ADM en un servidor de informes a OMS, mapas de dependencia ADM aparecerán en 10 minutos.  Si tiene problemas, envíe un correo electrónico [oms-adm-support@microsoft.com](mailto:oms-adm-support@microsoft.com).


### <a name="migrating-from-bluestripe-factfinder"></a>Migrar desde BlueStripe FactFinder
Monitor de dependencia de aplicación proporcionará BlueStripe tecnología en OMS en fases. FactFinder sigue siendo compatible con los clientes existentes, pero ya no está disponible para su compra individual.  Esta versión de vista previa del agente de dependencia solo puede comunicarse con OMS.  Si es un cliente actual de FactFinder, identifique un conjunto de servidores de prueba para ADM que no están administrados por FactFinder. 

### <a name="download-the-dependency-agent"></a>Descargue al agente de dependencia
Además del agente de administración de Microsoft (MMA) y OMS Linux agente que proporciona la conexión entre el equipo y OMS, todos los equipos analizados Monitor de dependencia de aplicaciones deben tener instalado el agente de dependencia.  En Linux, debe estar instalado el agente de OMS para Linux antes que el agente de dependencia. 

![Mosaico de aplicación Monitor de dependencia](media/operations-management-suite-application-dependency-monitor/tile.png)

Para descargar al agente de dependencia, haga clic en **Configurar la solución** en el icono de **Monitor de dependencia de aplicaciones** para abrir el módulo de **Agente de dependencia** .  El módulo de agente de dependencia tiene vínculos para las ventanas y los agentes de Linux. Haga clic en el vínculo correspondiente para descargar a cada agente. Vea las secciones siguientes para obtener más información sobre cómo instalar al agente en sistemas diferentes.

### <a name="install-the-dependency-agent"></a>Instalar al agente de dependencia

#### <a name="microsoft-windows"></a>Microsoft Windows
Se requieren privilegios de administrador para instalar o desinstalar al agente.

Está instalado el agente de dependencia en equipos con Windows con agente de ADM de Windows.exe. Si ejecuta este archivo ejecutable sin ninguna opción, se iniciará a un asistente que puede seguir para realizar la instalación de forma interactiva.  

Realice los pasos siguientes para instalar al agente de dependencia en cada equipo de Windows.

1.  Asegúrese de que está instalado el agente OMS siguiendo las instrucciones en equipos de conectar directamente a OMS.
2.  Descargue al agente de Windows y ejecutar con el siguiente comando.<br>*Agente de ADM de Windows.exe*
3.  Siga el Asistente para instalar al agente.
4.  Si no se puede iniciar el agente de dependencia, compruebe los registros para obtener información detallada del error. En los agentes de Windows, el directorio de registro es *C:\Program Files\BlueStripe\Collector\logs*. 

Un administrador a través del Panel de Control, se puede desinstalar el agente de dependencia para Windows.


#### <a name="linux"></a>Linux
Acceso raíz es necesario para instalar o configurar al agente.

Está instalado el agente de dependencia en equipos de Linux con ADM-agente-Linux64.bin, una secuencia de comandos de shell con un binario extracción automática. Puede ejecutar el archivo con sh o agregar permisos al propio archivo de ejecución.
 
Realice los pasos siguientes para instalar al agente de dependencia en cada equipo Linux.

1.  Asegúrese de que está instalado el agente OMS siguiendo las instrucciones en [recopilar y administrar datos de equipos Linux.  Debe instalarse antes que el agente de dependencia Linux](https://technet.microsoft.com/library/mt622052.aspx).
2.  Instalar al agente de dependencia Linux como raíz mediante el siguiente comando.<br>*sh agente de ADM de Linux64.bin*.
3.  Si no se puede iniciar el agente de dependencia, compruebe los registros para obtener información detallada del error. En los agentes de Linux, el directorio de registro es */var/opt/microsoft/dependency-agent/log*.

### <a name="uninstalling-the-dependency-agent-on-linux"></a>Desinstalar al agente de dependencia en Linux
Para desinstalar completamente el agente de dependencia de Linux, debe quitar el agente sí y el servidor proxy que se instala automáticamente con el agente.  Puede desinstalar ambos con el comando siguiente.

    rpm -e dependency-agent dependency-agent-connector


### <a name="installing-from-a-command-line"></a>Instalar desde una línea de comandos
La sección anterior proporciona instrucciones sobre cómo instalar al agente de supervisión de dependencia mediante las opciones predeterminadas.  Las siguientes secciones proporcionan instrucciones para instalar al agente desde una línea de comandos mediante opciones personalizadas.

#### <a name="windows"></a>Windows
Use las opciones de la tabla siguiente para realizar la instalación desde una línea de comandos. ¿Para ver una lista de la instalación de indicadores de ejecutan el programa de instalación con el /? Marcar como sigue.

    ADM-Agent-Windows.exe /?

| Marca | Descripción |
|:--|:--|
| /S | Realizar una instalación silenciosa sin preguntas. |

Archivos para el agente de dependencia de Windows se colocan en *C:\Program Files\BlueStripe\Collector* de forma predeterminada.


#### <a name="linux"></a>Linux
Use las opciones de la tabla siguiente para realizar la instalación. Para ver una lista de la instalación de indicadores ejecutan el programa de instalación con - ayuda marca como sigue.

    ADM-Agent-Linux64.bin -help

| Descripción del indicador
|:--|:--|
| -s | Realizar una instalación silenciosa sin preguntas. |
| : comprobar | Comprueba los permisos y el sistema operativo, pero no se instala al agente. |

Archivos para el agente de dependencia se colocan en los directorios siguientes.

| Archivos | Ubicación |
|:--|:--|
| Archivos principales | /usr/lib/bluestripe-Collector |
| Archivos de registro | /var/opt/Microsoft/Dependency-Agent/log |
| Archivos de configuración | /etc/opt/Microsoft/Dependency-Agent/config |
| Ejecutables de servicio | /sbin/bluestripe-Collector<br>/sbin/bluestripe-Collector-Manager |
| Archivos de almacenamiento binario | /var/opt/Microsoft/Dependency-Agent/Storage |



## <a name="troubleshooting"></a>Solución de problemas
Si tiene problemas con el Monitor de dependencia de aplicación, puede recopilar información de solución de problemas de varios componentes con la siguiente información.

### <a name="windows-agents"></a>Agentes de Windows

#### <a name="microsoft-dependency-agent"></a>Dependencia de Microsoft Agent
Para generar la solución de problemas de datos desde el agente de dependencia, abra un símbolo del sistema como administrador y ejecute la secuencia de comandos de CollectBluestripeData.vbs mediante el siguiente comando.  Puede agregar la--marca de ayuda para mostrar opciones adicionales.

    cd C:\Program Files\Bluestripe\Collector\scripts
    cscript CollectDependencyData.vbs

El paquete de compatibilidad de datos se guarda en el directorio % PERFILUSUARIO % para el usuario actual.  Puede usar el--archivo <filename> opción para guardar en una ubicación diferente.

#### <a name="microsoft-dependency-agent-management-pack-for-mma"></a>Módulo de administración de dependencia de Microsoft Agent para MMA
El módulo de administración de dependencia agente se ejecuta en el agente de administración de Microsoft.  Recibe datos desde el agente de dependencia y lo envía a través del servicio de nube ADM.
  
Compruebe que se ha descargado el módulo de administración realizando los siguientes pasos.

1.  Busque un archivo llamado Microsoft.IntelligencePacks.ApplicationDependencyMonitor.mp en C:\Program Files\Microsoft supervisión Agent\Agent\Health Service State\Management Packs.  
2.  Si el archivo no está presente y el agente está conectado a un grupo de administración de SCOM, compruebe que se ha importado en SCOM marcando los paquetes de administración, en el área de trabajo de administración de la consola de operaciones.

El panel de administración de ADM escribe eventos en el registro de eventos de Windows de Operations Manager.  El registro puede ser [buscado en OMS](../log-analytics/log-analytics-log-searches.md) a través de la solución de registro del sistema, que puede configurar para cargar los archivos de registro.  Si están habilitados los eventos de depuración, que se hayan escrito en el registro de eventos de la aplicación con el origen de evento *AdmProxy*.

#### <a name="microsoft-monitoring-agent"></a>Agente de supervisión de Microsoft
Recopilar seguimientos de diagnósticos, abra un símbolo del sistema como administrador y ejecute los comandos siguientes: 

    cd \Program Files\Microsoft Monitoring Agent\Agent\Tools
    net stop healthservice 
    StartTracing.cmd ERR
    net start healthservice

Trazas se escriben en c:\Windows\Logs\OpsMgrTrace.  Puede detener el seguimiento con StopTracing.cmd.


### <a name="linux-agents"></a>Agentes Linux

#### <a name="microsoft-dependency-agent"></a>Dependencia de Microsoft Agent
Para generar los datos de solución de problemas desde el agente de dependencia, inicie sesión con una cuenta que tenga privilegios sudo o raíz y ejecute el siguiente comando.  Puede agregar la--marca de ayuda para mostrar opciones adicionales.

    /usr/lib/bluestripe-collector/scripts/collect-dependency-agent-data.sh

El paquete de compatibilidad de datos se guardan en /var/opt/microsoft/dependency-agent/log (si raíz) en el directorio de instalación del agente, o para el directorio de inicio del usuario ejecutando el script (si no raíz).  Puede usar el--archivo <filename> opción para guardar en una ubicación diferente.

#### <a name="microsoft-dependency-agent-fluentd-plug-in-for-linux"></a>Dependencia de Microsoft Agent Fluentd complemento para Linux
El complemento de Fluentd agente de dependencia se ejecuta en el agente de OMS Linux.  Recibe datos desde el agente de dependencia y lo envía a través del servicio de nube ADM.  

Los registros se escriben en los dos archivos siguientes.

- /var/opt/Microsoft/omsagent/log/omsagent.log
- /var/log/messages

#### <a name="oms-agent-for-linux"></a>Agente OMS para Linux
Un recurso de solución de problemas para conectar servidores Linux con OMS pueden encontrarse aquí: [https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md) 

Los registros para el agente de OMS para Linux se encuentran en */var/participar/microsoft/omsagent/log/*.  

Los registros de omsconfig (configuración de agente) se encuentran en */var/participar/microsoft/omsconfig/log/*.
 
El registro de los componentes OMI y SCX que proporcionan datos de métricas de rendimiento se encuentran en */var/participar/omi/log/* y */var/opt/microsoft/scx/log*.


## <a name="data-collection"></a>Recopilación de datos
Puede esperar cada agente para transmitir aproximadamente 25MB por día, dependiendo de cómo de compleja son de las dependencias del sistema.  Datos de dependencia ADM se envían por cada agente cada 15 segundos.  

Normalmente, el agente de ADM consume 0,1% de memoria del sistema y 0,1% de CPU del sistema.


## <a name="supported-operating-systems"></a>Sistemas operativos compatibles
Las secciones siguientes enumeran los sistemas operativos compatibles del agente de dependencia.   arquitecturas de 32 bits no son compatibles para ningún sistema operativo.

### <a name="windows-server"></a>Windows Server
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Escritorio de Windows
- Nota: Windows 10 aún no se admite
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Rojos sombrero Enterprise Linux, CentOS Linux y Oracle Linux (con núcleo RHEL)
- Solo predeterminado y las versiones de kernel Linux SMP son compatibles.
- Versiones de kernel no estándar, como PAE y Xen, no se admiten para cualquier distribución de Linux. Por ejemplo, un sistema con la cadena de lanzamiento de "2.6.16.21-0.8-xen" no es compatible.
- Kernels personalizados, incluidos recompilaciones de kernels estándares, no son compatibles
- Núcleo CentOS Plus no es compatible.
- Oracle Unbreakable Kernel (UEK) está cubierto en otra sección siguiente.


#### <a name="red-hat-linux-7"></a>Rojos sombrero Linux 7
| Versión del SO | Versión de kernel |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |

#### <a name="red-hat-linux-6"></a>Rojos sombrero Linux 6
| Versión del SO | Versión de kernel |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6,7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Rojos sombrero Linux 5
| Versión del SO | Versión de kernel |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5,9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411 |

#### <a name="oracle-enterprise-linux-w-unbreakable-kernel-uek"></a>Oracle Enterprise Linux con núcleo Unbreakable (UEK)

#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Versión del SO | Versión de kernel
|:--|:--|
| 6.2 | 2.6.32-300 de Oracle (UEK R1) |
| 6.3 | 2.6.39-200 de Oracle (UEK R2) |
| 6.4 | 2.6.39-400 de Oracle (UEK R2) |
| 6.5 | 2.6.39-400 de Oracle (UEK R2 i386) |
| 6.6 | 2.6.39-400 de Oracle (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Versión del SO | Versión de kernel
|:--|:--|
| 5.8 | 2.6.32-300 de Oracle (UEK R1) |
| 5,9 | 2.6.39-300 de Oracle (UEK R2) |
| 5.10 | 2.6.39-400 de Oracle (UEK R2) |
| 5.11 | 2.6.39-400 de Oracle (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Versión del SO | Versión de kernel
|:--|:--|
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| SP2 11 | 3.0.13 |
| SP3 11 | 3.0.76 |
| 11 DE SERVICE PACK 4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10
| Versión del SO | Versión de kernel
|:--|:--|
| 10 SERVICE PACK 4 | 2.6.16.60 |

## <a name="diagnostic-and-usage-data"></a>Datos de diagnóstico y el uso
Microsoft recopila automáticamente los datos de uso y el rendimiento mediante el uso del servicio Monitor de dependencia de aplicaciones. Microsoft usa estos datos para proporcionar y mejorar la calidad, la seguridad y la integridad del servicio Monitor de dependencia de aplicaciones. Datos incluyen información sobre la configuración de su software como sistema operativo y versión y también incluyen la dirección IP, nombre DNS y nombre de estación de trabajo para proporcionar las capacidades de resolución precisas y eficaces. No recopilamos nombres, direcciones u otra información de contacto.

Para obtener más información sobre el uso y recopilación de datos, vea la [Declaración de privacidad de Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Pasos siguientes
- Obtenga información sobre cómo [utilizar el Monitor de dependencia de aplicación](operations-management-suite-application-dependency-monitor.md) una vez se ha implementado y configurado.
