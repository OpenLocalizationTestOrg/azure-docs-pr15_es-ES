<properties 
    pageTitle="Actualizar Media Services después sucesiva teclas de acceso de almacenamiento | Microsoft Azure" 
    description="En este artículo le ofrecen instrucciones sobre cómo actualizar Media Services después sucesiva teclas de acceso de almacenamiento." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako"
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="milangada;cenkdin;juliako"/>

#<a name="update-media-services-after-rolling-storage-access-keys"></a>Actualizar Media Services después sucesiva teclas de acceso de almacenamiento

Cuando se crea una nueva cuenta de Azure Media Services, también deberá seleccionar una cuenta de almacenamiento de Azure que se utiliza para almacenar el contenido multimedia. Tenga en cuenta que puede [Agregar más cuentas de almacenamiento](meda-services-managing-multiple-storage-accounts.md) a su cuenta de Media Services.

Cuando se crea una nueva cuenta de almacenamiento, Azure genera dos teclas de acceso de almacenamiento de 512 bits, que se utilizan para autenticar el acceso a su cuenta de almacenamiento. Para mantener las conexiones de almacenamiento sea más segura, se recomienda regenerar periódicamente y girar la tecla de acceso de almacenamiento. Dos teclas de acceso (primaria y secundarias) se proporcionan para permitirle mantener conexiones con la cuenta de almacenamiento usando una tecla de acceso mientras regenerar la clave de acceso a otros. Este procedimiento también se denomina "graduales teclas de acceso".

Media Services depende de una clave de almacenamiento que se le proporciona. Más concretamente, los localizadores que se utilizan para transmitir o descargar los activos dependen de la tecla de acceso de almacenamiento especificado. Cuando se crea una cuenta de AMS acepta una dependencia en la tecla de acceso de almacenamiento principal de forma predeterminada, pero como un usuario puede actualizar la clave de almacenamiento que tiene AMS. Debe asegurarse Media Services comunicar la clave que desea usar, siguiendo los pasos descritos en este tema. Además, durante la implementación de las teclas de acceso de almacenamiento, necesita para asegurarse de que actualizar su localizadores lo no habrá ninguna interrupción en el servicio de transmisión por secuencias (este paso también se describe en el tema).

>[AZURE.NOTE]Si tiene varias cuentas de almacenamiento, deberá realizar este procedimiento con cada cuenta de almacenamiento:
>
>Antes de ejecutar los pasos descritos en este tema en una cuenta de producción, asegúrese de probarlas en una cuenta de producción anterior.


## <a name="step-1-regenerate-secondary-storage-access-key"></a>Paso 1: Regenerar la clave de acceso de almacenamiento secundario

