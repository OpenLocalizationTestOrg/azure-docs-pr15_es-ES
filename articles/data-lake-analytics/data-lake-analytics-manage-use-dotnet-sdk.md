<properties 
   pageTitle="Administrar el análisis de lago de datos de Azure con Azure .NET SDK | Azure" 
   description="Obtenga información sobre cómo administrar trabajos de análisis de datos lago, orígenes de datos, los usuarios. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/23/2016"
   ms.author="jgao"/>

# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Administrar el análisis de lago de datos de Azure con Azure .NET SDK

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Obtenga información sobre cómo administrar cuentas de análisis de lago de datos de Azure, orígenes de datos, los usuarios y trabajos mediante el SDK de .NET de Azure. Para ver los temas de administración con otras herramientas, haga clic en la ficha, seleccione anterior.

**Requisitos previos**

Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).


<!-- ################################ -->
<!-- ################################ -->


## <a name="connect-to-azure-data-lake-analytics"></a>Conectarse a datos de Azure lago Analytics

Necesitará los paquetes de Nuget siguientes:

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common 
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre


El ejemplo siguiente muestra cómo conectarse a Azure y la lista de las cuentas de análisis de datos lago existentes en su suscripción de Azure.

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

            private static DataLakeAnalyticsAccountManagementClient _adlaClient;

            private static void Main(string[] args)
            {

                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = SUBSCRIPTIONID;

                var adlaAccounts = ListADLAAccounts();

                Console.WriteLine("You have %i Data Lake Analytics account(s).", adlaAccounts.Count);
                for (int i = 0; i < adlaAccounts.Count; i ++)
                {
                    Console.WriteLine(adlaAccounts[i].Name);
                }

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
            }

            public static ServiceClientCredentials AuthenticateAzure(
            string domainName,
            string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                return accounts;
            }
        }
    }


## <a name="manage-accounts"></a>Administrar cuentas

Antes de ejecutar los trabajos de análisis de datos lago, debe tener una cuenta de análisis de datos lago. A diferencia de HDInsight de Azure no pagar cuando no se ejecuta un trabajo para una cuenta de análisis.  Solo paga por vez cuando se está ejecutando una tarea.  Para obtener más información, vea [Información general sobre el análisis de lago datos Azure](data-lake-analytics-overview.md).  

###<a name="create-accounts"></a>Crear cuentas

Debe tener un grupo de administración de recursos de Azure y una cuenta de almacén de datos lago antes de poder ejecutar en el ejemplo siguiente.

El código siguiente muestra cómo crear un grupo de recursos:

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

El código siguiente muestra cómo crear una cuenta de la tienda de lago de datos:

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

El código siguiente muestra cómo crear una cuenta de análisis de datos lago:

    var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
    var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
    var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: location);
    var adlaAccount = _adlaClient.Account.Create(resourceGroupName, adlaAccountName, adlaParameters);

###<a name="list-accounts"></a>Lista de cuentas

Vea [conectarse a datos de Azure lago análisis](#connect_to_azure_data_lake_analytics).

###<a name="find-an-account"></a>Buscar una cuenta

Una vez creado un objeto de una lista de cuentas de análisis de lago de datos, puede usar lo siguiente para buscar una cuenta:

    Predicate<DataLakeAnalyticsAccount> accountFinder = (DataLakeAnalyticsAccount a) => { return a.Name == adlaAccountName; };
    var myAdlaAccount = adlaAccounts.Find(accountFinder);

###<a name="delete-data-lake-analytics-accounts"></a>Eliminar cuentas de análisis de datos lago

Fragmento de código siguiente elimina una cuenta de análisis de datos lago:

    _adlaClient.Account.Delete(resourceGroupName, adlaAccountName);

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Administrar orígenes de datos de la cuenta

Análisis de datos lago admite actualmente los siguientes orígenes de datos:

- [Almacenamiento de datos de Azure lago](../data-lake-store/data-lake-store-overview.md)
- [Almacenamiento de Azure](../storage/storage-introduction.md)

Cuando se crea una cuenta de análisis, debe designar una cuenta de almacenamiento de Azure datos lago sea la cuenta de almacenamiento. La cuenta predeterminada de la tienda de lago de datos se utiliza para almacenar los registros de auditoría de metadatos y la tarea de trabajo. Después de haber creado una cuenta de análisis, puede agregar cuentas adicionales de almacenamiento de datos lago o cuenta de almacenamiento de Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Buscar la cuenta predeterminada de almacén de datos lago

Consulte buscar una cuenta en este artículo para encontrar la cuenta de análisis de datos lago. A continuación, use la siguiente:

    string adlaDefaultDataLakeStoreAccountName = myAccount.Properties.DefaultDataLakeStoreAccount;


## <a name="use-azure-resource-manager-groups"></a>Usar los grupos de administrador de recursos de Azure

Aplicaciones normalmente están compuestas de muchos componentes, por ejemplo una aplicación web, base de datos, el servidor de base de datos, almacenamiento y servicios de terceros 3ª. Administrador de recursos de Azure le permite trabajar con los recursos de la aplicación como un grupo, que se conoce como un grupo de recursos de Azure. Puede implementar, actualizar, supervisar o eliminar todos los recursos para la aplicación en una única operación coordinada. Usar una plantilla para su implementación y esa plantilla puede trabajar para diferentes entornos, como pruebas, ensayo y producción. Puede aclarar facturación para su organización mediante la visualización de los costes para todo el grupo. Para obtener más información, vea [Información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md). 

Un servicio de análisis de datos lago puede incluir los siguientes componentes:

- Cuenta de análisis de lago de datos de Azure
- Cuenta de Azure datos lago almacenamiento predeterminado requerida
- Cuentas de almacenamiento adicional Azure datos lago
- Cuentas adicionales de almacenamiento de Azure

Puede crear todos estos componentes en un grupo de administración de recursos para facilitar la administración.

![Almacenamiento y cuenta de análisis de lago de datos de azure](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Una cuenta de análisis de datos lago y las cuentas de almacenamiento dependientes deben situarse en el mismo centro de datos de Azure.
No obstante, puede encontrarse el grupo de administración de recursos en un centro de datos diferente.  

##<a name="see-also"></a>Vea también 

- [Información general de análisis de datos lago de Microsoft Azure](data-lake-analytics-overview.md)
- [Introducción a análisis de lago de datos con el portal de Azure](data-lake-analytics-get-started-portal.md)
- [Administrar el análisis de lago de datos de Azure con Azure portal](data-lake-analytics-manage-use-portal.md)
- [Supervisar y solucionar problemas con Azure portal de trabajos de análisis de lago de datos de Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

