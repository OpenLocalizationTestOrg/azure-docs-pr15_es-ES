<properties
    pageTitle="Introducción a la biblioteca de Azure CDN para .NET | Microsoft Azure"
    description="Obtenga información sobre cómo escribir aplicaciones .NET para administrar CDN de Azure con Visual Studio."
    services="cdn"
    documentationCenter=".net"
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="casoper"/>

# <a name="get-started-with-azure-cdn-development"></a>Introducción a desarrollo CDN de Azure

> [AZURE.SELECTOR]
- [Node.js](cdn-app-dev-node.md)
- [.NET](cdn-app-dev-net.md)

Puede usar la [Biblioteca de Azure CDN para .NET](https://msdn.microsoft.com/library/mt657769.aspx) para automatizar la creación y administración de perfiles CDN y extremos.  Este tutorial le guía a través de la creación de una aplicación de consola de .NET simple que muestra varias de las operaciones disponibles.  Este tutorial no está pensado para describir todos los aspectos de la biblioteca de Azure CDN para .NET en detalle.

Debe 2015 de Visual Studio para completar este tutorial.  [Comunidad de Visual Studio 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) es descargar gratuitamente.

> [AZURE.TIP] El [completado proyecto a partir de este tutorial](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) está disponible para su descarga en MSDN.

[AZURE.INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Crear un proyecto y agregar paquetes de Nuget

Ahora que hemos creado un grupo de recursos para nuestros perfiles CDN y nuestro permiso de aplicación de Azure AD para administrar perfiles CDN y extremos dentro del grupo, podemos comenzar a crear nuestra aplicación.

Desde 2015 de Visual Studio, haga clic en **archivo**, **nuevo** **proyecto...** para abrir el cuadro de diálogo nuevo proyecto.  Expanda **Visual C#**, seleccione **Windows** en el panel de la izquierda.  En el panel central, haga clic en **Aplicación de consola** .  Nombre para el proyecto y luego haga clic en **Aceptar**.  

![Nuevo proyecto](./media/cdn-app-dev-net/cdn-new-project.png)

Nuestro proyecto va a utilizar algunas bibliotecas Azure incluidos en los paquetes de Nuget.  Vamos a agregarlos al proyecto.

1. Haga clic en el menú **Herramientas** , **Administrador de paquetes de Nuget**, **Consola del Administrador de paquetes**.

    ![Administrar paquetes de Nuget](./media/cdn-app-dev-net/cdn-manage-nuget.png)

2. En la consola del Administrador de paquetes, ejecute el comando siguiente para instalar la **Biblioteca de autenticación de Active Directory (ADAL)**:

    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`

3. Ejecute el siguiente procedimiento para instalar la **Biblioteca de administración de Azure CDN**:

    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Las directivas, constantes, método principal y métodos auxiliares

Vamos a obtener la estructura básica de nuestro programa escrito.

1. En la pestaña Program.cs, reemplace el `using` directivas en la parte superior con las siguientes acciones:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

2. Es necesario definir algunas constantes que usarán nuestros métodos.  En la `Program` de clase, pero antes del `Main` método, agregue lo siguiente.  Asegúrese de reemplazar los marcadores de posición, incluida la ** &lt;corchetes angulares&gt;**, con sus propios valores según sea necesario.

    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```

3. También en el nivel de clase, definir estas dos variables.  Usaremos estos más adelante para determinar si ya existen nuestro perfil y un punto final.

    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```

4.  Reemplazar el `Main` método como sigue:

    ```csharp
    static void Main(string[] args)
    {
        //Get a token
        AuthenticationResult authResult = GetAccessToken();

        // Create CDN client
        CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
            { SubscriptionId = subscriptionId };

        ListProfilesAndEndpoints(cdn);

        // Create CDN Profile
        CreateCdnProfile(cdn);

        // Create CDN Endpoint
        CreateCdnEndpoint(cdn);
        
        Console.WriteLine();

        // Purge CDN Endpoint
        PromptPurgeCdnEndpoint(cdn);

        // Delete CDN Endpoint
        PromptDeleteCdnEndpoint(cdn);

        // Delete CDN Profile
        PromptDeleteCdnProfile(cdn);

        Console.WriteLine("Press Enter to end program.");
        Console.ReadLine();
    }
    ```

5. Algunos de los otros métodos de nuestras va a preguntar al usuario con preguntas "Sí/No".  Agregue el siguiente método para hacer un poco más fácil:

    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Ahora que se ha escrito la estructura básica de nuestro programa, debemos creamos los métodos llamados por el `Main` método.

## <a name="authentication"></a>Autenticación

Antes de poder utilizar la biblioteca de administración de Azure CDN, es necesario autenticar a nuestro principal del servicio y obtener un símbolo de autenticación.  Este método usa ADAL para recuperar el token.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority); 
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult = 
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Si está utilizando la autenticación de usuario individual, el `GetAccessToken` método tendrá un aspecto algo diferente.

>[AZURE.IMPORTANT] Use este código de ejemplo únicamente si opta por la autenticación de usuario individual en lugar de un servicio principal.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Asegúrese de reemplazar `<redirect URI>` con la redirección URI especificado cuando se registra la aplicación en Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Lista CDN perfiles y extremos

Ahora podemos realizar operaciones de CDN.  Lo primero que hace el método es la lista de todos los perfiles y extremos en el grupo de recursos y si encuentra a una coincidencia de los nombres de perfil y extremo especificado en nuestras constantes realiza una nota de más adelante para que no intente crear duplicados.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Crear extremos y perfiles CDN

A continuación, vamos a crear un perfil.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

Una vez creado el perfil, vamos a crear un extremo.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

>[AZURE.NOTE] El ejemplo anterior asigna el punto final de un origen denominado *Contoso* con un nombre de host `www.contoso.com`.  Debe cambiar esta opción para que apunten a nombre de host de su propio origen.

## <a name="purge-an-endpoint"></a>Purgar un punto final

Suponiendo que se ha creado el extremo, una tarea común que podría desear realizar en nuestro programa purga el contenido en el extremo.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

>[AZURE.NOTE] En el ejemplo anterior, la cadena `/*` indica que deseo purgar todo el contenido de la raíz de la ruta de acceso de extremo.  Esto equivale a comprobar **Purgar todos** en el cuadro de diálogo del portal Azure "purgar". En la `CreateCdnProfile` método, he creado nuestro perfil como un perfil de **Azure CDN de Verizon** utilizando el código `Sku = new Sku(SkuName.StandardVerizon)`, por lo que esto se realizará correctamente.  Sin embargo, los perfiles de **Azure CDN de Akamai** no admiten **Purgar todos**, por lo que si estoy usando un perfil Akamai para este tutorial, debería incluir rutas específicas para purgar.

## <a name="delete-cdn-profiles-and-endpoints"></a>Eliminar extremos y perfiles CDN

Los últimos métodos eliminará nuestro punto final y el perfil.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>Ejecutar el programa

Ahora podemos compilar y ejecutar el programa haciendo clic en el botón **Inicio** , en Visual Studio.

![Programa que se ejecuta](./media/cdn-app-dev-net/cdn-program-running-1.png)

Cuando el programa llega a la pregunta anterior, debería poder volver a su grupo de recursos en el portal de Azure y ver que se ha creado el perfil.

![¡Éxito!](./media/cdn-app-dev-net/cdn-success.png)

A continuación, podemos las indicaciones para ejecutar el resto del programa.

![Finalización del programa](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Pasos siguientes

Para ver el proyecto completado en este tutorial, [descargar los datos de ejemplo](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Para buscar la documentación adicional en la biblioteca de administración de Azure CDN para .NET, vea la [referencia en MSDN](https://msdn.microsoft.com/library/mt657769.aspx).

Administrar los recursos de CDN con [PowerShell](./cdn-manage-powershell.md).


