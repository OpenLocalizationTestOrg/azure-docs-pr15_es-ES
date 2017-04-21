<properties
   pageTitle="Continuidad empresarial de nube: recuperación - base de datos SQL de la base de datos | Microsoft Azure"
   description="Obtenga información sobre cómo la base de datos de SQL Azure admite continuidad empresarial de nube y recuperación de la base de datos y ayuda a mantener aplicaciones críticas nube que se ejecutan."
   keywords="continuidad empresarial, continuidad empresarial de nube, de recuperación de base de datos, recuperación de base de datos"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Información general sobre continuidad empresarial con la base de datos de SQL Azure

Esta información general describe las capacidades que proporciona continuidad empresarial y recuperación de base de datos de SQL Azure. Proporciona opciones, recomendaciones y tutoriales de recuperación de eventos de interrupciones que podrían provocar la pérdida de datos o hacer que la base de datos y la aplicación esté disponible. La discusión incluye qué hacer cuando un usuario o error de la aplicación afecta a la integridad de datos, un área de Azure tiene una interrupción o la aplicación requiere mantenimiento. 

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Características de base de datos de SQL que puede usar para proporcionar continuidad empresarial

Base de datos SQL proporciona varias características de continuidad empresarial, incluidas las copias de seguridad automatizadas y la replicación de base de datos opcional. Cada una tiene características diferentes de posibles pérdidas de datos para las transacciones recientes y tiempo de recuperación estimada (Insertar). Una vez que entienda estas opciones, puede elegir entre ellas - y, en la mayoría de los escenarios, utilizarlas de forma conjunta para diferentes escenarios. Desarrollar el plan de continuidad empresarial, necesitará saber el tiempo máximo aceptable antes de la aplicación totalmente recupera después del evento interrupciones: este es el objetivo de tiempo de recuperación (RTO). También necesitará saber la cantidad máxima de actualizaciones recientes de datos (intervalo de tiempo) puede tolerar la aplicación perder al recuperar después del evento de interrupción - el objetivo de punto de recuperación (RPO). 

La siguiente tabla compara la insertar y RPO para los tres escenarios más comunes.

| Capacidad |  Nivel básico | Nivel estándar  | Nivel de Premium |
|---|---|---|---|
| Punto de tiempo restaurar desde la copia de seguridad | Cualquier punto de restauración dentro de 7 días   | Cualquier punto de restauración dentro de 35 días  | Cualquier punto de restauración dentro de 35 días |
Geo restaurar desde las copias de seguridad replicar geo | Insertar < 12 horas, RPO < 1h   | Insertar < 12 horas, RPO < 1h   | Insertar < 12 horas, RPO < 1h |
|Replicación de Geo Active | Insertar < 30s, RPO < 5s   | Insertar < 30s, RPO < 5s | Insertar < 30s, RPO < 5s |


### <a name="use-database-backups-to-recover-a-database"></a>Usar copias de seguridad de base de datos para recuperar una base de datos

Base de datos SQL realiza automáticamente una combinación de copias de seguridad completas semanales, diferenciales de base de datos cada hora copias de seguridad y copias de seguridad del registro de transacciones cada cinco minutos para proteger su negocio a una pérdida de datos. Estas copias de seguridad se almacenan en almacenamiento redundante local para 35 días para bases de datos en los niveles de servicio estándar y Premium y siete días para bases de datos en el nivel de servicio básico, vea [niveles de servicio](sql-database-service-tiers.md) para obtener más detalles sobre los niveles de servicio. Si el período de retención para su nivel de servicio no satisfacen sus necesidades empresariales, puede aumentar el período de retención cambiando [el nivel de servicio](sql-database-scale-up.md). La base de datos completa y diferencial copias de seguridad también se duplican en un [Centro de datos pareja](../best-practices-availability-paired-regions.md) de protección contra una interrupción de centro de datos: consulte [copias de seguridad de base de datos automática](sql-database-automated-backups.md) para obtener más detalles.

Puede usar estas copias de seguridad de base de datos automática para recuperar una base de datos distintos de eventos de interrupciones, dentro de su centro de datos y a otro centro de datos. Con las copias de seguridad automáticas de la base de datos, el tiempo estimado de recuperación depende de varios factores como el número total de bases de datos de recuperación de la misma región al mismo tiempo, el tamaño de la base de datos, el tamaño del registro de transacciones y el ancho de banda de red. En la mayoría de los casos, el tiempo de recuperación es menos de 12 horas. Al recuperar a otra región de datos, la pérdida de datos se limita a 1 hora por el almacenamiento geo redundantes de copias de seguridad de base de datos diferencial cada hora. 

