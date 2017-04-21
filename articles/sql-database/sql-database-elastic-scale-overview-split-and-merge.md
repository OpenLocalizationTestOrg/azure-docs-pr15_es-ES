<properties 
    pageTitle="Mover datos entre las bases de datos de escala de salida de la nube | Microsoft Azure" 
    description="Explica cómo manipular shards y mover los datos a través de un servicio independiente con las API de base de datos elástico." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="moving-data-between-scaled-out-cloud-databases"></a>Mover datos entre las bases de datos de escala de salida de la nube

Si es un Software como desarrollador de servicios y repente por su aplicación pasa enorme demanda, es necesario acomodar el crecimiento. Para agregar más bases de datos (shards). ¿Cómo se redistribuir los datos a las bases de datos sin interrumpir la integridad de datos? Use la **herramienta de combinación de división** para mover datos de bases de datos restringidas a las bases de datos.  

La herramienta de combinación de división se ejecuta como un servicio web de Azure. Un administrador o desarrollador usa la herramienta para mover shardlets (datos de un fragmentar) entre diferentes bases de datos (shards). La herramienta utiliza Administración del mapa de partes para mantener la base de datos de metadatos del servicio y garantizar la coherencia asignaciones.

![Información general][1]

## <a name="download"></a>Descargar
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)


