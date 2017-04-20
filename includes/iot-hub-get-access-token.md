## <a name="obtain-an-azure-resource-manager-token"></a>Obtener un token de administrador de recursos de Azure

Azure Active Directory debe autenticar todas las tareas que realiza en recursos mediante el Administrador de recursos de Azure. El ejemplo mostrado aquí utiliza la autenticación de contraseña, para otros enfoques [las solicitudes de autenticación Azure Resource Manager], consulte[lnk-authenticate-arm].

1. Agregue el código siguiente al método **Main** en Program.cs para recuperar un símbolo (token) de AD Azure con la contraseña e id de aplicación.

    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.windows.net/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
    
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```

2. Crear un objeto **ResourceManagementClient** que utiliza el token agregando el código siguiente al final del método **Main** :

    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```

3. Crear u obtener una referencia para el grupo de recursos que está utilizando:

    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx