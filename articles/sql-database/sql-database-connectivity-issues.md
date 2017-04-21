<properties
    pageTitle="Corregir un error de conexión de SQL, error transitoria | Microsoft Azure"
    description="Obtenga información sobre cómo solucionar problemas, diagnosticar y evitar que un error de conexión de SQL o transitorias en base de datos de SQL Azure. "
    keywords="conexión de SQL, cadena de conexión, problemas de conectividad, error transitoria, error de conexión"
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="daleche"/>


# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Solucionar problemas, diagnosticar y evitar errores de conexión de SQL y transitorias para base de datos de SQL

En este artículo se describe cómo evitar, solucionar problemas, diagnosticar y mitigar los errores de conexión y transitorias que la aplicación cliente encuentra cuando interactúa con la base de datos de SQL Azure. Obtenga información sobre cómo configurar la lógica de reintento, generar la cadena de conexión y ajustar otras opciones de configuración de conexión.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Transitorias errores transitorias

Un error transitorio - también, transitorias errores - tiene una causa subyacente que pronto resolver a sí mismo. Una causa ocasional de errores transitorias es cuando el sistema de Azure rápidamente turnos de recursos de hardware a mejorar el equilibrio de carga de varias cargas de trabajo. Mayoría de estos eventos nueva configuración a menudo completos en menos de 60 segundos. Durante este intervalo de tiempo de la nueva configuración, puede que tenga problemas de conectividad de base de datos de SQL Azure. Aplicaciones que se conectan a la base de datos de SQL Azure deben generarse esperan estos errores transitorios, controlar la implementación de lógica de reintento en su código, en lugar de ponerlos a los usuarios como errores de aplicación.