## <a name="documentation"></a>Documentación
1. [Tutorial de herramienta de base de datos elástico combinación de división](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
* [Configuración de seguridad de la combinación de división](sql-database-elastic-scale-split-merge-security-configuration.md)
* [Consideraciones de seguridad de la combinación de división](sql-database-elastic-scale-split-merge-security-configuration.md)
* [Administración del mapa de partes](sql-database-elastic-scale-shard-map-management.md)
* [Migrar bases de datos existentes a escalado](sql-database-elastic-convert-to-use-elastic-tools.md)
* [Herramientas de base de datos elásticas](sql-database-elastic-scale-introduction.md)
* [Elástico Glosario de herramientas de base de datos](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>¿Por qué usar la herramienta de combinación de división?

**Flexibilidad**

Aplicaciones necesitan estirar flexible más allá de los límites de una base de datos a una base de datos de SQL Azure. Use la herramienta para mover datos según sea necesario para nuevas bases de datos manteniendo la integridad.

**Dividir creciendo** 

Debe aumentar la capacidad total para manejar el aumento de explosión. Para ello, cree capacidad adicional por sharding los datos y si se distribuye entre incrementalmente más bases de datos hasta que se cumplen sus necesidades de capacidad. Este es un ejemplo de la función 'dividir'. 

**Combinación de reducir**

Capacidad necesita reducir debido a la naturaleza estacional de un negocio. La herramienta le permite escalar hacia abajo a menos unidades de escala cuando reduce la empresa. La característica 'combinación' en el servicio de combinación de división de escala elástico cubre este requisito. 

**Administrar los puntos de conexión al mover shardlets**

Con varios inquilinos por base de datos, puede provocar la asignación de shardlets a shards botella de capacidad en algunos shards. Esto requiere reasignación shardlets o mover shardlets ocupado a shards nuevos o menos utilizados. 

## <a name="concepts--key-features"></a>Conceptos y características clave

**Servicios alojados de cliente**

La combinación de división se ha entregado como un servicio hospedado de cliente. Debe implementar y alojar el servicio en su suscripción de Microsoft Azure. El paquete de que descarga de NuGet contiene una plantilla de configuración para completar la información para su implementación específica. Consulte el [tutorial de combinación de división](sql-database-elastic-scale-configure-deploy-split-and-merge.md) para obtener información detallada. Dado que el servicio se ejecuta en su suscripción de Azure, puede controlar y configurar la mayoría de los aspectos seguridad del servicio. La plantilla predeterminada incluye las opciones para configurar SSL, la autenticación de certificado de cliente, cifrado de credenciales almacenadas, DoS protección y las restricciones de IP. Puede encontrar más información sobre los aspectos de seguridad en la siguiente [configuración de seguridad de la combinación de la división](sql-database-elastic-scale-split-merge-security-configuration.md)de documento.

El valor predeterminado implementado se ejecuta el servicio con un trabajo y una función de web. Cada uno utiliza el tamaño de la máquina virtual de A1 en servicios de nube de Azure. Aunque no puede modificar esta configuración al implementar el paquete, se puede cambiar después de una implementación correcta en el servicio de nube ejecución (a través del portal de Azure). Tenga en cuenta que la función de trabajo no debe configurarse durante más de una sola instancia por razones técnicas. 

**Integración con el mapa fragmentar**

El servicio de combinación de división interactúa con el mapa de partes de la aplicación. Cuando se usa el servicio de combinación de división para dividir o combinar rangos o mover shardlets entre shards, el servicio mantiene automáticamente el mapa fragmentar actualizados. Para ello, el servicio se conecta a la base de datos de administrador de mapa de partes de la aplicación y mantiene rangos y asignaciones como el progreso de las solicitudes de combinación o división o mover. Así se garantiza que el mapa de partes siempre presenta una vista actualizada cuando va a cambiar las operaciones de combinación de división. Dividir, operaciones de movimiento de combinación y shardlet se implementan moviendo un lote de shardlets de las partes de origen a las partes de destino. Durante la shardlet operación de movimiento de la shardlets sujeto a la sección actual se marcan como sin conexión en el mapa de partes y no están disponible para las conexiones de enrutamiento de dependen de datos con la API **OpenConnectionForKey** . 

**Conexiones de shardlet coherente**

Cuando se inicia el movimiento de datos para un nuevo lote de shardlets, cualquier mapa fragmentar proporcionado dependen de datos enrutamiento las conexiones a las partes almacenar la shardlet es sacrificio y posteriores de la equivalencia de fragmentar las API de en estos shardlets están bloqueados mientras el movimiento de datos está en curso con el fin de evitar incoherencias. Conexiones a otros shardlets en la misma fragmentar también obtener se eliminado, pero se realizará correctamente nuevo inmediatamente al volver a intentarlo. Una vez que se mueve el lote, el shardlets se marcan online nuevamente para que las partes de destino y el origen de datos se quita de las partes de origen. El servicio va estos pasos para cada lote hasta que se han movido todos los shardlets. Esto llevará a varias operaciones de interrupción de conexión durante el curso de la operación Combinar o dividir o mover completa.  

**Administrar la disponibilidad de shardlet**

Limitar la conexión de eliminar el lote actual de shardlets como se indicó anteriormente restringe el ámbito de la falta de disponibilidad de un lote de shardlets a la vez. Esto es preferible un enfoque donde la fragmentar completa permanecerá sin conexión para todas sus shardlets durante el curso de una operación de división o combinación. El tamaño de un lote, definido como el número de distintas shardlets para desplazarse a la vez, es un parámetro de configuración. Puede definirse para cada operación de división y combinación según las necesidades de disponibilidad y el rendimiento de la aplicación. Tenga en cuenta que el rango que se está bloqueado en el mapa de fragmentar puede ser mayor que el tamaño de lote especificado. Esto es porque el servicio toma el tamaño del intervalo de forma que el número real de valores de clave de sharding en los datos aproximadamente coincida con el tamaño del lote. Esto es importante recordar en particular para claves de sharding apenas lleno. 

**Almacenamiento de metadatos**

El servicio de combinación de división usa una base de datos para mantener su estado y conservar los registros durante el proceso de solicitud. El usuario crea esta base de datos en su suscripción y proporciona la cadena de conexión para él en el archivo de configuración para la implementación de servicio. Los administradores de la organización del usuario también pueden conectarse a esta base de datos para revisar el progreso de la solicitud e investigar información detallada sobre posibles errores.

**Reconocimiento de sharding**

El servicio de combinación de división distingue entre tablas (1) sharded, las tablas de referencia (2) y las tablas (3) normales. La semántica de una operación de combinar o dividir o mover depende del tipo de la tabla utilizada y se define como sigue: 

* **Tablas sharded**: división, combinar y mover operaciones Mover shardlets de origen a partes de destino. Después de completar correctamente la solicitud general, esas shardlets ya no están presentes en el origen. Observe que las tablas de destino deben existir en las partes de destino y no deben contener datos en el rango de destino antes de procesamiento de la operación. 

* **Tablas de referencia**: tablas de referencia, la división, combinar y mover las operaciones copien los datos desde el origen de para las partes de destino. Sin embargo, tenga en cuenta que no se producen cambios en las partes de destino para una tabla dada si ya hay cualquier fila de esta tabla en el destino. La tabla debe estar vacía para cualquier operación de copia de tabla de referencia se procesan.

* **Otras tablas**: otras tablas pueden estar presentes en el origen o destino de una operación de división y combinación. El servicio de combinación de división omite estas tablas para los movimientos de datos y las operaciones de copia. Sin embargo, tenga en cuenta que pueden interferir con estas operaciones en el caso de las restricciones.

La información de referencia frente a tablas sharded se proporciona por **SchemaInfo** API en el mapa de partes. En el ejemplo siguiente se muestra el uso de estas API en un smm de objeto de administrador de mapa fragmentar determinado: 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

Las tablas 'región' y 'país' se definen como tablas de referencia y se copiarán con operaciones de combinación o división o mover. 'cliente' y 'pedidos' por separado se definen como tablas sharded. C_claveCliente y O_CUSTKEY son como la clave de sharding. 

**Integridad referencial**

El servicio de combinación de división analiza las dependencias entre las tablas y usa relaciones de clave principal y clave externa para las operaciones de movimiento de tablas de referencia y shardlets de la fase. En general, las tablas de referencia se copian primero en orden de dependencia y shardlets se copian en el orden de sus dependencias dentro de cada lote. Esto es necesario para que se respetan las restricciones de clave externa de clave principal en las partes de destino que llegan a los nuevos datos. 

**Fragmentar mapa coherencia y finalización final**

En presencia de errores, el servicio de combinación de división reanuda operaciones después de cualquier interrupción y pretende completar cualquiera de las peticiones de progreso. Sin embargo, es posible que no recuperables situaciones, por ejemplo, cuando se pierde o comprometido reparar las partes de destino. En esas circunstancias, puede seguir algunos shardlets que se supone que para desea mover residen en partes de origen. El servicio garantiza que solo se actualizan las asignaciones de shardlet después de copiar los datos necesarios para el destino. Shardlets solo se eliminan del origen de una vez todos sus datos se ha copiado en el destino y las asignaciones correspondientes se han actualizado correctamente. La operación de eliminación sucede en segundo plano mientras el intervalo ya está en línea en partes de destino. El servicio de combinación de división siempre garantiza la exactitud de las asignaciones almacenado en el mapa de partes.


## <a name="the-split-merge-user-interface"></a>La interfaz de usuario de la combinación de división

El paquete de servicio de la combinación de división incluye una función de trabajo y una función de web. El rol web se utiliza para enviar solicitudes de combinación de división de forma interactiva. Los componentes principales de la interfaz de usuario son las siguientes:

-    Tipo de operación: El tipo de operación es un botón de radio que controla el tipo de operación realizada por el servicio para esta solicitud. Puede elegir entre la división, combinar y mover escenarios. También puede cancelar una operación de enviados anteriormente. Puede usar la división, combinar y mover las solicitudes de mapas de partes de rango. Partes de la lista de mapas solo las operaciones de movimiento de soporte.

-    Mapa de partes: La siguiente sección de parámetros de solicitud cubra información sobre el mapa de partes y la base de datos que hospeda su mapa fragmentar. En particular, debe proporcionar el nombre del servidor de base de datos de SQL Azure y aloja el shardmap credenciales para conectarse a la base de datos del mapa de partes y, finalmente, el nombre de la equivalencia de partes de la base de datos. Actualmente, la operación sólo acepta un único conjunto de credenciales. Estas credenciales deben tener los permisos necesarios para realizar cambios en el mapa de partes, así como para los datos de usuario en el shards.

-    Rango de origen (dividir y combinar): una operación de división y combinación procesa un rango con su clave alto y bajo. Para especificar una operación con un valor de clave de alto ilimitado, active la casilla de verificación "clave de alta es max" y deje vacío el campo de clave de alto. Los valores de la clave del rango que especifique no es necesario que coincidan exactamente con una asignación y sus límites en el mapa de partes. Si no especifica los límites de intervalo en todo el servicio inferir automáticamente el rango más próximo para usted. Puede utilizar la secuencia de comandos de PowerShell GetMappings.ps1 para recuperar la actual en un mapa de fragmentar determinado.

-    Comportamiento de origen dividido (split): para operaciones de división, defina el punto para dividir el rango de origen. Para ello, proporcionando la clave de sharding donde desea realizar la división. Use el botón de radio para especificar si desea que la parte inferior del rango (excepto la clave de división) para mover o si desea que la parte superior para mover (incluido el dividido).

-    Origen Shardlet (mover): mover operaciones son diferentes de operaciones dividir o combinar como no requieren un rango para describir el origen. Un origen de para mover simplemente se identifica mediante el valor de clave de sharding que desea mover.

-    Fragmentar (split) de destino: una vez que haya proporcionado la información en el origen de la operación de división, debe definir dónde desea que los datos que se copiarán a proporcionando el nombre de servidor y base de datos de base de datos de SQL Azure para el destino.

-    Intervalo de destino (combinar): las operaciones de combinación mover shardlets a un fragmentar existente. Identificar las partes existente proporcionando los límites de intervalo del rango existente que desea combinar con.

-    Tamaño del lote: El tamaño del lote controla el número de shardlets que irá sin conexión en un momento durante el movimiento de datos. Esto es un valor entero que puede utilizar valores más pequeños cuando haya confidencial en largos períodos de tiempo de inactividad para shardlets. Valores mayores aumentan el tiempo que un determinado shardlet está sin conexión pero puede mejorar el rendimiento.

-    Id. de operación (cancelar): Si tiene una operación en curso que ya no es necesaria, puede cancelar la operación proporcionando su identificador de operación en este campo. Puede recuperar el identificador de la operación de la tabla de estado de la solicitud (consulte la sección 8.1) o desde el resultado en el explorador web donde se envió la solicitud.


## <a name="requirements-and-limitations"></a>Requisitos y limitaciones 

La implementación actual del servicio de combinación de división está sujeto a los siguientes requisitos y limitaciones: 

* La shards deben existir y registrarse en el mapa de fragmentar antes de poder realizar una operación de combinación de división en estos shards. 

* El servicio no crear tablas u otros objetos de base de datos automáticamente como parte de sus operaciones. Esto significa que deben existir en las partes de destino antes de cualquier operación de combinar o dividir o mover el esquema de sharded todas las tablas y las tablas de referencia. Tablas sharded en particular deben estar vacía en el rango donde están shardlets nuevas se agregue una operación de combinar o dividir o mover. En caso contrario, la operación falla la comprobación de coherencia inicial en partes de destino. Observe también que hacen referencia a datos sólo se copian si la referencia de tabla está vacía y que no hay ninguna garantía de coherencia en relación con otro simultánea escriben operaciones en las tablas de referencia. Se recomienda esto: cuando se ejecuta las operaciones de combinación o división, otras operaciones de escritura sin realizar cambios en las tablas de referencia.

* El servicio se basa en la identidad de fila establecido por un índice único o clave que incluye la clave de sharding para mejorar el rendimiento y la confiabilidad de grandes shardlets. Esto permite al servicio para mover los datos a un nivel de detalle implementado que simplemente el valor de clave de sharding. Esto ayuda a reducir la cantidad de espacio en el registro y bloqueos que son necesarios durante la operación. Considere la posibilidad de crear un índice único o una clave principal, incluida la clave de sharding en una tabla determinada si desea usar dicha tabla con las solicitudes de combinación o división o mover. Por motivos de rendimiento, la clave de sharding debe ser la columna inicial de la clave o el índice.

* Durante el curso de procesamiento de la solicitud, algunos datos de shardlet pueden presentar tanto en el origen y las partes de destino. Esto es necesario para protegerse frente a errores durante el movimiento de shardlet. La integración de combinación de división con el mapa de fragmentar asegura que las conexiones a través de los datos dependientes enrutamiento API del método **OpenConnectionForKey** en el mapa de partes no ve ningún estado intermedio incoherente. Sin embargo, cuando se conecta a la fuente o el shards de destino sin usar el método **OpenConnectionForKey** , estados intermedios incoherentes ya sea visibles cuando va a cambiar las solicitudes de combinación o división o mover. Estas conexiones pueden mostrar resultados parciales o duplicados según los intervalos o las partes de la conexión subyacente. Esta limitación incluye actualmente las conexiones realizadas por Shard múltiples escala elástico-consultas.

* No se debe compartir la base de datos de metadatos del servicio de combinación de división entre diferentes funciones. Por ejemplo, un rol del servicio de combinación de división que se ejecuta en ensayo necesita para que apunten a una base de datos de metadatos diferentes que la función de producción.
 

## <a name="billing"></a>Facturación 

Se ejecuta el servicio de combinación de división como un servicio de nube en su suscripción de Microsoft Azure. Por lo tanto, cargos para los servicios de nube se aplican a la instancia del servicio. A menos que realiza con frecuencia operaciones de combinación o división o mover, se recomienda que eliminar su servicio de nube de combinación de división. Que ahorra costos para ejecutar o implementar instancias de servicio de nube. Puede volver a implementar e iniciar la configuración puede ejecutar fácilmente siempre que tenga que realizar operaciones de dividir o combinar. 
 
## <a name="monitoring"></a>Supervisión 
### <a name="status-tables"></a>Tablas de estado 

El servicio de combinación de división proporciona la tabla de **estado de la solicitud** de los metadatos almacenar la base de datos para el seguimiento de las solicitudes en curso y completadas. La tabla muestra una fila para cada solicitud de combinación de división que se ha enviado a la instancia del servicio de combinación de división. Proporciona la información siguiente para cada solicitud:

* **Marca de tiempo**: la hora y fecha cuando se inició la solicitud.

* **OperationId**: un GUID que identifica la solicitud. Esta solicitud también se puede utilizar para cancelar la operación mientras está aún en curso.

* **Estado**: el estado actual de la solicitud. Para las solicitudes en curso, también muestra la fase actual en el que está la solicitud.

* **CancelRequest**: una marca que indica si se ha cancelado la solicitud.

* **Curso**: una estimación de porcentaje de finalización de la operación. Un valor de 50 indica que la operación es de aproximadamente un 50% completado.

* **Detalles**: valor XML que proporciona un informe de progreso más detallado. El informe de progreso se actualiza periódicamente como conjuntos de filas se copian desde el origen al destino. En caso de errores o excepciones, esta columna también incluye información más detallada sobre el error.


### <a name="azure-diagnostics"></a>Diagnóstico de Azure

El servicio de combinación de división usa basados en Azure SDK 2,5 para supervisar y diagnósticos de diagnósticos de Azure. Controlar la configuración de diagnósticos como se explica a continuación: [Habilitar diagnósticos en servicios de nube de Azure y máquinas virtuales de Windows](../cloud-services/cloud-services-dotnet-diagnostics.md). Descargue el paquete incluye dos configuraciones de diagnósticos: uno para la función web y otro para la función de trabajo. Estas configuraciones de diagnóstico para el servicio sigan las directrices de [Conceptos básicos de servicio de nube de Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Incluye las definiciones para registrar contadores de rendimiento, registros IIS, registros de eventos de Windows y registros de eventos de aplicación de la combinación de división. 

## <a name="deploy-diagnostics"></a>Implementar diagnósticos 

Para habilitar la supervisión y diagnóstico con la configuración de diagnóstico para los roles de web y trabajador proporcionados por el paquete NuGet, ejecute los comandos siguientes con PowerShell Azure: 

    $storage_name = "<YourAzureStorageAccount>" 
    
    $key = "<YourAzureStorageAccountKey" 
    
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  
    
    
    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 
    
    
    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

Puede encontrar más información acerca de cómo configurar e implementar la configuración de diagnósticos aquí: [Habilitar diagnósticos en servicios de nube de Azure y máquinas virtuales de Windows](../cloud-services/cloud-services-dotnet-diagnostics.md). 

## <a name="retrieve-diagnostics"></a>Recuperar diagnósticos 

Puede acceder fácilmente los diagnósticos desde el Explorador de servidor de Visual Studio en el elemento de árbol del explorador de servidor de Azure. Abrir una instancia de Visual Studio y, en la barra de menús, haga clic en vista y el Explorador de servidores. Haga clic en el icono de Azure para conectarse a su suscripción de Azure. A continuación, vaya a Azure -> almacenamiento -> <your storage account> -> tablas -> WADLogsTable. Para obtener más información, vea [Explorar los recursos de almacenamiento con el Explorador de servidores](http://msdn.microsoft.com/library/azure/ff683677.aspx). 

![WADLogsTable][2]

La WADLogsTable resaltado en la figura anterior contiene los eventos detallados de registro de la aplicación del servicio de combinación de división. Tenga en cuenta que la configuración predeterminada del paquete descargado está pensada para una implementación de producción. Por tanto el intervalo en el que se extraen registros y contadores de las instancias del servicio es grandes (5 minutos). Para pruebas y desarrollo, reduzca el intervalo ajustando la configuración de diagnósticos de la web o la función de trabajo a sus necesidades. Haga clic en el rol en el Explorador de servidor de Visual Studio (vea lo anterior) y, a continuación, ajustar el período de transferencia en el cuadro de diálogo Opciones de configuración de diagnósticos: 

![Configuración][3]


## <a name="performance"></a>Rendimiento

En general, mejor rendimiento es cabe esperar de mayor, más eficaz niveles de servicio en la base de datos de SQL Azure. Asignaciones de memoria, CPU y IO superior para los niveles de servicio superiores aprovecharse la copia masiva y eliminación las operaciones que usa el servicio de combinación de división. Por este motivo, aumentar el nivel de servicio únicamente para las bases de datos durante un período limitado, definido de tiempo.

El servicio también realiza las consultas de validación como parte de su funcionamiento normal. Estas consultas de validación Compruebe inesperados presencia de datos en el rango de destino y asegúrese de que cualquier operación de combinar o dividir o mover se inicia desde un estado coherente. Estas todas las consultas funcionan sobre los intervalos de clave sharding definidos por el ámbito de la operación y el tamaño del lote proporcionados como parte de la definición de la solicitud. Estas consultas mejor realizan cuando un índice está presente que tiene la clave sharding como la columna inicial. 

Además, una propiedad de exclusividad con la clave sharding como la columna inicial le permitirá al servicio que utilice un enfoque optimizado que limita el consumo de recursos en cuanto a memoria y espacio en el registro. Esta propiedad de exclusividad es necesario para mover los tamaños de datos de gran tamaño (normalmente por encima de 1GB). 

## <a name="how-to-upgrade"></a>Cómo actualizar

1. Siga los pasos de [implementar un servicio de combinación de división](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Cambiar el archivo de configuración de servicio de nube para la implementación de la combinación de división reflejar los nuevos parámetros de configuración. Un nuevo parámetro requerido es la información sobre el certificado utilizado para el cifrado. Una forma sencilla de hacerlo es comparar el nuevo archivo de plantilla de configuración de la descarga de la configuración actual. Asegúrese de que agrega la configuración de "DataEncryptionPrimaryCertificateThumbprint" y "DataEncryptionPrimary" para la web y la función de trabajo.
3. Antes de implementar la actualización de Azure, asegúrese de que han terminado todas las operaciones de combinación de división actualmente en ejecución. Puede hacerlo fácilmente consultando las tablas de estado de la solicitud y PendingWorkflows de la base de datos de combinación de división metadatos para las solicitudes en curso.
4. Actualizar la implementación de servicio de nube existente para la combinación de división en su suscripción de Azure con el nuevo paquete y su archivo de configuración de servicio actualizado.

No es necesario proporcionando una nueva base de datos de metadatos para dividir combinación actualizar. La nueva versión actualizará automáticamente la base de datos de metadatos existentes a la nueva versión. 

## <a name="best-practices--troubleshooting"></a>Prácticas recomendadas y solución de problemas
 
-    Definir a un inquilino de prueba y ejercicios las operaciones de combinación o división o mover más importantes con el inquilino de prueba a través de varias shards. Asegúrese de que todos los metadatos se ha definido correctamente en el mapa de partes y que las operaciones infringe restricciones o claves externas.
-    Mantener el inquilino de prueba problemas relacionados con el tamaño de los datos sobre el tamaño máximo de datos de su inquilino mayor para asegurarse de que no se encuentren con tamaño de los datos. Esto le permite evaluar un límite superior en el tiempo necesario para mover a un solo usuario. 
-    Asegúrese de que el esquema permite eliminaciones. El servicio de combinación de división requiere la capacidad de quitar los datos de las partes de origen una vez copiados los datos al destino. Por ejemplo, **Eliminar desencadenadores** puede impedir que el servicio de eliminación de los datos en el origen y puede provocar operaciones no se realice correctamente.
-    La clave de sharding debería la columna líder en la definición de índice único o clave principal. Asegura de que el mejor rendimiento para las consultas de validación dividir o combinar y para las operaciones de movimiento y eliminación de datos reales que siempre funcionan en intervalos de clave sharding.
-    Colocar su servicio de combinación de división en el centro de configuración regional y de datos donde residen las bases de datos. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]



<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
 
