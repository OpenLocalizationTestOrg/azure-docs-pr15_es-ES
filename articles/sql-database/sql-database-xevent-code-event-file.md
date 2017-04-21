<properties 
    pageTitle="Código de XEvent evento archivo de base de datos SQL | Microsoft Azure" 
    description="Proporciona PowerShell y Transact-SQL para código en dos fases de ejemplo que muestra el destino del archivo de evento en un evento ampliado en base de datos de SQL Azure. Almacenamiento de Azure es una parte requerida de este escenario." 
    services="sql-database" 
    documentationCenter="" 
    authors="MightyPen" 
    manager="jhubbard" 
    editor="" 
    tags=""/>


<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="genemi"/>


# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Código de destino de archivo de evento para eventos ampliados de base de datos de SQL

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Un ejemplo de código completo que desee para capturar e informe información para un evento ampliado forma sólida.


En Microsoft SQL Server, el [destino del archivo de eventos](http://msdn.microsoft.com/library/ff878115.aspx) se usa para almacenar los resultados de evento en un archivo de disco duro local. Pero estos archivos no están disponibles para la base de datos de SQL Azure. En su lugar, usamos el servicio de almacenamiento de Azure para admitir el destino del archivo de eventos.


Este tema presenta una muestra de código en dos fases:


- PowerShell, para crear un contenedor de almacenamiento de Azure en la nube.

- Transact-SQL:
 - Para asignar el contenedor de almacenamiento de Azure a un destino de archivo de eventos.
 - Para crear, iniciar la sesión de evento y así sucesivamente.


## <a name="prerequisites"></a>Requisitos previos


- Una cuenta de Azure y de suscripción. Puede suscribirse a una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).


- Puede crear una tabla en una base de datos.
 - Si lo desea, puede [crear una base de datos de demostración de **AdventureWorksLT** ](sql-database-get-started.md) en minutos.


- SQL Server Management Studio (ssms.exe), lo ideal es que su última versión actualización mensual. Puede descargar la última ssms.exe desde:
 - [Descargar SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx)tema.
 - [Un vínculo directo a la descarga.](http://go.microsoft.com/fwlink/?linkid=616025)


- Debe tener los [módulos de Azure PowerShell](http://go.microsoft.com/?linkid=9811175) instalado.
 - Los módulos proporcionan comandos como - **AzureStorageAccount de nuevo**.


## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fase 1: Código de PowerShell de para contenedor de almacenamiento de Azure


Este PowerShell es la fase 1 del ejemplo de código en dos fases.

La secuencia de comandos comienza con comandos para limpiar después de ejecutar una posible anterior y es rerunnable.



1. Pegue el script de PowerShell en un editor de textos como Notepad.exe y guarde el script como un archivo con la extensión **. ps1**.

2. Iniciar PowerShell ISE como administrador.

3. En el símbolo del sistema, escriba<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>y, a continuación, presione ENTRAR.

4. En PowerShell ISE, abra el archivo **. ps1** . Ejecute la secuencia de comandos.

5. En primer lugar, la secuencia de comandos inicia una nueva ventana en la que iniciar sesión en Azure.
 - Si volver a ejecutar la secuencia de comandos sin interrumpir la sesión, tiene la opción de comentar el comando **Agregar AzureAccount** cómoda.


![PowerShell ISE con módulo Azure instalado, listo para ejecutar la secuencia de comandos.][30_powershell_ise]


&nbsp;


```
## TODO: Before running, find all 'TODO' and make each edit!!

#--------------- 1 -----------------------


# You can comment out or skip this Add-AzureAccount
# command after the first run.
# Current PowerShell environment retains the successful outcome.

'Expect a pop-up window in which you log in to Azure.'


Add-AzureAccount

#-------------- 2 ------------------------


'
TODO: Edit the values assigned to these variables, especially the first few!
'

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME'
$policySasExpiryTime = '2016-01-28T23:44:56Z'
$policySasStartTime  = '2015-08-01'


$storageAccountName     = 'gmstorageaccountxevent'
$storageAccountLocation = 'West US'
$contextName            = 'gmcontext'
$containerName          = 'gmcontainerxevent'
$policySasToken         = 'gmpolicysastoken'


# Leave this value alone, as 'rwl'.
$policySasPermission = 'rwl'

#--------------- 3 -----------------------


# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.'


Select-AzureSubscription -SubscriptionName $subscriptionName


#-------------- 4 ------------------------


'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.'


If ($storageAccountName)
{
    Remove-AzureStorageAccount -StorageAccountName $storageAccountName
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString()

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...'

New-AzureStorageAccount `
    -StorageAccountName $storageAccountName `
    -Location           $storageAccountLocation

[System.DateTime]::Now.ToString()

[System.Media.SystemSounds]::Beep.Play()


'
Get the primary access key for your storage account.
'


$primaryAccessKey_ForStorageAccount = `
    (Get-AzureStorageKey `
        -StorageAccountName $storageAccountName).Primary

"`$primaryAccessKey_ForStorageAccount = $primaryAccessKey_ForStorageAccount"

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
'

#--------------- 6 -----------------------


# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
'

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $primaryAccessKey_ForStorageAccount


'Create a container within the storage account.
'


$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context


'Create a security policy to be applied to the SAS token.
'

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime 

'
Generate a SAS token for the container.
'
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken
}
Catch 
{
    $Error[0].Exception.ToString()
}

#-------------- 7 ------------------------


'Display the values that YOU must edit into the Transact-SQL script next!:
'

"storageAccountName: $storageAccountName"
"containerName:      $containerName"
"sasTokenWithPolicy: $sasTokenWithPolicy"

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
'

'
(Later, return here to delete your Azure Storage account. See the preceding - Remove-AzureStorageAccount -StorageAccountName $storageAccountName)'

'
Now shift to the Transact-SQL portion of the two-part code sample!'

# EOFile
```


&nbsp;


Tome nota de los valores con nombre algunos que se imprime el script de PowerShell cuando termina. Debe editar dichos valores en la secuencia de comandos de Transact-SQL que sigue a la fase 2.


## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>La fase 2: Código de Transact-SQL que utiliza el contenedor de almacenamiento de Azure


- En la fase 1 de este ejemplo de código, ejecutó un script de PowerShell para crear un contenedor de almacenamiento de Azure.
- A continuación en la fase 2, el siguiente script de Transact-SQL debe utilizar el contenedor.


La secuencia de comandos comienza con comandos para limpiar después de ejecutar una posible anterior y es rerunnable.


La secuencia de comandos de PowerShell imprime unos valores con nombre cuando terminó. Debe modificar la secuencia de comandos de Transact-SQL para usar esos valores. Buscar **TODO** en la secuencia de comandos de Transact-SQL para localizar los puntos de edición.


1. Abra SQL Server Management Studio (ssms.exe).

2. Conectarse a la base de datos de la base de datos de SQL Azure.

3. Haga clic para abrir un nuevo panel de consulta.

4. Pegue el siguiente script de Transact-SQL en el panel de consulta.

5. Busque cada **TODO** en la secuencia de comandos y realice las modificaciones correspondientes.

6. Guarde y ejecute la secuencia de comandos.


&nbsp;


> [AZURE.WARNING] El valor de clave SA generado por la secuencia de comandos de PowerShell anterior puede empezar con un '?' (signo de interrogación). Al usar la tecla SA en el siguiente script de SQL T, deberá *quitar el interlineado '?'*. En caso contrario, podrían bloquear los esfuerzos de seguridad.


&nbsp;


```
---- TODO: First, run the PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;
GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO


-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


&nbsp;


Si el destino no puede adjuntar al ejecutar, debe detener y reiniciar la sesión de evento:


```
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


&nbsp;


## <a name="output"></a>Salida


Cuando finalice la secuencia de comandos de Transact-SQL, haga clic en una celda en el encabezado de columna **event_data_XML** . Una **<event>** se muestran los elementos que muestra una instrucción UPDATE.

Aquí es una **<event>** elemento generado durante las pruebas:


&nbsp;


```
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

&nbsp;


La secuencia de comandos de Transact-SQL anterior utiliza la siguiente función del sistema para leer el event_file:

- [Sys.fn_xe_file_target_read_file (Transact-SQL)](http://msdn.microsoft.com/library/cc280743.aspx)


Explicación de las opciones avanzadas para la visualización de datos de eventos ampliados está disponible en:

- [Avanzadas de visualización de datos de destino de eventos ampliados](http://msdn.microsoft.com/library/mt752502.aspx)

&nbsp;


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Convertir el código de ejemplo para que se ejecute en SQL Server


Suponga que desea ejecutar el ejemplo anterior de Transact-SQL en Microsoft SQL Server.


- Para simplificar, ¿desea reemplazar completamente uso del contenedor de almacenamiento de Azure con una simple de archivos, como **C:\myeventdata.xel**. El archivo se deberán escribir en el disco duro local del equipo que aloja SQL Server.


- No sería necesario cualquier tipo de instrucciones Transact-SQL para **crear la clave del maestro** y **Crear CREDENCIALES**.


- En la instrucción **CREATE EVENT sesión** , en su cláusula **Agregar destino** , puede reemplazar el valor de Http asignado realizado en **nombre de archivo =** con una cadena de ruta de acceso completa como **C:\myfile.xel**.
 - No necesita estar implicada ninguna cuenta de almacenamiento de Azure.


## <a name="more-information"></a>Más información


Para obtener más información sobre las cuentas y contenedores en el servicio de almacenamiento de Azure, consulte:

- [Cómo usar el almacenamiento de blobs de .NET](../storage/storage-dotnet-how-to-use-blobs.md)
- [Nomenclatura y hacer referencia a los contenedores, BLOB y metadatos](http://msdn.microsoft.com/library/azure/dd135715.aspx)
- [Trabajar con el contenedor raíz](http://msdn.microsoft.com/library/azure/ee395424.aspx)
- [Lección 1: Crear una directiva de acceso almacenadas y una firma de acceso compartido en un contenedor de Azure](http://msdn.microsoft.com/library/dn466430.aspx)
    - [Lección 2: Crear una credencial de SQL Server con una firma de acceso compartido](http://msdn.microsoft.com/library/dn466435.aspx)




<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

