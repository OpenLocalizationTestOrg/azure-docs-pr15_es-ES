<properties 
   pageTitle="Recuperación de desastres de base de datos SQL | Microsoft Azure" 
   description="Obtener instrucciones y procedimientos recomendados para usar la base de datos de SQL Azure para realizar tareas de recuperación de desastres que le ayudarán a mantienen sus objetivos críticos las aplicaciones de negocio y son resistentes a errores y las interrupciones." 
   services="sql-database" 
   documentationCenter="" 
   authors="anosov1960" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/31/2016"
   ms.author="sstein; sashan"/>

#<a name="performing-disaster-recovery-drill"></a>Realizar el ejercicio de recuperación de desastres

Se recomienda que se realiza la validación de disponibilidad de la aplicación de flujo de trabajo de recuperación periódicamente. Comprobar el comportamiento de la aplicación e implicaciones de pérdida de datos o la interrupción implica que migración tras error es una buena práctica de ingeniería. También es necesario por la mayoría de los estándares como parte de una certificación de continuidad empresarial.

Realizar un ejercicio de recuperación de desastres consta de:

- Simular interrupción de nivel de datos
- Recuperar 
- Validar la recuperación de aplicaciones de publicación de integridad

Según cómo [diseñado la aplicación para continuidad empresarial](sql-database-business-continuity.md), el flujo de trabajo para ejecutar el ejercicio puede variar en. A continuación se describen los procedimientos recomendados que llevar a cabo un ejercicio de recuperación de desastres en el contexto de la base de datos de SQL Azure. 

##<a name="geo-restore"></a>Restaurar geo

Para evitar la pérdida de datos al realizar un ejercicio de recuperación de desastres, se recomienda realizar el ejercicio con un entorno de prueba: creación de una copia del entorno de producción y usarlo para comprobar el flujo de trabajo de migración tras error de la aplicación.
 
####<a name="outage-simulation"></a>Simulación de interrupción

Para simular la interrupción puede eliminar o cambiar el nombre de la base de datos de origen. Esto provocará errores de conectividad de la aplicación. 

####<a name="recovery"></a>Recuperación

- Realizar la restauración geográfico de la base de datos en un servidor distinto como se describe [a continuación](sql-database-disaster-recovery.md). 
- Cambiar la configuración de la aplicación para conectarse a las bases de datos recuperado y siga la Guía de [Configurar una base de datos después de recuperación](sql-database-disaster-recovery.md) para completar la recuperación.

####<a name="validation"></a>Validación

- Completar el ejercicio comprobando la recuperación de publicación de aplicación integridad (es decir, las cadenas de conexión, inicios de sesión, comprobación de funcionalidad básica u otro elemento de validaciones de procedimientos de firmas de aplicación estándar).

##<a name="geo-replication"></a>Replicación de geo

Para una base de datos está protegida mediante la replicación Geo el ejercicio de obtención de detalles implican failover planificado para la base de datos secundario. La migración tras error planeada garantiza que principal y las bases de datos secundarios permanezcan sincronizados cuando se cambian los roles. A diferencia de la recuperación ante fallos no planeada, esta operación no da de pérdida de datos, por lo que se puede realizar el ejercicio en el entorno de producción. 

####<a name="outage-simulation"></a>Simulación de interrupción

Para simular la interrupción puede deshabilitar la aplicación web o una máquina virtual conectado a la base de datos. Esto provocará errores de conectividad de los clientes web.

####<a name="recovery"></a>Recuperación

- Asegúrese de que la configuración de la aplicación en la región de DR apunta a la secundaria antigua que estará puede acceder completamente nuevo principal. 
- Realizar [planeado migración tras error](sql-database-geo-replication-powershell.md#initiate-a-planned-failover) para hacer un nuevo principal de la base de datos secundario
- Siga la Guía de [Configurar una base de datos después de recuperación](sql-database-disaster-recovery.md) para completar la recuperación.

####<a name="validation"></a>Validación

- Completar el ejercicio comprobando la recuperación de publicación de aplicación integridad (es decir, las cadenas de conexión, inicios de sesión, comprobación de funcionalidad básica u otro elemento de validaciones de procedimientos de firmas de aplicación estándar).


## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre escenarios de continuidad empresarial, vea [escenarios de continuidad](sql-database-business-continuity.md)
- Para obtener información sobre las copias de seguridad automatizadas de base de datos SQL Azure, consulte [copias de seguridad de automatizado de base de datos SQL](sql-database-automated-backups.md)
- Para obtener información sobre el uso de copias de seguridad automatizadas de recuperación, consulte [restaurar una base de datos de las copias de seguridad iniciado por el servicio](sql-database-recovery-using-backups.md)
- Para obtener información sobre las opciones de recuperación más rápidas, vea [Replicación de Geo activo](sql-database-geo-replication-overview.md)  
