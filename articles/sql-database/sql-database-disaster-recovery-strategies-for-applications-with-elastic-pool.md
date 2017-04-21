<properties 
   pageTitle="Diseño de soluciones de nube para usar Geo-replicación de base de datos SQL de recuperación de | Microsoft Azure"
   description="Aprenda a diseñar su solución de nube para recuperación eligiendo la trama derecha migración tras error."
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
   ms.workload="NA" 
   ms.date="07/16/2016"
   ms.author="sashan"/>

# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pool"></a>Estrategias para aplicaciones mediante grupo elástico de base de datos de SQL 

En los años que hemos aprendido que los servicios de nube no son incidencias de prueba de errores y graves puede y sucederá. Base de datos SQL proporciona varias funciones para proporcionar la continuidad empresarial de la aplicación cuando se produzcan estas incidencias. Bases de datos independiente y [agrupaciones elásticos](sql-database-elastic-pool.md) admiten el mismo tipo de capacidad de recuperación. Este artículo describen varias estrategias de DR para elástica grupos que aprovechar estas características de continuidad empresarial de base de datos de SQL.

Para el propósito de este artículo usaremos el modelo de aplicación de SaaS ISV canónico:

<i>Una nube moderna había basada web application disposiciones una base de datos SQL para cada usuario final. El proveedor de software tiene una gran cantidad de clientes y, por tanto, usa muchas bases de datos, conocidos como bases de datos del inquilino. Dado que las bases de datos del inquilino normalmente tienen patrones de actividad imprevistos, el proveedor de software utiliza un conjunto de elástico para asegurarse de la base de datos de costo muy predecible de largos períodos de tiempo. El grupo elástico también simplifica la administración de rendimiento cuando picos de la actividad de usuario. Además de las bases de datos del inquilino la aplicación también usa varias bases de datos para administrar perfiles de usuario, seguridad, recopilar los patrones de uso etcetera. Disponibilidad de los inquilinos individuales no afecta a la disponibilidad de la aplicación como todo. Sin embargo, la disponibilidad y el rendimiento de bases de datos de administración es fundamental para la función de la aplicación y si las bases de datos de administración están desconectados toda la aplicación está sin conexión.</i>  

En el resto del papel trataremos las estrategias de DR que cubre una gama de escenarios desde las aplicaciones de inicio confidencial de costo a los requisitos de disponibilidad estrictas.  

## <a name="scenario-1-cost-sensitive-startup"></a>Escenario 1. Costo inicial confidencial

<i>Soy un negocio de inicio y estoy precio muy confidencial.  Deseo simplificar la implementación y administración de la aplicación y vaya a tener un SLA limitado para clientes individuales. Pero quiero garantizar la aplicación como un todo nunca está sin conexión.</i>

Para satisfacer el requisito de simplificar, debería implementar todas las bases de datos de inquilinos en un grupo elástico en la región de su elección Azure e implementar las bases de datos de administración como bases de datos replicada geo independiente. Para la recuperación de inquilinos, use Restaurar geo, que se incluye sin costo adicional. Para garantizar la disponibilidad de las bases de datos de administración, se deben replicar geo para otra región (paso 1). El coste en curso de la configuración de recuperación de desastres en este escenario es igual que el costo total de las bases de datos secundario. Esta configuración se muestra en el diagrama siguiente.

