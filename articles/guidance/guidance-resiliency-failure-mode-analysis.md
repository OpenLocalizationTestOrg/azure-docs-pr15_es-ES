<properties
   pageTitle="Análisis de modo de errores | Microsoft Azure"
   description="Directrices para la realización de análisis de modo de errores para soluciones de nube basadas en Azure."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="mwasson"/>

# <a name="azure-resiliency-guidance-failure-mode-analysis"></a>Orientación de la resistencia Azure: análisis de modo de errores

Análisis de modo de error (FMA) es un proceso para la creación de la resistencia en un sistema, identificando puntos posibles errores en el sistema. El FMA debe ser parte de las fases de diseño y arquitectura, para que se puede generar recuperación de errores en el sistema desde el principio.

Aquí es el proceso general para llevar a cabo una FMA: 

1. Identificar todos los componentes del sistema. Incluir dependencias externas, como proveedores de identidades, servicios de terceros y así sucesivamente.   

2. Para cada componente, identificar posibles errores que pueden ocurrir. Un solo componente puede tener más de un modo de error. Por ejemplo, debe tener en cuenta los errores de lectura y errores de escritura de por separado, porque la reducción de impacto y posibles será diferente.

3. Tasa de cada modo de error según su riesgo general. Considere estos factores:  

    - ¿Qué es la probabilidad de que el error. ¿Es relativamente común? ¿Extrememly poco comunes? No es necesario exactos números; sirve para ayudar a clasificar la prioridad.

    - ¿Cuál es el impacto en la aplicación, en términos de disponibilidad, pérdida de datos, costo monetario e interrupción del negocio? 

4. Determinar cómo se responder y recuperar la aplicación para cada modo de error. Considere la posibilidad de compensaciones en complejidad de costo y de aplicación.   

Como punto de partida para el proceso FMA, este artículo contiene un catálogo de posibles modos de error y sus soluciones. El catálogo está organizado por tecnología o servicio de Azure, además de una categoría general para el diseño de nivel de aplicación. El catálogo no es exhaustivo, pero se tratan muchos de los principales Azure servicios. 


## <a name="app-service"></a>Aplicación de servicio

### <a name="app-service-app-shuts-down"></a>Aplicación de servicio de la aplicación se cierra.

**Detección**. Posibles causas:

- Apagado esperado
    
    - Un operador cierra la aplicación; Por ejemplo, con el portal de Azure.

    - La aplicación se ha descargado ya estaba inactivo. (Solo si la `Always On` se deshabilita.)

- Cierre inesperado

    - Se bloquea la aplicación.

    - Una instancia de máquina virtual de servicio de aplicación no está disponible.


Registro de Application_End capturará el cierre del dominio de aplicación (bloqueo de proceso suaves) y es la única manera de capturar cierres del dominio de aplicación.

**Recuperación**

- Si se esperaba el cierre, use el evento de cierre de la aplicación de cerrarse correctamente. Por ejemplo, en ASP.NET, use la `Application_End` método.

- Si la aplicación se ha descargado mientras está inactivo, se reinicia automáticamente en la siguiente solicitud. Sin embargo, se producirá una "fría principio" costo. 

- Para impedir que la aplicación está descargando mientras está inactivo, habilitar la `Always On` configuración en la aplicación web. Vea [Configurar web apps en el servicio de aplicación de Azure][app-service-configure].

- Para evitar que un operador cerrando la aplicación, establezca un bloqueo de recurso con `ReadOnly` nivel. Consulte los [recursos de bloqueo con el Administrador de recursos de Azure][rm-locks].

- Si se bloquea la aplicación o una máquina virtual de aplicación de servicio no está disponible, servicio de aplicación reinicia automáticamente la aplicación. 


**Diagnósticos**. Registros de aplicaciones y los registros del servidor web. Vea [Habilitar registro de diagnósticos para aplicaciones web en la aplicación de servicio de Azure][app-service-logging].


### <a name="a-particular-user-repeatedly-makes-bad-requests-or-overloads-the-system"></a>Un usuario concreto repetidamente realiza solicitudes incorrectas o sobrecarga del sistema. 

**Detección**. Autenticar a los usuarios e incluya el identificador de usuario en registros de aplicaciones.

**Recuperación**

- Utilizar la [Administración de la API de Azure] [ api-management] a las solicitudes de acelerador del usuario. Consulte [solicitud avanzada límite con la administración de la API de Azure][api-management-throttling]

- Impedir que el usuario.

**Diagnósticos**. Iniciar todas las solicitudes de autenticación.


### <a name="a-bad-update-was-deployed"></a>Se ha implementado una actualización errónea.

