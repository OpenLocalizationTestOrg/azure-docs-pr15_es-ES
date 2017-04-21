<properties 
    pageTitle="Almacenamiento de Azure usarlo para desarrollo y pruebas | Microsoft Azure" 
    description="El emulador de almacenamiento de Azure proporciona un entorno de desarrollo local gratuita para desarrollar y probar con el almacenamiento de Azure. Obtenga información sobre el emulador de almacenamiento, incluida la autenticación de solicitudes, cómo conectarse al emulador desde la aplicación y cómo usar la herramienta de línea de comandos." 
    services="storage" 
    documentationCenter="" 
    authors="tamram" 
    manager="carmonm" 
    editor="tysonn"/>
<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="tamram"/>

# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Almacenamiento de Azure usarlo para desarrollo y pruebas

## <a name="overview"></a>Información general

El emulador de almacenamiento de Microsoft Azure proporciona un entorno local que emula los servicios de blobs de Windows Azure, la cola y la tabla para fines de desarrollo. Usando el emulador de almacenamiento, puede probar la aplicación con los servicios de almacenamiento de forma local, sin necesidad de crear una suscripción de Azure ni provocar los costos. Cuando esté satisfecho con cómo funciona la aplicación en el emulador, puede cambiar para usar una cuenta de almacenamiento de Azure en la nube.

