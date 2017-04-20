<properties
   pageTitle="Aplicación web Scalable | Arquitectura de referencia de Azure | Microsoft Azure"
   description="Mejorar la escalabilidad en una aplicación web que se ejecuta en Microsoft Azure."
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
   ms.date="07/12/2016"
   ms.author="mwasson"/>


# <a name="improving-scalability-in-a-web-application"></a>Mejorar la escalabilidad en una aplicación web 

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artículo le muestra una arquitectura recomendada para mejorar el rendimiento de una aplicación web que se ejecutan en Microsoft Azure y escalabilidad. La arquitectura se basa en [arquitectura de referencia de Azure: aplicación web básica][basic-web-app]. Las recomendaciones y consideraciones de este artículo se aplican a esta arquitectura también.

>[AZURE.NOTE] Azure tiene dos diferentes modelos de implementación: el Administrador de recursos y clásico. Este artículo, se utiliza el Administrador de recursos, que Microsoft recomienda para implementaciones nuevas.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

![[0]][0]

La arquitectura tiene los siguientes componentes:

- **Grupo de recursos**. Un [grupo de recursos] [ resource-group] es un contenedor lógico para los recursos de Azure. 

- ** [Web app] [ app-service-web-app] ** y ** [aplicación API][app-service-api-app]**. Una aplicación moderna típica puede incluir un sitio Web y uno o más web REST API. Un web API se podría consumido por los clientes de explorador a través de AJAX, aplicaciones cliente nativo o aplicaciones de servidor. Por motivos de diseño web API, consulte [Guía de diseño de API][api-guidance].    

- **WebJob**. Usar [Azure WebJobs] [ webjobs] para ejecutar las tareas de ejecución larga en segundo plano. Puede ejecutar WebJobs en una programación, continuamente, o en respuesta a un desencadenador, como colocar un mensaje de una cola. Una WebJob se ejecuta como un proceso de fondo en el contexto de una aplicación de servicio de aplicación. 

- **Cola**. En la arquitectura que se muestra aquí, las colas de aplicación tareas en segundo plano colocando un mensaje en una [Cola de almacenamiento de Azure] [ queue-storage] cola. El mensaje activa una función en la WebJob. 

    Como alternativa, puede usar colas de Bus de servicio. Para ver una comparación, vea [colas de Azure y Bus de servicio - en comparación y frente][queues-compared].

- **Caché**. Almacenar datos estáticos semiestructurados en [Caché de Azure Redis][azure-redis].  

- **CDN**. Usar la [Red de entrega de contenido de Azure] [ azure-cdn] (CDN) para el contenido esté disponible públicamente caché, para entrega rápida de contenido y latencia inferior.

- **Almacenamiento de datos.** Usar la [Base de datos de SQL Azure] [ sql-db] de datos relacionales. Datos no relacionales, considere la posibilidad de un almacén, como almacenamiento de tablas de Azure o [DocumentDB][documentdb].

- **Búsqueda de azure**. Use la [Búsqueda de Azure] [ azure-search] para agregar la funcionalidad de búsqueda, que incluye sugerencias de búsqueda, búsqueda parcial y búsqueda específicos de idioma. Búsqueda de Azure normalmente se usa junto con otro almacén de datos, especialmente si el almacén de datos principal requiere coherencia estricta. En este enfoque haría con los datos relevantes en el otro almacén de datos y colocar el índice de búsqueda en la búsqueda de Azure. Búsqueda de Azure también puede utilizarse para consolidar un índice único de búsqueda de varios almacena datos.  

- **Correo electrónico o SMS**. Si la aplicación se necesita enviar correo electrónico o mensajes SMS, use un servicio de terceros como SendGrid o Twilio, en lugar de crear esta funcionalidad directamente en la aplicación.

## <a name="recommendations"></a>Recomendaciones

### <a name="app-service-apps"></a>Aplicaciones de servicio de aplicaciones 

Recomendamos crear la aplicación web y la API de web como aplicaciones de servicio de aplicación independientes. Este diseño permite ejecutarlos en planes de servicio de aplicación independientes, que a su vez le permite escalar de manera independiente. Si no necesita ese nivel de escalabilidad en primer lugar, puede implementar las aplicaciones en el mismo plan y muévalos a un plan independiente más adelante, si es necesario. (Para los planes básico, estándar y Premium, de facturación de las instancias VM en el plan, no por la aplicación. Ver [tarifas de servicio de aplicación][app-service-pricing])