![Figura 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

En el caso de una interrupción en el área principal, los pasos de recuperación para que se muestre la aplicación en línea se muestran en el diagrama siguiente.

- Inmediatamente migración tras error de la administración de bases de datos (2) a la región de DR. 
- Cambiar la cadena de conexión de la aplicación para que apunten a la región de DR. Se creará en la región de DR todas las cuentas nuevas y las bases de datos del inquilino. Los clientes existentes verán sus datos disponibles temporalmente.
- Crear el grupo elástico con la misma configuración que el grupo original (3). 
- Use restaurar geo crear copias del inquilino bases de datos (4). Puede considerar desencadenante la restaura individual por las conexiones de usuario final o utilizar algún otro esquema de prioridad específica de la aplicación.

En este momento la aplicación está volver a conectarse en el área de DR, pero algunos clientes experimentará un retraso al acceder a sus datos.

![Figura 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Si la interrupción es temporal, es posible que el área principal se recuperarán por Azure antes de todas la restaura en la región de DR. En este caso, debe organizar mover la aplicación a la región principal. El proceso siga los pasos que se muestra en el diagrama siguiente.
 
- Cancelar todas las solicitudes de restauración de geo pendientes.   
- Migración tras error las bases de datos de administración a la región principal (5). Nota: Después de recuperación del área los primarios antiguos automáticamente son secundarios. Ahora cambiará roles de nuevo. 
- Cambiar la cadena de conexión de la aplicación para que señalen a la región principal. Ahora todas las cuentas nuevas y las bases de datos del inquilino se creará en el área principal. Algunos de los clientes existentes verán sus datos disponibles temporalmente.   
- Establecer todas las bases de datos en el grupo de DR como solo lectura para asegurarse de que no se puede modificar en la región de DR (6). 
- Para cada base de datos en el grupo de DR que ha cambiado desde la recuperación, cambiar el nombre o eliminar las bases de datos correspondientes en el grupo principal (7). 
- Copie las bases de datos actualizados de la agrupación de DR al grupo principal (8). 
- Elimine el grupo de DR (9)

En este momento la aplicación estará en línea en el área principal con todos los inquilinos bases de datos disponibles en el grupo principal.

![Figura 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

La clave **beneficiarse** de esta estrategia es de bajo coste en curso para redundancia de nivel de datos. Se realizan copias de seguridad automáticamente el servicio de base de datos SQL con ninguna reescritura de la aplicación y sin costo adicional.  El costo se contrae solo cuando se restauran las bases de datos elásticos. El **Equilibrio** es que la recuperación completa de todas las bases de datos de inquilinos llevará mucho tiempo. Dependerá el total número de restaura iniciará en la región de DR y tamaño de las bases de datos del inquilino. Aunque prioridades restaura algunos inquilinos sobre otros usuarios, tendrá una competición con otro de restaura que se inicia en la misma región como determinar el servicio y limitar para minimizar la repercusión global en bases de datos de los clientes existentes. Además, no puede iniciar la recuperación de las bases de datos del inquilino hasta que se cree el nuevo grupo de elástico en la región de DR.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Escenario 2. Aplicación para adultos con niveles de servicio 

<i>Tengo una aplicación de SaaS para adultos con ofertas de niveles de servicio y diferentes SLA de los clientes de prueba y para clientes de pago. Para los clientes de prueba, puedo tener que reducir el costo tanto como sea posible. Clientes de prueba pueden tardar un tiempo de inactividad pero desea reducir su probabilidad. Para los clientes de pago, tiempo de inactividad es el riesgo de vuelo. Así que quiero para asegurarse de que pagar los clientes siempre están teniendo acceso a sus datos.</i> 

Para admitir este escenario, debe separar a los inquilinos de prueba de inquilinos de pagados al colocarlos en diferentes agrupaciones elásticos. Los clientes de prueba tendría eDTU inferior por inquilino y SLA inferior con un tiempo de recuperación. Los clientes de pago sería en un grupo con mayor eDTU por inquilino y un SLA superior. Para garantizar el tiempo de recuperación más bajo, bases de datos de los clientes pago inquilino debería replicar geo. Esta configuración se muestra en el diagrama siguiente. 

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Como en el primer escenario, las bases de datos de administración se activará bastante para usar una base de datos replicada geo de independiente para él (1). Esto asegurará el rendimiento predecible para suscripciones de cliente nuevo, actualizaciones de perfil y otras operaciones de administración. El área donde residen los primarios de las bases de datos de administración será la región principal y la región donde residen los secundarios de las bases de datos de administración será la región de DR.

Bases de datos de los clientes pago inquilino tendrán bases de datos activos en el grupo "pago" aprovisionado en el área principal. Debe proporcionar un grupo con el mismo nombre en la región de DR secundario. Cada inquilino sería replicar geo al grupo secundario (2). Esto le permitirá una recuperación rápida de todas las bases de datos de inquilinos con migración tras error. 

Si se produce una interrupción en el área principal, en el diagrama siguiente se muestran los pasos de recuperación para que se muestre la aplicación en línea:

![Figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

- Conmutar inmediatamente las bases de datos de administración a la región de DR (3).
- Cambiar la cadena de conexión de la aplicación para que apunten a la región de DR. Ahora se creará en la región de DR todas las cuentas nuevas y las bases de datos del inquilino. Los clientes existentes de prueba verán sus datos disponibles temporalmente.
- Migración tras error bases de datos del inquilino de pago para el grupo en la región de DR para restaurar inmediatamente su disponibilidad (4). Puesto que la migración tras error es un cambio de nivel de metadatos rápida puede considerar que una optimización donde se activa las migraciones tras error individuales a petición por las conexiones de usuario final. 
- Si el tamaño del grupo secundario eDTU era inferior principal porque las bases de datos secundarios sólo requiere la capacidad de procesar los registros de cambio mientras estaban secundarios, debe aumentar inmediatamente la capacidad de grupo ahora para adaptarse a la carga de trabajo completa de todos los inquilinos (5). 
- Crear nuevo grupo elástico con el mismo nombre y la misma configuración en la región de DR para bases de datos de los clientes prueba (6). 
- Una vez creada la agrupación de los clientes prueba, use geo restaurar para restaurar las bases de datos del inquilino de prueba individuales en el nuevo grupo (7). Puede considerar desencadenante la restaura individual por las conexiones de usuario final o utilizar algún otro esquema de prioridad específica de la aplicación.

En este momento la aplicación está volver a conectarse en el área de DR. Todos los clientes que pagan tienen acceso a sus datos, mientras que los clientes de prueba experimentará un retraso al acceder a sus datos.

Cuando se recupera la región principal restaurada por Azure *después de* la aplicación en la región de DR que puede seguir ejecutando la aplicación de esa región o puede decidir no el área principal. Si es el área principal recuperado *antes de* completar el proceso de migración tras error, debe considerar conmutar inmediatamente. La recuperación realice los pasos que se muestra en el diagrama siguiente: 
 
![Figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

- Cancelar todas las solicitudes de restauración de geo pendientes.   
- Migración tras error las bases de datos de administración (8). Después de recuperación del área principal antiguo tenía se convierten automáticamente en el secundario. Ahora se convierte en el principal nuevo.  
- Migración tras error del inquilino pago bases de datos (9). Del mismo modo, después de recuperación del área los primarios antiguos se convierten automáticamente en los secundarios. Ahora que se vuelven a los primarios. 
- Establecer las bases de datos de prueba restaurados que han cambiado en la región de DR a solo lectura (10).
- Para cada base de datos en el grupo de clientes de prueba DR modificado desde la recuperación, cambiar el nombre o eliminar la base de datos correspondiente en el grupo principal de clientes de prueba (11). 
- Copie las bases de datos actualizados de la agrupación de DR al grupo principal (12). 
- Elimine el grupo de DR (13) 

> [AZURE.NOTE] La operación de migración tras error es asincrónica. Para reducir el tiempo de recuperación es importante que ejecutar el comando de migración tras error de bases de datos del inquilino en lotes de al menos 20 bases de datos. 

La clave **beneficiarse** de esta estrategia es que proporciona el SLA más alto para los clientes de pago. También garantiza que los nuevos ensayos se desbloqueada tan pronto como se crea el grupo de DR prueba. El **Equilibrio** es que esta configuración aumentará el costo total de las bases de datos del inquilino por el costo de la agrupación de DR secundario para los clientes de pagados. Además, si el grupo secundario tiene un tamaño diferente, la clientes que pagan experimentar un rendimiento inferior después de la migración hasta que se complete la actualización de grupo en la región de DR. 

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Escenario 3. Aplicación distribuida geográfico con niveles de servicio

<i>Tengo una aplicación de SaaS para adultos con ofertas de niveles de servicio. Deseo ofrecer un SLA muy agresivo a Mis clientes pagadas y minimice el riesgo de impacto cuando se produzcan interrupciones porque incluso breve interrupción puede provocar insatisfacción del cliente. Es fundamental que el clientes que pagan siempre pueden tener acceso a sus datos. Las pruebas son gratuitas y no se ofrece un SLA durante el período de prueba.</i> 

Para admitir este escenario, debe tener tres grupos elásticos independientes. Deben aprovisionar dos grupos de igual tamaño con alta eDTUs por base de datos en dos regiones diferentes que contenga las bases de datos de los clientes pago inquilino. El tercer grupo que contiene a los inquilinos prueba tendría un eDTUs inferior por base de datos y aprovisionar en uno de los dos región.

Para garantizar el tiempo de recuperación más bajo durante interrupciones inquilino de los clientes pago bases de datos deben replicarse geo con 50% de las bases de datos principales de cada una de las dos regiones. Asimismo, cada región tendría 50% de las bases de datos secundarios. De este modo si no está conectada a una región 50% de las bases de datos de los clientes pago se verá afectado y tendría que la migración tras error. Otras bases de datos ' d permanecen intactos. Esta configuración se muestra en el diagrama siguiente:

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Como en los casos anteriores, las bases de datos de administración se activará bastante para que, debe configurarse como independiente bases de datos replicada geo (1). Esto asegurará el rendimiento predecible de las suscripciones de cliente nuevo, actualizaciones de perfil y otras operaciones de administración. Región A sería el área principal para las bases de datos de administración y la región B se usará para la recuperación de las bases de datos de administración.

Bases de datos de los clientes pago inquilino se mostrará también replicar geo pero con primarios y secundarios dividen entre región y zona B (2). De este modo las bases de datos principal de inquilinos afectadas por la interrupción puede migración tras error a las regiones y no estarán disponibles. No se verán afectada en absoluto la otra mitad de las bases de datos del inquilino. 

El diagrama siguiente muestra los pasos de recuperación deben realizarse si se produce una interrupción en región A.

![Figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

- Conmutar inmediatamente la administración bases de datos a región B (3).
- Cambiar la cadena de conexión de la aplicación para que apunten a las bases de datos de administración de región B. modificar la administración de bases de datos para asegurarse de que se crearán las nuevas cuentas y bases de datos del inquilino en región B y las bases de datos de inquilinos existentes se encuentra allí también. Los clientes existentes de prueba verán sus datos disponibles temporalmente.
- Migración tras error bases de datos del inquilino de pago al grupo 2 de región B para restaurar inmediatamente su disponibilidad (4). Puesto que la migración tras error es un cambio de nivel de metadatos rápida puede considerar que una optimización donde se activa las migraciones tras error individuales a petición por las conexiones de usuario final. 
- Ya que ahora grupo 2 contiene solo principales bases de datos de que la carga de trabajo total en el grupo aumentará inmediatamente deben aumentar su tamaño eDTU (5). 
- Crear nuevo grupo elástico con el mismo nombre y la misma configuración en la región B para bases de datos de los clientes prueba (6). 
- Una vez creado el grupo use geo restaurar para restaurar la base de datos del inquilino de prueba individuales en el grupo (7). Puede considerar desencadenante la restaura individual por las conexiones de usuario final o utilizar algún otro esquema de prioridad específica de la aplicación.


> [AZURE.NOTE] La operación de migración tras error es asincrónica. Para reducir el tiempo de recuperación es importante que ejecutar el comando de migración tras error de bases de datos del inquilino en lotes de al menos 20 bases de datos. 

En este momento la aplicación es volver a conectarse en región B. Todos los clientes que pagan tienen acceso a sus datos, mientras que los clientes de prueba experimentará un retraso al acceder a sus datos.

Cuando se recupera la región A debe decidir si desea usar región B para clientes de prueba o recuperación al uso de la agrupación de clientes de prueba en región A. Un criterio puede ser el % de bases de datos de prueba inquilino modificado desde la recuperación. Independientemente de esta decisión debe volver a equilibrar a los inquilinos pagados entre dos grupos. el diagrama siguiente muestra el proceso cuando las bases de datos de prueba inquilino no volver a región A.  
 
![Figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

- Cancelar todas las solicitudes de pendientes geo-restaurar al grupo de DR prueba.   
- Migración tras error la base de datos de administración (8). Después de la recuperación de la región, el principal anterior tenía estuvo automáticamente el secundario. Ahora se convierte en el principal nuevo.  
- Seleccione qué inquilino pago bases de datos se producirá un error al grupo 1 e iniciar migración tras error a sus secundarios (9). Después de recuperación del área todas las bases de datos en el grupo 1 automáticamente estuvo secundarios. Ahora 50% de ellos se vuelven a primarios. 
- Reducir el tamaño del grupo de 2 a la eDTU original (10).
- Restaurar de conjunto de todas las bases de datos de prueba en la región B a solo lectura (11).
- Para cada base de datos en el grupo de DR prueba que ha cambiado desde la recuperación de cambiar el nombre o eliminar la base de datos correspondiente en el grupo principal prueba (12). 
- Copie las bases de datos actualizados de la agrupación de DR al grupo principal (13). 
- Elimine el grupo de DR (14) 

Las principales **ventajas** de esta estrategia son:

- Es compatible con el SLA más agresivo para los clientes de pago porque garantiza que una interrupción no puede afectar a más de 50% de las bases de datos del inquilino. 
- Garantiza que los nuevos ensayos se desbloquea tan pronto como el seguimiento de DR grupo se crea durante la recuperación. 
- Permite un uso más eficaz de la capacidad de agrupación según el 50% de bases de datos secundarios en el grupo 1 y 2 se garantiza que menos activo, a continuación, las bases de datos principales.

Son las principales **ventajas y desventajas** :

- Las operaciones CRUD las bases de datos de administración tendrá menor latencia para los usuarios finales conectados a región A que para los usuarios finales conectados a región B como se ejecutará en el principal de las bases de datos de administración.
- Requiere un diseño más complejo de la base de datos de administración. Por ejemplo, cada registro inquilino tendría que tiene una etiqueta de ubicación que tenga que cambiar durante la migración tras error y recuperación.  
- El clientes que pagan pueden experimentar un rendimiento menor que habitual hasta que se complete la actualización de grupo de región B. 

## <a name="summary"></a>Resumen

En este artículo se centra en las estrategias para el nivel de base de datos utilizada por una aplicación de múltiples arrendatario SaaS ISV. La estrategia que elija debe basarse en las necesidades de la aplicación, como el modelo de empresa, el SLA que desea ofrecer a sus clientes, presupuesto restricción etcetera... Cada estrategia descrito describe las ventajas y compensaciones por lo que puede tomar una decisión informada. Además, su aplicación específica es probable que incluirá otros componentes de Azure. Por lo que debe revisar su guía de continuidad empresarial y organizar la recuperación del nivel de la base de datos con ellos. Para obtener más información sobre la administración de recuperación de aplicaciones de base de datos de Azure, consulte [diseñar soluciones de nube de recuperación](./sql-database-designing-cloud-solutions-for-disaster-recovery.md) .  


## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre las copias de seguridad automatizadas de base de datos SQL Azure, consulte [copias de seguridad de automatizado de base de datos SQL](sql-database-automated-backups.md)
- Para obtener una descripción de la continuidad empresarial y escenarios, vea [información general de continuidad empresarial](sql-database-business-continuity.md)
- Para obtener información sobre el uso de copias de seguridad automatizadas de recuperación, consulte [restaurar una base de datos de las copias de seguridad iniciado por el servicio](sql-database-recovery-using-backups.md)
- Para obtener información sobre las opciones de recuperación más rápidas, vea [Replicación de Geo activo](sql-database-geo-replication-overview.md)  
- Para obtener información sobre el uso de copias de seguridad automatizadas de archivado, vea [Copiar de base de datos](sql-database-copy.md)