> [AZURE.IMPORTANT] Para recuperar las copias de seguridad automatizadas, debe ser miembro del rol de colaborador de SQL Server o el propietario de la suscripción: vea [RBAC: funciones integradas](../active-directory/role-based-access-built-in-roles.md). Se puede recuperar con el portal de Azure, PowerShell o la API de REST. No puede usar Transact-SQL.

Usar copias de seguridad automatizadas como mecanismo de recuperación y continuidad empresarial si la aplicación:

- No se considera a objetivos crítica.
- No tiene un SLA enlace tanto el tiempo de inactividad de 24 horas o más no generará responsabilidad de finanzas.
- Tiene un bajo nivel de cambio de datos (transacciones bajos por hora) y perder hasta una hora de cambio es una pérdida aceptable de datos. 
- Es el coste confidencial. 

Si necesita una recuperación más rápida, use [La replicación de Geo Active](sql-database-geo-replication-overview.md) (descrito a continuación). Si necesita poder recuperar datos de un período anterior 35 días, considere la posibilidad de archivar su base de datos con regularidad a un archivo de MOCHILA (un archivo comprimido que contiene el esquema de la base de datos y datos asociados) se almacena en el almacenamiento de blobs de Windows Azure o en otra ubicación de su elección. Para obtener más información sobre cómo crear un archivo de base de datos coherentes, vea [crear una copia de la base de datos](sql-database-copy.md) y [exportar la copia de la base de datos](sql-database-export.md). 

### <a name="use-active-geo-replication-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Usar la replicación de Geo Active para reducir el tiempo de recuperación y limitar la pérdida de datos asociada con la recuperación

Además de usar copias de seguridad de base de datos para la recuperación de la base de datos en caso de una interrupción de la empresa, puede usar [La replicación de Geo Active](sql-database-geo-replication-overview.md) para configurar una base de datos para tener un máximo de cuatro legibles secundarios bases de datos en las regiones de su elección. Estas bases de datos secundarios se mantienen sincronizados con la base de datos principal con un mecanismo de replicación asincrónico. Esta característica se usa para protegerse de interrupción del negocio en caso de una interrupción de centro de datos o durante la actualización de una aplicación. Replicación de Geo Active también puede usarse para proporcionar un mejor rendimiento de consulta para las consultas de solo lectura a usuarios geográfico dispersos.

Si la base de datos principal pierde la conexión o debe ponerlo sin conexión para las actividades de mantenimiento, puede promover rápidamente un secundario para convertirse en el principal (también denominado un error) y configurar las aplicaciones para conectarse a la recién promovida principal. Con una migración tras error planeada, no hay ninguna pérdida de datos. Con un failover no planificado, es posible que algunos cantidad pequeña de pérdida de datos para las transacciones muy recientes debido a la naturaleza de replicación asincrónica. Después de una migración tras error, puede posterior recuperación - según un plan o cuando el centro de datos vuelve a estar conectado. En todos los casos, los usuarios experimentar una pequeña cantidad de tiempo de inactividad y necesitan volver a conectar. 

> [AZURE.IMPORTANT] Para usar la replicación de Geo Active, debe ser propietario de la suscripción o tener permisos administrativos de SQL Server. Puede configurar y migración tras error con el portal de Azure, PowerShell o la API de REST utilizando los permisos de la suscripción o Transact-SQL con permisos de SQL Server.

Use la replicación de Geo Active si la aplicación cumple cualquiera de estos criterios:

- ¿Es fundamental objetivos.
- Tiene un contrato de nivel de servicio (SLA) que no admite 24 horas o más de inactividad.
- El tiempo de inactividad da de responsabilidad de finanzas.
- Tiene una alta velocidad de datos es alto cambio y perder una hora de datos no es aceptable.
- El coste de la replicación de geo active es menor que la responsabilidad de finanzas posible y pérdida asociado de negocios.

## <a name="recover-a-database-after-a-user-or-application-error"></a>Recuperar una base de datos después de un error de usuario o la aplicación

