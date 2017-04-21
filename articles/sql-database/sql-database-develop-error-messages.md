<properties
    pageTitle="Códigos de error SQL: error de conexión de base de datos | Microsoft Azure"
    description="Obtenga más información sobre códigos de error SQL para las aplicaciones de cliente de base de datos SQL, como los errores comunes de conexión de base de datos, problemas de copia de la base de datos y errores generales. "
    keywords="código de error de SQL, access sql, error de conexión de base de datos, códigos de error de sql"
    services="sql-database"
    documentationCenter=""
    authors="annemill"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="annemill"/>


# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-error-and-other-issues"></a>Códigos de error SQL para las aplicaciones cliente de base de datos de SQL: error de conexión y otros problemas de la base de datos


<!--
Old Title on MSDN:  Error Messages (Azure SQL Database)
ShortId on MSDN:  ff394106.aspx
Dx 4cff491e-9359-4454-bd7c-fb72c4c452ca
-->


Este artículo enumeran los códigos de error SQL para las aplicaciones cliente de base de datos de SQL, incluidos errores de conexión de base de datos, transitorios errores (también denominados errores transitorias), errores de gobernanza de recursos, problemas de copia de la base de datos, grupo elástica y otros errores. La mayoría de categorías están de la base de datos de SQL Azure y no se aplican a Microsoft SQL Server.

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Errores de conexión de base de datos, errores transitorios y otros errores temporales

