<properties 
    pageTitle="Uso de la API de REST para tener acceso a las API de servicio de contratación de Azure Mobile" 
    description="Describe cómo utilizar las API de REST de contratación móvil para tener acceso a las API de servicio de contratación de Azure Mobile"       
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="wesmc7777" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/05/2016" 
    ms.author="wesmc;ricksal" />

#<a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>Usar el resto para tener acceso a las API de servicio de contratación de Azure Mobile

Compromiso de Azure Mobile proporciona la [API de REST de Azure Mobile compromiso](https://msdn.microsoft.com/library/azure/mt683754.aspx) para administrar dispositivos, campañas de inserción/alcance etcetera. Este ejemplo utiliza las API de REST directamente para crear una campaña de anuncio, a continuación, activar y enviarlo a un conjunto de dispositivos. 

Si no desea usar las API de REST directamente, también le proporcionamos un [archivo de Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) que puede usar con herramientas para generar SDK para su idioma preferido. Se recomienda usar la herramienta de [AutoRest](https://github.com/Azure/AutoRest) para generar el SDK de nuestro archivo Swagger. Hemos creado un .NET SDK de manera similar que le permite interactuar con estas API utilizando un contenedor de C# y no es necesario que realice la negociación de token de autenticación y actualizar usted mismo. Si desea recorrer un ejemplo similar con este contenedor, vea [Ejemplos de servicio API .NET SDK](mobile-engagement-dotnet-sdk-service-api.md)

Este ejemplo utiliza las API de REST directamente a crear y activar una campaña de anuncio. 

## <a name="adding-a-username-appinfo-to-the-mobile-engagement-app"></a>Agregar un appInfo nombreDeUsuario a la aplicación móvil compromiso

En este ejemplo se requiere una etiqueta de información de la aplicación agregada a la aplicación móvil compromiso. En el portal de contratación para la aplicación, puede agregar la etiqueta haciendo clic en **configuración de** > **etiqueta (información de la aplicación)** > **nueva etiqueta (información de la aplicación)**. Agregar la nueva etiqueta denominada **nombreDeUsuario** como un tipo de **cadena** .

![](./media/mobile-engagement-dotnet-rest-service-api/user-name-app-info.png)



Si ha seguido [Introducción a Azure compromiso para Windows Universal aplicaciones Mobile](mobile-engagement-windows-store-dotnet-get-started.md), puede probar a enviar la etiqueta **nombreDeUsuario** reemplazando simplemente `OnNavigatedTo()` en su `MainPage` clase para enviar la etiqueta de la información de la aplicación similar al siguiente código:

    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        base.OnNavigatedTo(e);

        String name = "Wesley"; // Prompt the user to provide this in your client app.

        Dictionary<object, object> info = new Dictionary<object, object>();
        info.Add("user_name", name);
        EngagementAgent.Instance.SendAppInfo(info);
    }
 


## <a name="creating-the-service-api-app"></a>Creación de la aplicación de la API del servicio

1. En primer lugar, necesitará cuatro parámetros de autenticación para usar con este ejemplo. Estos parámetros son **SubscriptionId**, **TenantId**, **ApplicationId** y **secreto**. Para obtener los parámetros de autenticación, se recomienda que use el método de script de PowerShell mencionado en la sección *configuración única (usando secuencias de comandos)* en el tutorial de [autenticación](mobile-engagement-api-authentication.md#authentication) . 

2. Vamos a utilizar una aplicación de consola de Windows simple para demostrar trabajar con las API de servicio REST para crear y activar una nueva campaña de anuncio. Así que abra Visual Studio y cree una nueva **Aplicación de consola**.   

3. A continuación, agregue el paquete de NuGet **Newtonsoft.Json** a su proyecto.

4. En el `Program.cs` , agregue lo siguiente `using` instrucciones para los espacios de nombres siguientes:

        using System.IO;
        using System.Net;
        using Newtonsoft.Json.Linq;
        using Newtonsoft.Json;

5. A continuación necesita definir las constantes siguientes en el `Program` clase. Se trata de utilizadas para la autenticación e interactuar con la aplicación de contratación Mobile en el que está creando la campaña de anuncio:


        // Parameters needed for authentication of API calls.
        // These are returned from the PowerShell script in the authentication tutorial. 
        // https://azure.microsoft.com/documentation/articles/mobile-engagement-api-authentication/
        static String SubscriptionId = "<Your Subscription Id>";
        static String TenantId = "<Your TenantId>";
        static String ApplicationId = "<Your Application Id>";
        static String Secret = "<Your Secret>";

        // The token for authenticating with your Mobile Engagement app.
        static String Token = null;

        // This is the Azure Resource group concept for grouping together resources 
        // See: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        static String ResourceGroup = "MobileEngagement";

        // For Mobile Engagement operations
        // App Collection Name from the Azure portal 
        static String Collection = "<Your App Collection Name>";

        // Application Resource Name - make sure you are using the one as specified in the dashboard of the
        // Azure portal. (This is NOT the App Name)
        static String AppName = "WesmcRestTest-windows";

        // New campaign id returned from creating the new campaign and used to activate and push the campaign
        // a set of devices.
        static String NewCampaignID = null;

        // This list will hold the device Ids we choose to push the campaign to.
        static List<String> deviceList = new List<String>();


6. Agregar los siguientes dos métodos para la `Program` clase. Se utilizará para ejecutar la API de REST y mostrar las respuestas en la consola.

        private static async Task<HttpWebResponse> ExecuteREST(string httpMethod, string uri, string authToken, WebHeaderCollection headers = null, string body = null, string contentType = "application/json")
        {
            //=== Execute the request 
            HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(uri);
            HttpWebResponse response = null;

            request.Method = httpMethod;
            request.ContentType = contentType;
            request.ContentLength = 0;

            if (authToken != null)
                request.Headers.Add("Authorization", authToken);

            if (headers != null)
            {
                request.Headers.Add(headers);
            }

            if (body != null)
            {
                byte[] bytes = Encoding.UTF8.GetBytes(body);

                try
                {
                    request.ContentLength = bytes.Length;
                    Stream requestStream = request.GetRequestStream();
                    requestStream.Write(bytes, 0, bytes.Length);
                    requestStream.Close();
                }
                catch (Exception e)
                {
                    Console.WriteLine(e.Message);
                }
            }

            try
            {
                response = (HttpWebResponse)await request.GetResponseAsync();
            }
            catch (WebException we)
            {
                if (we.Response != null)
                {
                    response = (HttpWebResponse)we.Response;
                }
                else
                    Console.WriteLine(we.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

            return response;
        }


        private static void DisplayResponse(dynamic data, HttpWebResponse response)
        {
            Console.WriteLine("HTTP Status " + (int)response.StatusCode + " : " + response.StatusDescription);
            Console.WriteLine(data + "\n");
        }

    }

7. Agregue el código siguiente a su `Main` método para generar un símbolo de autenticación con los parámetros de autenticación que ha recopilado:

        //***************************************************************************
        //*** Get a valid authorization token with your authentication parameters ***
        //***************************************************************************

        String methodUrl = "https://login.microsoftonline.com/" + TenantId + "/oauth2/token";
        String requestBody = "grant_type=client_credentials&client_id=" + ApplicationId +
                            "&client_secret=" + Secret +
                            "&resource=https://management.core.windows.net/";
        Console.WriteLine("Getting authorization token...\n");
        HttpWebResponse response = ExecuteREST("POST", methodUrl, null, null, requestBody, null).Result;
        Stream receiveStream = response.GetResponseStream();
        StreamReader readStream = new StreamReader(receiveStream, Encoding.UTF8);
        dynamic data = JObject.Parse(readStream.ReadToEnd());
        readStream.Close();
        receiveStream.Close();
        DisplayResponse(data, response);

        if (response.StatusCode == HttpStatusCode.OK)
        {
            Token = data.token_type + " " + data.access_token;
            Console.WriteLine("Got authorization token...");
            Console.WriteLine("Authorization : " + Token + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to get authorization token. Check your parameters for API calls.\n");
            return;
        }

8. Ahora que tenemos un símbolo de autenticación válida podemos crear una nueva campaña de alcance con la API de REST de [campaña de crear](https://msdn.microsoft.com/library/azure/mt683742.aspx) . La nueva campaña será un simple **en cualquier momento** & **solo notificación** campaña de anuncio con un título y un mensaje. Esta será una campaña de inserción manual como se muestra en la siguiente captura de pantalla. Esto significa sólo se insertará mediante las API.


    ![](./media/mobile-engagement-dotnet-rest-service-api/manual-push.png)

    Agregue el código siguiente a su `Main` método para crear la campaña de anuncio: 

        //*****************************************************************************
        //*** Create a campaign to send a notification using the user-name app-info ***
        //*****************************************************************************

        String newCampaignMethodUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
               "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
               Collection + "/apps/" + AppName + "/campaigns/announcements?api-version=2014-12-01";

        String campaignRequestBody = "{ \"name\": \"BirthdayCoupon\", " +
                                        "\"type\": \"only_notif\", " +
                                        "\"deliveryTime\": \"any\", " +
                                        "\"notificationType\": \"popup\", " +
                                        "\"pushMode\":\"manual\", " +
                                        "\"notificationTitle\": \"Happy Birthday ${user_name}\", " +
                                        "\"notificationMessage\": \"Take extra 10% off on your orders today!\"}";

        Console.WriteLine("Creating new campaign...\n");
        HttpWebResponse newCampaignResponse = ExecuteREST("POST", newCampaignMethodUrl, Token, null, campaignRequestBody).Result;
        Stream receiveCampaignStream = newCampaignResponse.GetResponseStream();
        StreamReader readCampaignStream = new StreamReader(receiveCampaignStream, Encoding.UTF8);
        dynamic newCampaignData = JObject.Parse(readCampaignStream.ReadToEnd());
        readCampaignStream.Close();
        receiveCampaignStream.Close();
        DisplayResponse(newCampaignData, newCampaignResponse);

        if (newCampaignResponse.StatusCode == HttpStatusCode.Created)
        {
            NewCampaignID = newCampaignData.id;
            Console.WriteLine("Created new campaign...");
            Console.WriteLine("New Campaign Id    : " + NewCampaignID + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to create birthday campaign.\n");
            return;
        }


9. Debe activar la campaña antes de se puede insertar en cualquier dispositivo. Se guarda el identificador de la campaña nuevo en el `NewCampaignID` variable. Se usará esto como un parámetro de ruta de acceso URI para activar la campaña con la API de REST de [campaña de activar](https://msdn.microsoft.com/library/azure/mt683745.aspx) . Esto debe cambiar el estado de la campaña a **programada** aunque solo se inserta manualmente con las API de.

    Agregue el código siguiente a su `Main` método para activar la campaña de anuncio: 

        //******************************************
        //*** Activate the new birthday campaign ***
        //******************************************

        String activateCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID +
                   "/activate?api-version=2014-12-01";

        Console.WriteLine("Activating new campaign (ID : " + NewCampaignID + ")...\n");
        HttpWebResponse activateCampaignResponse = ExecuteREST("POST", activateCampaignUrl, Token).Result;
        Stream activateCampaignStream = activateCampaignResponse.GetResponseStream();
        StreamReader activateCampaignReader = new StreamReader(activateCampaignStream, Encoding.UTF8);
        dynamic activateCampaignData = JObject.Parse(activateCampaignReader.ReadToEnd());
        activateCampaignReader.Close();
        activateCampaignStream.Close();
        DisplayResponse(activateCampaignData, activateCampaignResponse);

        if (activateCampaignResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Activated new campaign...");
            Console.WriteLine("New Campaign State : " + activateCampaignData.state + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to activate birthday campaign.\n");
            return;
        }

10. Para insertar la campaña necesitamos proporcionar los identificadores de dispositivo para los usuarios que se va a recibir la notificación. Para obtener todos los identificadores de dispositivo se utilizará la API de REST de [dispositivos de consulta](https://msdn.microsoft.com/library/azure/mt683826.aspx) . Agregaremos cada dispositivo identificador a la lista si está asociada **nombreDeUsuario** appInfo.

    Agregue el código siguiente a su `Main` método para obtener todos los identificadores de dispositivo y rellenar la lista de dispositivo:

        //************************************************************************
        //*** Now that the manualPush campaign is activated, get the deviceIds ***
        //*** that you want to trigger the push campaign on.                   ***
        //************************************************************************

        String getDevicesUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/devices?api-version=2014-12-01";

        Console.WriteLine("Getting device IDs...\n");
        HttpWebResponse devicesResponse = ExecuteREST("GET", getDevicesUrl, Token).Result;
        Stream devicesStream = devicesResponse.GetResponseStream();
        StreamReader devicesReader = new StreamReader(devicesStream, Encoding.UTF8);
        dynamic devicesData = JObject.Parse(devicesReader.ReadToEnd());
        devicesReader.Close();
        devicesStream.Close();
        DisplayResponse(devicesData, devicesResponse);

        if (devicesResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Got devices.");
            foreach (dynamic device in devicesData.value)
            {
                // Just adding all in this example
                if (device.appInfo.user_name != null)
                {
                    Console.WriteLine("Adding device for push campaign...");
                    Console.WriteLine("Device ID    : " + device.deviceId);
                    Console.WriteLine("user_name    : " + device.appInfo.user_name);
                    deviceList.Add((String)device.deviceId);
                }
            }
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine("*** Failed to get devices.\n");
            return;
        }


11. Por último, se push la campaña a todos los identificadores de dispositivo en nuestra lista con la API de REST de [campaña de inserción](https://msdn.microsoft.com/library/azure/mt683734.aspx) . Se trata de una notificación **de la aplicación** . Así que la aplicación tendrá que se ejecuta en el dispositivo para poder ser recibido por el usuario en el.

    Agregue el código siguiente a su `Main` método para vincular la campign a los dispositivos en la lista de dispositivo:

        //**************************************************************
        //*** Trigger the manualPush campaign on the desired devices ***
        //**************************************************************

        String pushCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID + 
                   "/push?api-version=2014-12-01";

        Console.WriteLine("Triggering push for new campaign (ID : " + NewCampaignID + ")...\n");
        String deviceIds = "{\"deviceIds\":" + JsonConvert.SerializeObject(deviceList) + "}";
        Console.WriteLine("\n" + deviceIds + "\n");
        HttpWebResponse pushDevicesResponse = ExecuteREST("POST", pushCampaignUrl, Token, null, deviceIds).Result;
        Stream pushDevicesStream = pushDevicesResponse.GetResponseStream();
        StreamReader pushDevicesReader = new StreamReader(pushDevicesStream, Encoding.UTF8);
        dynamic pushDevicesData = JObject.Parse(pushDevicesReader.ReadToEnd());
        pushDevicesReader.Close();
        pushDevicesStream.Close();
        DisplayResponse(pushDevicesData, pushDevicesResponse);

        if (pushDevicesResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Triggered push on new campaign.\n");
        }
        else
        {
            Console.WriteLine("*** Failed to push campaign to the device list.\n");
        }


12. Crear y ejecutar la aplicación de consola. El resultado debe ser similar al siguiente:


        C:\Users\Wesley\AzME_Service_API_Rest\test.exe

        Getting authorization token...
        
        HTTP Status 200 : OK
        {
          "token_type": "Bearer",
          "expires_in": "3599",
          "expires_on": "1458085162",
          "not_before": "1458081262",
          "resource": "https://management.core.windows.net/",
          "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPW
        b3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFh
        NzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0cy53
        MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoiNzJm
        F5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE3OAE
        hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-HASI8
        }
        
        Got authorization token...
        Authorization : Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNN
        aW5kb3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYt
        Zi1jNzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0
        OGU3MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoi
        iI-oF5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE
        vsf3hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-H
        
        Creating new campaign...
        
        HTTP Status 201 : Created
        {
          "id": 24,
          "state": "draft"
        }
        
        Created new campaign...
        New Campaign Id    : 24
        
        Activating new campaign (ID : 24)...
        
        HTTP Status 200 : OK
        {
          "id": 24,
          "state": "scheduled"
        }
        
        Activated new campaign...
        New Campaign State : scheduled
        
        Getting device IDs...
        
        HTTP Status 200 : OK
        {
          "value": [
            {
              "meta": {
                "lastSeen": 1458080534371,
                "firstSeen": 1458080534371,
                "lastLocation": 1458081389617,
                "lastInfo": 1458080571603
              },
              "appInfo": {
                "user_name": "Wesley"
              },
              "deviceId": "1d6208b8f281203ecb49431e2e5ce6b3"
            },
            {
              "meta": {
                "lastSeen": 1457990584698,
                "firstSeen": 1457949384025,
                "lastLocation": 1457990914895,
                "lastInfo": 1457990584597
              },
              "appInfo": {
                "user_name": "Wesley"
              },
              "deviceId": "302486644890e26045884ee5aa0619ec"
            }
          ]
        }
        
        Got devices.
        Adding device for push campaign...
        Device ID    : 1d6208b8f281203ecb49431e2e5ce6b3
        user_name    : Wesley
        Adding device for push campaign...
        Device ID    : 302486644890e26045884ee5aa0619ec
        user_name    : Wesley
        
        Triggering push for new campaign (ID : 24)...
        
        
        {"deviceIds":["1d6208b8f281203ecb49431e2e5ce6b3","302486644890e26045884ee5aa0619ec"]}
        
        HTTP Status 200 : OK
        {
          "invalidDeviceIds": []
        }
        
        Triggered push on new campaign.
        


<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
