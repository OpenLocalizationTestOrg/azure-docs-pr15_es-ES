<properties
   pageTitle="Ejecución de máquinas virtuales de Linux en varias áreas para alta disponibilidad | Arquitectura de referencia | Microsoft Azure"
   description="Cómo implementar máquinas virtuales en varias áreas de Azure para alta disponibilidad y resistencia."
   services=""
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
   ms.date="10/21/2016"
   ms.author="mwasson"/>

# <a name="running-linux-vms-in-multiple-regions-for-high-availability"></a>Ejecución de máquinas virtuales de Linux en varias áreas para alta disponibilidad

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [Ejecución de máquinas virtuales de Linux en varias áreas para alta disponibilidad](guidance-compute-multiple-datacenters-linux.md)
- [Ejecución de máquinas virtuales de Windows en varias áreas para alta disponibilidad](guidance-compute-multiple-datacenters.md)

En este artículo, se recomienda un conjunto de prácticas para ejecutar máquinas virtuales de Linux (VM) en varias áreas de Azure, lograr disponibilidad y una sólida infraestructura de recuperación.

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación: [El Administrador de recursos] [ resource groups] y clásica. Este artículo, se utiliza el Administrador de recursos, que Microsoft recomienda para implementaciones nuevas.

Una arquitectura de múltiples región puede proporcionar mayor disponibilidad que la implementación de una única región. Si un corte regional afecta a la región principal, puede usar el [Administrador de tráfico] [ traffic-manager] conmutar a la región secundaria. Esta arquitectura también puede ayudarle si se produce un error en un subsistema individual de la aplicación.

Hay varios enfoques generales para lograr una alta disponibilidad a través de los centros de datos:   
  
- Activo y pasivo con espera activa. El tráfico pasa a una región, mientras se espera el en suspensión. Máquinas virtuales de la región secundaria están asignados y en funcionamiento en todo momento.

- Activo y pasivo de suspensión fría. El mismo, pero máquinas virtuales en la región secundaria no se asignan hasta que sea necesario para la migración tras error. Este enfoque cuesta menos para ejecutar, pero generalmente tendrá ya tiempo de inactividad durante un error.

- Activo. Ambas regiones están activos y las solicitudes de carga están equilibrada entre ellas. Si un centro de datos no está disponible, se toma fuera de la rotación.

Esta arquitectura se centra en activo y pasivo con espera activa, mediante el Administrador de tráfico de migración tras error. Tenga en cuenta que podría implementar un pequeño número de máquinas virtuales de espera activa y, a continuación, escalar según sea necesario.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

El siguiente diagrama se basa en la arquitectura de la [confiabilidad de agregar a una arquitectura de N niveles en Azure](guidance-compute-n-tier-vm-linux.md). 

> Un documento de Visio que incluye este diagrama de arquitectura está disponible para su descarga en el [Centro de descarga de Microsoft][visio-download]. Este diagrama se encuentra en la "cálculo - página de múltiples región (Linux).

![[0]][0]

- **Áreas principales y secundarias**. Esta arquitectura usa dos regiones para lograr una mayor disponibilidad. Una es el área principal. Durante el funcionamiento normal, el tráfico de red se dirige a la región principal. Pero, si no está disponible, el tráfico se dirige a la región secundaria.

- ** [Manager de tráfico de azure] [ traffic-manager] ** enruta las solicitudes entrantes a la región principal. Si esa región no está disponible, el tráfico administrador no puede sobre la región secundaria. Para obtener más información, consulte la sección [Configuración de administrador de tráfico](#configuring-traffic-manager).

- **Grupos de recursos**. Crear distintos [grupos de recursos] [ resource groups] en el área principal, el secundario región y para el Administrador de tráfico. Esto le da la flexibilidad a administrar cada región como un conjunto único de recursos. Por ejemplo, puede volver a una región específica, sin tener en otro. [Vincular los grupos de recursos][resource-group-links], de modo que puede ejecutar una consulta para obtener una lista de todos los recursos de la aplicación.

- **VNets**. Crear un VNet independiente para cada región. Asegúrese de que no se superponen los espacios de direcciones.

- **Apache Casandra** había implementado en datos centros en Azure regiones. Centros de datos de Casandra se implementan en diferentes regiones Azure para alta disponibilidad. Dentro de cada región, nodos están configurados en modo de bastidor compatibles con errores y actualización los dominios de la resistencia dentro de la región.

## <a name="recommendations"></a>Recomendaciones

Azure ofrece numerosos recursos distintos y tipos de recursos, por lo que puede ser esta arquitectura de referencia se aprovisione varias formas diferentes. Proporcionamos una plantilla de administrador de recursos de Azure para instalar la arquitectura de referencia que sigue estas recomendaciones. Si elige crear su propia arquitectura de referencia siguiendo estas recomendaciones, a menos que tenga un requisito específico que no se ajusta una recomendación.

### <a name="regional-pairing"></a>Emparejamiento regional

Cada región Azure se corresponde con otra región dentro de la misma geografía. En general, elija regiones en el mismo par regional (por ejemplo, Estados Unidos oriental 2 y nos Central). Ventajas de hacerlo son:

- Si hay una amplia interrupción, tiene prioridad la recuperación de al menos una región de cada par.

- Actualizaciones de sistema previsto de Azure se despliegan las en regiones parejas secuencialmente, para reducir el tiempo de inactividad posible.

- Pares residen en el misma geografía, cumplir los requisitos de residencia de datos.

Sin embargo, asegúrese de que ambas regiones compatible con todos los servicios de Azure necesarios para la aplicación (vea [servicios por región][services-by-region]). Para obtener más información sobre los pares regionales, consulte [empresarial continuidad y recuperación ante desastres (BCDR): regiones de Azure emparejadas][regional-pairs].

### <a name="traffic-manager-configuration"></a>Configuración del Administrador de tráfico

Tenga en cuenta los siguientes puntos al configurar tráfico manager para su situación:

- **Enrutamiento.** Administrador de tráfico admite varios [algoritmos de enrutamiento][tm-routing]. Para el escenario descrito en este artículo, use la _prioridad_ enrutamiento (anteriormente denominado enrutamiento de _migración tras error_ ). Con esta configuración, Administrador de tráfico envía todas las solicitudes a la región principal, a menos que esté accesible el área principal. En ese momento, pasa automáticamente a la región secundaria. Vea [configurar de migración tras error enrutamiento método][tm-configure-failover].

- **Sondeo de estado.** Administrador de tráfico utiliza un [sondeo] de HTTP (o HTTPS)[ tm-monitoring] para supervisar la disponibilidad de cada región. El sondeo comprueba si hay una respuesta HTTP 200 para una dirección URL especificada. Como práctica recomendada, cree un extremo que informa del estado general de la aplicación y usar este extremo para el sondeo de estado. En caso contrario, el sondeo podría informar de un extremo "correcto" cuando en realidad se producen errores en las partes críticas de la aplicación. Para obtener más información, vea [Trama de supervisión de estado del extremo][health-endpoint-monitoring-pattern].

Cuando se produce un error en el Administrador de tráfico sobre, hay un período de tiempo cuando los clientes no pueden llegar a la aplicación, que puede ser varios minutos. Dos factores afectan a la duración total:

- Debe detectar el sondeo de estado que sea accesible el centro de datos principal.

- Los servidores DNS deben actualizar los registros DNS en caché para la dirección IP, que depende de la time to live (TTL de DNS). El TTL predeterminado es 300 segundos (5 minutos), pero puede configurar este valor cuando se crea el perfil de administrador de tráfico.

Para obtener más información, vea [Acerca de supervisar el tráfico administrador][tm-monitoring].

Si se produce un error en el Administrador de tráfico sobre, se recomienda realizar una recuperación manual, en lugar de volver automáticamente con errores. Compruebe que todos los subsistemas de aplicación están correcto en primer lugar. En caso contrario, puede crear una situación donde la aplicación voltea y hacia atrás entre los centros de datos.

De forma predeterminada, el tráfico administrador automáticamente no puede volver. Para evitar esto, disminuir manualmente la prioridad de la región principal después de una situación de error. Por ejemplo, supongamos que es el área principal prioridad 1 y el secundario es una prioridad de 2. Después de una migración tras error, establezca la región principal en prioridad 3, para evitar la recuperación automática. Cuando esté listo para volver, actualice la prioridad a 1.

El siguiente comando CLI Azure actualiza los valores de prioridad:

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --priority 3
```    

Otra manera de evitar biestable es deshabilitar temporalmente el punto final:

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --status Disabled
```    

Dependiendo de la causa de un error, puede que tenga los redploy los recursos dentro de una región. Si, antes de realizar una prueba de preparación operativa. La prueba debe comprobar cosas como:

- Máquinas virtuales están configuradas correctamente. (Todo el software necesario está instalado, IIS se está ejecutando, etcetera).

- Existen subsistemas de la aplicación.

- Prueba de funcionamiento. (Por ejemplo, el nivel de la base de datos es accesible desde el nivel de web).

### <a name="cassandra-deployment-across-multiple-regions"></a>Implementación de Casandra en diferentes regiones

Centros de datos de Casandra son divisiones de cargas de trabajo: un grupo de nodos relacionados configurados juntos en un clúster de separación de replicación y carga de trabajo.

Se recomienda [DataStax Enterprise] [ datastax] de producción usar. Para obtener más información sobre cómo ejecutar DataStax en Azure, consulte la [Guía de implementación de Enterprise DataStax de Azure][cassandra-in-azure]. Las siguientes recomendaciones generales se aplican a cualquier edición Casandra.

- Asignar una dirección IP pública a cada nodo. Esto permite que los clústeres para comunicarse a través de regiones mediante la infraestructura de Azure espina, proporcionando rendimiento alto a bajo coste.

- Proteger los nodos mediante el firewall adecuado y GSN configuraciones, que permite el tráfico únicamente a y desde los hosts conocidos, incluidos los clientes y otros nodos de clúster. Tenga en cuenta que Casandra usa puertos diferentes para las comunicaciones, OpsCenter, motor y así sucesivamente. Uso del puerto en Casandra, vea [Configurar el acceso de puerto de firewall][cassandra-ports].

- Uso del cifrado de SSL para todo el [nodo de cliente] [ ssl-client-node] y [nodo a otro] [ ssl-node-node] comunicaciones.

- Dentro de una región, siga las instrucciones de [recomendaciones Casandra](guidance-compute-n-tier-vm-linux.md#cassandra-recommendations).

## <a name="availability-considerations"></a>Consideraciones de disponibilidad

Con una aplicación de N niveles compleja, no tendrá que replicar toda la aplicación en la región secundaria. En su lugar, solo puede replicar un subsistema crítico que se necesita para admitir continuidad empresarial.

Administrador de tráfico es un punto de posibles errores en el sistema. Si se produce un error en el servicio, los clientes no pueden acceder a la aplicación durante el tiempo de inactividad. Revise el [Administrador de tráfico SLA][tm-sla]y determinar si con el Administrador de tráfico únicamente cumple los requisitos empresariales de alta disponibilidad. Si no es así, considere la posibilidad de agregar otra solución de administración de tráfico como una recuperación. Si el servicio Administrador de tráfico de Azure falla, cambie los registros CNAME en DNS para que apunten a otro servicio de administración de tráfico. (Este paso debe realizarse manualmente, y la aplicación no estará disponible hasta que se propagarán los cambios DNS).

Para el clúster Casandra, los escenarios de migración tras error para tener en cuenta dependen de los niveles de coherencia utilizados por la aplicación, así como el número de réplicas utilizado. Para niveles de coherencia y el uso de Casandra, consulte [Configurar coherencia de los datos] [ cassandra-consistency] y [Casandra: cuántos nodos están descritos a con quórum?][cassandra-consistency-usage] Disponibilidad de datos en Casandra viene determinada por el nivel de coherencia utilizado por la aplicación y el mecanismo de replicación. Para replicación en Casandra, consulte [La replicación de datos en explicación de las bases de datos NoSQL][cassandra-replication].

## <a name="manageability-considerations"></a>Consideraciones de capacidad de administración

Cuando actualiza la implementación, actualice una región a la vez, para reducir la posibilidad de un error global de una configuración incorrecta o un error en la aplicación.

Probar la resistencia del sistema para los errores. Estas son algunas situaciones de error comunes para probar:

- Cierre VM instancias.

- Recursos de presión como CPU y memoria.

- Red de desconectar o retraso.

- Bloqueo de procesos.

- Certificados de punto de expirar.

- Simular errores de hardware.

- Cierre el servicio DNS en los controladores de dominio.

Medir los tiempos de recuperación y compruebe que coincidan con los requisitos empresariales. Probar combinaciones de modos de error.

## <a name="next-steps"></a>Pasos siguientes

Esta serie se centra en implementaciones de nube puros. Escenarios empresariales a menudo requieren una red híbrida, conectarse a una red local con una red virtual Azure. Para obtener información sobre cómo crear dicha red híbrida, vea [implementar una arquitectura de red híbrida con Azure y VPN local][hybrid-vpn].

<!-- Links -->

[azure-sla]: https://azure.microsoft.com/support/legal/sla/
[cassandra-in-azure]: https://academy.datastax.com/resources/deployment-guide-azure
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[cassandra-consistency-usage]: https://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2
[cassandra-ports]: http://docs.datastax.com/en/latest-dse/datastax_enterprise/sec/secConfFirePort.html
[datastax]: https://www.datastax.com/products/datastax-enterprise
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[hybrid-vpn]: guidance-hybrid-network-vpn.md
[regional-pairs]: ../best-practices-availability-paired-regions.md
[resource groups]: ../azure-resource-manager/resource-group-overview.md
[resource-group-links]: ../resource-group-link-resources.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssl-client-node]: http://docs.datastax.com/en/cassandra/2.0/cassandra/security/secureSSLClientToNode_t.html
[ssl-node-node]: http://docs.datastax.com/en/cassandra/2.0/cassandra/security/secureSSLNodeToNode_t.html
[tablediff]: https://msdn.microsoft.com/en-us/library/ms162843.aspx
[tm-configure-failover]: ../traffic-manager/traffic-manager-configure-failover-routing-method.md
[tm-monitoring]: ../traffic-manager/traffic-manager-monitoring.md
[tm-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[tm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/en-us/services/traffic-manager/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vnet-dns]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[vnet-to-vnet]: ../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[wsfc]: https://msdn.microsoft.com/en-us/library/hh270278.aspx
[0]: ./media/blueprints/compute-multi-dc-linux.png "Arquitectura de red altamente disponible para las aplicaciones de Azure capas"
