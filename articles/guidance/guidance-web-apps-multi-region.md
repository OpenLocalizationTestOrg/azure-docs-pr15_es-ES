<properties
   pageTitle="Aplicación Web con alta disponibilidad | Arquitectura de referencia de Azure | Microsoft Azure"
   description="Arquitectura recomendada para la aplicación web con alta disponibilidad, que se ejecuta en Microsoft Azure."
   services="app-service,app-service\web,sql-database" 
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/27/2016"
   ms.author="mwasson"/>

# <a name="azure-reference-architecture-web-application-with-high-availability"></a>Arquitectura de referencia de Azure: aplicación Web con alta disponibilidad

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

En este artículo, se muestra una arquitectura recomendada para una aplicación web con alta disponibilidad, que se ejecutan en Microsoft Azure. La arquitectura se basa en [arquitectura de referencia de Azure: mejora escalabilidad en una aplicación web][guidance-web-apps-scalability].

## <a name="architecture-diagram"></a>Diagrama de arquitectura

![Arquitectura de referencia: aplicación Web con alta disponibilidad](media/blueprints/paas-web-app-multi-region.png)

Esta arquitectura se basa en el que se muestra en [mejora escalabilidad en una aplicación web][guidance-web-apps-scalability]. Las diferencias principales son:

- **Áreas principales y secundarias**. Esta arquitectura usa dos regiones para lograr una mayor disponibilidad. La aplicación se implementa en cada regiones. Durante el funcionamiento normal, el tráfico de red se dirige a la región principal. Pero, si no está disponible, el tráfico se dirige a la región secundaria. Para obtener más información acerca de la migración tras error, consulte [Administrar migración tras error](#managing-failover-and-failback).

- **Administrador de tráfico de azure**. [Administrador de tráfico] [ traffic-manager] enruta las solicitudes entrantes a la región principal. Si la aplicación que se ejecute esa región no está disponible, el tráfico administrador no puede sobre la región secundaria. 

- **Geo replicación** de base de datos SQL y DocumentDB.

## <a name="recommendations"></a>Recomendaciones

### <a name="regional-pairing"></a>Emparejamiento regional

Cada región Azure se corresponde con otra región dentro de la misma geografía. En general, elija regiones en el mismo par regional (por ejemplo, US oriental 2 y Central de Estados Unidos). Ventajas de hacerlo son:

- Si hay una amplia interrupción, tiene prioridad la recuperación de al menos una región de cada par.
- Actualizaciones de sistema previsto de Azure se despliegan las en regiones parejas secuencialmente, para reducir el tiempo de inactividad posible.
- En la mayoría de los casos, pares residen en el misma geografía, cumplir los requisitos de residencia de datos.

Sin embargo, asegúrese de que ambas regiones compatible con todos los servicios de Azure necesarios para su aplicación. Vea [servicios por región][services-by-region]. Para obtener más información sobre los pares regionales, consulte [empresarial continuidad y recuperación ante desastres (BCDR): regiones de Azure emparejadas][regional-pairs].

### <a name="resource-groups"></a>Grupos de recursos

Considere la ubicación de la región principal, secundaria región y Administrador de tráfico en diferentes [grupos de recursos][resource groups]. Esto le permite administrar los recursos que se implementa en cada región como una sola colección &mdash; puede implementar por separado, elimine la implementación y así sucesivamente. 

### <a name="traffic-manager"></a>Administrador de tráfico

**Enrutamiento.** Administrador de tráfico admite varios [algoritmos de enrutamiento][tm-routing]. Para el escenario descrito en este artículo, use la _prioridad_ enrutamiento (anteriormente denominado enrutamiento de _migración tras error_ ). Con esta configuración, Administrador de tráfico envía todas las solicitudes a la región principal, a menos que esté accesible el punto final de la región. En ese momento, pasa automáticamente a la región secundaria. Vea [configurar de migración tras error enrutamiento método][tm-configure-failover].

**Sondeo de estado.** Administrador de tráfico usa un sondeo HTTP (o HTTPS) para supervisar la disponibilidad de cada extremo. El sondeo ofrece a tráfico administrador una prueba de superar para falta sobre la región secundaria. Funciona mediante el envío de una solicitud a una dirección URL especificada. Si obtiene una respuesta 200 no dentro de un período de tiempo de espera, se produce un error en el sondeo. Después de cuatro solicitudes con error, Administrador de tráfico marca el extremo degradado y no en el otro extremo. Para obtener más información, consulte [Administrador de tráfico extremo supervisión y migración tras error][tm-monitoring].

Como práctica recomendada, crear un extremo de sondeo de estado que indica el estado general de la aplicación y usar este extremo para el sondeo de estado. El punto final debe comprobar dependencias críticas como aplicaciones de servicio de aplicaciones, la cola de almacenamiento y la base de datos de SQL. En caso contrario, el sondeo podría informar de un extremo "correcto" cuando en realidad se producen errores en las partes críticas de la aplicación. 

Por otro lado, no utilice el sondeo de estado para comprobar los servicios de prioridad inferior. Por ejemplo, si falla un servicio de correo electrónico, la aplicación puede cambiar a un proveedor de segundo o enviar mensajes de correo electrónico más adelante. La aplicación probablemente no debe conmutar en esta situación. Para obtener más información, vea [Trama de supervisión de estado del extremo][health-endpoint-monitoring-pattern].
  
### <a name="sql-database"></a>Base de datos SQL

Usar [La replicación de Geo Active] [ sql-replication] para crear un secundario legible en una región distinta. Puede tener un máximo de cuatro secundarios legibles. Si se produce un error en la base de datos principal, o simplemente debe realizarse sin conexión, puede migración tras error a cualquiera de las bases de datos secundarios. Duplicación de Geo activa se puede configurar para cualquier base de datos en cualquier grupo elásticos de la base de datos.

### <a name="documentdb"></a>DocumentDB

DocumentDB admite la replicación de geo en todas las regiones. Una región está designada como modificables y los demás son réplicas de sólo lectura. 

Si hay una interrupción regional, puede fallar por seleccionar otra región para ser la región de escritura. El cliente de DocumentDB SDK envía automáticamente escribir solicitudes en la región actual de escritura, por lo que no es necesario actualizar la configuración de cliente después de un error. Para obtener más información, vea [distribuir datos globalmente con DocumentDB][docdb-geo]. 

> [AZURE.NOTE] Todas las réplicas pertenecen al mismo grupo de recursos.

### <a name="storage"></a>Almacenamiento de información

Para el almacenamiento de Azure, usar [almacenamiento geo redundantes de acceso de lectura] [ ra-grs] (RA GRS). Con el almacenamiento de RA GRS, se replican los datos en una región secundaria. Tener acceso de solo lectura a los datos en la región secundaria, a través de un extremo independiente. Si hay un desastre o interrupción regional, puede determinar el equipo de almacenamiento de Azure realizar una migración geo de tras error a la región secundaria. No hay ninguna acción de cliente necesaria para esta migración tras error.

Para el almacenamiento de cola, cree una cola de copia de seguridad en la región secundaria. Durante la migración tras error, la aplicación puede utilizar la cola de la copia de seguridad, hasta que el área principal vuelva a estar disponible. De este modo, la aplicación todavía puede procesar solicitudes nuevas. 

## <a name="availability-considerations"></a>Consideraciones de disponibilidad

Una arquitectura de múltiples región puede proporcionar mayor disponibilidad que la implementación de una única región. Si un corte regional afecta al área principal, puede conmutar a la región secundaria. Esta arquitectura también puede ayudarle si se produce un error en un subsistema individual de la aplicación.  
     
Hay varios enfoques generales para lograr una alta disponibilidad a través de los centros de datos:      
- Activo y pasivo con espera activa. El tráfico pasa a una región, mientras se espera el en suspensión. La aplicación está implementada y en ejecución en el área secundario. Puede empezar con un recuento de instancia más pequeño en el centro de datos secundarios y escalar, a continuación, según sea necesario. 

- Activo y pasivo de suspensión fría. No se implementa el mismo, pero aplicación hasta que sea necesario para la migración tras error. Este enfoque cuesta menos para ejecutar, pero generalmente tendrá ya tiempo de inactividad durante un error. 

- Activo. Ambas regiones están activos y las solicitudes de carga están equilibrada entre ellas. Si un centro de datos no está disponible, se toma fuera de la rotación. 

En este artículo se centra en activo y pasivo con espera activa, con el [Administrador de tráfico de Azure] [ traffic-manager] para enrutar el tráfico a la región. 

### <a name="traffic-manager"></a>Administrador de tráfico

Administrador de tráfico pasa automáticamente si la región principal no está disponible. Cuando se produce un error en el Administrador de tráfico sobre, hay un período de tiempo cuando los clientes no pueden llegar a la aplicación, que puede ser varios minutos. Dos factores afectan a la duración total:

- Debe detectar el sondeo de estado que sea accesible el centro de datos principal.

- Los servidores DNS deben actualizar los registros DNS en caché para la dirección IP, que depende de la time to live (TTL de DNS). El TTL predeterminado es 300 segundos (5 minutos), pero puede configurar este valor cuando se crea el perfil de administrador de tráfico.

Para obtener más información, vea [Acerca de supervisar el tráfico administrador][tm-monitoring]. 

Administrador de tráfico es un punto de posibles errores en el sistema. Si se produce un error en el servicio, los clientes no pueden acceder a la aplicación durante el tiempo de inactividad. Revise el [Administrador de tráfico SLA][tm-sla]y determinar si con el Administrador de tráfico únicamente cumple los requisitos empresariales de alta disponibilidad. Si no es así, considere la posibilidad de agregar otra solución de administración de tráfico como una recuperación. Si el servicio Administrador de tráfico de Azure falla, cambie los registros CNAME en DNS para que apunten a otro servicio de administración de tráfico. (Este paso debe realizarse manualmente, y la aplicación no estará disponible hasta que se propagarán los cambios DNS). 

### <a name="sql-database"></a>Base de datos SQL

El objetivo de punto de recuperación (RPO) y el tiempo de recuperación estimada (Insertar) para la base de datos SQL que se describen [aquí][sql-rpo]. 

### <a name="storage"></a>Almacenamiento de información

Almacenamiento de RA GRS proporciona resistentes, pero es importante conocer qué puede ocurrir durante una interrupción: 

- Si se produce un corte de almacenamiento, será un período de tiempo cuando no tiene acceso de escritura a los datos. Aún puede leer desde el extremo secundario durante la interrupción.

- Si un desastre o interrupción regional afecta a la ubicación principal y no se puede recuperar los datos, puede determinar el equipo de almacenamiento de Azure realizar una migración geo de tras error a la región secundaria. 

- Replicación de datos a la región secundaria se realiza de forma asincrónica. Por lo tanto, si se realiza un error de geo, es posible, no se pueden recuperar los datos de la región principal algunas pérdidas de datos.

- Errores transitorias, como una interrupción de la red, no activarán un error de almacenamiento. Diseñar la aplicación sea y son resistentes a errores transitorias. Posibles soluciones:

    - Lea la secundaria.

    - Cambiar temporalmente a otra cuenta de almacenamiento para operaciones de escritura de nuevo (por ejemplo, en los mensajes de cola). 

    - Copie datos de la secundaria a otra cuenta de almacenamiento.

    - Proporcionar funcionalidad reducida hasta que el sistema no volver.

Para obtener más información, vea [Qué hay que hacer si se produce una interrupción de almacenamiento de Azure][storage-outage].

## <a name="managing-failover-and-failback"></a>Administrar conmutación y

### <a name="traffic-manager"></a>Administrador de tráfico

Administrador de tráfico pasa automáticamente si la región principal no está disponible. De forma predeterminada, se también automáticamente producirá, una vez que vuelva a estar disponible el área principal.

Sin embargo, se recomienda realizar una recuperación manual, en lugar de automáticamente errores volver. Antes de que no atrás, compruebe que todos los subsistemas de aplicación están correcto. En caso contrario, puede crear una situación donde la aplicación voltea y hacia atrás entre los centros de datos. 

Para evitar la recuperación automática, disminuir manualmente la prioridad de la región principal después de una situación de error. Por ejemplo, supongamos que es el área principal prioridad 1 y el secundario es una prioridad de 2. Después de una migración tras error, establezca la región principal en prioridad 3, para evitar la recuperación automática. Cuando esté listo para volver, restablecer la prioridad a 1.

Los siguientes comandos actualizan la prioridad.

**PowerShell** 

```bat
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name <endpoint> -ProfileName <profile> -ResourceGroupName <resource-group> -Type AzureEndpoints
$endpoint.Priority = 3
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

Para obtener más información, vea [Cmdlets del Administrador de tráfico de Azure][tm-ps].

**CLI de Azure**

```bat
azure network traffic-manager endpoint set --name <endpoint> --profile-name <profile> --resource-group <resource-group> --type AzureEndpoints --priority 3
```    

### <a name="sql-database"></a>Base de datos SQL

Si se produce un error en la base de datos principal, realizar una migración tras error manual a la base de datos secundario. Consulte [restaurar una base de datos de SQL Azure o una migración tras error a un secundario][sql-failover]. Hasta conmutar, la base de datos secundaria será de solo lectura. 


<!-- links -->

[azure-sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[docdb-geo]: ../documentdb/documentdb-distribute-data-globally.md
[guidance-web-apps-scalability]: guidance-web-apps-scalability.md
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[ra-grs]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[regional-pairs]: ../best-practices-availability-paired-regions.md
[resource groups]: ../resource-group-overview.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[sql-failover]: ../sql-database/sql-database-disaster-recovery.md
[sql-replication]: ../sql-database/sql-database-geo-replication-overview.md
[sql-rpo]: ../sql-database/sql-database-business-continuity.md#sql-database-business-continuity-features
[storage-outage]: ../storage/storage-disaster-recovery-guidance.md
[tm-configure-failover]: ../traffic-manager/traffic-manager-configure-failover-routing-method.md
[tm-monitoring]: ../traffic-manager/traffic-manager-monitoring.md
[tm-ps]: https://msdn.microsoft.com/en-us/library/mt125941.aspx
[tm-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[tm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/en-us/services/traffic-manager/