Si va a usar las características de *Las API de fácil* de aplicaciones de aplicación de servicio móvil o *Tablas fácil* , cree una aplicación de servicio de aplicación independiente para ello.  Estas características se basan en un marco de aplicación específica para habilitarlos.

### <a name="webjobs"></a>WebJobs

Si la WebJob es recursos, considere la posibilidad de implementarlo en una aplicación de servicio de aplicación vacía dentro de un plan de servicio de aplicación independiente, para proporcionar instancias dedicados a la WebJob. Consulte la [Guía de trabajos de fondo][webjobs-guidance].  

### <a name="cache"></a>Caché

Puede mejorar el rendimiento y escalabilidad utilizando [Azure Redis caché] [ azure-redis] a algunos datos en caché. Considere la utilización de caché Redis para:

- Datos de transacción semiestructurados estático.

- Estado de la sesión.

- Resultado HTML. Esto puede ser útil en las aplicaciones que representan los resultados HTML complejo. 

Para obtener más instrucciones sobre cómo diseñar una estrategia de caché, consulte [almacenamiento en caché instrucciones][caching-guidance].

### <a name="cdn"></a>CDN 

Usar [CDN Azure] [ azure-cdn] en contenido estático de caché. La ventaja principal de una CDN es reducir la latencia de los usuarios, porque el contenido se almacena en un *servidor perimetral* geográfico más cercano al usuario. CDN también puede reducir la carga de la aplicación, porque la aplicación no controla ese tráfico.

- Si su aplicación consta principalmente de páginas estáticas, considere usar CDN para la aplicación completa en caché. Ver [usar CDN Azure en Azure de aplicación de servicio][cdn-app-service].

- En caso contrario, poner contenido estático, como imágenes, CSS y HTML, archivos, en el almacenamiento de Azure y utilice CDN para almacenar los archivos en caché. Vea [integrar una cuenta de almacenamiento con CDN][cdn-storage-account].

> [AZURE.NOTE] CDN Azure no puede servir contenido que requiere autenticación.

Para obtener más instrucciones, consulte [Guía de red de entrega de contenido (CDN)][cdn-guidance]. 

### <a name="storage"></a>Almacenamiento de información

Las aplicaciones modernas a menudo procesan grandes cantidades de datos. Para cambiar la escala de la nube, es importante elegir el tipo de almacenamiento derecha. Estas son algunas recomendaciones de línea base.  Para obtener más instrucciones, consulte [Evaluar capacidades de almacén de datos para soluciones de persistencia Polyglot][polyglot-storage].

¿Qué desea almacenar | Ejemplo | Almacenamiento recomendada
--- | --- | ---
Archivos | Imágenes, documentos, archivos PDF | Almacenamiento de blobs de Windows Azure
Pares clave/valor | Datos de perfil de usuario buscados por ID de usuario | Almacenamiento de tablas de Azure
Mensajes cortos pretenden desencadenar procesamiento | Solicitudes de pedido | Almacenamiento de Azure cola, cola de Bus de servicio o tema de Bus de servicio
Datos no relacionales, con un esquema flexible, que requieren consultas básicas | Catálogo de productos | Base de datos de documento, como DocumentDB de Azure, MongoDB o Apache CouchDB
Datos relacionales, que requieren más compatibilidad para consultas, el esquema estricto o la coherencia fuerte | Inventario de productos | Base de datos SQL Azure

## <a name="scalability-considerations"></a>Consideraciones de escalabilidad

### <a name="app-service-app"></a>Aplicación de servicio de aplicaciones

Si su solución incluye varias aplicaciones de servicio de la aplicación, considere la posibilidad de implementar para separar los planes de servicio de aplicación. Este enfoque permite escalar ellos por separado, porque se ejecutan en instancias independientes. Para obtener más información sobre el escalado, consulte las [Consideraciones de escalabilidad] [ basic-web-app-scalability] sección en la [arquitectura de la aplicación web básica][basic-web-app].

Del mismo modo, considere la posibilidad de colocar un WebJob en su propio plan, para que no se ejecuten en las mismas instancias que controlan las solicitudes HTTP tareas en segundo plano.  

### <a name="sql-database"></a>Base de datos SQL

Aumentar la escalabilidad de una base de datos SQL en *sharding* la base de datos &mdash; es decir, horizontalmente la partición la base de datos. Sharding le permite escalar horizontalmente, la base de datos con [Herramientas de base de datos elástico][sql-elastic]. Posibles ventajas de sharding incluyen:

- Mejor rendimiento de las transacciones.

- Las consultas se ejecutan más rápidamente en un subconjunto de los datos. 

### <a name="azure-search"></a>Búsqueda de Azure

Búsqueda de Azure quita la carga de realizar búsquedas de datos complejos del almacén de datos principal y, a continuación, puede ampliarse para controlar la carga. Ver los [niveles de recursos de escala de consulta y las cargas de trabajo de indización de búsqueda de Azure][azure-search-scaling].

## <a name="security-considerations"></a>Consideraciones de seguridad

### <a name="cross-origin-resource-sharing-cors"></a>Recursos de origen entre uso compartido (CORS)

Si crea un sitio Web y web API como aplicaciones independientes, el sitio Web no puede realizar llamadas de AJAX del cliente a la API a menos que habilite CORS. 

> [AZURE.NOTE] Seguridad del explorador impide que una página web que hacen solicitudes de AJAX a otro dominio. Esta limitación se denomina la directiva de mismo origen y evita que un sitio malintencionado lea sentitive datos de otro sitio. CORS es un estándar de W3C que permite a un servidor para relajarse la directiva de mismo origen y permitir algunas solicitudes entre origen y rechazar los otros usuarios.

Servicios de aplicación tiene compatibilidad integrada para CORS, sin necesidad de escribir el código de la aplicación. Vea [Consume una aplicación de la API de JavaScript mediante CORS][cors]. Agregar el sitio Web a la lista de orígenes permitidos para la API. 

### <a name="sql-database-encryption"></a>Cifrado de base de datos SQL

Usar [Cifrado de datos transparente] [ sql-encryption] si necesita cifrar datos almacenados en la base de datos. Esta característica realiza cifrado en tiempo real y descifrar una base de datos completa (incluidas las copias de seguridad y archivos de registro de transacciones), sin necesidad de cambios en la aplicación. Cifrado agregar cierta latencia, por lo que es una buena práctica para separar los datos que se deben segura en su propia base de datos y habilitar el cifrado solo para dicha base de datos.  

## <a name="next-steps"></a>Pasos siguientes

- Para una mayor disponibilidad, implementar la aplicación en más de una región y usar el [Administrador de tráfico de Azure] [ tm] para la migración tras error. Para obtener más información, vea [arquitectura de referencia de Azure: aplicación Web con alta disponibilidad][web-app-multi-region].    

<!-- links -->

[api-guidance]: ../best-practices-api-design.md
[app-service-web-app]: ../app-service-web/app-service-web-overview.md
[app-service-api-app]: ../app-service-api/app-service-api-apps-why-best-platform.md
[app-service-pricing]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[azure-cdn]: https://azure.microsoft.com/en-us/services/cdn/
[azure-redis]: https://azure.microsoft.com/en-us/services/cache/
[azure-search]: https://azure.microsoft.com/en-us/documentation/services/search/
[azure-search-scaling]: ../search/search-capacity-planning.md
[background-jobs]: ../best-practices-background-jobs.md
[basic-web-app]: guidance-web-apps-basic.md
[basic-web-app-scalability]: guidance-web-apps-basic.md#scalability-considerations 
[caching-guidance]: ../best-practices-caching.md
[cdn-app-service]: ../app-service-web/cdn-websites-with-cdn.md
[cdn-storage-account]: ../cdn/cdn-create-a-storage-account-with-cdn.md
[cdn-guidance]: ../best-practices-cdn.md
[cors]: ../app-service-api/app-service-api-cors-consume-javascript.md
[documentdb]: https://azure.microsoft.com/en-us/documentation/services/documentdb/
[polyglot-storage]: https://github.com/mspnp/azure-guidance/blob/master/Polyglot-Solutions.md
[queue-storage]: ../storage/storage-dotnet-how-to-use-queues.md
[queues-compared]: ../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md
[resource-group]: ../resource-group-overview.md
[sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[sql-elastic]: ../sql-database/sql-database-elastic-scale-introduction.md
[sql-encryption]: https://msdn.microsoft.com/en-us/library/dn948096.aspx
[tm]: https://azure.microsoft.com/en-us/services/traffic-manager/
[web-app-multi-region]: ./guidance-web-apps-multi-region.md
[webjobs-guidance]: ../best-practices-background-jobs.md
[webjobs]: ../app-service/app-service-webjobs-readme.md
[0]: ./media/blueprints/paas-web-scalability.png "Aplicación Web de Azure con escalabilidad mejorada"