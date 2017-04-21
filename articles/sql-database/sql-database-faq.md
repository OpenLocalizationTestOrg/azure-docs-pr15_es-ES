<properties 
   pageTitle="Preguntas más frecuentes de base de datos de SQL Azure" 
   description="Solicitar respuestas a los clientes de preguntas comunes acerca de las bases de datos de la nube y base de datos de SQL Azure, sistema de administración de base de datos relacional de Microsoft (RDBMS) y base de datos como un servicio en la nube." 
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
   ms.workload="data-management" 
   ms.date="08/16/2016"
   ms.author="sashan;carlrab"/>

# <a name="sql-database-faq"></a>Preguntas más frecuentes de base de datos SQL

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>¿Cómo mostrar el uso de la base de datos SQL en mi factura? 
Facturas de la base de datos SQL en una tasa por hora predecible según el nivel de servicio + nivel de rendimiento para bases de datos o eDTUs por grupo elásticos de la base de datos. Uso real se calcula y prorrateado por horas, por lo que puede mostrar fracciones de una hora de la factura. Por ejemplo, si existe una base de datos de 12 horas en un mes, la factura muestra el uso de 0,5 días. Además, los niveles de servicio + nivel de rendimiento y eDTUs por cada grupo se dividen en la factura para que sea más fácil ver el número de días de la base de datos que usó para cada una en un mes.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>¿Qué ocurre si una base de datos activa para menos de una hora o utiliza un nivel de servicio superior para menos de una hora?
Se cargarán para cada hora existe de una base de datos utilizando el nivel de servicio más alto + el nivel de rendimiento que se aplica durante la hora, independientemente de uso o de la base de datos estaba activa para menos de una hora. Por ejemplo, si crea una base de datos y eliminar cinco minutos más tarde la factura refleja un cargo por hora de una base de datos. 

Ejemplos
    
- Si crea una base de datos básico y, a continuación, actualizar inmediatamente a S1 estándar, se cargará a la tasa de S1 estándar para la primera hora.

- Si actualiza una base de datos de Basic a Premium a 10:00 p.m. y complete la actualización a 1:35 a.m. en el día siguiente, se cargará a la tasa de Premium empezando por 1:00 a.m. 

- Si degradar una base de datos de Premium básicas a 11:00 a.m. finaliza a las 2:15 p.m. y la base de datos se cargará a la tasa de Premium hasta 3:00 p.m., después de que se cargará a las tasas básicas.

