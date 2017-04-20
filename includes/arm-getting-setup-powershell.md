## <a name="setting-up-powershell-for-resource-manager-templates"></a>Configuración de PowerShell para plantillas de administrador de recursos

Antes de poder usar PowerShell de Azure con el Administrador de recursos, debe tener el derecho de Windows PowerShell y versiones de PowerShell de Azure.

### <a name="verify-powershell-versions"></a>Comprobar las versiones de PowerShell

Compruebe que dispone de Windows PowerShell versión 3.0 o 4.0. Para buscar la versión de Windows PowerShell, escriba este comando en el símbolo del sistema de Windows PowerShell.

    $PSVersionTable

Recibirá el siguiente tipo de información:

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2


Compruebe que el valor de **PSVersion** es 3.0 o 4.0. Si no es así, vea [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

### <a name="set-your-azure-account-and-subscription"></a>Configurar su cuenta de Azure y suscripción

Si todavía no tiene una suscripción de Azure, puede activar los [beneficios de suscriptor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o signo hacia arriba a una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).

Abra un símbolo del sistema de PowerShell de Azure e inicie sesión en Azure con este comando.

    Login-AzureRmAccount

Si tiene varias suscripciones de Azure, se pueden mostrar las suscripciones Azure con este comando.

    Get-AzureRmSubscription

Recibirá el siguiente tipo de información:

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName :
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Puede configurar la suscripción actual de Azure ejecutando estos comandos en el símbolo del sistema de PowerShell de Azure. Reemplazar todo el contenido de las comillas, incluyendo la < y > caracteres, con el nombre correcto.

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

Para obtener más información acerca de las suscripciones de Azure y cuentas, vea [Cómo: conectarse a su suscripción](powershell-install-configure.md#Connect).