La tabla siguiente describen los códigos de error SQL de errores de pérdida de conexión y otros errores transitorias que pueden surgir cuando la aplicación intenta obtener acceso a la base de datos de SQL. Para obtener tutoriales de introducción sobre cómo conectarse a la base de datos de SQL Azure, vea [Conectar con la base de datos de SQL Azure](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Errores de conexión de base de datos más comunes y transitorias errores

La infraestructura de Azure tiene la posibilidad de configurar dinámicamente servidores cuando surgen cargas de trabajo en el servicio de base de datos de SQL.  Este comportamiento dinámico puede provocar que el programa cliente pierda la conexión a la base de datos de SQL. Este tipo de condición de error se denomina *error transitoria*.

Si el programa cliente tiene lógica de reintento, puede intentar restablecer una conexión después de darle el tiempo de errores transitorias corrija solo.  Se recomienda retrasar durante 5 segundos antes de su primer reintento. Reintentar después de un retraso inferior a una sobrecarga del servicio de nube de riesgos de 5 segundos. Para cada intentos subsiguientes el retraso debe crecer exponencial, hasta un máximo de 60 segundos.

Errores de transitorias normalmente la información como uno de los siguientes mensajes de error de los programas de cliente:

- Base de datos < db_name > servidor < Azure_instance > no está disponible actualmente. Vuelva a intentar la conexión más adelante. Si el problema continúa, póngase en contacto con soporte al cliente y proporcionarles el identificador de seguimiento de la sesión de < session_id >

- Base de datos < db_name > servidor < Azure_instance > no está disponible actualmente. Vuelva a intentar la conexión más adelante. Si el problema continúa, póngase en contacto con soporte al cliente y proporcionarles el identificador de seguimiento de la sesión de < session_id >. (Microsoft SQL Server, Error: 40613)

- El host remoto cerró forzar una conexión existente.

- System.Data.Entity.Core.EntityCommandExecutionException: Error al ejecutar la definición de comando. Vea la excepción interna para obtener más detalles. ---> System.Data.SqlClient.SqlException: ha ocurrido un error de nivel de transporte al recibir resultados desde el servidor. (proveedor: proveedor de sesión, error: 19 - conexión física no es utilizable)

Para obtener ejemplos de código de lógica de reintento, consulte:

- [Bibliotecas de conexión de base de datos SQL y SQL Server](sql-database-libraries.md) 
- [Acciones para solucionar errores de conexión y transitorias de base de datos de SQL](sql-database-connectivity-issues.md)

Una discusión del *período de bloqueo* para los clientes que utilizan ADO.NET está disponible en [SQL Server conexión agrupación (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Códigos de error de errores transitorias

Los siguientes errores son transitorios y reintentos de lógica de la aplicación 

| Código de error | Gravedad | Descripción |
| ---: | ---: | :--- |
| 4060 | 16 | No se puede abrir la base de datos "% & #x2a; ls" solicitada por el inicio de sesión. Error en el inicio de sesión. |
|40197|17|El servicio ha encontrado un error al procesar su solicitud. Vuelva a intentarlo. Código de error %d.<br/><br/>Recibirá este error cuando el servicio está cerrado debido a software o actualizaciones de hardware, errores de hardware u otros problemas de migración tras error. El código de error (%d) incrustado en el mensaje de error 40197 proporciona información adicional sobre el tipo de error o migración tras error que se ha producido. Algunos ejemplos del error códigos están incrustados en el mensaje de error 40197 son 40020, 40143, 40166 y 40540.<br/><br/>Volver a conectarse a su servidor de base de datos SQL automáticamente le conectará a una copia correcta de la base de datos. La aplicación debe detectar el registro de errores de 40197, el código de error incrustado (%d) en el mensaje para solucionar este problema y vuelva a conectar a la base de datos SQL hasta los recursos están disponibles y, a continuación, vuelva a establecer la conexión.|
|40501|20|El servicio está ocupado. Después de 10 segundos, vuelva a intentar la solicitud. Id. de incidente: %ls. Código: %d.<br/><br/>*Nota:* Para obtener más información, consulte:<br/>• [Los límites de recursos de base de datos de SQL azure](sql-database-resource-limits.md).
|40613|17|Base de datos '%. & #x2a; ls' en el servidor '% & #x2a; ls' no está disponible actualmente. Vuelva a intentar la conexión más adelante. Si el problema continúa, póngase en contacto con soporte al cliente y proporcionarles un identificador de seguimiento '% & #x2a; ls'.|
|49918|16|No puede procesar la solicitud. No hay suficiente recursos para procesar la solicitud.<br/><br/>El servicio está ocupado. Vuelva a intentar la solicitud más tarde. |
|49919|16|No puede proceso crear o actualizar la solicitud. Demasiado muchos crear o actualizar las operaciones en curso para suscripción "% ld".<br/><br/>El servicio está ocupado procesamiento varios crear o actualizar las solicitudes de la suscripción o el servidor. Las solicitudes están bloqueadas para la optimización de recursos. Consultar [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para operaciones pendientes. Espere hasta que la pendiente de crear o actualizar solicitudes están completos o eliminar una de las solicitudes pendientes y vuelva a intentar la solicitud más adelante. |
|49920|16|No puede procesar la solicitud. Demasiadas operaciones en curso para suscripción "% ld".<br/><br/>El servicio está ocupado procesando varias solicitudes para esta suscripción. Las solicitudes están bloqueadas para la optimización de recursos. [Sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) estado de la operación de consulta. Espere hasta que las solicitudes pendientes están completo o eliminar una de las solicitudes pendientes y vuelva a intentar la solicitud más adelante. |

## <a name="database-copy-errors"></a>Errores de copia de la base de datos

Pueden encontrar los siguientes errores al copiar una base de datos en la base de datos de SQL Azure. Para obtener más información, vea [Copiar una base de datos de SQL Azure](sql-database-copy.md).


|Código de error|Gravedad|Descripción|
|---:|---:|:---|
|40635|16|Cliente con la dirección IP '% & #x2a; ls' está deshabilitado temporalmente.|
|40637|16|Crear copia de la base de datos está actualmente deshabilitado.|
|40561|16|Error de copia de la base de datos. Base de datos de origen o de destino no existe.|
|40562|16|Error de copia de la base de datos. Se ha eliminado la base de datos de origen.|
|40563|16|Error de copia de la base de datos. Se ha eliminado la base de datos de destino.|
|40564|16|Error de copia de la base de datos debido a un error interno. Eliminar base de datos de destino y vuelva a intentarlo.|
|40565|16|Error de copia de la base de datos. Se permite no más de 1 copia simultánea de la base de datos de la misma fuente. Vuelva anular base de datos de destino e inténtelo de nuevo más tarde.|
|40566|16|Error de copia de la base de datos debido a un error interno. Eliminar base de datos de destino y vuelva a intentarlo.|
|40567|16|Error de copia de la base de datos debido a un error interno. Eliminar base de datos de destino y vuelva a intentarlo.|
|40568|16|Error de copia de la base de datos. Base de datos de origen de estar disponible. Eliminar base de datos de destino y vuelva a intentarlo.|
|40569|16|Error de copia de la base de datos. Base de datos de destino de estar disponible. Eliminar base de datos de destino y vuelva a intentarlo.|
|40570|16|Error de copia de la base de datos debido a un error interno. Vuelva anular base de datos de destino e inténtelo de nuevo más tarde.|
|40571|16|Error de copia de la base de datos debido a un error interno. Vuelva anular base de datos de destino e inténtelo de nuevo más tarde.|

## <a name="resource-governance-errors"></a>Errores de gobernanza de recursos

Los siguientes errores son causados por un uso excesivo de recursos mientras trabaja con la base de datos de SQL Azure. Por ejemplo:

- Una transacción ha estado abierta demasiado tiempo.
- Una transacción mantiene demasiados bloqueos.
- Una aplicación consumo demasiada memoria.
- Una aplicación se consumo demasiado `TempDb` espacio.

Temas relacionados:

* Obtener información detallada está disponible aquí: [límites de recursos de base de datos de SQL Azure](sql-database-resource-limits.md)

|Código de error|Gravedad|Descripción|
|---:|---:|:---|
|10928|20|Id. de recurso: %d. El límite de %s para la base de datos es %d y se ha alcanzado. Para obtener más información, consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>El identificador de recurso indica el recurso que se ha alcanzado el límite. Para subprocesos de trabajo, el identificador del recurso = 1. Para las sesiones, el identificador del recurso = 2.<br/><br/>*Nota:* Para obtener más información acerca de este error y cómo solucionarlo, vea:<br/>• [Los límites de recursos de base de datos de SQL azure](sql-database-resource-limits.md). |
|10929|20|Id. de recurso: %d. La garantía mínima de %s es %d, el límite máximo es %d y el uso de la base de datos actual es %d. Sin embargo, el servidor actualmente está demasiado ocupado para admitir solicitudes mayores que %d para esta base de datos. Para obtener más información, consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). En caso contrario, vuelva a intentarlo más tarde.<br/><br/>El identificador de recurso indica el recurso que se ha alcanzado el límite. Para subprocesos de trabajo, el identificador del recurso = 1. Para las sesiones, el identificador del recurso = 2.<br/><br/>*Nota:* Para obtener más información acerca de este error y cómo solucionarlo, vea:<br/>• [Los límites de recursos de base de datos de SQL azure](sql-database-resource-limits.md).|
|40544|20|La base de datos ha alcanzado el límite de tamaño. Colocar índices partición o eliminar datos, o consultar la documentación de soluciones posibles.|
|40549|16|Finalice la sesión debido a que tiene una transacción de ejecución larga. Intente acortar la transacción.|
|40550|16|Ha finalizado la sesión porque ha adquirido demasiados bloqueos. Intente leer o modificar menos filas en una sola transacción.|
|40551|16|Ha finalizado la sesión debido a excesivo `TEMPDB` uso. Intente modificando la consulta para reducir el uso del espacio de tabla temporal.<br/><br/>*Sugerencia:* Si está utilizando objetos temporales, ahorrar espacio en el `TEMPDB` colocando objetos temporales que ya no necesitan la sesión de base de datos.|
|40552|16|Ha finalizado la sesión debido a uso de espacio de registro de transacciones excesivo. Intente modificar menos filas en una sola transacción.<br/><br/>*Sugerencia:* Si realiza masa inserta utilizando la `bcp.exe` utilidad o la `System.Data.SqlClient.SqlBulkCopy` de clase, intente usar la `-b batchsize` o `BatchSize` opciones para limitar el número de filas que se copian en el servidor de cada transacción. Si va a reconstruir un índice con la `ALTER INDEX` instrucción, intente usar la `REBUILD WITH ONLINE = ON` opción.|
|40553|16|La sesión ha finalizado debido a su uso excesivo de la memoria. Intente modificar la consulta para procesar menos filas.<br/><br/>*Sugerencia:* Reducir el número de `ORDER BY` y `GROUP BY` operaciones en el código de Transact-SQL reduce los requisitos de memoria de la consulta.|

## <a name="elastic-pool-errors"></a>Errores de grupo elásticas

Los siguientes errores están relacionados con la creación y uso de grupos de Elastics.

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX_RESOURCE | El grupo elástico ha alcanzado el límite de almacenamiento. El uso de almacenamiento para el grupo elástico no puede exceder MB (%d). | Límite de espacio de agrupación elásticos en MB. | Intentar escribir datos en una base de datos cuando se ha alcanzado el límite de almacenamiento de la agrupación elástico. | Vuelva considere aumentar la DTUs del grupo elástico si es posible para aumentar el límite de almacenamiento, reducir el almacenamiento de bases de datos individuales dentro del grupo elástico o quitar bases de datos del grupo elástico. |
| 10929 | EX_USER | La garantía mínima de %s es %d, el límite máximo es %d y el uso de la base de datos actual es %d. Sin embargo, el servidor actualmente está demasiado ocupado para admitir solicitudes mayores que %d para esta base de datos. Consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) para obtener ayuda. En caso contrario, vuelva a intentarlo más tarde. | Min DTU por base de datos; Max DTU por base de datos | El número total de trabajadores simultáneos (solicitudes) en todas las bases de datos en el grupo elástico intentó superar el límite del grupo. | Por favor, considere aumentar el DTUs del grupo elástico si es posible para aumentar el límite de trabajo o quitar bases de datos del grupo elástico de. |
| 40844 | EX_USER | Base de datos '%ls' servidor '%ls' es una base de datos de edición '%ls' en un grupo de elástico y no puede tener una relación de copia continuo. | base de datos, edición de la base de datos, el nombre de servidor | Se ejecuta un comando de StartDatabaseCopy para una base de datos no premium en un grupo de elástico. | Próximamente |
| 40857 | EX_USER | Grupo elástico no encontrado para servidor: '%ls', nombre del grupo elástico: '%ls'. | nombre del servidor; nombre del grupo elásticas | Grupo elástico especificado no existe en el servidor especificado. | Proporcione un nombre de grupo elástico válida. |
| 40858 | EX_USER | Grupo elástico '%ls' ya existe en el servidor: '%ls' | nombre del grupo elástico, nombre de servidor | Grupo elástico especificado ya existe en el servidor lógico especificado. | Proporcione el nombre del nuevo grupo elástico. |
| 40859 | EX_USER | Grupo elástico no admite el nivel de servicio '%ls'. | nivel de servicio de grupo elásticas | Nivel de servicio especificado no es compatible para el aprovisionamiento de grupo elástico. | Proporcionar la edición correcta o deje en blanco para usar el nivel de servicio de forma predeterminada el nivel de servicio. |
| 40860 | EX_USER | Combinación de objetivo '%ls' de '%ls' y servicio de grupo elásticos no es válida. | nombre del grupo elástico; nombre de objetivo de nivel de servicio | Elástico objetivo de grupo y el servicio se puede especificar juntos solo si el objetivo de servicio se especifica como 'ElasticPool'. | Especifique la combinación correcta de grupo elástica y objetivo de servicio. |
| 40861 | EX_USER | La edición de la base de datos ' %. *! no puede tener difiere del nivel de servicio de grupo elásticos que es ' %.* ls'. | edición de la base de datos, nivel de servicio de grupo elástico | La edición de la base de datos es diferente de nivel de servicio de grupo elástico. | No especifique una edición de base de datos que es diferente a nivel de servicio de grupo elástico.  Tenga en cuenta que la edición de la base de datos no es necesario que se especifique. |
| 40862 | EX_USER | Debe ser el nombre del grupo elástico especificar si se especifica el objetivo de servicio de grupo elástico. | Ninguno | Objetivo de servicio de grupo elástica para identificar un grupo elástico. | Especifique el nombre del grupo elástico si usa el objetivo de servicio de grupo elástico. |
| 40864 | EX_USER | La DTUs para el grupo elástico deben tener al menos (%d) en DTUs de nivel de servicio ' %. * ls'. | DTUs para grupo elástico; nivel de servicio de grupo elástico. | Al intentar establecer la DTUs para el grupo elástico por debajo del límite mínimo. | Vuelva a intentar establecer la DTUs para la elástica grupo al menos el límite mínimo. |
| 40865 | EX_USER | La DTUs para el grupo elástico no pueden superar (%d) en DTUs de nivel de servicio ' %. * ls'. | DTUs para grupo elástico; nivel de servicio de grupo elástico. | Al intentar establecer la DTUs para el grupo elástico por encima del límite máximo. | Vuelva a intentar configurar la DTUs para el grupo elástico no supere el límite máximo. |
| 40867 | EX_USER | Debe ser la DTU máxima por base de datos en menos (%d) para el nivel de servicio ' %. * ls'. | Max DTU por base de datos; nivel de servicio de grupo elásticas | Al intentar establecer el límite máximo de DTU por debajo del límite admitido de base de datos. | Considere la posibilidad de usar el nivel de servicio de grupo elástico compatible con la opción que desee. |
| 40868 | EX_USER | La DTU máxima por base de datos no puede superar (%d) para el nivel de servicio ' %. * ls'. | Max DTU por base de datos; nivel de servicio de grupo elástico. | Al intentar establecer el límite máximo de DTU por base de datos más allá del límite admitido. | Considere la posibilidad de usar el nivel de servicio de grupo elástico compatible con la opción que desee. |
| 40870 | EX_USER | Min DTU por base de datos no puede superar (%d) para el nivel de servicio ' %. * ls'. | Min DTU por base de datos; nivel de servicio de grupo elástico. | Al intentar establecer la min DTU por base de datos más allá del límite admitido. | Considere la posibilidad de usar el nivel de servicio de grupo elástico compatible con la opción que desee. |
| 40873 | EX_USER | El número de bases de datos (%d) y min DTU por base de datos (%d) no puede superar la DTUs del grupo elástico (%d). | Número de bases de datos en el grupo elástico; Min DTU por base de datos; DTUs de agrupación elástico. | Intenta especificar min DTU para bases de datos en el grupo elástico que supera el DTUs del grupo elástico. | Considere aumentar la DTUs del grupo elástico, o reducir al mínimo DTU por base de datos o bien, reduzca el número de bases de datos en el grupo elástico. |
| 40877 | EX_USER | No se puede eliminar un grupo elástico a menos que no contiene las bases de datos. | Ninguno | El grupo elástico contiene una o más bases de datos y no se pueden eliminar. | Quite las bases de datos de la agrupación elástica para eliminarla. |
| 40881 | EX_USER | El grupo elástico ' %. * ls' ha alcanzado el límite de recuento de base de datos.  El límite de recuento de base de datos para el grupo elástico no puede superar (%d) para un grupo de elástico con DTUs (%d). | Nombre del grupo elástico; límite de recuento de base de datos del grupo elástico; e DTUs para el grupo de recursos. | Al intentar crear o agregar base de datos al grupo elástico cuando se ha alcanzado el límite de recuento de base de datos del grupo elástico. | Por favor, considere aumentar el DTUs del grupo elástico si es posible para aumentar el límite de la base de datos o quitar bases de datos del grupo elástico de. |
| 40889 | EX_USER | El DTUs o el límite de almacenamiento para el grupo elástico ' %. * ls' no se puede disminuir desde que no proporciona suficiente espacio de almacenamiento para sus bases de datos. | Nombre del grupo elástico. | Intentar reducir el límite de almacenamiento de la agrupación elástico debajo de su uso de almacenamiento. | Por favor, considere reducir el uso de almacenamiento de bases de datos individuales en el grupo elástico o quitar bases de datos desde el grupo con el fin de reducir su DTUs o el límite de almacenamiento. |
| 40891 | EX_USER | Min DTU por base de datos (%d) no puede superar el límite máximo de DTU por base de datos (%d). | Min DTU por base de datos; DTU máximo por base de datos. | Al intentar establecer la min DTU por mayor que el límite máximo de DTU por base de datos de la base de datos. | Asegúrese de que la min DTU por las bases de datos no supere el límite máximo de DTU por base de datos. |
| POR DETERMINAR | EX_USER | El tamaño de almacenamiento para una base de datos individual en un grupo de elástico no puede superar el tamaño máximo permitido por ' %. * ls' grupo elástica de nivel de servicio. | nivel de servicio de grupo elásticas | El tamaño máximo de la base de datos supera el tamaño máximo permitido por el nivel de servicio de grupo elástico. | Establezca el tamaño máximo de la base de datos dentro de los límites del tamaño máximo permitido por el nivel de servicio de grupo elástico. |

Temas relacionados:

* [Crear un grupo de la base de datos elástica (C#)](sql-database-elastic-pool-create-csharp.md) 
* [Administrar un grupo de la base de datos elástica (C#)](sql-database-elastic-pool-manage-csharp.md). 
* [Crear un grupo de base de datos elásticas (PowerShell)](sql-database-elastic-pool-create-powershell.md) 
* [Monitor y administrar un grupo de base de datos elásticas (PowerShell)](sql-database-elastic-pool-manage-powershell.md).

## <a name="general-errors"></a>Errores generales

Los siguientes errores no entran en las categorías anteriores.

|Código de error|Gravedad|Descripción|
|---:|---:|:---|
|15006|16|<AdministratorLogin>no es un nombre válido porque contiene caracteres no válidos.|
|18452|14|Error de inicio de sesión. El inicio de sesión es de un dominio de confianza y no se pueden usar con Windows authentication.%. & #x2a; ls (inicios de sesión de Windows no son compatibles en esta versión de SQL Server).|
|18456|14|Error de inicio de sesión para el usuario ' % & #x2a;ls'.%. & #x2a; % ls. & #x2a; ls (error en el inicio de sesión de usuario "% & #x2a; ls". Error al cambiar contraseña. Cambiar la contraseña durante el inicio de sesión no es compatible en esta versión de SQL Server.)|
|18470|14|Error de inicio de sesión para el usuario '% & #x2a; ls'. Causa: La cuenta se encuentra disabled.%. & #x2a; ls|
|40014|16|No se pueden usar varias bases de datos en la misma transacción.|
|40054|16|Tablas sin un índice agrupado no son compatibles con esta versión de SQL Server. Crear un índice agrupado e inténtelo de nuevo.|
|40133|15|Esta operación no es compatible con esta versión de SQL Server.|
|40506|16|SID especificado no es válido para esta versión de SQL Server.|
|40507|16|' % & #x2a; ls' no se puede llamar con parámetros en esta versión de SQL Server.|
|40508|16|No se admite la instrucción USE para cambiar entre las bases de datos. Usar una nueva conexión para conectarse a una base de datos diferente.|
|40510|16|Instrucción '% & #x2a; ls' no es compatible con esta versión de SQL Server|
|40511|16|Función integrada '% & #x2a; ls' no es compatible con esta versión de SQL Server.|
|40512|16|Característica obsoleta '%ls' no es compatible con esta versión de SQL Server.|
|40513|16|Servidor de variable '% & #x2a; ls' no es compatible con esta versión de SQL Server.|
|40514|16|'%ls' no es compatible con esta versión de SQL Server.|
|40515|16|Referencia a la base de datos o del servidor de nombre de '% & #x2a; ls' no es compatible con esta versión de SQL Server.|
|40516|16|Objetos temporales globales no son compatibles con esta versión de SQL Server.|
|40517|16|Opción de palabra clave o una instrucción '% & #x2a; ls' no es compatible con esta versión de SQL Server.|
|40518|16|Comando DBCC '% & #x2a; ls' no es compatible con esta versión de SQL Server.|
|40520|16|Clase protegibles '% S_MSG' no compatible con esta versión de SQL Server.|
|40521|16|Clase protegibles '% S_MSG' no se admite en el ámbito del servidor en esta versión de SQL Server.|
|40522|16|Tipo de base de datos principal '% & #x2a; ls' no es compatible con esta versión de SQL Server.|
|40523|16|Creación de usuario implícito '% & #x2a; ls' no es compatible con esta versión de SQL Server. Crear explícitamente al usuario antes de usarla.|
|40524|16|Tipo de datos '%. & #x2a; ls' no es compatible con esta versión de SQL Server.|
|40525|16|CON '%.ls' no es compatible con esta versión de SQL Server.|
|40526|16|' % & #x2a; proveedor de conjunto de filas de ls no compatibles con esta versión de SQL Server.|
|40527|16|Servidores vinculados no son compatibles con esta versión de SQL Server.|
|40528|16|Los usuarios no pueden asignarse a certificados, claves asimétricas o inicios de sesión de Windows en esta versión de SQL Server.|
|40529|16|Función integrada '% & #x2a; ls' en suplantación contexto no es compatible en esta versión de SQL Server.|
|40532|11|No se puede abrir el servidor "% & #x2a; ls" solicitada por el inicio de sesión. Error en el inicio de sesión.|
|40553|16|La sesión ha finalizado debido a su uso excesivo de la memoria. Intente modificar la consulta para procesar menos filas.<br/><br/>*Nota:* Reducir el número de `ORDER BY` y `GROUP BY` operaciones en el código de Transact-SQL le ayuda a reducir los requisitos de memoria de la consulta.|
|40604|16|Podría no CREATE/ALTER DATABASE porque excede la cuota del servidor.|
|40606|16|Adjuntar bases de datos no es compatible en esta versión de SQL Server.|
|40607|16|Inicios de sesión de Windows no son compatibles con esta versión de SQL Server.|
|40611|16|Servidores pueden tener como máximo 128 reglas de firewall definidas.|
|40614|16|Dirección IP de inicio de la regla de firewall no puede superar la dirección IP final.|
|40615|16|No puede abrir el servidor '{0}' solicita el inicio de sesión. Cliente con la dirección IP '{1}' no puede tener acceso al servidor.  Para habilitar el acceso, use el Portal de la base de datos de SQL o ejecutar sp_set_firewall_rule en la base de datos para crear una regla de firewall para esta dirección IP o el intervalo de direcciones.  Puede tardar hasta cinco minutos para que el cambio surta efecto.|
|40617|16|El nombre de la regla de firewall que comienza con <rule name> es demasiado larga. Longitud máxima es 128.|
|40618|16|El nombre de la regla de firewall no puede estar vacía.|
|40620|16|Error en el inicio de sesión de usuario "% & #x2a; ls". Error al cambiar contraseña. Cambiar la contraseña durante el inicio de sesión no es compatible con esta versión de SQL Server.|
|40627|20|Operación en el servidor '{0}' y la base de datos '{1}' está en curso. Espere unos minutos antes de intentarlo.|
|40630|16|Error de validación de contraseña. La contraseña no cumple los requisitos de la directiva porque es demasiado corto.|
|40631|16|La contraseña que ha especificado es demasiado larga. La contraseña debe tener no más de 128 caracteres.|
|40632|16|Error de validación de contraseña. La contraseña no cumple los requisitos de la directiva porque no es lo suficientemente complejo.|
|40636|16|No se puede usar un nombre de base de datos reservado '% & #x2a; ls' en esta operación.|
|40638|16|Identificador de la suscripción no válidos < Id. de suscripción >. Suscripción no existe.|
|40639|16|Solicitud no se ajusta al esquema: <schema error>.|
|40640|20|El servidor encontró una excepción inesperada.|
|40641|16|La ubicación especificada no es válida.|
|40642|17|El servidor está demasiado ocupado. Vuelva a intentarlo más tarde.|
|40643|16|El valor del encabezado x-ms-versión especificado no es válido.|
|40644|14|Error al autorizar el acceso a la suscripción especificada.|
|40645|16|Nombre_de_servidor <servername> no puede estar vacía ni ser nula. Solo puede estar formado por letras minúsculas 'a'-'z', los números 0-9 y el guión. El guión no puede producir o seguimiento en el nombre.|
|40646|16|Id. de suscripción no puede estar vacía.|
|40647|16|Suscripción < Id. de suscripción no tiene el nombre del servidor.|
|40648|17|Se han realizado demasiadas solicitudes. Vuelva a intentarlo más adelante.|
|40649|16|Se especifica el tipo de contenido no válido. Es compatible la aplicación solo/xml.|
|40650|16|Suscripción < Id. de suscripción > no existe o no está lista para la operación.|
|40651|16|Error al crear servidor porque la suscripción < Id. de suscripción > está deshabilitada.|
|40652|16|No puede mover ni crear servidor. Suscripción < Id. de suscripción > supere la cuota de servidor.|
|40671|17|Error de comunicación entre la puerta de enlace y el servicio de administración. Vuelva a intentarlo más adelante.|
|40852|16|No se puede abrir la base de datos ' %. *ls' en el servidor ' %.* ls' solicitan el inicio de sesión. Solo se permite el acceso a la base de datos utilizando una cadena de conexión con seguridad habilitada. Para obtener acceso a esta base de datos, modifique las cadenas de conexión que contenga seguro en el servidor FQDN - nombre del servidor.database.windows .net debe modificarse para .database nombre del servidor. `secure`. windows.net.|
|45168|16|El sistema de SQL Azure está bajo carga y está colocando un límite superior en operaciones CRUD DB simultáneas para un solo servidor (por ejemplo, crear base de datos). El servidor especificado en el mensaje de error ha superado el número máximo de conexiones simultáneas. Inténtelo de nuevo.|
|45169|16|El sistema SQL azure está bajo carga y está colocando un límite superior en el número de operaciones de CRUD servidor simultáneas para una sola suscripción (por ejemplo, crear servidor). La suscripción especificada en el mensaje de error ha superado el número máximo de conexiones simultáneas y ha denegado la solicitud. Inténtelo de nuevo.|

## <a name="related-links"></a>Vínculos relacionados

- [Directrices y limitaciones de General de la base de datos SQL Azure](sql-database-general-limitations.md)
- [Límites de recursos de base de datos de SQL Azure](sql-database-resource-limits.md)
