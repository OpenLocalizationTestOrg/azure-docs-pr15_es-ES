<properties
   pageTitle="Soluciones de recuperación de desastres - base de datos activa Geo replicación de SQL en la nube | Microsoft Azure"
   description="Obtenga información sobre cómo diseñar soluciones de recuperación de desastres de nube de planificación de continuidad del negocio con replicación geo para copia de seguridad de datos de aplicación con la base de datos de SQL Azure."
   keywords="en la nube de recuperación, soluciones de recuperación de desastres, copia de seguridad de datos de aplicación, geo replicación, planificación de continuidad empresarial"
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
   ms.date="07/20/2016"
   ms.author="sashan"/>

# <a name="design-an-application-for-cloud-disaster-recovery-using-active-geo-replication-in-sql-database"></a>Diseñar una aplicación para la recuperación de nube con la replicación de Geo Active de base de datos de SQL


> [AZURE.NOTE] [Replicación de Geo Active](sql-database-geo-replication-overview.md) ahora está disponible para todas las bases de datos en todos los niveles.



Aprenda a usar [Active Geo replicación](sql-database-geo-replication-overview.md) de base de datos de SQL para las aplicaciones de base de datos diseño y son resistentes a errores regionales y graves interrupciones. Para la planificación de continuidad empresarial, tenga en cuenta la topología de implementación de la aplicación, el contrato de nivel de servicio de destino, latencia de tráfico y costos. En este artículo, mire los patrones de aplicaciones comunes y obtener información sobre las ventajas y desventajas de cada opción. Para obtener información sobre Active Geo replicación con agrupaciones elástico, vea [grupo elástico estrategias](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## <a name="design-pattern-1-active-passive-deployment-for-cloud-disaster-recovery-with-a-co-located-database"></a>Modelo de diseño 1: implementación activo pasivo de recuperación de nube con una base de datos comparten ubicación

Esta opción es más adecuada para las aplicaciones con las características siguientes:

+ Instancia activa en una sola región de Azure
+ Dependencia fuerte de acceso de lectura y escritura (RW) a los datos
+ Conectividad entre región entre la lógica de la aplicación y la base de datos no es aceptable debido a la latencia y el tráfico de costo    

En este caso, la topología de implementación de la aplicación está optimizada para administrar desastres regionales cuando todos los componentes de la aplicación se ven afectados y es necesitan para conmutar por error como una unidad. Para redundancia geográfica, la lógica de la aplicación y la base de datos se duplican para otra región pero no se utilizan para la carga de trabajo de la aplicación en las condiciones normales. La aplicación en la región secundaria debe estar configurada para usar una cadena de conexión de SQL en la base de datos secundario. Administrador de tráfico está configurado para usar el [método de enrutamiento de migración tras error](../traffic-manager/traffic-manager-configure-failover-routing-method.md).  

> [AZURE.NOTE] [Administrador de tráfico Azure](../traffic-manager/traffic-manager-overview.md) se usa en este artículo solo con fines ilustrativos. Puede usar cualquier solución de equilibrio de carga que admita el método de enrutamiento de migración tras error.    

Además de las instancias de aplicación principal, considere la posibilidad de implementar una pequeña [aplicación de la función de trabajo](cloud-services-choose-me.md#tellmecs) que supervisa la base de datos principal por emitir periódicos T SQL de sólo lectura (RO) comandos. Puede usarlo para activar automáticamente la migración tras error, para generar una alerta en la consola de administración de la aplicación, o ambas. Para asegurarse de que supervisión no se ve afectada por interrupciones de toda la región, se debe implementar la supervisión instancias de la aplicación para cada región y cada instancia conectado a la base de datos principal de la región principal y la base de datos secundario en la región local. 

> [AZURE.NOTE] Ambas aplicaciones de supervisión deben estar activo y sondeo bases de datos principales y secundarios. Este último puede utilizarse para detectar un error en la región secundaria y alerta cuando la aplicación no está protegida.     

En el diagrama siguiente muestra esta configuración antes de una interrupción.

![Configuración de Geo-replicación de base de datos de SQL. Nube de recuperación.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Después de una interrupción en el área principal, la aplicación de supervisión detecta que la base de datos principal no es accesible y registra una alerta. Según el SLA de aplicación, puede decidir si falla cuántos sondeos consecutivos de supervisión antes de declarar un corte de la base de datos. Para lograr coordinada migración tras error de punto final de la aplicación y la base de datos, debe tener la aplicación de supervisión realice los pasos siguientes:

1. [Actualizar el estado de extremo de principal](https://msdn.microsoft.com/library/hh758250.aspx) para desencadenar la migración tras error de punto final.
2. Llamar a la base de datos secundaria para [iniciar las bases de datos](sql-database-geo-replication-portal.md).

Después de la migración, la aplicación procesa las solicitudes de usuario en la región secundaria pero permanecerá posicionar con la base de datos porque la base de datos principal estará en la región secundaria. Este escenario se muestra en el diagrama siguiente. En todos los diagramas, las líneas continuas indican las conexiones activas, líneas de puntos indican conexiones suspendidas y señales de stop indicar desencadenadores de acción.


![Replicación geo: Migración tras error a la base de datos secundaria. Copia de seguridad de datos de aplicación.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

Si se produce una interrupción en la región secundaria, el vínculo de réplica entre el principal y la base de datos secundario se ha suspendido y la aplicación de supervisión registra una alerta que se expone la base de datos principal. Rendimiento de la aplicación no se ve afectado, pero funciona expuesta la aplicación y, por tanto, un mayor riesgo en caso de ambas regiones no seguidas.

> [AZURE.NOTE] Solo se recomienda configuraciones de implementación con una sola región de DR. Esto es porque la mayoría de las ubicaciones geográficas Azure tiene dos regiones. Estas configuraciones protegen la aplicación de un error grave de ambas regiones. En un caso poco probable de este error, puede recuperar las bases de datos en una región terceros con la [operación de restauración geo](sql-database-disaster-recovery.md#recovery-using-geo-restore).

Una vez que se reduce la interrupción del servicio, la base de datos secundaria se sincroniza automáticamente con el principal. Durante la sincronización, rendimiento del primario podría verse afectado ligeramente dependiendo de la cantidad de datos que se sincronicen. El siguiente diagrama muestra una interrupción de la región secundaria.

![Base de datos secundaria se sincroniza con principal. Nube de recuperación.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)


Las principales **ventajas** de este modelo de diseño son:

+ La cadena de conexión de SQL se establece durante la implementación de aplicaciones en cada región y no cambia después de la migración.
+ Rendimiento de la aplicación no se ve afectado por la migración tras error como la aplicación y la base de datos siempre se encuentran.

La principal **desventaja** es que la instancia de aplicación redundantes de la región secundaria solo se utiliza para la recuperación.

## <a name="design-pattern-2-active-active-deployment-for-application-load-balancing"></a>Modelo de diseño 2: implementación activo activo equilibrio de carga de aplicación
Esta opción de recuperación de desastres de nube es más adecuada para las aplicaciones con las características siguientes:

+ Lee, escribe alta proporción de base de datos
+ Latencia de escritura de la base de datos no afecta a la experiencia del usuario final  
+ Lógica de solo lectura se puede separado de la lógica de lectura y escritura mediante una cadena de conexión diferente
+ Lógica de solo lectura no dependen de datos que se sincronizan totalmente con las últimas actualizaciones  

Si las aplicaciones tienen estas características, equilibrio de carga las conexiones de usuario final en varias instancias de la aplicación en diferentes regiones puede mejorar rendimiento y la experiencia del usuario final. Para implementar el equilibrio de carga, cada región debe tener una instancia activa de la aplicación con el valor lógico (RW) de lectura y escritura conectado a la base de datos principal en el área principal. La lógica de solo lectura (RO) debe estar conectada a una base de datos secundario en la misma región como la instancia de aplicación. Administrador de tráfico se debe configurar usar [enrutamiento de turnos](../traffic-manager/traffic-manager-configure-round-robin-routing-method.md) o [enrutamiento de rendimiento](../traffic-manager/traffic-manager-configure-performance-routing-method.md) con la [supervisión de extremo](../traffic-manager/traffic-manager-monitoring.md) habilitados para cada instancia de la aplicación.

Al igual que en la trama #1, considere la posibilidad de implementar una aplicación de supervisión similar. Pero a diferencia de trama #1, la aplicación de supervisión no será responsable de desencadenar el punto final de migración tras error.

> [AZURE.NOTE] Mientras este patrón usa más de una base de datos secundaria, solo uno de los secundarios se usaría para migración tras error para los motivos que se indicó anteriormente. Dado que este patrón requiere acceso de solo lectura a la secundaria, requiere la replicación de Geo Active.

Administrador de tráfico debe configurarse rendimiento enrutamiento para dirigir las conexiones de usuario a la instancia de aplicación más cercana a la ubicación geográfica del usuario. El diagrama siguiente muestra esta configuración antes de una interrupción.
![Sin interrupción: enrutamiento de rendimiento a más próximo de la aplicación. Replicación de geo.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Si se detecta un corte de la base de datos en la región principal, iniciar migración tras error de la base de datos principal a una de las regiones secundarias, cambiar la ubicación de la base de datos principal. El Administrador de tráfico automáticamente excluye el punto final sin conexión de la tabla de enrutamiento, pero sigue enruta el tráfico de usuario final para las restantes instancias en línea. Dado que la base de datos principal es ahora en una región diferente, todas las instancias de online deben cambiar su cadena de conexión de SQL de lectura y escritura para conectar con el nuevo principal. Es importante que este cambio antes de iniciar la migración tras error de base de datos. Como siempre señalan a la base de datos de la misma región se deben modifican las cadenas de conexión de SQL de solo lectura. Los pasos de la migración tras error son:  

1. Cambiar las cadenas de conexión de SQL de lectura y escritura para que apunten a la nueva principal.
2. Llamar a la base de datos secundaria designada en orden para [iniciar bases de datos](sql-database-geo-replication-portal.md).

El diagrama siguiente muestra la nueva configuración después del error.
![Configuración después de la migración. Nube de recuperación.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

En el caso de una interrupción de una de las regiones secundarias, el Administrador de tráfico elimina automáticamente el punto final sin conexión en esa región de la tabla de enrutamiento. Se ha suspendido el canal de replicación a la base de datos secundaria en dicha región. Dado que el resto de áreas obtener tráfico de usuario adicionales en este escenario, rendimiento de la aplicación se ve afectado durante la interrupción. Una vez que se reduce la interrupción del servicio, la base de datos secundario en la región afectada inmediatamente se sincroniza con la principal. Durante la sincronización rendimiento del primario podría verse afectado ligeramente dependiendo de la cantidad de datos que se sincronicen. El siguiente diagrama muestra una interrupción de una de las regiones secundarias.

![Interrupción en región secundaria. Recuperación - Geo replicación de la nube.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

La **ventaja** de clave de este modelo de diseño es que puede escalar la carga de trabajo de la aplicación en varios secundarios para lograr el rendimiento óptimo para el usuario final. Las **compensaciones** de esta opción son:

+ Conexiones de lectura y escritura entre las instancias de la aplicación y la base de datos tienen distintos latencia y costo
+ Rendimiento de la aplicación se ve afectado durante la interrupción
+ Instancias de la aplicación se requieren para cambiar dinámicamente la cadena de conexión de SQL después de la migración de base de datos.  

> [AZURE.NOTE] Un enfoque similar puede utilizarse para descargar las cargas de trabajo especializadas, como informes de trabajos, herramientas de inteligencia empresarial o realizar copias de seguridad. Estas cargas de trabajo consumen recursos importantes de la base de datos, por tanto, se recomienda designar una de las bases de datos secundarios para ellos con el nivel de rendimiento que coinciden con la carga de trabajo previsto.

## <a name="design-pattern-3-active-passive-deployment-for-data-preservation"></a>Modelo de diseño 3: implementación de activo y pasivo para la conservación de datos  
Esta opción es más adecuada para las aplicaciones con las características siguientes:

+ Pérdida de datos es el riesgo para la empresa alta. La base de datos de migración tras error sólo puede usarse como último recurso si la interrupción es permanente.
+ La aplicación puede funcionar en "modo de solo lectura" durante un período de tiempo.

En este modelo, la aplicación cambia al modo de solo lectura cuando se conecta a la base de datos secundario. La lógica de aplicación en el área principal se encuentra conjuntamente con la base de datos principal y funciona en modo de lectura y escritura (RW). La lógica de la aplicación en la región secundaria se encuentran con la base de datos secundario y está lista para que funcione en modo de solo lectura (RO).  Administrador de tráfico se debe configurar utilizar el [enrutamiento de migración tras error](../traffic-manager/traffic-manager-configure-failover-routing-method.md) con la [supervisión de extremo](../traffic-manager/traffic-manager-monitoring.md) habilitados para ambas instancias de aplicación.

El diagrama siguiente muestra esta configuración antes de una interrupción.
![Implementación de activo y pasivo antes de la migración tras error. Nube de recuperación.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Cuando el Administrador de tráfico detecta un error de conectividad a la región principal, cambia automáticamente el tráfico de usuario a la instancia de la aplicación en la región secundaria. Con este modelo, es importante que **no** iniciar bases de datos después de haber detectado la interrupción. La aplicación en la región secundaria está activada y funciona en modo de solo lectura con la base de datos secundario. Esto se muestra en el siguiente diagrama.

![Interrupción: Aplicación en modo de solo lectura. Nube de recuperación.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Una vez que se reduce la interrupción en el área principal, el Administrador de tráfico detecta la restauración de conectividad en el área principal y cambia el tráfico de usuario a la instancia de aplicación en el área principal. Esa instancia de la aplicación se reanuda y funciona en modo de lectura y escritura con la base de datos principal.

> [AZURE.NOTE] Dado que este patrón requiere acceso de solo lectura a la secundaria requiere la replicación de Geo Active.

En el caso de una interrupción de la región secundaria, el Administrador de tráfico marca el punto final de la aplicación en la región principal degradado y el canal de replicación se ha suspendido. Sin embargo, esta interrupción no afectar al rendimiento de la aplicación durante la interrupción. Una vez que se reduce la interrupción del servicio, la base de datos secundaria inmediatamente se sincroniza con la principal. Durante la sincronización rendimiento del primario podría verse afectado ligeramente dependiendo de la cantidad de datos que se sincronicen.

![Interrupción: Base de datos secundaria. Nube de recuperación.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

En este modelo de diseño tiene varias **ventajas**:

+ Evita la pérdida de datos durante las interrupciones temporales.
+ No requiere implementar una aplicación de supervisión, como la recuperación se activa por el Administrador de tráfico.
+ El tiempo de inactividad sólo depende de la rapidez Administrador de tráfico detecta el error de conectividad, que se puede configurar.

Las **compensaciones** son:

+ Aplicación puedan funcionar en modo de solo lectura.
+ Es necesario cambiar dinámicamente a él cuando está conectado a una base de datos de solo lectura.
+ Reanudación de las operaciones de lectura y escritura requiere la recuperación de la región principal, lo que significa que el acceso a datos completa puede deshabilitarse por horas o incluso días.

> [AZURE.NOTE] En el caso de una interrupción del servicio permanentes en la región, debe activar bases de datos manualmente y acepte la pérdida de datos. La aplicación será funcional en la región secundaria con acceso de lectura y escritura a la base de datos.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planificación de continuidad empresarial: elija un diseño de la aplicación de recuperación de nube

La estrategia de recuperación nube específica puede combinar o extender estos patrones de diseño para satisfacer mejor las necesidades de la aplicación.  Como se mencionó anteriormente, la estrategia que elija se basa en el SLA quiere ofrecer a sus clientes y la topología de implementación de la aplicación. Para ayudar a guiar la decisión, la siguiente tabla compara las opciones en función de la pérdida de datos estimado o estimado de tiempo de recuperación (Insertar) y objetivo (RPO) de punto de recuperación.

| Trama | RPO | INSERTAR
| :--- |:--- | :---
| Implementación de activo y pasivo de recuperación de acceso comparten ubicación de la base de datos | Acceso de lectura y escritura < 5 s | Tiempo de detección de errores, API de migración tras error llamar + pruebas de comprobación de aplicación
| Implementación de activo activo equilibrio de carga de aplicación | Acceso de lectura y escritura < 5 s | Tiempo de detección de error migración tras error API llamada cadena de conexión de SQL cambiar + + pruebas de comprobación de aplicación
| Implementación de activo y pasivo para la conservación de datos | Acceso de solo lectura < 5 s acceso de lectura y escritura = cero | Acceso de solo lectura = tiempo de detección de errores de conectividad + prueba de comprobación de la aplicación <br>Acceso de lectura y escritura = tiempo para reducir la interrupción

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre las copias de seguridad automatizadas de base de datos SQL Azure, consulte [copias de seguridad de automatizado de base de datos SQL](sql-database-automated-backups.md)
- Para obtener una descripción de la continuidad empresarial y escenarios, vea [información general de continuidad empresarial](sql-database-business-continuity.md)
- Para obtener información sobre el uso de copias de seguridad automatizadas de recuperación, consulte [restaurar una base de datos de las copias de seguridad iniciado por el servicio](sql-database-recovery-using-backups.md)
- Para obtener información sobre las opciones de recuperación más rápidas, vea [Replicación de Geo activo](sql-database-geo-replication-overview.md)  
- Para obtener información sobre el uso de copias de seguridad automatizadas de archivado, vea [Copiar de base de datos](sql-database-copy.md)
- Para obtener información sobre Active Geo replicación con agrupaciones elástico, vea [grupo elástico estrategias](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
