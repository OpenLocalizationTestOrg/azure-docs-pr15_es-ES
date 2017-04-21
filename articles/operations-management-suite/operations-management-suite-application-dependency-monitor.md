<properties
   pageTitle="Monitor de dependencia de aplicaciones (ADM) en el conjunto de aplicaciones de administración de operaciones (OMS) | Microsoft Azure"
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

# <a name="using-application-dependency-monitor-solution-in-operations-management-suite-oms"></a>Utilizar solución Monitor de dependencia de aplicaciones en conjunto de aplicaciones de administración de operaciones (OMS)
![Icono de alerta de administración](media/operations-management-suite-application-dependency-monitor/icon.png) Monitor de dependencia de aplicación (ADM) automáticamente descubre los componentes de la aplicación en los sistemas de Windows y Linux y asigna la comunicación entre los servicios. Permite ver los servidores como pensar que sistemas interconectados que ofrecen servicios críticos de:.  Monitor de dependencia de aplicaciones muestra las conexiones entre servidores, procesos, y puertos a través de cualquier arquitectura TCP conectado con ninguna configuración necesarios excepto la instalación de un agente.

En este artículo se describe los detalles del Monitor de dependencia de aplicaciones de uso.  Para obtener información sobre cómo configurar agentes de ADM e integrado, consulte [solución de configurar el Monitor de dependencia de aplicaciones en conjunto de aplicaciones de administración de operaciones (OMS)](operations-management-suite-application-dependency-monitor-configure.md)

>[AZURE.NOTE]Monitor de dependencia de la aplicación está en vista previa privado.  Puede solicitar acceso a la vista previa privada de ADM en [https://aka.ms/getadm](https://aka.ms/getadm).
>
>Durante la vista previa de privado, todas las cuentas OMS tienen acceso ilimitado a ADM  Nodos ADM son gratuitos, pero los datos de análisis de registro para los tipos de AdmComputer_CL y AdmProcess_CL se va a medir como cualquier otra solución.
>
>Después de ADM entra en versión preliminar pública, estará disponible únicamente para clientes gratuitos y de pagados de conocimientos y análisis en el Plan de precios de OMS.  Cuentas de nivel gratuita se limitarán a 5 nodos ADM.  Si está participando en la vista previa privada y no inscrito en el Plan de precios de OMS cuando ADM entra en versión preliminar pública, ADM deshabilitará en ese momento. 


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Casos de uso: Realice su TI procesa conscientes de dependencia

### <a name="discovery"></a>Detección
ADM crea automáticamente un mapa de referencia comunes de dependencias entre servidores, procesos y servicios de terceros 3ª.  Descubre y asigna todas las dependencias TCP, identificar conexiones sorpresa, remoto 3ª sistemas de proveedores dependen de y las dependencias entre las áreas oscuras tradicionales de su red como DNS y AD.  ADM descubre conexiones de red ha fallado que los sistemas administrados están intentando hacer, ayudarle a identificar los posibles errores de configuración de servidor, interrupciones del servicio y problemas de red.

### <a name="incident-management"></a>Administración de incidencias
ADM ayuda a eliminar las suposiciones de aislamiento de problemas, que muestra cómo se conectan los sistemas y que ello afecte entre sí.  Además de conexiones errores, información sobre los clientes conectados ayudan a identificar equilibradores de carga mal configurados, carga sorprendente o excesivo en servicios críticos y no fiables clientes como máquinas programador hablando con sistemas de producción.  Flujos de trabajo integrados con cambiar seguimiento OMS también le permite ver si un evento de cambio en un equipo de back-end o servicio explica la causa de un incidente.

### <a name="migration-assurance"></a>Garantía de migración
ADM permite planear eficazmente, acelerar y validar migraciones Azure, asegurar que nada queda y no hay interrupciones sorpresa.  Puede detectar todos los sistemas interdependientes que necesitan para migrar juntas, evaluar la capacidad y configuración del sistema e identificar si un sistema en ejecución aún está atendiendo usuarios o es un candidato para retirar en lugar de migración.  Cuando haya terminado el movimiento, puede comprobar identidad para verificar que se conectan los clientes y sistemas de prueba y la carga del cliente.  Si las definiciones de planeación y el firewall de subred tienen problemas, conexiones de error en mapas ADM le indicará los sistemas que necesitan conectividad.

### <a name="business-continuity"></a>Continuidad empresarial
Si está utilizando la recuperación de sitios de Azure y necesita ayuda para definir la secuencia de recuperación para su entorno de aplicación, ADM puede automáticamente mostrarle cómo sistemas dependen de ellos para asegurarse de que su plan de recuperación es confiable.  Al elegir un servidor crítico y ver a sus clientes, puede identificar los sistemas de front-end que deben recuperarse después de dicho servidor crítico es restaurada y está disponible.  Por el contrario, consultando las dependencias de back-end de un servidor críticas, puede identificar los sistemas que deben ser recuperados antes de que se restaura el sistema de foco.

### <a name="patch-management"></a>Administración de revisiones
ADM mejora el uso de la evaluación de actualización de sistema OMS, que muestra que otros equipos y servidores dependen de su servicio, por lo que puede notificarles previamente antes de realizar los sistemas hacia abajo para la revisión.  ADM también mejora la administración de revisiones en OMS, que muestra si los servicios están disponibles y conectado correctamente después de revisar y reiniciar. 


## <a name="mapping-overview"></a>Información general de asignación
Agentes ADM recopilan información acerca de todos los procesos de TCP conectado en el servidor donde están instalados, así como detalles acerca de las conexiones entrantes y salientes para cada proceso.  En la lista de equipo en el lado izquierdo de la solución ADM, equipos con agentes ADM pueden seleccionarse para visualizar las dependencias entre un intervalo de tiempo seleccionado.  Dependencia de máquina asigna el foco en un equipo concreto y mostrar todos los equipos que son clientes TCP directos o servidores de ese equipo.

![Información general ADM](media/operations-management-suite-application-dependency-monitor/adm-overview.png)

Máquinas se pueden expandir en el mapa para mostrar los procesos con conexiones de red activas durante el intervalo de tiempo seleccionado.  Cuando un equipo remoto con un agente ADM se expande para mostrar detalles de proceso, se muestran únicamente los procesos comunicarse con el equipo de foco.  El recuento de equipos de cliente sin agente conectarse al equipo de foco se indica en el lado izquierdo de los procesos que se conectan a.  Si el equipo de foco está realizando una conexión a un equipo de back-end sin un agente que back-end se representa con un nodo en el mapa que muestra su dirección IPv4 y el nodo se puede expandir para mostrar los puertos individuales y los servicios que el equipo de foco se comunica con.

De forma predeterminada, mapas ADM mostrar los últimos 10 minutos de la información de dependencia.  Mediante los controles de tiempo en la esquina superior izquierda, mapas pueden consultar para intervalos de tiempo histórico, hasta para toda una hora, para mostrar cómo se buscan dependencias en el pasado, por ejemplo, durante un incidente o antes de que se ha producido un cambio.    Datos ADM se almacenan durante 30 días en áreas de trabajo de pagadas y durante 7 días en áreas de trabajo gratuitas.

![Mapa de máquina con las propiedades del equipo seleccionado](media/operations-management-suite-application-dependency-monitor/machine-map.png)

## <a name="failed-connections"></a>Error conexiones
Error conexiones se muestran en mapas ADM para procesos y equipos, con una línea roja discontinua que muestra una si falla un sistema de cliente llegar a un proceso o el puerto.  Error conexiones se informaron de cualquier sistema con un agente ADM distribuido si ese sistema es el único que intenta la conexión error.  ADM las medidas observando sockets TCP que no se pueden establecer una conexión.  Esto puede deberse a un firewall, un error de configuración en el cliente de servidor o un servicio remoto no está disponible. 

![Error conexiones](media/operations-management-suite-application-dependency-monitor/failed-connections.png)

Descripción de error conexiones pueden ayudar a la solución de problemas, validación de migración, análisis de seguridad y una descripción general de arquitectura.  A veces no se pudo las conexiones responsabilidad, sino que apuntan a menudo directamente a un problema, como un entorno de migración tras error repente convertirse en inaccesible,.. o dos niveles de la aplicación no poder hablar después de una migración a la nube.  En la imagen anterior, IIS y WebSphere se están ejecutando, pero no pueden conectarse. 

## <a name="computer-and-process-properties"></a>Propiedades de proceso y de equipo
Al navegar por un mapa de ADM, puede seleccionar equipos y procesos para obtener contexto adicional sobre sus propiedades.  Máquinas proporcionan información sobre el nombre DNS, direcciones IPv4, capacidad de CPU y memoria, tipo de máquina virtual, versión del sistema operativo, reinicie de última hora y los identificadores de sus agentes OMS y ADM.

Se recopilan los detalles del proceso de sistema operativo metadatos acerca de la ejecución de procesos, incluido el nombre del proceso, la descripción de proceso, nombre de usuario y dominio (en Windows), nombre de la compañía, nombre de producto, versión del producto, directorio de trabajo, línea de comandos y hora de inicio del proceso.

![Propiedades del proceso](media/operations-management-suite-application-dependency-monitor/process-properties.png)

El panel Resumen de procesos proporciona información adicional sobre conectividad de ese proceso, incluida su dependientes puertos, conexiones entrantes y salientes y no pudo conexiones. 

![Resumen del proceso](media/operations-management-suite-application-dependency-monitor/process-summary.png)

## <a name="oms-change-tracking-integration"></a>Integración de seguimiento de cambios de OMS
Integración de ADM con seguimiento de cambios es automática cuando están habilitados y configurados en el área de trabajo OMS dos soluciones.

El Panel de resumen de máquina indica si se han producido eventos de seguimiento de cambios en el equipo seleccionado durante el intervalo de tiempo seleccionado.

![Panel de resumen de equipo](media/operations-management-suite-application-dependency-monitor/machine-summary.png)

El Panel de seguimiento de cambio de máquina muestra una lista de todos los cambios, con la más reciente en primer lugar, junto con un vínculo para explorar en profundidad de la búsqueda de registro para obtener más información.
![Panel de seguimiento de cambio de máquina](media/operations-management-suite-application-dependency-monitor/machine-change-tracking.png)

A continuación se muestra una vista de evento de cambio de configuración de resúmenes después de seleccionar **Mostrar en el análisis de registro**.
![Evento de cambio de configuración](media/operations-management-suite-application-dependency-monitor/configuration-change-event.png)


## <a name="log-analytics-records"></a>Análisis de registros
Datos de inventario de equipo y el proceso de ADM están disponibles para la [búsqueda](../log-analytics/log-analytics-log-searches.md) en el análisis de registro.  Esto se puede aplicar a escenarios incluidos planificación de migración, análisis de capacidad, detección y solución de problemas de rendimiento ad hoc. 

Un registro se genera por hora para cada equipo único y proceso además de registros cuando genera que el proceso o equipo empieza o es de-efectuar a ADM  Estos registros tienen las propiedades en las tablas siguientes. 

Hay propiedades generados internamente que puede usar para identificar los equipos y procesos únicos:

- PersistentKey_s exclusivos por la configuración de proceso, por ejemplo, línea de comandos e Id.  Es único para un equipo determinado, pero se puede repetir entre equipos.
- ProcessId_s y ComputerId_s son globalmente únicos en el modelo ADM.



### <a name="admcomputercl-records"></a>Registros de AdmComputer_CL
Registros con un tipo de **AdmComputer_CL** tienen datos de inventario de servidores con agentes ADM.  Estos registros tienen las propiedades de la tabla siguiente.  

| (Propiedad) | Descripción |
|:--|:--|
| Tipo | *AdmComputer_CL* |
| SourceSystem | *OpsManager* |
| ComputerName_s | Nombre del equipo Windows o Linux |
| CPUSpeed_d | Velocidad de CPU en MHz |
| DnsNames_s | Lista de todos los nombres DNS para este equipo |
| IPv4s_s | Lista de todas las direcciones IPv4 en este equipo |
| IPv6s_s | Lista de todas las direcciones IPv6 en este equipo.  (ADM identifica direcciones IPv6 pero descubrir dependencias de IPv6). |
| Is64Bit_b | True o false según el tipo de sistema operativo |
| MachineId_s | Un GUID interno, único en un área de trabajo OMS  |
| OperatingSystemFamily_s | Windows o Linux |
| OperatingSystemVersion_s | Cadena de versión de sistema operativo larga |
| TimeGenerated | Fecha y hora en que se creó el registro. |
| TotalCPUs_d | Número de núcleos de CPU |
| TotalPhysicalMemory_d | Capacidad de memoria en MB |
| VirtualMachine_b | True o false en función de si el sistema operativo es un Invitado VM |
| VirtualMachineID_g | Identificador de la máquina virtual Hyper-V |
| VirtualMachineName_g | Nombre de la máquina virtual Hyper-V |
| VirtualMachineType_s | HyperV, Vmware, Xen, Kvm, Ldom, Lpar, Virtual PC |


### <a name="admprocesscl-type-records"></a>Registros del tipo de AdmProcess_CL 
Registros con un tipo de **AdmProcess_CL** tienen datos de inventario de procesos conectado TCP en servidores con agentes ADM.  Estos registros tienen las propiedades de la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| Tipo | *AdmProcess_CL* |
| SourceSystem | *OpsManager* |
| CommandLine_s | Línea de comandos completa del proceso |
| CompanyName_s | Nombre de la compañía (desde Windows PE o Linux RPM) |
| Description_s | Descripción de proceso largo (desde Windows PE o Linux RPM) |
| FileVersion_s | Versión del archivo ejecutable (desde Windows PE, sólo Windows) |
| FirstPid_d | ID de proceso de sistema operativo |
| InternalName_s | Nombre de archivo ejecutable interno (desde Windows PE, sólo Windows) |
| MachineId_s | GUID interno único a través de un área de trabajo OMS  |
| Name_s | El nombre de archivo ejecutable del proceso |
| Path_s | Ruta de acceso del archivo ejecutable de proceso |
| PersistentKey_s | GUID interno único en este equipo |
| PoolId_d | Id. interno para la agregación de procesos en función de las líneas de comandos similares. |
| ProcessId_s | GUID interno único a través de un área de trabajo OMS  |
| ProductName_s | Cadena de nombre de producto (desde Windows PE o Linux RPM) |
| ProductVersion_s | Cadena de versión del producto (desde Windows PE o Linux RPM) |
| StartTime_t | Hora de inicio de proceso en el reloj del equipo local |
| TimeGenerated | Fecha y hora en que se creó el registro. |
| UserDomain_s | Dominio de propietario del proceso (sólo Windows) |
| UserName_s | Nombre del propietario del proceso (sólo Windows) |
| WorkingDirectory_s | Directorio de trabajo del proceso |


## <a name="sample-log-searches"></a>Búsquedas de registro de ejemplo

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>La capacidad de memoria física de todos los equipos administrados de la lista. 
Tipo = AdmComputer_CL | Seleccione TotalPhysicalMemory_d, ComputerName_s | Dedup ComputerName_s

![Ejemplo de consulta ADM](media/operations-management-suite-application-dependency-monitor/adm-example-01.png)

### <a name="list-computer-name-dns-ip-and-os-version"></a>Nombre del equipo de lista, DNS, IP y versión del SO.
Tipo = AdmComputer_CL | Seleccione ComputerName_s, OperatingSystemVersion_s, DnsNames_s, IPv4s_s | dedup ComputerName_s

![Ejemplo de consulta ADM](media/operations-management-suite-application-dependency-monitor/adm-example-02.png)

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Buscar todos los procesos con "sql" en la línea de comandos
Tipo = AdmProcess_CL CommandLine_s = \*sql\* | dedup ProcessId_s

![Ejemplo de consulta ADM](media/operations-management-suite-application-dependency-monitor/adm-example-03.png)

### <a name="after-viewing-event-data-for-given-process-use-its-machine-id-to-retrieve-the-computers-name"></a>Después de ver los datos del evento para procesar, utilice su ID de máquina para recuperar el nombre del equipo
Tipo = AdmComputer_CL "m!m-9bb187fa-e522-5f73-66d2-211164dc4e2b" | DISTINCT ComputerName_s

![Ejemplo de consulta ADM](media/operations-management-suite-application-dependency-monitor/adm-example-04.png)

### <a name="list-all-computers-running-sql"></a>Lista de todos los equipos que ejecutan SQL
Tipo = AdmComputer_CL MachineId_s IN {tipo = AdmProcess_CL \*sql\* | DISTINCT MachineId_s} | DISTINCT ComputerName_s

![Ejemplo de consulta ADM](media/operations-management-suite-application-dependency-monitor/adm-example-05.png)

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>Lista de todas las versiones de producto único de doblez en mi centro de datos
Tipo = AdmProcess_CL Name_s = doblez | DISTINCT ProductVersion_s

![Ejemplo de consulta ADM](media/operations-management-suite-application-dependency-monitor/adm-example-06.png)

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Crear un grupo de equipo de todos los equipos que ejecutan CentOS

![Ejemplo de consulta ADM](media/operations-management-suite-application-dependency-monitor/adm-example-07.png)



## <a name="diagnostic-and-usage-data"></a>Datos de diagnóstico y el uso
Microsoft recopila automáticamente los datos de uso y el rendimiento mediante el uso del servicio Monitor de dependencia de aplicaciones. Microsoft usa estos datos para proporcionar y mejorar la calidad, la seguridad y la integridad del servicio Monitor de dependencia de aplicaciones. Datos incluyen información sobre la configuración de su software como sistema operativo y versión y también incluyen la dirección IP, nombre DNS y nombre de estación de trabajo para proporcionar las capacidades de resolución precisas y eficaces. No recopilamos nombres, direcciones u otra información de contacto.

Para obtener más información sobre el uso y recopilación de datos, vea la [Declaración de privacidad de Microsoft Online Services](hhttps://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Pasos siguientes
- Más información sobre [las búsquedas de registro](../log-analytics/log-analytics-log-searches.md] in Log Analytics to retrieve data collected by Application Dependency Monitor.)
