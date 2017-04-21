<properties
    pageTitle="Registro de seguridad de datos de análisis | Microsoft Azure"
    description="Obtenga información sobre cómo el análisis de registro protege su privacidad y protege los datos."
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
    ms.date="09/23/2016"
    ms.author="banders"/>

# <a name="log-analytics-data-security"></a>Registro de seguridad de análisis de datos

Microsoft se compromete a proteger su privacidad y seguridad de los datos, cuando se realiza software y servicios que ayudan a administrar la infraestructura de TI de la organización. Se reconoce cuando confiar sus datos con otros, esa confianza requiere seguridad rigurosa. Microsoft se adhiere a las directrices de seguridad y cumplimiento estrictas: desde la codificación a un servicio de funcionamiento.

Seguridad y protección de datos son una prioridad máxima de Microsoft. Póngase en contacto con nosotros si tiene preguntas, sugerencias o problemas acerca de la información siguiente, incluido nuestras directivas de seguridad en [Opciones de soporte técnico de Azure](http://azure.microsoft.com/support/options/).

En este artículo se explica cómo datos es recopiladas, procesadas y protegidos mediante el análisis de registro en el conjunto de administración de operaciones (OMS). Puede usar a agentes para conectarse al servicio web, use System Center Operations Manager para recopilar datos operativos o recuperar datos de diagnóstico de Azure para el uso de análisis de registro. Los datos recopilados se envían a través de Internet mediante la autenticación basada en certificados y 3 de SSL para el servicio de análisis de registro que está alojado en Microsoft Azure. Datos se comprimen el agente antes de enviarlo.

El servicio de análisis de registro administra los datos en la nube de forma segura mediante los métodos siguientes:

- separación de datos
- retención de datos
- seguridad física
- administración de incidencias
- cumplimiento
- certificados de estándares de seguridad


## <a name="data-segregation"></a>Separación de datos

Datos del cliente se mantienen lógicamente independientes en cada componente en todo el servicio OMS. Etiquetado todos los datos por la organización. Este etiquetado continúa durante el ciclo de vida de datos y, a continuación, se aplica en cada nivel del servicio. Cada cliente tiene un blobs de Windows Azure dedicada que contiene los datos a largo plazo

## <a name="data-retention"></a>Retención de datos

Datos de búsqueda de registro indizados se almacena y se conservan según su plan de precio. Para obtener más información, vea [Registro de análisis de precios](https://azure.microsoft.com/pricing/details/log-analytics/).

Microsoft elimina datos del cliente de 30 días después de cerrar el área de trabajo OMS. Microsoft también elimina la cuenta de almacenamiento de Azure donde residen los datos. Cuando se eliminan datos del cliente, no se eliminan unidades físicas.

La siguiente tabla enumeran algunas de las soluciones disponibles en OMS y ejemplos de los tipos de datos que recopilan.

| **Solución** | **Tipos de datos** |
| --- | --- |
| Evaluación de configuración | Datos de configuración de metadatos y datos de estado |
| Planificación de capacidad | Datos de rendimiento y metadatos |
| Antimalware | Metadatos y datos de configuración |
| Evaluación de actualización de sistema | Datos de estado y metadatos |
| Administración de registros | Definidas por el usuario registros de eventos, registros de eventos de Windows o registros de IIS |
| Seguimiento de cambios | Inventario de software y los metadatos del servicio de Windows |
| Evaluación de Active Directory y SQL | Datos WMI, los datos del registro, los datos de rendimiento y administración dinámica de SQL Server ver resultados |

La siguiente tabla muestran ejemplos de tipos de datos:

| **Tipo de datos** | **Campos** |
| --- | --- |
| Alertas | Alerta nombre, descripción de la alerta, BaseManagedEntityId, Id. del problema, IsMonitorAlert, identificador de regla, ResolutionState, prioridad, gravedad, categoría, propietario, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configuración | IdCliente, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, ValorActual, ChangeDate |
| Evento | IdEvento, EventOriginalID, BaseManagedEntityInternalId, identificador de regla, PublisherId, PublisherName, FullNumber, número, categoría, ChannelLevel, LoggingComputer, EventData, objetos EventParameter, TimeGenerated, TimeAdded <br>**Nota:** Cuando inicie eventos con campos personalizados en el registro de eventos de Windows, OMS recopila ellos. |
| Metadatos | BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, dirección IP, ForestDNSName, NombreDeEquipoNetBIOS, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, dirección IP, NetBIOSDomainName., LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Rendimiento | ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Estado | StateChangeEventId, el identificador, NewHealthState, OldHealthState, contexto, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Seguridad física

El análisis de registro en el servicio OMS es atendido por personal de Microsoft y se registran todas las actividades y se pueden auditar. El servicio se ejecuta completamente en Azure y cumple los criterios de ingeniería comunes de Azure. Puede ver detalles sobre la seguridad física de los activos de Azure en la página 18 de la [Información general de seguridad de Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Se cambien los derechos de acceso físico para proteger áreas dentro de un día laborable para todos los usuarios que ya no tiene responsabilidad al servicio de OMS, incluidos transferencia y finalización. Puede leer sobre la infraestructura física global que usamos en [Centros de datos de Microsoft](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Administración de incidencias

OMS tiene un proceso de administración de incidencias que cumplen todos los servicios de Microsoft. En resumen, hemos:

- Usar un modelo de responsabilidad compartida que una parte de la responsabilidad de seguridad pertenece Microsoft mientras alguna parte pertenece al cliente
- Administrar las incidencias de seguridad de Azure
  - Detectar un incidente en la primera indicación para iniciar una investigación
  - Evaluar el impacto y la gravedad de un incidente por un miembro del equipo de respuesta a incidencias de llamada. En función de prueba, la evaluación puede o no puede traducirse en más escalado al equipo de respuesta de seguridad.
  - Diagnosticar un incidente por expertos de respuesta de seguridad para realizar la investigación técnica o detallada, identificar estrategias de contenido, mitigación y solución. Si el equipo de seguridad se considera que los datos del cliente pueden convertirse en expuesto a una persona ilegal o no autorizada, ejecución en paralelo del proceso de notificación de cliente incidente comienza en paralelo.  
  - Recuperar desde el incidente y estabilizar. El equipo de respuesta a incidencias crea un plan de recuperación para mitigar el problema. Pasos de contenido de crisis como cuarentena sistemas afectados pueden producirse inmediatamente y en paralelo con diagnóstico. Pueden planeadas más reducciones de términos que se produzcan una vez haya pasado el riesgo inmediato.  
  - Cierre el incidente y realizar un análisis detallado. El equipo de respuesta a incidencias crea un análisis detallado que describe los detalles del incidente, con la intención de revisar directivas, procedimientos y procesos para evitar que se reproduzca en un evento.
- Notificar a los clientes de incidencias de seguridad
  - Determinar el ámbito de clientes afectados y proporcionar cualquiera que se ve afectada tan detallado un aviso como sea posible
  - Crear un aviso para proporcionar los clientes con detallan suficiente información para que puedan realizar una investigación en su finalización y cumplir con los compromisos que hayan hecho a sus usuarios finales mientras no indebidamente retrasar el proceso de notificación.
  - Confirmar y declare el incidente, según sea necesario.
  - Notificar a los clientes con una notificación de incidencias sin demora excesiva y de acuerdo con cualquier compromiso legal o contractual. Notificación de incidencias de seguridad se entregarán a uno o varios de los administradores de un cliente por cualquier medio que selecciona de Microsoft, incluido el correo electrónico.
- Realizar formación y preparación de grupo
  - Personal de Microsoft es necesario para completar la seguridad y concienciación que les ayuda a identificar e informar de problemas de seguridad posibles.  
  - Operadores que trabajan en el servicio de Microsoft Azure tienen obligaciones de formación de adición adyacente su acceso a sistemas confidenciales hospedaje datos del cliente.
  - Personal de respuesta de seguridad de Microsoft recibe formación especializada para sus funciones


En caso de pérdida de datos de cualquier cliente, se le notifique a cada cliente dentro de un día. Sin embargo, nunca se ha producido pérdida de datos de clientes con OMS. Además, se mantienen copias de los datos que se crean y geográfico se distribuye.

Para obtener más información sobre cómo Microsoft responde a las incidencias de seguridad, consulte [Respuesta de seguridad de Microsoft Azure en la nube](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf).

## <a name="compliance"></a>Cumplimiento

Programa de seguridad y control de la información de la OMS software desarrollo y servicio del equipo es compatible con los requisitos de su empresa y cumple las leyes y normativas, como se describe en el [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/) y [Cumplimiento del centro de confianza de Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx). ¿Cómo OMS establece los requisitos de seguridad identifica los controles de seguridad, administra y supervisa los riesgos también se describen allí. Anualmente, realizamos una revisión de directivas, estándares, procedimientos y directrices.

Cada miembro del equipo de desarrollo de OMS recibe formación de seguridad de aplicación formal. Internamente, usamos un sistema de control de versiones para el desarrollo de software. Cada proyecto de software está protegido por el sistema de control de versiones.

Microsoft dispone de un grupo de seguridad y cumplimiento que supervisa y evalúa todos los servicios de Microsoft. Los supervisores de seguridad de información conforman el equipo y no están asociados con los departamentos de ingeniería que desarrollar OMS. Los agentes de seguridad tienen su propia cadena de mando y realizar evaluaciones independientes de productos y servicios para garantizar la seguridad y cumplimiento.

Junta directiva de Microsoft es una notificación de e informe anual acerca de la seguridad de la información todos los programas de Microsoft.

El equipo de servicio y de desarrollo de software OMS está trabajando con los equipos de Microsoft Legal y cumplimiento y otros socios de la industria para adquirir una gran variedad de certificados.

## <a name="security-standards-certifications"></a>Certificados de estándares de seguridad

Análisis de registro en OMS actualmente cumplir las normas de seguridad siguientes:

- [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm) y compatible con [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
- Pago tarjeta industria compatible con seguridad estándar de datos (PCI DSS) por el Consejo de estándares de seguridad PCI.
- [Tipo de controles de la organización de servicio (campus) 1 1 y 2 escriba 1 de seguridad social](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) compatibles
- Criterios de ingeniería común de Windows
- Certificación de informática de confianza de Microsoft
- Como un servicio de Azure, los componentes que usa OMS cumplen los requisitos de cumplimiento de Azure. Puede obtener más información al [Cumplimiento del centro de confianza de Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx).


## <a name="cloud-computing-security-data-flow"></a>Flujo de datos de seguridad de informática en nube
El siguiente diagrama muestra una arquitectura de seguridad de la nube como el flujo de información de su empresa y cómo se protege como se mueve al servicio de análisis de registro, en última instancia visto por el usuario en el portal OMS. Para obtener más información acerca de cada paso sigue el diagrama.

![Imagen de recopilación de datos OMS y seguridad](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. registrarse para el análisis de registro y recopilar datos

Para que su organización enviar datos al análisis de registro, configurar a agentes de Windows, agentes que se ejecutan en máquinas virtuales de Windows Azure o agentes de OMS para Linux. Si utiliza a agentes de Operations Manager, deberá usar a un asistente de configuración en la consola de operaciones configurarlos. Los usuarios (que pueden ser otros usuarios individuales o un grupo de personas) crear una o varias cuentas OMS (áreas de trabajo OMS) y registrar a agentes mediante una de las cuentas siguientes:


- [Identificador de organización](../active-directory/sign-up-organization.md)

- [Cuenta de Microsoft - Outlook, Office Live, MSN](http://www.microsoft.com/account/default.aspx)

Un área de trabajo OMS es donde datos se recopilan, agregados, analizar y presentar. Un área de trabajo se utiliza principalmente como un medio para dividir los datos, y cada área de trabajo es único. Por ejemplo, que desea que los datos de producción administrados con un área de trabajo OMS y los datos de prueba administrados con otra área de trabajo. Áreas de trabajo también ayudan a un acceso de usuario de control de administrador a los datos. Cada área de trabajo puede tener varias cuentas de usuario asociadas, y cada cuenta de usuario puede tener acceso a varias área de trabajo OMS. Crear áreas de trabajo basados en la región del centro de datos. Cada área de trabajo se duplica en otros centros de datos en la región, principalmente para la disponibilidad del servicio OMS.

Operations Manager, cuando finalice el Asistente de configuración, cada grupo de administración de Operations Manager establece una conexión con el servicio de análisis de registro. A continuación, use el Asistente para agregar equipos para elegir qué equipos en el grupo de administración tiene permiso para enviar datos al servicio. Para otros tipos de agente, cada uno conecta segura al servicio de OMS.

Todas las comunicaciones entre sistemas conectados y el servicio de análisis de registro está cifrada.  Se usa el protocolo TLS (HTTPS) para el cifrado.  El proceso de SDL de Microsoft se sigue para garantizar que el análisis de registro está actualizado con los últimos avances en los protocolos cifrados.

Cada tipo de agente recopila datos para el análisis de registro. El tipo de datos que se recopilan depende de los tipos de soluciones utiliza. Puede ver un resumen de recopilación de datos en [soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md). Además, la información más detallada de la colección está disponible para la mayoría de las soluciones. Una solución es un paquete de vistas predefinidas, las consultas de búsqueda de registro, reglas de recopilación de datos y lógica de procesamiento. Solo los administradores pueden usar el análisis de registro para importar una solución. Después de importa la solución, se mueve a los servidores de administración de Operations Manager (si usa) y, a continuación, los agentes que ha elegido. A continuación, los agentes recopilan los datos.

## <a name="2-send-data-from-agents"></a>2. enviar datos de agentes

Registrar todos los tipos de agente con una clave de registro y se establece una conexión segura entre el agente y el servicio de análisis de registro con autenticación basada en certificados y SSL con puerto 443. OMS usa una tienda secreta para generar y mantener las teclas. Claves privadas giran cada 90 días y se almacenan en Azure y administran las operaciones de Azure que sigan prácticas normativas y cumplimiento estrictas.

Con Operations Manager, registrar un área de trabajo con el servicio de análisis de registro y se establece una conexión segura HTTPS entre el servidor de administración de Operations Manager.

Para los agentes de Windows que se ejecutan en máquinas virtuales de Windows Azure, una clave de almacenamiento de sólo lectura se utiliza para leer los eventos de diagnóstico en tablas de Azure.

Si cualquier agente no se puede comunicar con el servicio por cualquier motivo, los datos recopilados se almacenan localmente en una caché temporal y el servidor de administración intenta volver a enviar los datos de cada minutos 8 horas 2. Datos de la caché del agente está protegidos por el almacén de credenciales del sistema operativo. Si el servicio no puede procesar los datos después de 2 horas, los agentes cola los datos. Si la cola se llena, OMS inicia eliminando los tipos de datos, comenzando con los datos de rendimiento. El límite de cola de agente es una clave del registro para poder modificar, si es necesario. Los datos recopilados se comprimen y se envían al servicio, omitiendo bases de datos local, por lo que no agrega ninguna carga en ellos. Después de envían los datos recopilados, se quita de la memoria caché.

Como se describió anteriormente, los datos de los agentes se envían sobre SSL a centros de datos de Microsoft Azure. Si lo desea, puede usar ExpressRoute para proporcionar seguridad adicional para los datos. ExpressRoute es una manera de conectarse a Azure directamente desde la red WAN existente, como un protocolo varias etiquetas cambio VPN (MPLS), proporcionado por un proveedor de servicios de red. Para obtener más información, consulte [ExpressRoute](https://azure.microsoft.com/services/expressroute/).


## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. el servicio de análisis de registro recibe y procesa datos

El servicio de análisis de registro garantiza que los datos entrantes esté desde un origen de confianza validar los certificados y la integridad de datos con la autenticación de Azure. Los datos sin formato sin procesar se almacenarán como un blob en [Microsoft Azure almacenamiento](../storage/storage-introduction.md) y no está cifrados. Sin embargo, cada blobs de Windows Azure almacenamiento tiene un conjunto de conjunto único de teclas que sólo es accesible para ese usuario. Depende del tipo de datos que se almacenan en los tipos de soluciones que se han importado y utilizada para recopilar datos. A continuación, el servicio de análisis de registro procesa los datos sin formato para el blog de almacenamiento de Azure.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. utilizar el análisis de registro para tener acceso a los datos

Puede iniciar sesión en el análisis de registro en el portal OMS mediante la cuenta profesional o una cuenta de Microsoft que haya configurado previamente. Todo el tráfico entre el portal OMS y el análisis de registro en OMS se envía a través de un canal seguro de HTTPS. Al usar el portal OMS, se genera un identificador de sesión en el cliente de usuario (explorador web) y datos se almacenan en una caché local hasta que finalice la sesión. Cuando termina, se elimina la memoria caché. Cookies de cliente, que no contienen información de identificación personal, no se eliminan automáticamente. Las cookies de sesión se marcan HTTPOnly y se protegen. Tras un periodo de inactividad predeterminado, finalice la sesión de portal de OMS.

Con el portal OMS, puede exportar datos a un archivo CSV y puede tener acceso a datos con la API de búsqueda. Exportar a CSV está limitado a 50.000 filas por exportar y datos de la API están restringidos a 5.000 filas por la búsqueda.

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de registro](log-analytics-get-started.md) para obtener más información sobre el análisis de registro y póngase en marcha en minutos.
