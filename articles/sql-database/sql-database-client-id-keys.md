<properties
   pageTitle="Obtener los valores requeridos para la autenticación de una aplicación para tener acceso a la base de datos SQL de código | Microsoft Azure"
   description="Crear una entidad de seguridad de servicio para obtener acceso a la base de datos SQL de código."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/30/2016"
   ms.author="sstein"/>

# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Obtener los valores requeridos para la autenticación de una aplicación para tener acceso a la base de datos SQL de código

Para crear y administrar la base de datos SQL de código debe registrar la aplicación en el dominio de Azure Active Directory (AAD) en la suscripción donde se han creado los recursos de Azure.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Crear un servicio principal desde una aplicación de acceso a los recursos

Debe tener la última [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) instalado y en ejecución. Para obtener información detallada, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

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




## <a name="see-also"></a>Vea también

- [Crear una base de datos SQL con C#](sql-database-get-started-csharp.md)
- [Conectarse a la base de datos SQL mediante la autenticación de Azure Active Directory](sql-database-aad-authentication.md)