* Nadie es perfecto! Un usuario podría eliminar accidentalmente algunos datos, accidentalmente eliminar una tabla importante o incluso anular una base de datos completa. O bien, una aplicación podría sobrescribir accidentalmente datos con datos incorrectos debido a un defecto de la aplicación. 

En este escenario, estas son las opciones de recuperación.

### <a name="perform-a-point-in-time-restore"></a>Realizar una restauración de punto en el tiempo

Puede usar las copias de seguridad automatizadas para recuperar una copia de la base de datos a un buen punto conocido en el tiempo, siempre que sea el tiempo dentro de un período de retención de la base de datos. Después de que se restaura la base de datos, puede reemplazar la base de datos original con la base de datos o copie los datos necesarios de los datos restaurados en la base de datos original. Si la base de datos utiliza la replicación de Geo Active, se recomienda copiar los datos necesarios de la copia restaurada a la base de datos original. Si la base de datos original se reemplaza con la base de datos, debe volver a configurar y volver a sincronizar Active replicación Geo (que puede tardar algún tiempo para una base de datos de gran tamaño). 

Para obtener más información y conocer los pasos detallados para restaurar una base de datos en un punto en el tiempo con el portal de Azure o con PowerShell, vea [restaurar un momento](sql-database-recovery-using-backups.md#point-in-time-restore). No se pueden recuperar mediante Transact-SQL.

### <a name="restore-a-deleted-database"></a>Restaurar una base de datos eliminado

Si se elimina la base de datos, pero no se ha eliminado el servidor lógico, puede restaurar la base de datos eliminado al punto en el que se ha eliminado. Una copia de seguridad de base de datos se restaura en el mismo servidor SQL lógico desde la que se ha eliminado. Puede restaurar usando el nombre original o proporcionar un nuevo nombre o la base de datos.

Para obtener más información y conocer los pasos detallados para restaurar una base de datos eliminado con el portal de Azure o con PowerShell, vea [restaurar una base de datos eliminado](sql-database-recovery-using-backups.md#deleted-database-restore). No se puede restaurar mediante Transact-SQL.

> [AZURE.IMPORTANT] Si se elimina el servidor lógico, se puede recuperar una base de datos eliminado. 

### <a name="import-from-a-database-archive"></a>Importar desde un archivo de base de datos

Si se ha producido la pérdida de datos fuera del período de retención actual para las copias de seguridad automatizadas y se ha archivado la base de datos, puede [importar un archivo de MOCHILA archivado](sql-database-import.md) a una nueva base de datos. En este momento, puede reemplazar la base de datos original con la base de datos importado o copie los datos necesarios de los datos importados en la base de datos original. 

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Recuperar una base de datos a otra área de una interrupción de centro de datos regional Azure

<!-- Explain this scenario -->

Aunque raro, un centro de datos de Azure puede tener una interrupción. Cuando se produce una interrupción, hace una interrupción de empresas que solo puede durar unos minutos o puede durar horas. 

- Una opción es que esperar para que la base de datos que volver a conectarse cuando finaliza la interrupción del centro de datos. Esto funciona para las aplicaciones que pueden permitir que la base de datos sin conexión. Por ejemplo, un proyecto de desarrollo o una prueba gratuita no necesita trabajar en constantemente. Cuando un centro de datos tiene una interrupción, no sabe cuánto durará la interrupción del servicio, por lo que esta opción solo funciona si no necesita la base de datos para un tiempo.
- Otra opción es cualquier migración tras error para otra región de datos si está utilizando la replicación de Geo Active o la recuperación mediante copias de seguridad de base de datos redundantes de geo (Geo restaurar). Migración tras error tiene unos pocos segundos, mientras la recuperación de copias de seguridad toma horas.

Cuando se ejecute una acción, ¿cuánto tiempo se tarda en recuperar y cuánto pérdida de datos se produce en caso de una interrupción de centro de datos depende de cómo decide usar las características de continuidad empresarial descritas anteriormente en la aplicación. De hecho, puede usar una combinación de copias de seguridad de base de datos y replicación Geo activa según los requisitos de la aplicación. Para información sobre consideraciones de diseño de la aplicación para las bases de datos independientes y agrupaciones elásticos usar estas características de continuidad empresarial, vea [Diseño de una aplicación para la recuperación de la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md) y [grupo elástico estrategias](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Las siguientes secciones proporcionan una descripción general de los pasos para recuperar con copias de seguridad de base de datos o la replicación de Geo Active. Para los pasos detallados que incluye la planificación de requisitos, pasos posteriores a la recuperación y obtener información acerca de cómo simular una interrupción para realizar un ejercicio de recuperación de desastres, consulte [recuperar una base de datos SQL de una interrupción](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Prepararse para una interrupción

Independientemente de la característica de continuidad empresarial que utilice, debe:

- Identificar y preparar el servidor de destino, incluidas las reglas de firewall de nivel de servidor, inicios de sesión y los permisos de nivel de base de datos principal.
- Determinar cómo redirigir a los clientes y aplicaciones de cliente en el servidor nuevo
- Otras dependencias, como alertas y configuración de auditoría de documentos 
 
Si no planear y preparar correctamente, poner las aplicaciones en línea después de un error o una recuperación tarda más tiempo y es muy probable que requieren también solucionar problemas en un tiempo de carga - una mala combinación.

### <a name="failover-to-a-geo-replicated-secondary-database"></a>Migración tras error a una base de datos secundaria replicar geo 

Si está utilizando la replicación de Geo Active como mecanismo de recuperación, [forzar una migración tras error a un secundario replicar geo](sql-database-disaster-recovery.md#failover-to-geo-replicated-secondary-database). En pocos segundos, el secundario pasa a ser el nuevo principal y está listo para registrar nuevas transacciones y responder a las consultas - con solo unos segundos de pérdida de datos para los datos que no tenían ha duplicado todavía. Para obtener información acerca de cómo automatizar el proceso de migración tras error, vea [Diseño de una aplicación para la recuperación de la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [AZURE.NOTE] Cuando conecte el centro de datos, puede recuperación en el primario original (o no).

### <a name="perform-a-geo-restore"></a>Realizar una restauración Geo 

Si está utilizando habían automatizada copias de seguridad con la replicación de almacenamiento geo redundante como mecanismo de recuperación, [iniciar una recuperación de base de datos usando Geo restaurar](sql-database-disaster-recovery.md#recover-using-geo-restore). Recuperación normalmente realizará dentro de 12 horas, con pérdida de datos de una hora determinada por cuando la última copia diferencial cada hora con tomadas y duplicados. Hasta que se complete la recuperación, la base de datos es capaz de grabar las transacciones o responder a las consultas. 

> [AZURE.NOTE] Si el centro de datos vuelve a estar conectado antes de cambiar la aplicación la base de datos recuperado, simplemente puede cancelar la recuperación.  

### <a name="perform-post-failover--recovery-tasks"></a>Registrar realizar migración tras error y las tareas de recuperación 

Después de recuperación de cualquier mecanismo de recuperación, debe realizar las siguientes tareas adicionales antes de los usuarios y aplicaciones están nuevo en funcionamiento:

- Redirigir clientes y aplicaciones de cliente en el servidor nuevo y la base de datos restaurada
- Asegúrese de reglas de firewall de nivel de servidor correspondiente a los usuarios para conectarse (o utilice [los firewalls de nivel de base de datos](sql-database-firewall-configure.md#creating-database-level-firewall-rules))
- Asegúrese de inicios de sesión correspondientes y permisos de nivel de base de datos principal (o utilice [contenido los usuarios](https://msdn.microsoft.com/library/ff929188.aspx))
- Configurar auditoría, según corresponda
- Configurar alertas, según corresponda

## <a name="upgrade-an-application-with-minimal-downtime"></a>Actualizar una aplicación con el tiempo de inactividad mínimo

A veces una aplicación necesita desconectar debido a mantenimiento planeado como actualización de una aplicación. [Actualizaciones de la aplicación de administrar](sql-database-manage-application-rolling-upgrade.md) describe cómo utilizar la replicación de Geo Active habilitar actualizaciones graduales de la aplicación de nube para minimizar el tiempo de inactividad durante las actualizaciones y proporcione una ruta de recuperación en caso de que algo va mal. Este artículo examina dos métodos diferentes para coordinar el proceso de actualización y explica las ventajas y desventajas de cada opción.

## <a name="next-steps"></a>Pasos siguientes

Para información sobre consideraciones de diseño de la aplicación para las bases de datos independientes y agrupaciones elásticos, vea [Diseño de una aplicación para la recuperación de la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md) y [grupo elástico estrategias](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).






