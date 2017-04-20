<properties
    pageTitle="Administración de recursos con .NET de lote de administración de la cuenta | Microsoft Azure"
    description="Crear, eliminar y modificar recursos de cuenta de Azure lote con la biblioteca de .NET de administración del lote."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/19/2016"
    ms.author="marsma"/>

# <a name="manage-azure-batch-accounts-and-quotas-with-batch-management-net"></a>Administrar cuentas de Azure lote y las cuotas de lote administración .NET

> [AZURE.SELECTOR]
- [Portal de Azure](batch-account-create-portal.md)
- [Administración de lote .NET](batch-management-dotnet.md)

Puede reducir mantenimiento sobrecarga en las aplicaciones de Azure lote mediante el [Lote de administración .NET] [ api_mgmt_net] biblioteca para automatizar la creación de la cuenta de proceso por lotes, eliminación, administración de claves y detección de cuota.

- **Crear y eliminar las cuentas por lotes** dentro de cualquier región. Si, por ejemplo, como un proveedor de software independiente (ISV) proporciona un servicio para los clientes en el que cada uno se asigna una cuenta de proceso por lotes independiente con fines de facturación, puede agregar capacidades de creación y eliminación de la cuenta en el portal de cliente.
- **Recuperar y volver a crear cuenta teclas** mediante programación por cualquiera de sus cuentas de lote. Esto puede ayudarle a cumplir con las directivas de seguridad que imponen renovación periódica o expiración de las teclas de la cuenta. Cuando tiene varias cuentas de proceso por lotes en varias áreas de Azure, automatización de este proceso de renovación aumenta la eficacia de su solución.
- **Compruebe las cuotas de cuenta** y tomar las suposiciones de prueba y error determinar qué cuentas lote tienen los límites. Activando las cuotas de cuenta antes de iniciar trabajos crear grupos o agregar nodos de cálculo, puede ajustar proactiva dónde o cuando estos calculan recursos se crean. Puede determinar las cuentas que requieren cuota aumenta antes de asignar recursos adicionales en estas cuentas.
- **Combinar las características de otros servicios de Azure** para una experiencia de administración completos--mediante .NET de administración de proceso por lotes, [Azure Active Directory][aad_about]y el [Administrador de recursos de Azure] [ resman_overview] juntos en la misma aplicación. Mediante estas funciones y sus API, puede proporcionar una experiencia de autenticación sin dificultades, la capacidad de crear y eliminar grupos de recursos y las capacidades que descritos anteriormente para una solución de administración de llevar a cabo.

> [AZURE.NOTE] Aunque en este artículo se centra en la administración de sus cuentas de proceso por lotes, las teclas y las cuotas de programación, puede realizar muchas de estas actividades a través del [portal de Azure][azure_portal]. Para obtener más información, vea [crear una cuenta de Azure lote con el portal de Azure](batch-account-create-portal.md) y [las cuotas y límites del servicio de Azure lote](batch-quota-limit.md).

## <a name="create-and-delete-batch-accounts"></a>Crear y eliminar las cuentas por lotes

Como se mencionó, una de las principales características de la API de administración de lote es crear y eliminar cuentas de lote en un área de Azure. Para ello, use [BatchManagementClient.Account.CreateAsync] [ net_create] y [DeleteAsync][net_delete], o sus homólogos sincrónicos.