Comience con regenerar la clave de almacenamiento secundario. De forma predeterminada, la clave secundaria no se usa servicios de medios.  Para obtener información sobre cómo restaurar claves de almacenamiento, consulte [Cómo: vista, copiar y volver a crear almacenamiento las teclas de acceso](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
  
##<a id="step2"></a>Paso 2: Actualizar Media Services para usar la nueva clave de almacenamiento secundario

Actualizar Media Services para usar la tecla de acceso de almacenamiento secundario. Puede usar uno de los siguientes dos métodos para sincronizar la clave de almacenamiento generado con Media Services.

- Usar el portal de Azure: para buscar el nombre y la clave de valores, vaya al portal de Azure y seleccione la cuenta. Aparece la ventana de configuración de la derecha. En la ventana Configuración, seleccione teclas. Dependiendo de la clave de almacenamiento que desee para los servicios de medios para sincronizar con, seleccione la clave principal de sincronización o sincronizar el botón secundario. En este caso, use la clave secundaria.

- Usar la administración de servicios multimedia API de REST.

En el ejemplo siguiente se muestra cómo crear la https://endpoint/***subscriptionId/servicios/mediaservices/cuentas/nombre de la cuenta*/StorageAccounts/*storageAccountName*solicitud Key para poder sincronizar la clave de almacenamiento especificado con Media Services. En este caso, se usa el valor de clave de almacenamiento secundario. Para obtener más información, vea [Cómo: API de REST de administración de servicios de uso multimedia](http://msdn.microsoft.com/library/azure/dn167656.aspx).
    
    public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
    {
        var clientCert = GetCertificate(CertThumbprint);
        
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
        request.Method = "PUT";
        request.ContentType = "application/json; charset=utf-8";
        request.Headers.Add("x-ms-version", "2011-10-01");
        request.Headers.Add("Accept-Encoding: gzip, deflate");
        request.ClientCertificates.Add(clientCert);
        
        
        using (var streamWriter = new StreamWriter(request.GetRequestStream()))
        {
            streamWriter.Write("\"");
            streamWriter.Write(storageAccountKey);
            streamWriter.Write("\"");
            streamWriter.Flush();
        }
        
        using (var response = (HttpWebResponse)request.GetResponse())
        {
            string jsonResponse;
            Stream receiveStream = response.GetResponseStream();
            Encoding encode = Encoding.GetEncoding("utf-8");
            if (receiveStream != null)
            {
                var readStream = new StreamReader(receiveStream, encode);
                jsonResponse = readStream.ReadToEnd();
            }
        }
    }

Después de este paso, actualice localizadores existentes (que tienen la dependencia de la clave de almacenamiento antigua) como se muestra en el paso siguiente.

>[AZURE.NOTE]Esperar 30 minutos antes de realizar operaciones con los servicios de Media (por ejemplo, crear nuevos localizadores) con el fin de evitar cualquier impacto en trabajos pendientes.

##<a name="step-3-update-locators"></a>Paso 3: Localizadores de actualización

>[AZURE.NOTE]Sucesiva teclas de acceso de almacenamiento, debe asegurarse de que actualizar su localizadores existentes, lo que no hay ninguna interrupción en el servicio de transmisión por secuencias.

Espere al menos 30 minutos después de la nueva clave de almacenamiento de sincronizar con AMS. A continuación, puede volver a crear su localizadores petición para que toman dependencia en la clave de almacenamiento especificada y mantienen la dirección URL existente.

Tenga en cuenta que al actualizar (o vuelva a crear) un localizador SA, la dirección URL siempre cambia.

>[AZURE.NOTE] Para asegurarse de que la opción conservar las direcciones URL existentes de su localizadores petición, debe eliminar el localizador existente y crear uno nuevo con el mismo Id.

El ejemplo .NET siguiente muestra cómo volver a crear un localizador con el mismo Id.

privado ILocator RecreateLocator(CloudMediaContext context, ILocator locator) estático {/ / guardar propiedades de localizador existente.
var activo = localizador. Activo; var accessPolicy = localizador. AccessPolicy; var locatorId = localizador. Id. var fechaDeInicio = localizador. Hora de inicio; var locatorType = localizador. Tipo; var locatorName = localizador. Nombre;

Eliminar localizador antiguo.
localizador. Delete();

Si (localizador. ExpirationDateTime < = DateTime.UtcNow) {excepción nueva (String.Format ("no se puede volver a crear localizador Id = {0} porque su fecha de expiración de localizador está en el pasado", localizador. ID)); }
    
        // Create new locator using saved properties.
        var newLocator = context.Locators.CreateLocator(
            locatorId,
            locatorType,
            asset,
            accessPolicy,
            startDate,
            locatorName);
    
    
    
        return newLocator;
    }


##<a name="step-5-regenerate--primary-storage-access-key"></a>Paso 5: Regenerar la clave de acceso de almacenamiento principal

Regenerar la clave de acceso de almacenamiento principal. Para obtener información sobre cómo restaurar claves de almacenamiento, consulte [Cómo: vista, copiar y volver a crear almacenamiento las teclas de acceso](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

##<a name="step-6-update-media-services-to-use-the-new-primary-storage-key"></a>Paso 6: Actualización Media Services para usar la nueva clave de almacenamiento principal
    
Usar el mismo procedimiento como se describe en el [paso 2](media-services-roll-storage-access-keys.md#step2) , pero esta vez sincronizar la nueva tecla de acceso de almacenamiento principal con la cuenta de Media Services.

>[AZURE.NOTE]Esperar 30 minutos antes de realizar operaciones con los servicios de Media (por ejemplo, crear nuevos localizadores) con el fin de evitar cualquier impacto en trabajos pendientes.

##<a name="step-7-update-locators"></a>Paso 7: Localizadores de actualización  

Después de 30 minutos puede volver a crear su localizadores petición para que toman dependencia en la nueva clave de almacenamiento principal y mantienen la dirección URL existente.

Utilice el mismo procedimiento descrito en el [paso 3](media-services-roll-storage-access-keys.md#step-3-update-locators).


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



###<a name="acknowledgments"></a>Confirmaciones 

Nos gustaría conscientes de las siguientes personas quién ha contribuido hacia la creación de este documento: Cenk Dingiloglu, Gada Milán, Seva Titov.