**Detección**. Supervisar el estado de la aplicación a través del Portal de Azure (vea el [rendimiento de aplicación web de Azure Monitor][app-insights-web-apps]) o implementar el [patrón de supervisión de extremo de salud][health-endpoint-monitoring-pattern].

**Recuperación**. Usar varias [ranuras de implementación] [ app-service-slots] y volver a la implementación de la última configuración buena conocida. Para obtener más información, consulte [arquitectura de referencia de aplicación web básica][ra-web-apps-basic].


## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="openid-connect-oidc-authentication-fails"></a>Se produce un error en la autenticación OpenID conectar (OIDC).

**Detección**. Modos de error posibles incluyen:

1. Azure AD no está disponible, o no se puede llegar debido a un problema de red. Se produce un error en la redirección al extremo de autenticación y el software intermedio OIDC produce una excepción.
2. Azure inquilino AD no existe. Redireccionamiento al extremo de autenticación devuelve un código de error HTTP y el software intermedio OIDC produce una excepción.
3. No se puede autenticar el usuario. No hay estrategia de detección es necesario; Azure AD controla los errores de inicio de sesión.

**Recuperación**

1. Detectar excepciones no controladas desde el software intermedio.
2. Controlar `AuthenticationFailed` eventos.
3. Redirigir al usuario a una página de error.
4. Reintentos de usuario.


## <a name="azure-search"></a>Búsqueda de Azure

### <a name="writing-data-to-azure-search-fails"></a>No se puede escribir datos en la búsqueda de Azure.

**Detección**. Detectar `Microsoft.Rest.Azure.CloudException` errores.

**Recuperación**

La [Búsqueda .NET SDK] [ search-sdk] reintentos automáticamente tras transitorias errores. Las excepciones iniciadas por el cliente de SDK deben tratará como no transitorios errores.

La directiva predeterminada de reintento usa exponencial interrupción. Para utilizar una directiva de reintento diferente, llame a `SetRetryPolicy` en la `SearchIndexClient` o `SearchServiceClient` clase. Para obtener más información, vea [Reintentos automática][auto-rest-client-retry].

**Diagnósticos**. Usar [el análisis de tráfico de búsqueda][search-analytics].


### <a name="reading-data-from-azure-search-fails"></a>No se puede leer los datos de la búsqueda de Azure.

**Detección**. Detectar `Microsoft.Rest.Azure.CloudException` errores.

**Recuperación**

La [Búsqueda .NET SDK] [ search-sdk] reintentos automáticamente tras transitorias errores. Las excepciones iniciadas por el cliente de SDK deben tratará como no transitorios errores.

La directiva predeterminada de reintento usa exponencial interrupción. Para utilizar una directiva de reintento diferente, llame a `SetRetryPolicy` en la `SearchIndexClient` o `SearchServiceClient` clase. Para obtener más información, vea [Reintentos automática][auto-rest-client-retry].

**Diagnósticos**. Usar [el análisis de tráfico de búsqueda][search-analytics].



## <a name="cassandra"></a>Casandra


### <a name="reading-or-writing-to-a-node-fails"></a>Leer o escribir en un nodo produce un error.