Si el programa cliente usa ADO.NET, el programa se les sobre el error transitorio la captura de una **SqlException**. La propiedad de **número** se puede comparar con la lista de errores transitorias cerca de la parte superior del tema: [códigos de error SQL para las aplicaciones cliente de base de datos de SQL](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Conexión frente a comandos

Deberá volver a intentar la conexión de SQL o establecer de nuevo, dependiendo de lo siguiente:

* **Se produce un error transitorio durante un intento de conexión**: la conexión debe volver a intentar después de que se retrase durante unos segundos.

* **Se produce un error transitorio durante un comando de la consulta SQL**: el comando no debe volverá inmediatamente. En su lugar, después de un retraso, establecer la conexión debe ser recién. A continuación, puede volver a intentar el comando.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

### <a name="retry-logic-for-transient-errors"></a>Vuelva a intentar lógica transitorias errores


Programas de cliente que en ocasiones se encuentran un error transitorio son más rigurosa cuando contienen lógica de reintento.


Cuando el programa se comunica con la base de datos de SQL Azure a través de un software de terceros 3ª intermedio, consultar con el proveedor si el software intermedio contiene lógica de reintento transitorias errores.

<a id="principles-for-retry" name="principles-for-retry"></a>

#### <a name="principles-for-retry"></a>Principios para volver a intentar


- Si el error es transitorio debe volverá a intentar abrir una conexión.


- Una instrucción SQL SELECT que se produce un error transitorio no reintentos directamente.
 - En su lugar, establezca una nueva conexión y vuelva a intentar la seleccionar.


- Cuando una instrucción SQL UPDATE produce un error transitorio, una nueva conexión debe establecerse antes de que se vuelve a intentar la actualización.
 - La lógica de reintento debe asegurarse de que la transacción de base de datos completa completado o que toda la transacción se deshace.


#### <a name="other-considerations-for-retry"></a>Otras consideraciones para volver a intentar


- Puede permitir un programa por lotes que se inicia automáticamente después de horas de trabajo y que se completará antes de mañana, muy paciencia con cuánto tiempo los intervalos de tiempo entre sus reintentos.


- Un programa de la interfaz de usuario es responsable de la tendencia humana a renunciar tras demasiado una espera.
 - Sin embargo, no debe ser la solución para volver a intentar cada pocos segundos, porque esta directiva puede difunda el sistema con solicitudes.


#### <a name="interval-increase-between-retries"></a>Aumento de intervalo entre reintentos



Se recomienda retrasar durante 5 segundos antes de su primer reintento. Reintentar después de un retraso inferior a una sobrecarga del servicio de nube de riesgos de 5 segundos. Para cada intentos subsiguientes el retraso debe crecer exponencial, hasta un máximo de 60 segundos.

Una discusión del *período de bloqueo* para los clientes que utilizan ADO.NET está disponible en [SQL Server conexión agrupación (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

También puede establecer el número máximo de reintentos antes de que el programa finaliza automáticamente.


#### <a name="code-samples-with-retry-logic"></a>Ejemplos de código con lógica de reintento


Ejemplos de código con lógica de reintento, en una gran variedad de lenguajes de programación, están disponibles en:

- [Bibliotecas de conexión de base de datos de SQL y SQL Server](sql-database-libraries.md)


<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

#### <a name="test-your-retry-logic"></a>Probar la lógica de reintento


Para probar la lógica de reintento, debe simular o producir un error que puede corregirse mientras aún se está ejecutando el programa.


##### <a name="test-by-disconnecting-from-the-network"></a>Desconectarse de la red para probar


Una forma puede probar la lógica de reintento es desconectar el equipo del cliente de la red mientras se está ejecutando el programa. El error será:

- **SqlException.Number** = 11001
- Mensaje: "No se desconoce el host"


Como parte de la primera reintento, el programa puede corregir la ortografía y, a continuación, intente conectarse.


Para que esta práctica, desconecte el equipo de la red antes de iniciar el programa. A continuación, el programa reconoce un parámetro de tiempo de ejecución que hace que el programa:

1. Agregar temporalmente 11001 a su lista de errores para tener en cuenta como transitorio.
2. Intentar su primera conexión como de costumbre.
3. Una vez que se captura el error, quitar 11001 de la lista.
4. Mostrar un mensaje que indica el usuario que conecte el equipo a la red.
 - Detener la ejecución más utilizando el método **Console.ReadLine** o un cuadro de diálogo con un botón Aceptar. El usuario presiona la tecla ENTRAR después de que el equipo conectado a la red.
5. Intente volver a conectarse, esperando éxito.


##### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Error al escribir el nombre de la base de datos cuando se conecta para probar


El programa propósito puede incorrectamente el nombre de usuario antes del primer intento de conexión. El error será:

- **SqlException.Number** = 18456
- Mensaje: "Error al iniciar sesión para usuario 'WRONG_MyUserName'."


Como parte de la primera reintento, el programa puede corregir la ortografía y, a continuación, intente conectarse.


Para que esta práctica, el programa reconoce un parámetro de tiempo de ejecución que hace que el programa:

1. Agregar temporalmente 18456 a su lista de errores para tener en cuenta como transitorio.
2. Agregar deliberadamente 'WRONG_' en el nombre de usuario.
3. Una vez que se captura el error, quitar 18456 de la lista.
4. Quitar 'WRONG_' el nombre de usuario.
5. Intente volver a conectarse, esperando éxito.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

### <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parámetros de .NET SqlConnection para reintentar la conexión


Si el programa cliente se conecta a la base de datos de Azure SQL mediante .NET Framework clase **System.Data.SqlClient.SqlConnection**, debe usar .NET 4.6.1 o posterior, por lo que puede aprovechar su característica de intentos de conexión. Detalles de la función son [aquí](http://go.microsoft.com/fwlink/?linkid=393996).


<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


Cuando se crea la [cadena de conexión](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) para el objeto **SqlConnection** , debe coordinar los valores entre los parámetros siguientes:

- ConnectRetryCount &nbsp; &nbsp; *(el valor predeterminado es 1. Rango es 0 y 255.)*
- ConnectRetryInterval &nbsp; &nbsp; *(el valor predeterminado es 1 segundo. Rango es 1 a 60).*
- Tiempo de espera de conexión &nbsp; &nbsp; *(el valor predeterminado es 15 segundos. Intervalo es de 0 a 2147483647)*


Más concretamente, los valores elegidos deberían ser siguientes de igualdad true:

- Tiempo de espera de conexión = ConnectRetryCount * ConnectionRetryInterval

Por ejemplo, si el recuento de = 3 e intervalo = 10 segundos, tiempo de espera de sólo 29 segundos no bastante daría el sistema de tiempo suficiente para su reintentos 3er y final en conexión: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Conexión frente a comandos


Los parámetros **ConnectRetryCount** y **ConnectRetryInterval** le permiten objeto **SqlConnection** vuelva a intentar la operación de conexión sin que indica o molestarle su programa, como devolver el control al programa. Los reintentos pueden ocurrir en las siguientes situaciones:

- llamada a un método mySqlConnection.Open
- llamada a un método mySqlConnection.Execute

Hay un problema. Si se produce un error transitorio mientras se ejecuta la *consulta* , el objeto **SqlConnection** no volver a intentar la operación de conexión y por supuesto no volver a intentar la consulta. Sin embargo, **SqlConnection** muy rápidamente comprueba la conexión antes de enviar la consulta para la ejecución. Si la comprobación rápida detecta un problema de conexión, **SqlConnection** vuelve a intentar la operación de conexión. Si los intentos se realiza correctamente, consulta se envía para la ejecución.


#### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>¿ConnectRetryCount se debe combinar con lógica de reintento de la aplicación?

Supongamos que su aplicación tiene lógica de reintento personalizado sólidos. Puede reintentar la operación de conexión 4 veces. Si agrega **ConnectRetryInterval** y **ConnectRetryCount** = 3 a la cadena de conexión, aumentará el número de reintentos a 4 * 3 = 12 reintentos. Es posible que no desee un gran número de reintentos.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-azure-sql-database"></a>Conexiones de la base de datos SQL Azure

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Conexión: Cadena de conexión


La cadena de conexión necesaria para conectarse a la base de datos de SQL Azure es ligeramente diferente de la cadena de conexión a Microsoft SQL Server. Puede copiar la cadena de conexión para la base de datos desde el [Portal de Azure](https://portal.azure.com/).


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Conexión: Dirección IP


Configure el servidor de base de datos SQL para aceptar la comunicación de la dirección IP del equipo que aloja el programa cliente. Hacer esto mediante la edición de la configuración del firewall a través del [Portal de Azure](https://portal.azure.com/).


Si olvida configurar la dirección IP, se producirá un error en el programa con un mensaje de error de mano que indica la dirección IP necesaria.


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


Para obtener más información, consulte: [Cómo: configurar el firewall en base de datos de SQL](sql-database-configure-firewall-settings.md)


<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Conexión: puertos


Normalmente solo debe asegurarse de que el puerto 1433 está abierto para las comunicaciones salientes, en el equipo que aloja el programa de cliente.


Por ejemplo, cuando el programa cliente está alojado en un equipo Windows, el Firewall de Windows en el host le permite abrir puerto 1433:


1. Abra el Panel de Control
2. &gt;Todos los elementos de Panel de Control
3. &gt;Firewall de Windows
4. &gt;Configuración avanzada
5. &gt;Reglas de salida
6. &gt;Acciones
7. &gt;Nueva regla


Si el programa cliente se hospeda en una máquina virtual (VM) en Azure, debe leer:<br/>[Puertos más allá de 1433 para ADO.NET 4.5 y V12 de base de datos SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


Para obtener información general sobre la configuración de puertos y la dirección IP, consulte: [firewall de base de datos de SQL Azure](sql-database-firewall-configure.md)


<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Conexión: ADO.NET 4.6.1


Si su programa usa clases ADO.NET como **System.Data.SqlClient.SqlConnection** para conectarse a la base de datos de SQL Azure, le recomendamos que use .NET Framework versión 4.6.1 o superior.


ADO.NET 4.6.1:

- Para la base de datos de SQL Azure, hay mayor confiabilidad al abrir una conexión a través del método **SqlConnection.Open** . El método **Open** incorpora ahora mejores mecanismos de intentos de esfuerzo en respuesta a errores transitorias, para algunos errores dentro de un período de tiempo de espera de conexión.
- Es compatible con la agrupación de conexiones. Esto incluye una comprobación eficaz que sirve el objeto de conexión le proporciona el programa.



Cuando se utiliza un objeto de conexión de un grupo de conexión, se recomienda que el programa de cerrar la conexión temporalmente cuando no inmediatamente usando. Volver a abrir una conexión no es caro de crear una nueva conexión de forma.


Si está utilizando ADO.NET 4.0 o versiones anteriores, le recomendamos que actualice a la última ADO.NET.

- A partir de noviembre de 2015, puede [Descargar ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx).


<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnósticos

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnósticos: Comprobar si utilidades se pueden conectar


Si el programa no puede conectar a la base de datos de SQL Azure, una opción de diagnóstico es intenta conectar con un programa de utilidad. Lo ideal es que la utilidad conectará usando la misma biblioteca que usa el programa.


En cualquier equipo con Windows, puede probar estas utilidades:

- SQL Server Management Studio (ssms.exe), que se conecta utilizando ADO.NET.
- SQLCMD.exe, que se conecta mediante [ODBC](http://msdn.microsoft.com/library/jj730308.aspx).


Una vez conectado, compruebe si funciona una breve consulta SQL SELECT.


<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnósticos: Comprobar los puertos abiertos


Suponga que sospecha que los intentos de conexión dan error debido a problemas de puerto. En el equipo puede ejecutar una herramienta de informes en las configuraciones de puerto.


En Linux las siguientes utilidades podrían ser útiles:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`
 - (Cambie el valor de ejemplo para utilizarlo como su dirección IP).


En Windows la [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) utilidad puede resultar útil. Esto es una ejecución de ejemplo que consultar la situación de puerto en un servidor de base de datos de SQL Azure, y que se ejecute en un equipo portátil:


```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnóstico: Los errores de registro


A veces mejor es diagnosticar un problema intermitente por detección de un patrón general en días o semanas.


El cliente puede ayudar a un diagnóstico por todos los errores que encuentre el registro. Es posible que pueda relacionar las entradas del registro con los datos de error que inicie una base de datos de SQL Azure propio internamente.


Biblioteca de empresa 6 (EntLib60) ofrece clases administrado para ayudar con el registro:

- [5 - tan fácil como acaben desactivar un registro: utilizar el bloque de aplicación de registro](http://msdn.microsoft.com/library/dn440731.aspx)


<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnósticos: Examinar registros de errores del sistema


Estas son algunas instrucciones Transact-SQL SELECT esa consulta los registros de error y otra información.


| Consulta de registro | Descripción |
| :-- | :-- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` | La vista [sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) ofrece información sobre los eventos individuales, incluidas algunas que pueden causar errores transitorios o errores de conectividad.<br/><br/>Lo ideal es que puede relacionar los valores **start_time** o **end_time** con información sobre cuando el programa cliente había tenido problemas.<br/><br/>**Sugerencia:** Debe conectarse a **la base de datos para ejecutar esta** . |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` | La vista [sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) ofrece agregados recuentos de tipos de eventos, para realizar diagnósticos adicionales.<br/><br/>**Sugerencia:** Debe conectarse a **la base de datos para ejecutar esta** . |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnósticos: Búsqueda eventos de problema en el registro de la base de datos SQL


Puede buscar entradas sobre los eventos del problema en el registro de la base de datos de SQL Azure. Pruebe la siguiente instrucción SELECT de Transact-SQL en **la base de datos** :


```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>Algunas filas devueltas desde sys.fn_xe_telemetry_blob_target_read_file


Siguiente es el aspecto que puede tener una fila devuelta. Los valores nulos que se muestra a menudo no son nulos de otras filas.


```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Biblioteca de Enterprise 6


Biblioteca de empresa 6 (EntLib60) es un marco de clases .NET que le ayude a implementar a clientes sólidos de servicios en la nube, uno de los cuales es el servicio de base de datos de SQL Azure. Puede encontrar temas dedicados a cada área en la que puede ayudar EntLib60 primera visitando:

- [Biblioteca de Enterprise 6: abril de 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)


Lógica de reintento para controlar los errores transitorios es un área donde puede ayudar EntLib60:

- [4 - su perseverancia, secreto de todos los logros: utilizar el bloque de aplicación de control de errores transitorias](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)


> [AZURE.NOTE] El código fuente de EntLib60 está disponible para el público [Descargar](http://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft no tiene previsto realizar más actualizaciones de característica o mantenimiento a EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Clases de EntLib60 para errores transitorios y vuelva a intentarlo


Las clases de EntLib60 siguientes son particularmente útiles para lógica de reintento. Todas estas se encuentran en o están bajo el espacio de nombres **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

*En el espacio de nombres* *Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling* *:*

- Clase **RetryPolicy**
 - Método **ExecuteAction**


- Clase **ExponentialBackoff**


- Clase **SqlDatabaseTransientErrorDetectionStrategy**


- Clase **ReliableSqlConnection**
 - Método **ExecuteCommand**


En el espacio de nombres **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- Clase **AlwaysTransientErrorDetectionStrategy**

- Clase **NeverTransientErrorDetectionStrategy**


Estos son los vínculos a información sobre EntLib60:

- Gratuita [descarga de la libreta: Guía del desarrollador de Microsoft Enterprise Library, 2ª edición](http://www.microsoft.com/download/details.aspx?id=41145)

- Procedimientos recomendados: [Reintentar directrices generales](../best-practices-retry-general.md) tiene un excelente debate en profundidad de lógica de reintento.

- Descarga de NuGet de [Enterprise Library - bloque de aplicaciones de tratamiento de errores transitorias 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: El bloqueo de registro


- El bloque de registro es una solución altamente flexible y configurable que le permite:
 - Crear y almacenar mensajes de registro en una amplia variedad de ubicaciones.
 - Clasificar y filtrar los mensajes.
 - Recopilar información contextual de los requisitos de registro útil para depuración y seguimiento, así como auditoría y general.


- El bloqueo de registro resume la funcionalidad de registro de destino de registro para que el código de la aplicación sea coherente, con independencia de la ubicación y el tipo de la tienda de registro de destino.


Para obtener información detallada, vea: [5 - como fácil como acaben desactivar un registro: utilizar el bloque de aplicación de registro](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Código fuente del método IsTransient EntLib60


A continuación, en la clase **SqlDatabaseTransientErrorDetectionStrategy** , es el código fuente de C# para el método **IsTransient** . El código fuente explica qué errores se consideran transitorias y pena intentos a partir de abril de 2013.

Varias líneas de **//comment** se han quitado de esta copia para enfatizar la legibilidad.


```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## <a name="next-steps"></a>Pasos siguientes

- Para solucionar otros problemas de conexión de base de datos de SQL Azure comunes, visite [solucionar problemas de conexión a la base de datos de SQL Azure](sql-database-troubleshoot-common-connection-issues.md).

- [SQL Server agrupación de conexiones (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)


- [*Retrying* es que un Apache 2.0 con licencia general biblioteca reintento, escrita en **Python**para simplificar la tarea de agregar el comportamiento de reintento a prácticamente cualquier elemento.](https://pypi.python.org/pypi/retrying)
