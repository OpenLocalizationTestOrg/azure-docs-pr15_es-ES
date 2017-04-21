<properties 
    pageTitle="Compromiso móvil Azure - integración back-end" 
    description="Conectar el compromiso de Azure Mobile con un servidor de SharePoint para crear campañas de SharePoint" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement---api-integration"></a>Compromiso móvil Azure - integración de la API

En un sistema automatizado de marketing, creación y activación de las campañas de marketing también se producen automáticamente. Para ello: compromiso de Azure Mobile permite crear dichas campañas de marketing automatizadas mediante API también. 

Normalmente los clientes usa la interfaz de front-end de contratación móvil para crear anuncios/sondeos etcetera como parte de sus campañas de marketing. Sin embargo a medida que las campañas de marketing para adultos, es necesario aprovechar los datos que se bloquea en los sistemas de back-end (como cualquier sistema CRM o sistema CMS como SharePoint) para que se puede crear una canalización completamente automatizada que crea campañas de contratación Mobile dinámicamente en función de los datos que fluye en los sistemas de back-end. 

![][5]

En este tutorial va a través de un escenario donde un usuario de empresa de SharePoint rellena una lista de SharePoint con datos de marketing y un proceso automatizado recoge elementos de la lista y se conecta con el sistema de contratación Mobile con la API de REST disponibles para crear una campaña de marketing desde los datos de SharePoint. 
 
> [AZURE.IMPORTANT] En general, puede utilizar este ejemplo como punto de partida para comprender cómo llamar a las API de REST de contratación móvil como detalla los dos aspectos fundamentales de la llamada a la API - parámetros de autenticación y superan. 

## <a name="sharepoint-integration"></a>Integración de SharePoint
1. Aquí es el aspecto de la lista de SharePoint de ejemplo. **Título**, **categoría**, **NotificationTitle**, **mensaje** y la **dirección URL** se usan para crear la presentación. Hay una columna denominada **IsProcessed** que se usa el proceso de automatización de ejemplo en forma de un programa de consola. Puede que se ejecute esta consola del programa como un WebJob de Azure para que se pueden programar o directamente, puede usar el flujo de trabajo de SharePoint al programa de creación y activación de la presentación cuando se inserta un elemento en la lista de SharePoint. En este ejemplo se utiliza el programa de consola que pasa por los elementos de la lista de SharePoint y crea anuncio en Azure Mobile compromiso para cada uno de ellos y, a continuación, por último marca el indicador **IsProcessed** para ser verdaderos creación satisfactoria anuncio de.

    ![][1]

2. Estamos utilizando el código de la muestra *autenticación remota en SharePoint Online con el modelo de objetos de cliente* [aquí](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) para autenticar con la lista de SharePoint.
 
3. Una vez autenticada, nos recorrer los elementos de lista para averiguar los elementos recién creados (lo que tendrá **IsProcessed** = false). 

        static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);

                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();

                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;

                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;

                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";

                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Integración de compromiso de móvil
1.  Una vez que encontramos un elemento que requiere procesamiento - nos extraer la información necesaria para crear un anuncio desde el elemento de lista y la llamada `CreateAzMECampaign` crearlo y, posteriormente, `ActivateAzMECampaign` para activarlo. Estos son esencialmente llamadas de API de REST que llama para el compromiso de móvil de back-end. 

2.  Las API de REST de contratación Mobile requieren un **encabezado de autorización de esquema de autenticación básica** que se compone de los `ApplicationId` y la `ApiKey` que obtener desde el portal de Azure. Asegúrese de que está utilizando la clave de la sección de **claves api** y *no* desde la sección de **claves de sdk** . 

    ![][2]

        static string CreateAuthZHeader()
        {
            string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
            return BasicAuthzHeaderString;
        }

        static public string EncodeTo64(string toEncode)
        {
            byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
            string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
            return returnValue;
        }  

3. Para crear el anuncio type campaña - consultar la [documentación](https://msdn.microsoft.com/library/azure/mt683750.aspx). Debe asegurarse de que está especificando la campaña `kind` como *anuncio* y la [carga](https://msdn.microsoft.com/library/azure/mt683751.aspx) y pasarlos como FormUrlEncodedContent. 

        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";

            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
                
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
                
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });

                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }

4. Una vez que tenga el anuncio creado, verá algo parecido al siguiente en el portal de Mobile compromiso (tenga en cuenta que el estado = borrador y activado = n/a)

    ![][3]

5. `CreateAzMECampaign`crea una campaña de anuncio y devuelve su identificador a la persona que llama. `ActivateAzMECampaign`requiere este Id como parámetro para activar la campaña. 

        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());

                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });

                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }

6. Una vez que tenga el anuncio activado, se verá algo parecido al siguiente en el portal de compromiso de móvil:

    ![][4]

7. Tan pronto como se activa la campaña, los dispositivos que cumplen el criterio de la campaña empezará a ver notificaciones. 

8. Verá que el elemento de lista se marca con IsProcessed = false se ha establecido en True una vez creada la campaña de anuncio.  

En este ejemplo crea una campaña de anuncio simple que especifica principalmente las propiedades necesarias. Puede personalizar tanto como posible desde el portal con la información [aquí](https://msdn.microsoft.com/library/azure/mt683751.aspx). 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png


 