> [AZURE.NOTE] El emulador de almacenamiento está disponible como parte del [SDK de Microsoft Azure](https://azure.microsoft.com/downloads/). También puede instalar el emulador de almacenamiento utilizando el [Instalador independiente](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409). Para configurar el emulador de almacenamiento, debe tener privilegios de administrador en el equipo.
> 
> El emulador de almacenamiento actualmente sólo se ejecuta en Windows.
>  
> Observe que creó en una versión del emulador de almacenamiento de datos no se garantiza que sean accesibles cuando se usa una versión diferente. Si desea conservar los datos a largo plazo, se recomienda almacenar los datos en una cuenta de almacenamiento de Azure, en lugar de en el emulador de almacenamiento.

## <a name="how-the-storage-emulator-works"></a>Cómo funciona el emulador de almacenamiento
 
El emulador de almacenamiento utiliza una instancia de Microsoft SQL Server local y el sistema de archivos local para emular los servicios de almacenamiento de Azure. De forma predeterminada, el emulador de almacenamiento utiliza una base de datos de Microsoft SQL Server 2012 Express LocalDB.  Puede configurar el emulador de almacenamiento para tener acceso a una instancia de SQL Server local en lugar de la instancia de LocalDB. Para obtener más información, vea [Inicio e inicialización emulador de almacenamiento](#start-and-initialize-the-storage-emulator) debajo.

Puede instalar SQL Server Management Studio Express para administrar su instalación LocalDB. El emulador de almacenamiento se conecta a SQL Server o LocalDB que usan autenticación de Windows. 

Existen algunas diferencias de funcionalidad entre emulador de almacenamiento y los servicios de almacenamiento de Azure. Para obtener más información sobre estas diferencias, consulte [diferencias entre el emulador de almacenamiento y el almacenamiento de Azure](#differences-between-the-storage-emulator-and-azure-storage).

## <a name="authenticating-requests-against-the-storage-emulator"></a>Autenticación de solicitudes de emulador de almacenamiento

Al igual que con el almacenamiento de Azure en la nube, se deben autenticar todas las solicitudes que se realicen en el emulador de almacenamiento, a menos que sea una solicitud anónima. Puede autenticar solicitudes contra el emulador de almacenamiento mediante la autenticación de clave compartida o con una firma de acceso compartido (SA).

### <a name="authentication-with-shared-key-credentials"></a>Autenticación con credenciales de clave compartida

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Para obtener más detalles sobre las cadenas de conexión, vea [Configurar las cadenas de conexión de almacenamiento de Azure](storage-configure-connection-string.md). 

### <a name="authentication-with-a-shared-access-signature"></a>Autenticación con una firma de acceso compartido 

Algunas bibliotecas de cliente de almacenamiento de Azure, como la Xamarin, solo admiten la autenticación con un token de acceso compartido firma (SA). Debe crear este token SA utilizando una herramienta o aplicación que admita la autenticación de clave compartida. Una forma sencilla para generar el token de SA es a través de Azure PowerShell:

1. Si todavía no lo ha hecho, instale PowerShell de Azure. Se recomienda utilizar la versión más reciente de los cmdlets de PowerShell de Azure. Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md#Install) para obtener instrucciones de instalación.

2. Abra Azure PowerShell y ejecute los comandos siguientes. No olvide reemplazar *ACCOUNT_NAME* y *ACCOUNT_KEY ==* con sus propias credenciales. Reemplace *CONTAINER_NAME* con un nombre de su elección.

        $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
        
        New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
        
        $now = Get-Date 
        
        New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

La firma de acceso compartido URI resultante para el nuevo contenedor debe ser similar al siguiente:

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

La firma de acceso compartido creada con este ejemplo es válida para un día. La firma concede acceso completo (es decir, leer, escribir, eliminar, lista) a BLOB dentro del contenedor.

Para obtener más información sobre las firmas de acceso compartido, consulte [Uso compartido acceso firmas (SA)](storage-dotnet-shared-access-signature-part-1.md).


## <a name="start-and-initialize-the-storage-emulator"></a>Iniciar e inicializar el emulador de almacenamiento

Para iniciar el emulador de almacenamiento de Azure, seleccione el botón Inicio o presione la tecla de Windows. Empiece a escribir **Emulador de almacenamiento de Azure**y seleccione el emulador de la lista de aplicaciones. 

Cuando se ejecuta el emulador, verá un icono en el área de notificación de la barra de tareas de Windows.

Cuando se inicia el emulador de almacenamiento, aparecerá una ventana de línea de comandos. Puede utilizar esta ventana de línea de comandos para iniciar y detener el emulador de almacenamiento, así como para borrar los datos, obtener el estado actual e inicializar el emulador. Para obtener más información, vea [Referencia de herramienta de línea de comandos de almacenamiento emulador](#storage-emulator-command-line-tool-reference).

Cuando se cierra la ventana de la línea de comandos, el emulador almacenamiento continúa ejecutándose. Para volver a abrir la línea de comandos, siga los pasos anteriores como si iniciar el emulador de almacenamiento.

La primera vez que ejecute el emulador de almacenamiento, el entorno de almacenamiento local está inicializado para usted. El proceso de inicialización crea una base de datos en LocalDB y reserva puertos HTTP para cada servicio de almacenamiento local. 

El emulador de almacenamiento está instalado en el directorio de emulador C:\Program Files (x86) \Microsoft SDKs\Azure\Storage de forma predeterminada. 

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicializar el emulador de almacenamiento para usar otra base de datos SQL

Puede usar la herramienta de línea de comandos de almacenamiento emulador iniciar el emulador de almacenamiento para que apunten a una instancia de base de datos SQL distinta de la instancia de LocalDB predeterminado. Tenga en cuenta que debe ejecutar la herramienta de línea de comandos con privilegios de administrador para iniciar la base de datos back-end para el emulador de almacenamiento:

1. Haga clic en el botón **Inicio** o presione la tecla de **Windows** . Empiece a escribir `Azure Storage Emulator` y selecciónelo cuando aparece para que aparezca la herramienta de línea de comandos de almacenamiento emulador.
2. En la ventana de símbolo del sistema, escriba el comando siguiente, donde `<SQLServerInstance>` es el nombre de la instancia de SQL Server. Para utilizar LocalDb, especifique `(localdb)\v11.0` como la instancia de SQL Server.

        AzureStorageEmulator init /server <SQLServerInstance> 
    
    También puede usar el siguiente comando, que indica el emulador para usar la instancia de SQL Server predeterminada:

        AzureStorageEmulator init /server .\\ 

    O bien, puede usar el comando siguiente, los volver a la base de datos a la instancia de LocalDB predeterminada:

        AzureStorageEmulator init /forceCreate 

Para obtener más información acerca de estos comandos, vea [Referencia de herramienta de línea de comandos de almacenamiento emulador](#storage-emulator-command-line-tool-reference).

## <a name="addressing-resources-in-the-storage-emulator"></a>Recursos de direcciones en el emulador de almacenamiento

Los extremos de servicio para el emulador de almacenamiento son diferentes a las de una cuenta de almacenamiento de Azure. La diferencia es debido a que el equipo local no realiza la resolución de nombres de dominio, por lo que los extremos de almacenamiento emulador requieren una dirección local en lugar de un nombre de dominio.

Cuando la dirección de un recurso en una cuenta de almacenamiento de Azure, use la combinación siguiente, donde el nombre de cuenta forma parte del nombre de host URI y el recurso que se enfrenta es parte de la ruta de acceso URI:

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>

Por ejemplo, el siguiente URI es una dirección válida para un blob en una cuenta de almacenamiento de Azure:

    https://myaccount.blob.core.windows.net/mycontainer/myblob.txt

En el emulador de almacenamiento, porque el equipo local no realiza la resolución de nombres de dominio, nombre de la cuenta forma parte de la ruta de acceso URI en lugar del nombre de host. Use el siguiente esquema para un recurso que se ejecuta en el emulador de almacenamiento:

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

Por ejemplo, la siguiente dirección puede utilizarse para obtener acceso a un objeto binario en el emulador de almacenamiento:

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

Los extremos de servicio para el emulador de almacenamiento son:

    Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
    Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
    Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Escribir direcciones en la cuenta secundaria con GRS RA

A partir de la versión 3.1, la cuenta de almacenamiento emulador admite la replicación de geo redundantes de acceso de lectura (RA GRS). Para los recursos de almacenamiento en la nube y en el emulador local, puede tener acceso a la ubicación secundaria se agrega - secundario en el nombre de cuenta. Por ejemplo, la siguiente dirección podría estar acostumbrada para tener acceso a un blob utilizando el secundario de solo lectura en el emulador de almacenamiento:

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] Puede obtener acceso a la secundaria con el emulador de almacenamiento, use la biblioteca de cliente de almacenamiento para .NET versión 3.2 o posterior. Consulte la [Biblioteca de cliente de Microsoft Azure almacenamiento de .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) para obtener más detalles.

## <a name="storage-emulator-command-line-tool-reference"></a>Referencia de la herramienta de línea de comandos de almacenamiento emulador

Comenzando en la versión 3.0, inicie el emulador de almacenamiento, se muestra una ventana de línea de comandos emergente. Utilice la ventana de línea de comandos para iniciar y detener el emulador, así como para el estado de la consulta y realizar otras operaciones.

> [AZURE.NOTE] Si tiene Microsoft Azure calcular emulador instalado, aparecerá un icono de la bandeja del sistema cuando se inicia el emulador de almacenamiento. Haga clic en el icono para mostrar un menú, que proporciona una forma gráfica para iniciar y detener el emulador de almacenamiento.

### <a name="command-line-syntax"></a>Sintaxis de la línea de comandos

    AzureStorageEmulator [start] [stop] [status] [clear] [init] [help]

### <a name="options"></a>Opciones

Para ver la lista de opciones, escriba `/help` en el símbolo del sistema.

| Opción | Descripción                                                    | Comando                                                                                                 | Argumentos                                                                                                         |
|--------|----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **Inicio**  | Se inicia el emulador de almacenamiento.                                | `AzureStorageEmulator start [-inprocess]`                                                                    | *-inprocess*: iniciar el emulador en el proceso actual en lugar de crear un nuevo proceso.                          |
| **Detener**   | Deja el emulador de almacenamiento.                                    | `AzureStorageEmulator stop`                                                                                  |                                                                                                                   |
| **Estado** | Imprime el estado del emulador de almacenamiento.                     | `AzureStorageEmulator status`                                                                                |                                                                                                                   |
| **Borrar**  | Borra los datos de todos los servicios que se especifica en la línea de comandos. | `AzureStorageEmulator clear [blob] [table] [queue] [all]                                                    `| *blobs*: borra blob datos. <br/>*cola*: borra los datos de cola. <br/>*tabla*: borra los datos de la tabla. <br/>*todo*: borra todos los datos en todos los servicios. |
| **Inicialización**   | Realiza una inicialización única para configurar el emulador.       | `AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate] [-inprocess]` | *-servidor Nombreservidor\nombreinstancia*: especifica el servidor que hospeda la instancia SQL. <br/>*sqlinstance - instanceName*: especifica el nombre de la instancia de SQL que se utilizará en la instancia del servidor de forma predeterminada. <br/>*-forcecreate*: fuerza la creación de la base de datos SQL, incluso si ya existe. <br/>*-inprocess*: realiza la inicialización del proceso actual en lugar de generar un proceso de nuevo. Debe iniciar el proceso actual con permisos elevados para poder realizar inicialización.          |
                                                                                                                  
## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Diferencias entre el emulador de almacenamiento y el almacenamiento de Azure

Dado que el emulador de almacenamiento es un entorno emulado ejecuta en una instancia SQL local, existen algunas diferencias de funcionalidad entre el emulador y una cuenta de almacenamiento de Azure en la nube:

- El emulador de almacenamiento es compatible con una sola cuenta fija y una clave de autenticación conocidos.

- El emulador de almacenamiento no es un servicio de almacenamiento scalable y no admite un gran número de clientes simultáneos.

- Como se describe en los [recursos de direcciones en el emulador de almacenamiento](#addressing-resources-in-the-storage-emulator), los recursos están cubiertos diferente en el emulador almacenamiento frente a una cuenta de almacenamiento de Azure. Esta diferencia es puesto que la resolución de nombres de dominio está disponible en la nube, pero no en el equipo local.

- A partir de la versión 3.1, la cuenta de almacenamiento emulador admite la replicación de geo redundantes de acceso de lectura (RA GRS). En el emulador, todas las cuentas tienen habilitado de GRS RA y nunca hay cualquier posposición entre las réplicas primaria y secundarias. Las operaciones de obtener estadísticas de servicio de blobs, obtener estadísticas de cola de servicio y obtener estadísticas de servicio de tabla son compatibles con la cuenta secundaria y siempre devolverá el valor de la `LastSyncTime` elemento de respuesta como la hora actual, según la base de datos SQL subyacente.

    Puede obtener acceso a la secundaria con el emulador de almacenamiento, use la biblioteca de cliente de almacenamiento para .NET versión 3.2 o posterior. Consulte la [Biblioteca de cliente de Microsoft Azure almacenamiento de .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) para obtener más detalles.

- El servicio de archivo y los extremos del servicio de protocolo SMB no se admiten actualmente en el emulador de almacenamiento.

- El emulador almacenamiento devuelve un error de VersionNotSupportedByEmulator (código de estado HTTP 400 - Solicitud incorrecta) si usa una versión de los servicios de almacenamiento que todavía es incompatible con la versión del emulador que está utilizando.

### <a name="differences-for-blob-storage"></a>Diferencias de almacenamiento de blobs 

Las siguientes diferencias se aplican al almacenamiento de blobs de Windows en el emulador:

- El almacenamiento emulador solo es compatible con blob tamaños de hasta 2 GB.

- Una operación de colocar Blob puede tener éxito contra un blob que existe en el emulador de almacenamiento y tiene una concesión activa, incluso si el identificador de concesión no se ha especificado como parte de la solicitud. 

- Anexar Blob operaciones no son compatibles con el emulador. Intentando una operación en un objeto binario de datos anexados, devuelve un error de FeatureNotSupportedByEmulator (código de estado HTTP 400 - Solicitud incorrecta).

### <a name="differences-for-table-storage"></a>Diferencias de almacenamiento de tablas 

Las siguientes diferencias se aplican a almacenamiento de tablas en el emulador:

- Propiedades de fecha en el servicio de la tabla en el emulador de almacenamiento admiten sólo el intervalo compatible con SQL Server 2005 (*es decir*, están necesarios ser posterior al 1 de enero de 1753). Todas las fechas anteriores al 1 de enero de 1753 se cambian a este valor. La precisión de las fechas se limita a la precisión de SQL Server 2005, lo que significa que las fechas son precisas a 1/300th de segundo.

- El emulador de almacenamiento es compatible con los valores de propiedad de clave de partición clave y fila de menos de 512 bytes. Además, el tamaño total de los nombres de propiedad clave juntos, el nombre de la tabla y el nombre de la cuenta no puede superar 900 bytes.

- El tamaño total de una fila de una tabla en el emulador de almacenamiento se limita a menos de 1 MB.

- En el emulador de almacenamiento, escriba propiedades de datos `Edm.Guid` o `Edm.Binary` solo admite la `Equal (eq)` y `NotEqual (ne)` cadenas de filtro de operadores de comparación de consulta.

### <a name="differences-for-queue-storage"></a>Diferencias de almacenamiento en cola

No hay ningún diferencias específicas de almacenamiento de cola en el emulador.

## <a name="storage-emulator-release-notes"></a>Notas de la versión de almacenamiento emulador

### <a name="version-45"></a>Versión 4.5

- Corregir un error que provocó inicialización e instalación del emulador de almacenamiento no se realice correctamente cuando se cambió el nombre de la base de datos de copia de seguridad.

### <a name="version-44"></a>Versión 4.4

- Emulador de almacenamiento admite ahora versión 2015-12-11 de los servicios de almacenamiento de blobs, cola y tabla extremos de servicio.

- Recolección del emulador de almacenamiento de datos blob ahora es más eficaz cuando se trabaja con grandes cantidades de BLOB.

- Corregir un error que provocó el contenedor ACL XML validando ligeramente diferente de cómo lo hace el servicio de almacenamiento.

- Corregir un error que provocó a veces max y min valores de fecha y hora para registrarse en la zona horaria incorrecta.

### <a name="version-43"></a>Versión 4.3

- Emulador de almacenamiento ahora admite la versión 2015-07-08 de los servicios de almacenamiento de blobs, cola y tabla extremos de servicio.

### <a name="version-42"></a>Versión 4.2

- El emulador de almacenamiento admite ahora versión 2015-04-05 de los servicios de almacenamiento en los extremos de servicio de blobs de Windows, la cola y la tabla.

### <a name="version-41"></a>Versión 4.1

- Emulador de almacenamiento ahora es compatible con la versión 2015-02-21 de los servicios de almacenamiento de blobs, cola y tabla extremos de servicio, con la excepción de las nuevas características de blobs de anexar. 

- El emulador de almacenamiento ahora devuelve un mensaje de error significativo si usa una versión de los servicios de almacenamiento que no es compatible todavía con esa versión del emulador. Le recomendamos que uses la última versión del emulador. Si se produce un error de VersionNotSupportedByEmulator (código de estado HTTP 400 - Solicitud incorrecta), descargue la última versión del emulador de almacenamiento.

- Corrige un error en una carrera condición causado tabla entidad de datos para ser incorrectas durante las operaciones de combinación simultáneas.

### <a name="version-40"></a>Versión 4.0

- El emulador de almacenamiento ejecutable se ha cambiado a *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Versión 3.2

- Emulador de almacenamiento ahora admite la versión 2014-02-14 de los servicios de almacenamiento de blobs, cola y tabla extremos de servicio. Tenga en cuenta que los extremos de servicio de archivo no se admiten actualmente en el emulador de almacenamiento. Consulte [control de versiones para los servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd894041.aspx) para más información sobre la versión de 2014-02-14.

### <a name="version-31"></a>Versión 3.1

- Almacenamiento de acceso de lectura geo redundantes (RA GRS) ahora es compatible con el emulador de almacenamiento. Las API de estadísticas de servicio de tabla de obtener, obtener estadísticas de servicio de cola y obtener estadísticas de servicio de blobs son compatibles para la cuenta secundaria y siempre devolverá el valor del elemento de respuesta LastSyncTime como la hora actual, según la base de datos SQL. Puede obtener acceso a la secundaria con el emulador de almacenamiento, use la biblioteca de cliente de almacenamiento para .NET versión 3.2 o posterior. Para obtener información detallada, consulte la biblioteca de cliente de Microsoft Azure almacenamiento para .NET referencia.

### <a name="version-30"></a>Versión 3.0

- El emulador de almacenamiento de Azure ya no se incluye en el mismo paquete como emulador de cálculo.

- La interfaz de usuario gráfica de almacenamiento emulador ha quedado desusada una interfaz de línea de comandos secuencias de comandos. Para obtener detalles sobre la interfaz de línea de comandos, vea Referencia de herramienta de línea de comandos de almacenamiento emulador. La interfaz gráfica seguirán presentes en la versión 3.0, pero solo puede obtenerse cuando se instala el emulador calcular haciendo doble clic en el icono de la bandeja del sistema y seleccionando Mostrar la interfaz de usuario de emulador del almacenamiento.

- Versión 2013-08-15 de los servicios de almacenamiento de Azure ahora está totalmente compatibles. (Esta versión se admitía únicamente por almacenamiento emulador versión 2.2.1 vista previa.)
