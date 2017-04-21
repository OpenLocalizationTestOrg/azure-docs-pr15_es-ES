<properties 
    pageTitle="Usando el SDK de .NET para tener acceso a las API de servicio de contratación de Azure Mobile" 
    description="Describe cómo utilizar el SDK de .NET Mobile compromiso para tener acceso a las API de servicio de contratación de Azure Mobile"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>Usando el SDK de .NET para tener acceso a las API de servicio de contratación de Azure Mobile

Compromiso de Azure Mobile expone un conjunto de API para administrar dispositivos, campañas de inserción/alcance etcetera. Para interactuar con estas API, nos también le ofrece un [archivo de Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) que puede usar con herramientas para generar SDK para su idioma preferido. Se recomienda usar la herramienta de [AutoRest](https://github.com/Azure/AutoRest) para generar el SDK de nuestro archivo Swagger. 

Hemos creado un .NET SDK de manera similar que le permite interactuar con estas API utilizando un contenedor de C# y no es necesario que realice la negociación de token de autenticación y actualizar usted mismo.  

En este ejemplo se pasa por el conjunto de pasos a seguir para utilizar .NET SDK:

1. En primer lugar, debe configurar la autenticación para su API con Azure Active Directory como se describe [a continuación](mobile-engagement-api-authentication.md#authentication). Al final de estos pasos, debe tener un **SubscriptionId**, **TenantId**, **ApplicationId** y **secreto**válida. 

2. Vamos a utilizar una aplicación de consola de Windows simple para demostrar trabajar con el SDK de .NET con el escenario de creación de una campaña de anuncio. Así que abra Visual Studio y cree una **Aplicación de consola**.   

3. A continuación debe descargar el SDK de .NET que está disponible como la **Biblioteca de administración de Microsoft Azure compromiso** en la Galería de Nuget [aquí](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/).
Si está instalando la Nuget desde Visual Studio, debe asegurarse de que tiene marcado la opción **incluir la versión preliminar** durante la búsqueda para el paquete de verificación:

    ![][1]

4. En el `Program.cs` , agregue los espacios de nombres siguientes:

        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;

5. A continuación debe definir las constantes que se usará para la autenticación y la interacción con la aplicación de contratación Mobile en el que está creando la campaña de anuncio:

        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";

        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";

        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";

6. Definir la `EngagementManagementClient` variable que se utilizará para llamar a los métodos de SDK de contratación Mobile:

        static EngagementManagementClient engagementClient; 

7. Agregue lo siguiente a su `Main` método:

        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();

                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }

8. Definir el siguiente método que se encarga de inicialización de la `EngagementManagementClient` mediante la autenticación en primer lugar y, a continuación, asociar propio con la aplicación de contratación Mobile en el que tiene previsto crear la campaña de anuncio:

        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
            
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;

            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }

    > [AZURE.IMPORTANT] Tenga en cuenta que debe usar el **Nombre de recurso de aplicación** , según se define en el portal de administración de Azure para el parámetro de nombre de aplicación. 

9. Por último, defina el método de CreateCampaign que se ocupará de uso de la EngagementClient previamente inicializado para crear un simple **en cualquier momento** & **solo notificación** campaña con un título y un mensaje: 

        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/en-us/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );

            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/en-us/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }

10. Ejecute la aplicación de consola y verá lo siguiente en la creación correcta de la campaña:

    **Identificador de campaña '159' se creó correctamente y se encuentra en estado de "borrador"**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