**Detección**. Detectar la excepción. Para los clientes de .NET, normalmente será `System.Web.HttpException`. Otro cliente puede tener otros tipos de excepción.  Para obtener más información, vea [control de errores de Casandra bien](http://www.datastax.com/dev/blog/cassandra-error-handling-done-right).

**Recuperación**

- Cada [cliente Casandra](https://wiki.apache.org/cassandra/ClientOptions) posee su propio directivas de reintento. Para obtener más información, vea [control de errores de Casandra bien][cassandra-error-handling].
- Use una implementación de bastidor compatible, con nodos de datos que se distribuye entre los dominios de error.
- Implementar en varias áreas con coherencia de quórum local. Si se produce un error no transitorios, conmutar a otra región.

**Diagnósticos**. Registros de aplicación


## <a name="cloud-service"></a>Servicio de nube

### <a name="web-or-worker-roles-are-unexpectedlybeing-shut-down"></a>Funciones Web o de trabajo se se cierra inesperadamente.

**Detección**. La [RoleEnvironment.Stopping] [ RoleEnvironment.Stopping] se activa el evento.

**Recuperación**. Reemplazar el [RoleEntryPoint.OnStop] [ RoleEntryPoint.OnStop] método para limpiar correctamente. Para obtener más información, vea [La manera de derecha a controlar eventos de Azure OnStop] [ onstop-events] (blog).


## <a name="documentdb"></a>DocumentDB

### <a name="reading-data-from-documentdb-fails"></a>No se puede leer los datos de DocumentDB.

**Detección**. Detectar `System.Net.Http.HttpRequestException` o `Microsoft.Azure.Documents.DocumentClientException`. 

**Recuperación**

- El SDK de reintentos automáticamente intentos. Para establecer el número de reintentos y el tiempo de espera máximo, configurar `ConnectionPolicy.RetryOptions`. Las excepciones que genera el cliente están más allá de la directiva de reintento o no son errores transitorios. 

- Si DocumentDB limita al cliente, devuelve un error de HTTP 429. Compruebe el código de estado la `DocumentClientException`. Si obtiene el error 429 coherente, considere la posibilidad de aumentar el valor de rendimiento de la colección de DocumentDB.

- Replicar la base de datos de DocumentDB en dos o más regiones. Todas las réplicas son legibles. Mediante el SDK de cliente, especifique la `PreferredLocations` parámetro. Esta es una lista ordenada de Azure regiones. Todas las lecturas se enviarán a la primera área disponibles en la lista. Si falla la solicitud, el cliente intentará las demás regiones en la lista, en orden. Para obtener más información, vea [desarrollar con cuentas de región múltiples DocumentDB][docdb-multi-region].

**Diagnósticos**. Registrar todos los errores en el cliente.


### <a name="writing-data-to-documentdb-fails"></a>Escribir datos en DocumentDB se produce un error.

**Detección**. Detectar `System.Net.Http.HttpRequestException` o `Microsoft.Azure.Documents.DocumentClientException`. 

**Recuperación**

- El SDK de reintentos automáticamente intentos. Para establecer el número de reintentos y el tiempo de espera máximo, configurar `ConnectionPolicy.RetryOptions`. Las excepciones que genera el cliente están más allá de la directiva de reintento o no son errores transitorios. 

- Si DocumentDB limita al cliente, devuelve un error de HTTP 429. Compruebe el código de estado la `DocumentClientException`. Si obtiene el error 429 coherente, considere la posibilidad de aumentar el valor de rendimiento de la colección de DocumentDB.

- Replicar la base de datos de DocumentDB en dos o más regiones. Si se produce un error en la región principal, aparecerán otra región para escribir. También puede activar manualmente un error. El SDK realiza la detección automática y enrutamiento, por lo que el código de la aplicación continúa funcionando después de un error. Durante el período de migración (normalmente minutos), las operaciones de escritura tienen una latencia mayor, como el SDK encuentra la nueva área de escritura. Para obtener más información, vea [desarrollar con cuentas de región múltiples DocumentDB][docdb-multi-region].

- Como alternativa, se mantiene el documento a una cola de copia de seguridad y procesar la cola más adelante.

**Diagnósticos**. Registrar todos los errores en el cliente.


## <a name="elasticsearch"></a>Elasticsearch

### <a name="reading-data-from-elasticsearch-fails"></a>No se puede leer los datos de Elasticsearch.

**Detección**. Capturar la excepción adecuada para el [cliente Elasticsearch] en particular[ elasticsearch-client] que se usa. 

**Recuperación**

- Use un mecanismo de reintento. Cada cliente tiene sus propias directivas de reintento. 

- Implementar varios nodos Elasticsearch y usar la replicación de alta disponibilidad.

Para obtener más información, vea [Ejecutar Elasticsearch en Azure][elasticsearch-azure].

**Diagnósticos**. Puede usar las herramientas de supervisión para Elasticsearch o registrar todos los errores del cliente con la carga. Consulte la sección 'Supervisión' en [Elasticsearch ejecuta en Azure][elasticsearch-azure].


### <a name="writing-data-to-elasticsearch-fails"></a>Escribir datos en Elasticsearch se produce un error.

**Detección**. Capturar la excepción adecuada para el [cliente Elasticsearch] en particular[ elasticsearch-client] que se usa.  

**Recuperación**

- Use un mecanismo de reintento. Cada cliente tiene sus propias directivas de reintento. 
 
- Si la aplicación puede tolerar un nivel de coherencia reducida, considere la posibilidad de escritura con `write_consistency` de `quorum`.

Para obtener más información, vea [Ejecutar Elasticsearch en Azure][elasticsearch-azure].

**Diagnósticos**. Puede usar las herramientas de supervisión para Elasticsearch o registrar todos los errores del cliente con la carga. Consulte la sección 'Supervisión' en [Elasticsearch ejecuta en Azure][elasticsearch-azure].


## <a name="queue-storage"></a>Almacenamiento de cola

### <a name="writing-a-message-to-azure-queue-storage-fails-consistently"></a>Escribir un mensaje en cola de Azure almacenamiento correctamente de forma uniforme.

**Detección**. Después de *N* reintentos, la operación de escritura sigue sin funcionar.

**Recuperación**

- Almacenar los datos en una caché local y reenviar la escritura a almacenamiento más adelante, cuando el servicio esté disponible.
- Crear una cola secundaria y escribir en ese cola si la cola principal no está disponible.

**Diagnósticos**. Usar [métrica de almacenamiento][storage-metrics].


### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>La aplicación no puede procesar un mensaje concreto de la cola. 

**Detección**. Específico de la aplicación. Por ejemplo, el mensaje contiene datos no válidos, o la lógica empresarial falla por algún motivo. 

**Recuperación**

Mover el mensaje a una cola independiente. Ejecutar un proceso separado para examinar los mensajes en la cola.

Considere la posibilidad de usar colas de mensajería de Bus de servicio de Azure, que proporciona una [cola] [ sb-dead-letter-queue] funcionalidad para ello.

Nota: Si está utilizando colas de almacenamiento con WebJobs, el SDK de WebJobs proporciona control de mensajes dudosos integrados. Vea [cómo usar el almacenamiento de Azure cola con el SDK de WebJobs][sb-poison-message].

**Diagnósticos**. Usar el registro de aplicación.


## <a name="redis-cache"></a>Redis caché

### <a name="reading-from-the-cache-fails"></a>Se produce un error en la lectura de la memoria caché.

**Detección**. Detectar `StackExchange.Redis.RedisConnectionException`.

**Recuperación**

1. Vuelva a intentar en transitorias errores. Caché de Azure Redis admite reintento integrado vea [directrices de intentos de caché Redis][redis-retry].
2. Tratar errores no transitorios como un error de caché y volver al origen de datos.

**Diagnósticos**. Utilizar los [Diagnósticos de caché Redis][redis-monitor].


### <a name="writing-to-the-cache-fails"></a>No se puede escribir en la memoria caché.

**Detección**. Detectar `StackExchange.Redis.RedisConnectionException`.

**Recuperación**

1. Vuelva a intentar en transitorias errores. Caché de Azure Redis admite reintento integrado vea [directrices de intentos de caché Redis][redis-retry].
2. Si el error no es transitorio, omitirla y permitir que otras transacciones a escribir en la memoria caché más adelante.

**Diagnósticos**. Utilizar los [Diagnósticos de caché Redis][redis-monitor].


## <a name="sql-database"></a>Base de datos SQL

### <a name="cannot-connect-to-the-database-in-the-primary-region"></a>No puede conectarse a la base de datos en el área principal.

**Detección**. Se produce un error en la conexión.

**Recuperación**

Requisito previo: La base de datos debe estar configurado para la replicación de geo active. Consulte la [Base de datos activa Geo replicación de SQL][sql-db-replication].

- Para las consultas, lea desde una réplica secundaria. 
- Para las inserciones y las actualizaciones, manualmente conmutar a una réplica secundaria. Consulte [iniciar una migración tras error planificada o para la base de datos de SQL Azure][sql-db-failover].

La réplica usa una cadena de conexión diferente, por lo que deberá actualizar la cadena de conexión en la aplicación.


### <a name="client-runs-out-of-connections-in-the-connection-pool"></a>Cliente se ejecuta sin conexiones de la agrupación de conexiones.

**Detección**. Detectar `System.InvalidOperationException` errores. 

**Recuperación**

- Vuelva a intentar la operación.
- Como un plan de mitigación, aislar los grupos de conexión para cada caso de uso, para que un caso de uso no puede dominar todas las conexiones.
- Aumentar los grupos de conexión máxima.

**Diagnósticos**. Registros de aplicaciones.


### <a name="database-connection-limit-is-reached"></a>Se ha alcanzado el límite de conexión de base de datos. 

**Detección**. Base de datos de SQL Azure limita el número de trabajadores simultáneos, inicios de sesión y las sesiones. Los límites dependen de nivel de servicio. Para obtener más información, consulte [límites de recursos de base de datos de SQL Azure][sql-db-limits].

Para detectar estos errores, detectar `System.Data.SqlClient.SqlException` y compruebe el valor de `SqlException.Number` para el código de error SQL. Para obtener una lista de códigos de error relevantes, vea [códigos de error SQL para las aplicaciones cliente de base de datos de SQL: error de conexión y otros problemas de la base de datos][sql-db-errors].

**Recuperación**. Estos errores se consideran transitorios, para volver a intentar puede resolver el problema. Si presiona constantemente estos errores, considere la posibilidad de ajuste de escala de la base de datos.

**Diagnósticos**. -La [sys.event_log] [ sys.event_log] consulta devuelve conexiones correcta de la base de datos, errores de conexión y bloqueos.

- Crear una [regla de alerta] [ azure-alerts] para conexiones de errores.

- Habilitar la [auditoría de la base de datos SQL] [ sql-db-audit] y comprobar si hay inicios de sesión erróneos.


## <a name="service-bus-messaging"></a>Mensajería de Bus de servicio

### <a name="reading-a-message-from-a-service-bus-queue-fails"></a>No se puede leer un mensaje de una cola de Bus de servicio.

**Detección**. Detectar excepciones desde el SDK de cliente. La clase base para excepciones de Bus de servicio es [MessagingException][sb-messagingexception-class]. Si el error es transitorio, el `IsTransient` propiedad es true. 

Para obtener más información, vea [Bus de servicios de mensajería excepciones][sb-messaging-exceptions].

**Recuperación**

1. Vuelva a intentar en transitorias errores. Vea [Bus de servicio Reintentar directrices][sb-retry].

2. Los mensajes no se entregarán a cualquier destinatario se colocan en una *cola*. Use esta cola para ver los mensajes que no se pueden recibir. No hay ninguna limpieza automática de la cola. Mensajes permanecen allí hasta que los recupera explícitamente. Vea [información general de Bus de servicio colas][sb-dead-letter-queue].


### <a name="writing-a-message-to-a-service-bus-queue-fails"></a>Escribir un mensaje a un Bus de servicio cola produce un error.

**Detección**. Detectar excepciones desde el SDK de cliente. La clase base para excepciones de Bus de servicio es [MessagingException][sb-messagingexception-class]. Si el error es transitorio, el `IsTransient` propiedad es true. 

Para obtener más información, vea [Bus de servicios de mensajería excepciones][sb-messaging-exceptions].

**Recuperación**

1. El cliente de Bus de servicio reintentos automáticamente tras transitorios errores. De forma predeterminada, utiliza exponencial interrupción. Después de la cantidad máxima de reintentos o el período de tiempo de espera máximo, el cliente inicia una excepción. Para obtener más información, vea [Bus de servicio Reintentar directrices][sb-retry].

2. Si se ha superado la cuota de cola, el cliente produce [QuotaExceededException][QuotaExceededException]. El mensaje de excepción proporciona más información. Vacíe algunos mensajes de la cola antes de Reintentar y a continuación, piense en usar el modelo de disyuntor para evitar reintentos continuadas mientras se ha superado la cuota. Además, asegúrese de que la propiedad [BrokeredMessage.TimeToLive] no está establecida demasiado alta. 

3. Dentro de una región, se puede mejorar la resistencia mediante [colas divididas o temas][sb-partition]. Se asigna una cola sin particiones o un tema a un almacén de mensajería. Si este almacén mensajería no está disponible, se producirá un error en todas las operaciones en esa cola o tema. Una cola dividida o tema está dividida en varios stores mensajería. 

4. Resistencia adicional, cree dos espacios de nombres de Bus de servicio de diferentes regiones y replicar los mensajes. Puede usar replicación activa o replicación pasiva.

    - Replicación activa: el cliente envía todos los mensajes a ambas colas. El receptor escucha en dos colas. Etiquetar los mensajes con un identificador único, por lo que el cliente puede descartar mensajes duplicados.

    - Replicación pasiva: el cliente envía el mensaje a una cola. Si hay un error, el cliente pasa a la cola de otra. El receptor escucha en dos colas. Este enfoque reduce el número de mensajes duplicados que se envían. Sin embargo, el receptor debe controlar mensajes duplicados.

    Para obtener más información, vea [ejemplo de GeoReplication] [ sb-georeplication-sample] y [los procedimientos recomendados para aplicaciones aislantes contra interrupciones de Bus de servicio y problemas][sb-outages].


### <a name="duplicate-message"></a>Duplicar el mensaje.

**Detección**. Examine la `MessageId` y `DeliveryCount` propiedades del mensaje.

**Recuperación**

- Si es posible, diseñe el mensaje operaciones que sean idempotentes. En caso contrario, almacenar los identificadores de mensaje de los mensajes que ya se procesan y a continuación, compruebe el identificador antes de procesamiento de un mensaje.

- Habilitar la detección de duplicados, mediante la creación de la cola con `RequiresDuplicateDetection` establecida en true. Con esta configuración, Bus de servicio elimina automáticamente los mensajes que se envían con el mismo `MessageId` como un mensaje anterior.  Tenga en cuenta lo siguiente:

    -  Esta configuración impide que se coloca en la cola mensajes duplicados. Un receptor no impide el mismo mensaje de procesamiento de más de una vez.

    - Detección de duplicados tiene una ventana de tiempo. Si se ha enviado un duplicado más allá de esta ventana, no se detectan.

**Diagnósticos**. Inicie sesión mensajes duplicados.


### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>La aplicación no puede procesar un mensaje concreto de la cola. 

**Detección**. Específico de la aplicación. Por ejemplo, el mensaje contiene datos no válidos, o la lógica empresarial falla por algún motivo. 

**Recuperación**

Existen dos modos de error para tener en cuenta. 

- El receptor detecta el error. En este caso, puede mover el mensaje a la cola. Más adelante, ejecute un proceso separado para examinar los mensajes en la cola.

- Se produce un error en el receptor en el medio de procesamiento del mensaje &mdash; por ejemplo, debido a una excepción no controlada. Para controlar este caso, utilice `PeekLock` modo. En este modo, si el bloqueo caduca, el mensaje esté disponible para otros receptores. Si el mensaje supera el número máximo de entrega o el tiempo de vida, el mensaje se mueve automáticamente a la cola.

Para obtener más información, vea [información general de Bus de servicio colas][sb-dead-letter-queue].

**Diagnósticos**. Cada vez que la aplicación mueve un mensaje a la cola, escribir un evento en los registros de la aplicación.


## <a name="service-fabric"></a>Tela de servicio

### <a name="a-request-to-a-service-fails"></a>Se produce un error en una solicitud de un servicio.

**Detección**. El servicio devuelve un error.

**Recuperación**

- Buscar un proxy nuevo (`ServiceProxy` o `ActorProxy`) y llamar al método de servicio o actor nuevamente. 

- **Servicio de estado**. Ajustar operaciones en las colecciones fiables en una transacción. Si hay un error, la transacción se interrumpe. La solicitud, si extrae de una cola procesará nuevamente. 
 
- **Servicio de estado**. Si el servicio guarda los datos en un almacén externo, todas las operaciones deben estar idempotente.


**Diagnósticos**. Registro de la aplicación

### <a name="service-fabric-node-is-shut-down"></a>Se cierra el nodo de servicio tela.

**Detección**. Se pasa un token de cancelación del servicio `RunAsync` método. Servicio tela cancela la tarea antes de cerrar el nodo.

**Recuperación**. Utilice el token de cancelación para detectar apagado. Cuando la cancelación de solicitudes de servicio tela, Finalizar todo el trabajo y salir de `RunAsync` lo antes posible.

**Diagnósticos**. Registros de aplicación


## <a name="storage"></a>Almacenamiento de información

### <a name="writing-data-to-azure-storage-fails"></a>Escribir datos en el almacenamiento de Azure falla

**Detección**. El cliente recibe errores al escribir.

**Recuperación**

1. Vuelva a intentar la operación, recuperarse de errores transitorias. La [Directiva de reintento] [ Storage.RetryPolicies] en el cliente de SDK controla este aspecto automáticamente.
2. Implementar el patrón de disyuntor para evitar gran almacenamiento.
3. Si N reintentos fallan, realizar reserva correcta. Por ejemplo:

    - Almacenar los datos en una caché local y reenviar la escritura a almacenamiento más adelante, cuando el servicio esté disponible.
    - Si la acción de escritura se encontraba en un ámbito de transacciones, compensa la la transacción.

**Diagnósticos**. Usar [métrica de almacenamiento][storage-metrics].


### <a name="reading-data-from-azure-storage-fails"></a>No se puede leer datos desde el almacenamiento de Azure.

**Detección**. El cliente recibe errores al leer.

**Recuperación**

1. Vuelva a intentar la operación, recuperarse de errores transitorias. La [Directiva de reintento] [ Storage.RetryPolicies] en el cliente de SDK controla este aspecto automáticamente.
2. Para el almacenamiento de RA GRS, si se produce un error de lectura desde el extremo principal, intente lectura desde el extremo secundario. El SDK de cliente puede controlar esto automáticamente. Consulte [la replicación de almacenamiento de Azure][storage-replication].
3. Si *N* reintentos produce un error, realice una acción de reserva para apartar. Por ejemplo, si no se puede recuperar una imagen del producto de almacenamiento, mostrar una imagen de marcador de posición genérico.

**Diagnósticos**. Usar [métrica de almacenamiento][storage-metrics].


## <a name="virtual-machine"></a>Máquina virtual

### <a name="connection-to-a-backend-vm-fails"></a>Se produce un error en la conexión a un máquina virtual de back-end.

**Detección**. Errores de conexión de red.

**Recuperación**

- Implementar máquinas virtuales de al menos dos back-end de un conjunto de disponibilidad, detrás de un equilibrador de carga.

- Si el error de conexión es transitorio, a veces TCP correctamente intentará enviar el mensaje. 

- Implementar una directiva de reintento en la aplicación. 

- Errores de persistente o no transitorios, implementar el [disyuntor] [ circuit-breaker] trama.

- Si la máquina virtual llamada supera el límite de salida de la red, se llenará la cola de salida. Si la cola de salida es coherente completa, considere la posibilidad de escalado. 

**Diagnósticos**. Eventos de registro en los límites del servicio.

### <a name="vm-instance-becomes-unavailable-or-unhealthy"></a>Instancia de VM se convierte en mal estado o no disponible.

**Detección**. Configurar un equilibrador de carga del [sondeo de salud] [ lb-probe] que indica si la instancia VM es correcta. El sondeo debe comprobar si críticas responde correctamente. 

**Recuperación**. Para cada nivel de aplicación, colocar varias instancias VM en el mismo conjunto de disponibilidad y coloque un equilibrador de carga delante de las máquinas virtuales. Si se produce un error en la comprobación de estado, el equilibrador de carga deja de enviar nuevas conexiones a la instancia de mal estada.

**Diagnósticos**. -Usar [el análisis de registro]del equilibrador de carga[lb-monitor].
- Configurar el sistema de supervisión para supervisar todos los extremos de supervisión de estado.


### <a name="operator-accidentally-shuts-down-a-vm"></a>Operador cierra accidentalmente una máquina virtual.

**Detección**. N/A.

**Recuperación**. Establecer un bloqueo de recurso con `ReadOnly` nivel. Consulte los [recursos de bloqueo con el Administrador de recursos de Azure][rm-locks].

**Diagnósticos**. Usar [registros de actividad de Azure][azure-activity-logs].


## <a name="webjobs"></a>WebJobs

### <a name="continuous-job-stops-running-when-the-scm-host-is-idle"></a>Trabajo continuo dejará de ejecutarse cuando el host SCM está inactivo.

**Detección**. Pasar un token de cancelación a la función WebJob. Para obtener más información, vea [saliendo][web-jobs-shutdown]. 

**Recuperación**. Habilitar la `Always On` configuración en la aplicación web. Para obtener más información, vea [tareas de fondo ejecutar con WebJobs][web-jobs].


## <a name="application-design"></a>Diseño de la aplicación

### <a name="application-cant-handle-a-spike-in-incoming-requests"></a>Aplicación no puede controlar un aumento en las solicitudes entrantes.

**Detección**. Depende de la aplicación. Síntomas típicos:

- El sitio Web comienza a devolver códigos de error HTTP 5xx.
- Inician servicios dependientes, como base de datos o almacenamiento limitar las solicitudes. Buscar errores HTTP como HTTP 429 (demasiado muchas solicitudes), dependiendo del servicio.
- Longitud de la cola HTTP crece.

**Recuperación**

- Escalar para manejar el aumento de carga. 

- Reducir los errores para evitar errores en cascada interrumpir toda la aplicación. Estrategias de mitigación incluyen:

    - Implemente la [Limitación de trama] [ throttling-pattern] para evitar gran sistemas de back-end.
    - Usar [redistribución de carga basado en cola] [ queue-based-load-leveling] a búfer peticiones y procesar a un ritmo adecuado.
    - Priorizar a ciertos clientes. Por ejemplo, si la aplicación tiene niveles gratuitas y de pagadas, limitar los clientes en el nivel de gratuito, pero no pagados clientes. Ver [patrón de cola de prioridad][priority-queue-pattern].

**Diagnósticos**. Usar [el registro de diagnóstico de servicio de aplicación][app-service-logging]. Usar un servicio como [El análisis del registro de Azure][azure-log-analytics], [Aplicación perspectivas][app-insights], o [Relic nueva] [ new-relic] para ayudar a comprender los registros de diagnóstico.


### <a name="one-of-the-operations-in-a-workflow-or-distributed-transaction-fails"></a>Se produce un error en una de las operaciones en un flujo de trabajo o la transacción distribuida.

**Detección**. Después de *N* reintentos, sigue sin funcionar.

**Recuperación**

- Como un plan de mitigación, implementar el [Programador agente Supervisor] [ scheduler-agent-supervisor] trama para administrar el flujo de trabajo completo. 
- No volver a intentar en tiempos de espera. Hay una tasa de éxito baja para este error. 
- Trabajo de cola con el fin de volver a intentar más tarde.

**Diagnósticos**. Iniciar todas las operaciones (correctos y erróneas), incluida la compensación acciones. Use correlación identificadores, de modo que puede realizar un seguimiento de todas las operaciones en la misma transacción.


### <a name="a-call-to-a-remote-service-fails"></a>Se produce un error en una llamada a un servicio remoto.

**Detección**. Código de error HTTP.

**Recuperación**

1. Vuelva a intentar en transitorias errores. 
2. Si se produce un error en la llamada después de *N* intentos, realice una acción de reserva. (Aplicación específica).
3. Implementar el [patrón de disyuntor] [ circuit-breaker] para evitar errores en cascada. 

**Diagnósticos**. Inicie sesión en todos los errores de remoto de llamadas.


## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el proceso FMA, vea [resistencia de diseño de los servicios de nube] [ resilience-by-design-pdf] (descargar PDF).

<!-- links -->

[api-management]: https://azure.microsoft.com/documentation/services/api-management/
[api-management-throttling]: ../api-management/api-management-sample-flexible-throttling.md
[app-insights]: ../application-insights/app-insights-overview.md
[app-insights-web-apps]: ../application-insights/app-insights-azure-web-apps.md
[app-service-configure]: ../app-service-web/web-sites-configure.md
[app-service-logging]: ../app-service-web/web-sites-enable-diagnostic-log.md
[app-service-slots]: ../app-service-web/web-sites-staged-publishing.md
[auto-rest-client-retry]: https://github.com/Azure/autorest/blob/master/Documentation/clients-retry.md
[azure-activity-logs]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[azure-alerts]: ../monitoring-and-diagnostics/insights-alerts-portal.md
[azure-log-analytics]: ../log-analytics/log-analytics-overview.md
[BrokeredMessage.TimeToLive]: https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
[cassandra-error-handling]: http://www.datastax.com/dev/blog/cassandra-error-handling-done-right
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[docdb-multi-region]: ../documentdb/documentdb-developing-with-multiple-regions.md
[elasticsearch-azure]: guidance-elasticsearch-running-on-azure.md
[elasticsearch-client]: https://www.elastic.co/guide/en/elasticsearch/client/index.html
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[onstop-events]: https://azure.microsoft.com/blog/the-right-way-to-handle-azure-onstop-events/
[lb-monitor]: ../load-balancer/load-balancer-monitor-log.md
[lb-probe]: ../load-balancer/load-balancer-custom-probe-overview.md#learn-about-the-types-of-probes
[new-relic]: https://newrelic.com/
[priority-queue-pattern]: https://msdn.microsoft.com/library/dn589794.aspx
[queue-based-load-leveling]: https://msdn.microsoft.com/library/dn589783.aspx
[QuotaExceededException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx
[ra-web-apps-basic]: guidance-web-apps-basic.md
[redis-monitor]: ../redis-cache/cache-how-to-monitor.md
[redis-retry]: ../best-practices-retry-service-specific.md#cache-redis-retry-guidelines
[resilience-by-design-pdf]: http://download.microsoft.com/download/D/8/C/D8C599A4-4E8A-49BF-80EE-FE35F49B914D/Resilience_by_Design_for_Cloud_Services_White_Paper.pdf
[RoleEntryPoint.OnStop]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[RoleEnvironment.Stopping]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx
[rm-locks]: ../resource-group-lock-resources.md
[sb-dead-letter-queue]: ../service-bus-messaging/service-bus-dead-letter-queues.md
[sb-georeplication-sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/GeoReplication
[sb-messagingexception-class]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
[sb-messaging-exceptions]: ../service-bus-messaging/service-bus-messaging-exceptions.md
[sb-outages]: ../service-bus/service-bus-outages-disasters.md#protecting-queues-and-topics-against-datacenter-outages-or-disasters
[sb-partition]: ../service-bus-messaging/service-bus-partitioning.md
[sb-poison-message]: ../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#poison
[sb-retry]: ../best-practices-retry-service-specific.md#service-bus-retry-guidelines
[search-sdk]: https://msdn.microsoft.com/library/dn951165.aspx
[scheduler-agent-supervisor]: https://msdn.microsoft.com/library/dn589780.aspx
[search-analytics]: ../search/search-traffic-analytics.md
[sql-db-audit]: ../sql-database/sql-database-auditing-get-started.md
[sql-db-errors]: ../sql-database/sql-database-develop-error-messages.md#resource-governanance-errors
[sql-db-failover]: ../sql-database/sql-database-geo-replication-failover-portal.md
[sql-db-limits]: ../sql-database/sql-database-resource-limits.md
[sql-db-replication]: ../sql-database/sql-database-geo-replication-overview.md
[storage-metrics]: https://msdn.microsoft.com/library/dn782843.aspx
[storage-replication]: ../storage/storage-redundancy.md
[Storage.RetryPolicies]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.aspx
[sys.event_log]: https://msdn.microsoft.com/library/dn270018.aspx
[throttling-pattern]: https://msdn.microsoft.com/library/dn589798.aspx
[web-jobs]: ../app-service-web/web-sites-create-web-jobs.md
[web-jobs-shutdown]: ../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful

