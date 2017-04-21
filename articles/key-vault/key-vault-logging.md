<properties
    pageTitle="Registro de depósito clave Azure | Microsoft Azure"
    description="Use este tutorial para ayudarle a empezar a trabajar con Azure depósito de clave de registro."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/31/2016"
    ms.author="cabailey"/>

# <a name="azure-key-vault-logging"></a>Registro de Azure depósito clave #
Azure depósito clave está disponible en la mayoría de las regiones. Para obtener más información, consulte la [página de precios de depósito de clave](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introducción  
Después de haber creado uno o más depósitos claves, es probable que desee supervisar cómo y cuándo su claves depósitos son acceder y por quién. Para ello, puede habilitar el registro de clave de cámara, que guarda información en una cuenta de almacenamiento de Azure que proporciona. Se crea automáticamente un nuevo contenedor denominado **auditevent de registros de información** para su cuenta de almacenamiento especificada y puede utilizar esta misma cuenta de almacenamiento para recopilar registros de varios depósitos claves.

Puede tener acceso a su información de registro máximo, 10 minutos después de la clave depósito operación. En la mayoría de los casos, es más rápido que esto.  Es hacia arriba para administrar los registros en su cuenta de almacenamiento:

- Utilizar los métodos de control de acceso de Azure estándar para proteger los registros por restringir quién puede tener acceso a ellos.
- Eliminar los registros que ya no desea conservar en su cuenta de almacenamiento.

Use este tutorial para ayudarle a empezar a trabajar con Azure depósito de clave de registro para crear su cuenta de almacenamiento, habilitar el registro e interpretar la información de registro de información recopilada.  


>[AZURE.NOTE]  Este tutorial no incluye las instrucciones crear depósitos claves, claves o información confidencial. Para esta información, vea [Introducción a Azure clave depósito](key-vault-get-started.md). O bien, para obtener instrucciones de la interfaz de línea de comandos de varias plataformas, vea [este tutorial equivalente](key-vault-manage-with-cli.md).
>
>Actualmente, no puede configurar depósito de clave de Azure en el portal de Azure. En su lugar, siga estas instrucciones de PowerShell de Azure.

Los registros que haya reunido pueden visualizarse usando análisis de registro de la serie de administración de operaciones. Para obtener más información, vea [solución de Azure clave depósito (Preview) en el análisis de registro](../log-analytics/log-analytics-azure-key-vault.md).

Para obtener información general acerca de la cámara de clave de Azure, consulte [¿Qué es depósito de clave de Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, debe tener el siguiente:

- Depósito de clave existente que ha estado utilizando.  
- PowerShell Azure, **versión mínima de 1.0.1**. Para instalar Azure PowerShell y asociar con su suscripción de Azure, consulte [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md). Si ya ha instalado Azure PowerShell y no conoce la versión, desde la consola de PowerShell de Azure, escriba `(Get-Module azure -ListAvailable).Version`.  
- Suficiente espacio de almacenamiento en Azure para los registros del depósito de clave.


## <a id="connect"></a>Conectarse a las suscripciones ##

Iniciar una sesión de Azure PowerShell e inicie sesión en su cuenta de Azure con el siguiente comando:  

    Login-AzureRmAccount

En la ventana emergente del explorador, escriba su nombre de usuario de la cuenta de Azure y la contraseña. PowerShell Azure obtendrá todas las suscripciones que están asociados a esta cuenta y de forma predeterminada, se utiliza la primera.

Si tiene varias suscripciones, debe especificar una específica que se usó para crear su depósito de clave de Azure. Escriba lo siguiente para ver las suscripciones para su cuenta:

    Get-AzureRmSubscription

A continuación, para especificar la suscripción que está asociada a su cámara clave que inicie sesión, escriba:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Para obtener más información acerca de cómo configurar Azure PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).


## <a id="storage"></a>Crear una nueva cuenta de almacenamiento para los registros ##

Aunque puede utilizar una cuenta existente de almacenamiento para los registros, se creará una nueva cuenta de almacenamiento que se dedicará a los registros de depósito de clave. Para mayor comodidad para cuando hay que especificar esta más adelante, se almacenará los detalles en una variable denominada **sa**.

Para facilitar la administración adicional, usaremos también el mismo grupo de recursos que contiene nuestro depósito clave. [Tutorial de introducción](key-vault-get-started.md), este grupo de recursos se denomina **ContosoResourceGroup** y se continuará utilizando la ubicación de Asia oriental. Sustituir estos valores para su propia, según corresponda:

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name ContosoKeyVaultLogs -Type Standard_LRS -Location 'East Asia'


>[AZURE.NOTE]  Si decide usar una cuenta existente de almacenamiento, debe usar la misma suscripción como su depósito clave y debe utilizar el modelo de implementación de administrador de recursos, en lugar del modelo de implementación de clásico.

## <a id="identify"></a>Identificar el depósito clave para los registros ##

En nuestro tutorial Introducción al obtener nuestro nombre de depósito clave fue **ContosoKeyVault**, por lo que vamos a continuar usar ese nombre y almacenar los detalles en una variable denominada **kv**:

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Habilitar el registro ##

Para habilitar el registro para depósito clave, usaremos el cmdlet Set-AzureRmDiagnosticSetting, junto con las variables que se creó para nuestra nueva cuenta de almacenamiento y nuestra depósito clave. También se configure la **-habilitado** marcar **$true** y establecer la categoría a AuditEvent (la única categoría para el registro de clave depósito,):


    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

El resultado de esta incluye:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0


Esto confirma que el registro está habilitado para su depósito clave, guardar la información de su cuenta de almacenamiento.

Opcionalmente, también puede establecer Directiva de retención para los registros que se eliminarán automáticamente los registros antiguos. Por ejemplo, establecer Directiva de retención con marca **- RetentionEnabled** **$true** y establezca el parámetro de **-RetentionInDays** a **90** para que los registros de más de 90 días se eliminarán automáticamente.

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

¿Qué se registra:

- Se registran todas las solicitudes de API de REST autenticadas, que incluye las solicitudes con error como resultado de permisos de acceso, errores del sistema o solicitudes incorrectas.
- Operaciones en la tecla depósito propio, que incluye la creación, la eliminación, la configuración de directivas de acceso de clave depósito, y actualizar los atributos del depósito clave como etiquetas.
- Operaciones de claves y la información confidencial en la caja fuerte clave, que incluye crear, modificar o eliminar estas claves o información confidencial; operaciones como inicio de sesión, comprobar, cifrar, descifrar, ajustar y desajustar teclas, obtener información confidencial, las claves de la lista e información confidencial y sus versiones.
- Solicitudes no autenticadas como resultado una respuesta 401. Por ejemplo, las solicitudes que no tiene un token portador, u o son incorrectos expirada o tienen un símbolo no válido.  


## <a id="access"></a>Obtener acceso a los registros ##

Registros de depósito clave se almacenan en el contenedor de **auditevent de registros de información** de la cuenta de almacenamiento que ha proporcionado. Para obtener una lista de todos los BLOB en este contenedor, escriba:

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

El resultado tendrá un aspecto algo parecido a este:

**Uri del contenedor: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**


**Nombre**

**----**

**resourceId = / suscripciones/361DA5D4-A47A-79 de C 4-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/proveedores/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId = / suscripciones/361DA5D4-A47A-79 de C 4-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/proveedores/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

** resourceId = / suscripciones/361DA5D4-A47A-79 de C 4-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/proveedores/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json***


Como puede ver este resultado, los BLOB siguen una convención de nomenclatura: **resourceId =<ARM resource ID>/ y =<year>/m =<month>/d =<day of month>/h =<hour>/m =<minute>/filename.json**

Los valores de fecha y hora use UTC.

Debido a la misma cuenta de almacenamiento puede usarse para recopilar registros para varios recursos, el identificador de recursos completo en el nombre de blob es muy útil para tener acceso o descargar solo los BLOB que necesita. Pero antes de hacerlo, primero trataremos cómo descargar todos los BLOB.

Primero, cree una carpeta para descargar el BLOB. Por ejemplo:

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

A continuación, obtener una lista de todos los blobs:  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Canalización esta lista a través de 'Get-AzureStorageBlobContent' descargar el BLOB en la carpeta de destino:

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

Cuando ejecuta este comando de la segunda, el **/** delimitador en los nombres de blobs crear una estructura de carpetas completa en la carpeta de destino y, a continuación, esta estructura se usará para descargar y almacenar los BLOB como archivos.

Para descargar selectivamente BLOB, usar caracteres comodín. Por ejemplo:

- Si tiene varios depósitos claves y desea descargar registros para un solo depósito clave, denominado CONTOSOKEYVAULT3:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3

- Si tiene varios grupos de recursos y desea descargar registros para un solo grupo de recursos, use `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'

- Si desea descargar todos los registros para el mes de enero de 2016, use `-Blob '*/year=2016/m=01/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

Ahora ya está listo para comenzar a pensar en lo que está en los registros. Pero antes de pasar hasta que, dos parámetros más para obtener AzureRmDiagnosticSetting que necesita saber:

- Para consultar el estado de la configuración de diagnóstico para el recurso de depósito clave:`Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`

- Para deshabilitar el registro para el recurso de depósito clave:`Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`


## <a id="interpret"></a>Interpretar los registros de depósito de clave ##

BLOB individual se almacena como texto, con formato de un blob JSON. Se trata de una entrada de registro de ejemplo se ejecuten `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }


La siguiente tabla enumeran los nombres de campo y las descripciones.


| Nombre de campo        | Descripción |
| ------------- |-------------|
| hora      | Fecha y hora (UTC).|
| resourceId      | Id. de recurso de administrador de recursos de Azure Para los registros del depósito de clave, siempre es el identificador de recurso de clave depósito.|
| operationName      | Nombre de la operación, tal como se describe en la tabla siguiente.|
| operationVersion      | Esta es la versión de la API de REST solicitada por el cliente.|
| categoría      | Para los registros del depósito de clave, AuditEvent es el valor único, disponible.|
| resultType      | Resultado de solicitud de API de REST.|
| resultSignature      | Estado HTTP.|
| resultDescription     | Descripción adicional acerca del resultado, cuando esté disponible.|
| durationMs      | Tiempo que tardó la solicitud de la API de REST, en milisegundos. Esto no incluye la latencia de red, por lo que el tiempo que se mide en el cliente no puede ajustarse a esta vez.|
| callerIpAddress      | Dirección IP del cliente que realizó la solicitud.|
| correlationId      | GUID de opcional que el cliente puede pasar para relacionar registros del cliente con los registros del servicio (depósito de clave).|
| identidad      | Identidad del token que se presenta al realizar la solicitud de la API de REST. Suele ser un "usuario", "principales de servicio" o una combinación "usuario + ID" como en caso de una solicitud es el resultado de un cmdlet de PowerShell de Azure.|
| propiedades      | Este campo contendrá información diferente en función de la operación (operationName). En la mayoría de los casos, contiene información del cliente (la cadena useragent pasada por el cliente), la URI de la solicitud de API de REST exacto y el código de estado HTTP. Además, cuando se devuelve un objeto como resultado de una solicitud (por ejemplo, KeyCreate o VaultGet) también contendrá el URI clave (como "id"), URI de cámara o URI de secreto.|




Los valores de campo **operationName** están en formato de ObjectVerb. Por ejemplo:

- Todas las operaciones de depósito clave tienen el ' depósito`<action>`' dar formato como `VaultGet` y `VaultCreate`.

- Todas las operaciones claves tienen la ' clave`<action>`' dar formato como `KeySign` y `KeyList`.

- Todas las operaciones secretas tienen el ' secreto`<action>`' dar formato como `SecretGet` y `SecretListVersions`.

La siguiente tabla enumera los operationName y el comando de API de REST correspondiente.

| operationName        | Comando de la API de REST |
| ------------- |-------------|
| Autenticación      | A través de extremo de Azure Active Directory|
| VaultGet      | [Obtener información sobre cómo un depósito clave](https://msdn.microsoft.com/en-us/library/azure/mt620026.aspx)|
| VaultPut      | [Crear o actualizar un depósito clave](https://msdn.microsoft.com/en-us/library/azure/mt620025.aspx)|
| VaultDelete      | [Eliminar un depósito clave](https://msdn.microsoft.com/en-us/library/azure/mt620022.aspx)|
| VaultPatch      | [Actualizar un depósito clave](https://msdn.microsoft.com/library/azure/mt620025.aspx)|
| VaultList      | [Lista de todos los depósitos claves en un grupo de recursos](https://msdn.microsoft.com/en-us/library/azure/mt620027.aspx)|
| KeyCreate      | [Crear una clave](https://msdn.microsoft.com/en-us/library/azure/dn903634.aspx)|
| KeyGet      | [Obtener información acerca de una clave](https://msdn.microsoft.com/en-us/library/azure/dn878080.aspx)|
| KeyImport      | [Importar una clave en un depósito](https://msdn.microsoft.com/en-us/library/azure/dn903626.aspx)|
| KeyBackup      | [Copia de seguridad de una clave](https://msdn.microsoft.com/en-us/library/azure/dn878058.aspx).|
| KeyDelete      | [Eliminar una clave](https://msdn.microsoft.com/en-us/library/azure/dn903611.aspx)|
| KeyRestore      | [Restaurar una clave](https://msdn.microsoft.com/en-us/library/azure/dn878106.aspx)|
| KeySign      | [Inicie sesión con una clave](https://msdn.microsoft.com/en-us/library/azure/dn878096.aspx)|
| KeyVerify      | [Compruebe con una clave](https://msdn.microsoft.com/en-us/library/azure/dn878082.aspx)|
| KeyWrap      | [Ajustar una clave](https://msdn.microsoft.com/en-us/library/azure/dn878066.aspx)|
| KeyUnwrap      | [Desajustar una clave](https://msdn.microsoft.com/en-us/library/azure/dn878079.aspx)|
| KeyEncrypt      | [Cifrar con una clave](https://msdn.microsoft.com/en-us/library/azure/dn878060.aspx)|
| KeyDecrypt      | [Descifrar con una clave](https://msdn.microsoft.com/en-us/library/azure/dn878097.aspx)|
| KeyUpdate      | [Actualizar una clave](https://msdn.microsoft.com/en-us/library/azure/dn903616.aspx)|
| Lista de claves      | [Lista de las teclas en un depósito](https://msdn.microsoft.com/en-us/library/azure/dn903629.aspx)|
| KeyListVersions      | [Lista de versiones de una clave](https://msdn.microsoft.com/en-us/library/azure/dn986822.aspx)|
| SecretSet      | [Crear un secreto](https://msdn.microsoft.com/en-us/library/azure/dn903618.aspx)|
| SecretGet      | [Obtener secreta](https://msdn.microsoft.com/en-us/library/azure/dn903633.aspx)|
| SecretUpdate      | [Actualizar un secreto](https://msdn.microsoft.com/en-us/library/azure/dn986818.aspx)|
| SecretDelete      | [Eliminar un secreto](https://msdn.microsoft.com/en-us/library/azure/dn903613.aspx)|
| SecretList      | [Información confidencial de la lista en un depósito](https://msdn.microsoft.com/en-us/library/azure/dn903614.aspx)|
| SecretListVersions      | [Lista de versiones de un secreto](https://msdn.microsoft.com/en-us/library/azure/dn986824.aspx)|




## <a id="next"></a>Pasos siguientes ##

Para obtener un tutorial que utiliza depósito de clave de Azure en una aplicación web, vea [Usar Azure clave depósito desde una aplicación Web](key-vault-use-from-web-application.md).

Para programar referencias, vea [Guía del desarrollador de Azure clave depósito](key-vault-developers-guide.md).

Para obtener una lista de los cmdlets de PowerShell 1.0 de Azure para Azure clave depósito, vea [Cmdlets de depósito de clave de Azure](https://msdn.microsoft.com/library/azure/dn868052.aspx).

Para obtener un tutorial sobre la rotación de clave y registro de auditoría con Azure clave depósito, vea [cómo configurar clave depósito con la rotación de clave de llevar a cabo y auditoría](key-vault-key-rotation-log-monitoring.md).