Fragmento de código siguiente crea una cuenta, obtiene la cuenta recién creada desde el servicio de lote y, a continuación, elimina. En este fragmento de código y otros usuarios en este artículo, `batchManagementClient` es una instancia totalmente inicializada de [BatchManagementClient][net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE] Aplicaciones que utilizan la biblioteca de lote administración .NET y su clase BatchManagementClient requieren acceso de **Administrador de servicio** o **coadministrator** a la suscripción que es el propietario de la cuenta del lote deben administrarse. Para obtener más información, consulte la sección de [Azure Active Directory](#azure-active-directory) y la [AccountManagement] [ acct_mgmt_sample] código de ejemplo.

## <a name="retrieve-and-regenerate-account-keys"></a>Recuperar y generar las claves de cuenta

Obtener claves principales y secundarias cuenta desde cualquier cuenta por lotes dentro de su suscripción con [ListKeysAsync][net_list_keys]. Volver a crear las claves usando [RegenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [AZURE.TIP] Puede crear un flujo de trabajo de conexión simplificada para las aplicaciones de administración. En primer lugar, obtener una clave de cuenta para la cuenta del lote que desea administrar con [ListKeysAsync][net_list_keys]. A continuación, utilizar esta clave al inicialización de la biblioteca de .NET lote [BatchSharedKeyCredentials] [ net_sharedkeycred] clase, que se utiliza cuando inicialización [BatchClient][net_batch_client].

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Revisar la suscripción de Azure y las cuotas de cuenta por lotes

Suscripciones de Azure y los servicios de Azure individuales como lote todos tienen cuotas predeterminadas que limitan el número de determinadas entidades dentro de ellas. Para las cuotas predeterminadas para las suscripciones de Azure, consulte [suscripción Azure y límites de servicio, cuotas y las restricciones](./../azure-subscription-service-limits.md). Para las cuotas predeterminadas del servicio por lotes, consulte [cuotas y límites para el servicio del lote de Azure](batch-quota-limit.md). Mediante el uso de la biblioteca de .NET de administración de lote, puede comprobar estas cuotas en las aplicaciones. Esto le permite tomar decisiones de asignación antes de agregar cuentas o calcular recursos como grupos y nodos de cálculo.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Comprobar una suscripción de Azure para las cuotas de cuenta por lotes

Antes de crear una cuenta de lote en una región, puede comprobar su suscripción de Azure para ver si se puede agregar una cuenta de esa región.

En el fragmento de código siguiente, usamos primero [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] para obtener una colección de todas las cuentas de proceso por lotes que están dentro de una suscripción. Una vez que hemos obtenido esta colección, se determinar cuántas cuentas están en la región de destino. Después, use [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] para obtener la cuota de la cuenta de proceso por lotes y determinar cuántas cuentas (si existe) se pueden crear en dicha región.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

En el fragmento de código anterior, `creds` es una instancia de [TokenCloudCredentials][azure_tokencreds]. Para ver un ejemplo de cómo crear este objeto, consulte la [AccountManagement] [ acct_mgmt_sample] código de ejemplo en GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Comprobar una cuenta por lotes las cuotas de recursos de cálculo

Antes de aumentar los recursos de cálculo en la solución de lote, puede comprobar garantizar que los recursos que desea asignar no supere las cuotas de la cuenta. En el fragmento de código siguiente, se imprime la información de cuota de la cuenta de proceso por lotes denominada `mybatchaccount`. En su propia aplicación, podría usar esta información para determinar si la cuenta puede controlar los recursos adicionales que debe crearse.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT] Si bien hay cuotas predeterminadas para los servicios y suscripciones Azure, muchos de estos límites pueden ser generados por emitir una solicitud en el [portal de Azure][azure_portal]. Por ejemplo, vea [las cuotas y límites para el servicio de Azure lote](batch-quota-limit.md) para obtener instrucciones sobre el aumento de las cuotas de cuenta por lotes.

## <a name="batch-management-net-azure-ad-and-resource-manager"></a>Lote administración .NET, Azure AD y el Administrador de recursos

Al trabajar con la biblioteca de lote Management. NET, normalmente también se usa [Azure Active Directory] [ aad_about] (Azure AD) y el [Administrador de recursos de Azure][resman_overview]. El proyecto de ejemplo que se describen a continuación utiliza Azure Active Directory y el Administrador de recursos mientras se muestra la API de .NET lote de administración.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure utiliza Azure AD para la autenticación de sus clientes, los administradores de servicios y los usuarios de la organización. En el contexto de lote Management. NET, se utiliza Azure AD para autenticar un administrador de la suscripción o el Administrador de compañeros. Esto permite a la biblioteca de administración consultar el servicio de lote y realizar las operaciones descritas en este artículo.

En el proyecto de ejemplo descritos a continuación, la Azure [Active Directory Authentication Library] [ aad_adal] (ADAL) se usa para solicitar al usuario sus credenciales de Microsoft. Cuando se proporcionan las credenciales de administrador o coadministrator de servicio, la aplicación puede consultar Azure para obtener una lista de suscripciones y puede crear y eliminar grupos de recursos y las cuentas por lotes.

### <a name="resource-manager"></a>Administrador de recursos

Al crear cuentas de lote con la biblioteca de .NET de administración de proceso por lotes, normalmente creará ellos dentro de un [grupo de recursos][resman_overview]. Puede crear el grupo de recursos mediante programación utilizando la [ResourceManagementClient] [ resman_client] clase en el [Administrador de recursos .NET] [ resman_api] biblioteca. O puede agregar una cuenta a un grupo de recursos existente que ha creado anteriormente mediante el [portal de Azure][azure_portal].

## <a name="sample-project-on-github"></a>Proyecto de ejemplo en GitHub

Para ver el lote de administración .NET en acción, revise la [AccountManagment] [ acct_mgmt_sample] proyecto de ejemplo en GitHub. Esta aplicación de consola muestra la creación y el uso de [BatchManagementClient] [ net_mgmt_client] y [ResourceManagementClient][resman_client]. También se muestra el uso de Azure [Active Directory Authentication Library] [ aad_adal] (ADAL), que es necesario para los clientes.

Para ejecutar la aplicación de ejemplo correctamente, debe primero registrar se con Azure AD mediante el portal de Azure. Siga los pasos de la sección [Agregar una aplicación](../active-directory/active-directory-integrating-applications.md#adding-an-application) de [integración de aplicaciones con Azure Active Directory] [ aad_integrate] registrar la aplicación de ejemplo en su cuenta de directorio predeterminado. Asegúrese de seleccionar **Aplicación nativa de cliente** para el tipo de aplicación, y puede especificar cualquier URI válido (como `http://myaccountmanagementsample`) para la **Redirección URI**: no es necesario ser un extremo real.

Después de agregar la aplicación, delegar el permiso de **Administración de servicios de Access Azure como organización** a la aplicación de la *API de administración de servicio de Windows Azure* en la configuración de la aplicación en el portal:

![Permisos de aplicación en el portal de Azure][2]

> [AZURE.TIP] Si no aparece la **API de administración de servicio de Windows Azure** en *permisos a otras aplicaciones*, haga clic en **Agregar aplicación**, seleccione la **API de administración de servicio de Windows Azure**, haga clic en el botón de marca de verificación. A continuación, delegar permisos anteriormente especificados.

Una vez que ha agregado a la aplicación como se describió anteriormente, actualice `Program.cs` en la [AccountManagment] [ acct_mgmt_sample] proyecto de ejemplo con la aplicación redirección URI e ID de cliente. Puede encontrar estos valores en la ficha **Configurar** de la aplicación:

![Configuración de la aplicación en el portal de Azure][3]

La [AccountManagment] [ acct_mgmt_sample] aplicación de ejemplo muestra las siguientes operaciones:

1. Adquirir un token de seguridad de Azure AD usando [ADAL][aad_adal]. Si el usuario no se ha iniciado sesión, le pedirá que especifique sus credenciales de Azure.
2. Mediante el token de seguridad que se obtienen de Azure AD, crear una [SubscriptionClient] [ resman_subclient] a consulta Azure para obtener una lista de suscripciones asociadas a la cuenta. De esta forma se encuentran la selección de una suscripción si hay varios.
3. Crear un objeto de credenciales asociado con la suscripción seleccionada.
4. Crear [ResourceManagementClient] [ resman_client] con las credenciales.
5. Usar [ResourceManagementClient] [ resman_client] para crear un grupo de recursos.
6. Usar [BatchManagementClient] [ net_mgmt_client] para realizar diversas operaciones de cuenta por lotes:
  - Crear una cuenta de lote en el nuevo grupo de recursos.
  - Obtener la cuenta recién creada desde el servicio de lote.
  - Imprimir las claves de cuenta para la nueva cuenta.
  - Volver a crear una nueva clave principal de la cuenta.
  - Imprimir la información de cuota de la cuenta.
  - Imprimir la información de cuota para la suscripción.
  - Imprimir todas las cuentas de la suscripción.
  - Eliminar cuenta recién creada.
7. Eliminar el grupo de recursos.

Antes de eliminar el grupo de cuentas y recursos de lote recién creado, puede inspeccionar tanto en el [portal de Azure][azure_portal]:

![Portal de Azure mostrando el grupo de recursos y de la cuenta de proceso por lotes][1]
<br />
*Portal de Azure mostrando el nuevo grupo de recursos y cuenta por lotes*

[aad_about]: ../active-directory/active-directory-whatis.md "¿Qué es Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Escenarios de autenticación para Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integración de aplicaciones con Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspxs
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
