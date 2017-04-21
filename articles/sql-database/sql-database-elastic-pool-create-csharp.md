<properties
    pageTitle="Crear un grupo de la base de datos elástico con C# | Microsoft Azure"
    description="Utilice técnicas de desarrollo de base de datos de C# para crear un grupo de base de datos elástico scalable en base de datos de SQL Azure lo que puede compartir recursos en muchas bases de datos."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="10/04/2016"
    ms.author="sstein"/>

# <a name="create-an-elastic-database-pool-with-cx23"></a>Crear un grupo de la base de datos elástico con C & #x 23;

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


En este artículo se describe cómo utilizar C# para crear un grupo de elásticos de la base de datos de SQL Azure con la [Biblioteca de base de datos de SQL Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Para crear una base de datos SQL independiente, consulte [utilizar C# para crear una base de datos SQL con la biblioteca de base de datos de SQL para .NET](sql-database-get-started-csharp.md).

La biblioteca de base de datos de SQL Azure para .NET proporciona un [Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md)-según la API que se ajusta a la [API de REST de base de datos de SQL en función de administrador de recursos](https://msdn.microsoft.com/library/azure/mt163571.aspx).

>[AZURE.NOTE] Solo se admiten muchas características nuevas de base de datos de SQL cuando se utiliza el [modelo de implementación de administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md), por lo que debe usar siempre la información más reciente **biblioteca de administración de base de datos de Azure SQL para .NET ([documentos](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [Paquete NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. Antiguo [bibliotecas basadas en el modelo de implementación clásica](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) son compatibles para la compatibilidad con versiones anteriores, por lo que recomendamos usar las bibliotecas en función de administrador de recursos más recientes.

Para completar los pasos de este artículo, se necesita lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción de Azure simplemente haga clic en **Cuenta gratuita** en la parte superior de esta página y, a continuación, vuelva a fin de este artículo.
- Visual Studio. Para obtener una copia gratuita de Visual Studio, consulte la página de [Descargas de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .


## <a name="create-a-console-app-and-install-the-required-libraries"></a>Crear una aplicación de consola e instalar las bibliotecas necesarias

1. Inicie Visual Studio.
2. Haga clic en **archivo** > **nueva** > **proyecto**.
3. Crear una **Aplicación de consola** C# y asígnele el nombre: *SqlElasticPoolConsoleApp*


Para crear una base de datos SQL con C#, cargar las bibliotecas de administración necesarios (con la [consola del Administrador de paquete](http://docs.nuget.org/Consume/Package-Manager-Console)):

1. Haga clic en **Herramientas** > **Administrador de paquetes de NuGet** > **Consola del Administrador de paquetes**.
2. Tipo de `Install-Package Microsoft.Azure.Management.Sql –Pre` para instalar la [Biblioteca de administración de SQL de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).
3. Tipo de `Install-Package Microsoft.Azure.Management.ResourceManager –Pre` para instalar la [Biblioteca de administrador de recursos de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
4. Tipo de `Install-Package Microsoft.Azure.Common.Authentication –Pre` para instalar la [Biblioteca de autenticación común de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication). 



> [AZURE.NOTE] Los ejemplos de este artículo utilizan un formulario sincrónico de cada solicitud de API y bloquean hasta finalizar el resto de llamadas en el servicio subyacente. Existen métodos asincrónico.


## <a name="create-a-sql-elastic-database-pool---c-example"></a>Crear un grupo de elásticos de la base de datos SQL - ejemplo de C#

En el ejemplo siguiente crea un grupo de recursos, servidor, reglas de firewall, grupo elástico y, a continuación, se crea una base de datos SQL en el grupo. Consulte [crear un servicio principal para tener acceso a los recursos](#create-a-service-principal-to-access-resources) para obtener la `_subscriptionId, _tenantId, _applicationId, and _applicationSecret` variables.

Reemplazar el contenido de **Program.cs** con los siguientes y a continuación, actualice la `{variables}` con los valores de la aplicación (no incluya la `{}`).


```
using Microsoft.Azure;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.Azure.Management.Sql;
using Microsoft.Azure.Management.Sql.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace SqlElasticPoolConsoleApp
{
    class Program
        {

        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _poolName = "{pool-name}";
        static string _poolEdition = "{Standard}";
        static int _poolDtus = {100};
        static int _databaseMinDtus = {10};
        static int _databaseMaxDtus = {100};

        static string _databaseName = "{elasticdbfromcsarticle}";



        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Elastic pool...");
            ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool(_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);
            Console.WriteLine("Elastic pool: " + epr.ElasticPool.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static ElasticPoolCreateOrUpdateResponse CreateOrUpdateElasticDatabasePool(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string poolName, string poolEdition, int poolDtus, int databaseMinDtus, int databaseMaxDtus)
        {
            // Retrieve the server that will host this elastic pool
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus,
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlMgmtClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, poolName, newPoolParameters);
            return newPoolResponse;
        }




        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string poolName)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    ElasticPoolName = poolName
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
    }
}
```





## <a name="create-a-service-principal-to-access-resources"></a>Crear un servicio principal acceso a los recursos

El siguiente script de PowerShell crea la aplicación de Active Directory (AD) y la principal del servicio que se debe autenticar nuestra aplicación C#. La secuencia de comandos genera valores que se necesita para el ejemplo anterior de C#. Para obtener información detallada, consulte [Usar PowerShell de Azure para crear un servicio principal para tener acceso a los recursos](../resource-group-authenticate-service-principal.md).

   
    # Sign in to Azure.
    Add-AzureRmAccount
    
    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.
    
    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"
    
    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret
    
    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    
    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15
    
    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    
    
    # Output the values we need for our C# application to successfully authenticate
    
    Write-Output "Copy these values into the C# sample app"
    
    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret


  

## <a name="next-steps"></a>Pasos siguientes

- [Administrar el grupo de servidores](sql-database-elastic-pool-manage-csharp.md)
- [Crear elásticos trabajos](sql-database-elastic-jobs-overview.md): trabajos elásticos le permiten ejecutar secuencias de comandos SQL T en cualquier número de bases de datos en un grupo.
- [Escalar con la base de datos de SQL Azure](sql-database-elastic-scale-introduction.md): usar herramientas de base de datos elástico escalar.

## <a name="additional-resources"></a>Recursos adicionales

- [Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [API de administración de recursos de Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)