## <a name="how-does-elastic-database-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>¿Cómo uso del grupo elásticos de la base de datos se muestra en mi factura y qué ocurre cuando se cambia eDTUs por cada grupo?
Cargos de grupo elásticos de la base de datos se muestran en su factura como elástico DTUs (eDTUs) en los incrementos que se muestra en eDTUs por cada grupo en [la página de precio](https://azure.microsoft.com/pricing/details/sql-database/). No hay ningún cargo por base de datos para los grupos de elásticos de la base de datos. Se cargarán por cada hora que existe un grupo en la eDTU más alto, independientemente de uso o el grupo estaba activo para menos de una hora. 

Ejemplos

- Si crea un grupo de base de datos elástico estándar con 200 eDTUs a las 11:18 a.m., agregar cinco bases de datos al grupo, se cargando 200 eDTUs para la hora toda, comenzando en 11 a.m. el resto del día.
- En el día 2 a 5:05 a.m., 1 de la base de datos comienza consumo 50 eDTUs y se mantiene constante durante todo el día. Bases de datos de 2 a 5 fluctúan entre 0 y 80 eDTUs. Durante el día, agregue cinco otras bases de datos que consuman diversos eDTUs durante todo el día. Día 2 es un día completo facturado a eDTU 200. 
- Día 3 a 5 a.m. agregar otro 15 bases de datos. Uso de la base de datos aumenta durante el día para el punto donde decide aumentar eDTUs para el grupo de 200 a 400 a 8:05 p.m. Cargos en el nivel de 200 eDTU estaban en vigor hasta 8 p.m. y aumentan a 400 eDTUs para las horas de cuatro restantes. 

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-database-pool-show-up-on-my-bill"></a>¿Cómo mostrar el uso de activos Geo replicación de un grupo de base de datos elásticos en mi factura?
A diferencia de bases de datos, usando [La replicación de Geo Active](sql-database-geo-replication-overview.md) con elásticos bases de datos no tiene un impacto directo de facturación.  Sólo se cargan para la eDTUs aprovisionado para cada uno de los grupos (grupo principal y secundario)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>¿Cómo afecta el uso de la característica de auditoría a mi factura? 
Auditoría integrada en el servicio de base de datos SQL sin adicional de costo y está disponible para las bases de datos básico, estándar y Premium. Sin embargo, para almacenar los registros de auditoría, la auditoría utiliza característica que aplicarán una cuenta de Azure almacenamiento y las tasas de tablas y colas en el almacenamiento de Azure en función del tamaño de su registro de auditoría.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-database-pools"></a>¿Cómo puedo encontrar el nivel de rendimiento y nivel de servicio correcto de bases de datos y conjuntos de elásticos de la base de datos? 
Existen algunas herramientas disponibles para usted. 

- Bases de datos local, use el [Asesor de tamaño DTU](http://dtucalculator.azurewebsites.net/) recomendar las bases de datos y DTUs obligatorios y evaluar varias bases de datos para los grupos de elásticos de la base de datos.
- Si una base de datos ' d aprovecharse de estar en un grupo, motor inteligente de Azure recomienda un grupo de la base de datos elástico si ve un patrón de uso histórico que garantiza. Consulte [Monitor y administrar un grupo de elásticos de la base de datos con el portal de Azure](sql-database-elastic-pool-manage-portal.md). Para obtener más información acerca de cómo realizar los cálculos usted mismo, consulte [Consideraciones de precio y rendimiento para un grupo de elásticos de la base de datos](sql-database-elastic-pool-guidance.md)
- Para ver si necesita marcar una base de datos hacia arriba o hacia abajo, vea [Guía de rendimiento de bases de datos](sql-database-performance-guidance.md).

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>¿Con qué frecuencia se puede cambiar el nivel de rendimiento o nivel de servicio de una base de datos? 
V12 bases de datos, puede cambiar el nivel de servicio (entre básico, estándar y Premium) o el nivel de rendimiento dentro de un nivel de servicio (por ejemplo, S1 a S2) tantas veces como desee. Bases de datos de versiones anteriores, puede cambiar el nivel de rendimiento o nivel de servicio un total de cuatro veces en un período de 24 horas.

##<a name="how-often-can-i-adjust-the-edtus-per-pool"></a>¿Con qué frecuencia se puede ajustar la eDTUs por cada grupo? 
Tantas veces como desee.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-database-pool"></a>¿Cuánto tiempo tarda para cambiar el nivel de rendimiento o nivel de servicio de una base de datos o mover una base de datos dentro y fuera de un grupo de la base de datos elástico? 
Cambiar el nivel de servicio de una base de datos y mover dentro y fuera de un grupo requieren la base de datos se copian en la plataforma como una operación en segundo plano. Cambiar el nivel de servicio puede tardar de unos minutos a varias horas, según el tamaño de las bases de datos. En ambos casos, las bases de datos siguen estando en línea y disponible durante el desplazamiento. Para obtener más información sobre cómo cambiar bases de datos, vea [cambiar el nivel de servicio de una base de datos](sql-database-scale-up.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>¿Cuándo debo usar una base de datos frente a elásticos bases de datos? 
En general, grupos elásticos de la base de datos están diseñados para un [modelo de aplicación de software como servicio (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md)típico, donde hay una base de datos por cliente o inquilino. Compra de bases de datos individuales y para otros fines para cumplir la variable y pico petición para cada base de datos a menudo no es el coste eficaz. Con los grupos, administrar el rendimiento del grupo de colectivos y las bases de datos escalar hacia arriba y hacia abajo automáticamente. 

Motor inteligente de Azure recomienda un grupo de bases de datos cuando un modelo de uso es significativa. Para obtener información detallada, vea [recomendaciones de nivel de precios de base de datos de SQL](sql-database-service-tier-advisor.md). Para obtener instrucciones detalladas sobre cómo elegir entre una única columna y elásticos bases de datos, vea [Consideraciones de precio y rendimiento para grupos de elásticos de la base de datos](sql-database-elastic-pool-guidance.md).

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>¿Qué significa tener hasta 200% de su almacenamiento de base de datos de preparación máximo para el almacenamiento de copia de seguridad? 
Almacenamiento de copia de seguridad es el almacenamiento de las copias de seguridad automatizadas de la base de datos que se usan para [punto-en-hora-restaurar](sql-database-recovery-using-backups.md#-point-in-time-restore) y [Geo restaurar](sql-database-recovery-using-backups.md#geo-restore). Base de datos de SQL de Microsoft Azure proporciona hasta 200% de su almacenamiento de base de datos de preparación máximo de almacenamiento de copia de seguridad sin costo adicional. Por ejemplo, si tiene una instancia de DB estándar con un tamaño de DB preparación de 250 GB, se proporcionan con 500 GB de almacenamiento de copia de seguridad sin costo adicional. Si la base de datos supera el almacenamiento de copia de seguridad proporcionado, puede elegir reducir el período de retención por ponerse en contacto con soporte técnico de Azure o de pago para el almacenamiento de copia de seguridad adicional facturado a la tasa estándar de almacenamiento geográfico redundantes de acceso de lectura (RA GRS). Para obtener más información sobre facturación RA GRS, consulte detalles de precios de almacenamiento.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Que estoy pasando desde la Web o de negocios con los nuevos niveles de servicio, ¿qué tengo que saber?
Ahora se retiren bases de datos de negocio y Web de SQL Azure. Los niveles de Basic, estándar, Premium y elástica reemplazar las bases de datos Web y empresa retirar. Hemos preguntas más frecuentes sobre adicionales que le ayudarán a en este período de transición. [P+F de puesta de sol Web y Business Edition](sql-database-web-business-sunset-faq.md)

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>¿Qué es un retraso de replicación prevista al replicar geo una base de datos entre dos regiones dentro de la misma geografía Azure?  
Nos estamos admite actualmente un RPO de cinco segundos y el retraso en la replicación se ha inferior cuando el secundario geo se hospeda en el Azure recomienda pareja región y en el mismo nivel de servicio.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>¿Qué es un retraso en la replicación esperados cuando geo secundario se crea en la misma región como la base de datos principal?  
Basado en datos empíricas, no hay demasiado diferencia entre dentro de la región y de posposición entre región replicación cuando la Azure recomienda utiliza pareja región. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Si hay un error de red entre dos regiones, ¿cómo la lógica de reintento funciona cuando se configura la replicación de Geo?  
Si hay una desconexión, se vuelva a intentar cada 10 segundos para volver a establecer conexiones.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>¿Qué puedo hacer para garantizar que se duplica un cambio crítico en la base de datos principal?
El secundario geo es una réplica asincrónica y no tratamos de mantener sincronizado completamente con el principal. Pero, le proporcionamos un método para exigir la sincronización para garantizar la replicación de los cambios importantes (por ejemplo, las actualizaciones de contraseña). Sincronización forzada afecta al rendimiento porque bloquea el subproceso de llamada hasta que se duplican todas las transacciones confirmadas. Para obtener información detallada, consulte [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>¿Qué herramientas están disponibles para supervisar la posposición replicación entre la base de datos principal y secundario geo?
Se expone el retraso de replicación en tiempo real entre la base de datos principal y secundaria geo a través de un DMV. Para obtener información detallada, consulte [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).
