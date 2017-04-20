<properties 
    pageTitle="Cómo implementar mediante copia de seguridad de servicio de recuperación y restaurar en administración de la API de Azure | Microsoft Azure" 
    description="Obtenga información sobre cómo utilizar copia de seguridad y restauración para realizar recuperación de administración de la API de Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Cómo implementar mediante copia de seguridad de servicio de recuperación y restaurar en administración de la API de Azure

Si elige publicar y administrar su API a través de administración de la API de Azure aprovecha de muchas tolerancia a errores y las capacidades de infraestructura que tendría para diseñar, implementar y administrar. La plataforma de Windows Azure soluciona una gran parte de los posibles errores en una fracción del costo.

Para recuperar de disponibilidad fallos de la región donde la administración de la API de servicio está alojado deberían estar preparados para reconstituir el servicio en una región diferente en cualquier momento. Según los objetivos de disponibilidad y objetivo de tiempo de recuperación desee reservar un servicio de copia de seguridad en una o más regiones e intenta mantener su configuración y contenido en sincronización con el servicio activo. La copia de seguridad de servicio y restaurar característica proporciona el bloque de creación necesario para implementar la estrategia de recuperación.

Esta guía muestra cómo autenticar las solicitudes de administrador de recursos de Azure y cómo hacer copia de seguridad y restaurar las instancias de servicio de administración de la API.

>[AZURE.NOTE] El proceso para realizar copias de seguridad y restauración de una instancia de servicio de administración de la API de recuperación también puede utilizarse para replicar instancias de servicio de administración de la API para escenarios como ensayo.
>
>Tenga en cuenta que cada copia de seguridad caduca después de 7 días. Si intenta restaurar una copia de seguridad una vez finalizado el período de expiración de 7 días, se producirá un error en la restauración con un `Cannot restore: backup expired` mensaje.

## <a name="authenticating-azure-resource-manager-requests"></a>Solicitudes de autenticación Administrador de recursos de Azure

>[AZURE.IMPORTANT] La API de REST de copia de seguridad y restauración utiliza el Administrador de recursos de Azure y tiene un mecanismo de autenticación diferente que las API de REST para administrar las entidades de administración de la API. Los pasos de esta sección describen cómo autenticar las solicitudes de administrador de recursos de Azure. Para obtener más información, vea [las solicitudes de autenticación de administrador de recursos de Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Todas las tareas que realizar en recursos mediante el Administrador de recursos de Azure se deben autenticar con Azure Active Directory con los pasos siguientes.

-   Agregar una aplicación para el inquilino de Azure Active Directory.
-   Establecer permisos para la aplicación que ha agregado.
-   Obtener el token para autenticar solicitudes para el Administrador de recursos de Azure.

El primer paso es crear una aplicación de Azure Active Directory. Inicie sesión en el [Portal de Azure clásica](http://manage.windowsazure.com/) con la suscripción que contiene la instancia de servicio de administración de la API y vaya a la pestaña de **aplicaciones** de Azure Active Directory predeterminada.

>[AZURE.NOTE] Si el directorio de Azure Active Directory predeterminado no está visible a su cuenta, póngase en contacto con el Administrador de la suscripción de Azure para conceder los permisos necesarios a su cuenta. Para obtener información sobre cómo localizar el directorio de forma predeterminada, vea [localizar el directorio de forma predeterminada](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-portal).

![Crear la aplicación de Azure Active Directory][api-management-add-aad-application]

Haga clic en **Agregar**, **Agregar una aplicación se desarrolla mi organización**y elija la **aplicación cliente nativa**. Escriba un nombre descriptivo y haga clic en la flecha siguiente. Escriba una dirección URL de marcador de posición como `http://resources` para la **Redirección URI**, tal y como es un campo obligatorio, pero no se utiliza el valor más adelante. Haga clic en la casilla de verificación para guardar la aplicación.

Una vez guardada la aplicación, haga clic en **Configurar**, desplácese hacia abajo hasta la sección **permisos a otras aplicaciones** y haga clic en **Agregar aplicación**.

![Agregar permisos][api-management-aad-permissions-add]

Seleccione **Windows** **Azure API de administración de servicio** y haga clic en la casilla de verificación para agregar la aplicación.

![Agregar permisos][api-management-aad-permissions]

Haga clic en **Permisos de delegado** junto a la aplicación de **Windows** **Azure API de administración de servicio** recién agregada, active la casilla de la **Administración de servicios de Azure Access (vista preliminar)**y haga clic en **Guardar**.

![Agregar permisos][api-management-aad-delegated-permissions]

Antes de llamar a las API que generan la copia de seguridad y restauración, es necesario obtener un símbolo. En el ejemplo siguiente se utiliza el paquete de nuget [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) para recuperar el token.

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace GetTokenResourceManagerRequests
    {
        class Program
        {
            static void Main(string[] args)
            {
                var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
                var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

                if (result == null) {
                    throw new InvalidOperationException("Failed to obtain the JWT token");
                }

                Console.WriteLine(result.AccessToken);

                Console.ReadLine();
            }
        }
    }

Reemplazar `{tentand id}`, `{application id}`, y `{redirect uri}` siguiendo las instrucciones siguientes.

Reemplazar `{tenant id}` con el identificador de inquilinos de la aplicación de Azure Active Directory que acaba de crear. El identificador puede tener acceso haciendo clic en **los extremos de la vista**.

![Extremos][api-management-aad-default-directory]

![Extremos][api-management-endpoint]

Reemplazar `{application id}` y `{redirect uri}` con el **Identificador de cliente** y la dirección URL de la sección de **Redirigir URI** de ficha de **Configurar** de la aplicación de Azure Active Directory. 

![Recursos][api-management-aad-resources]

Una vez que se especifican los valores, el ejemplo de código debe devolver un token similar al siguiente ejemplo.

![Token][api-management-arm-token]

Antes de llamar a las operaciones de copia de seguridad y restauración descritas en las secciones siguientes, establezca el encabezado de la solicitud de autorización de la llamada del resto.

    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);

## <a name="step1"> </a>Un servicio de administración de la API de copia de seguridad
Para una solicitud de emisión siguiente HTTP de servicio de administración de la API de copia de seguridad:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

donde:

* `subscriptionId`-identificador de la suscripción que contiene el servicio de administración de la API que está tratando de copia de seguridad
* `resourceGroupName`-una cadena en forma de 'Api - Default: {servicio región}' donde `service-region` identifica el área de Azure en la administración de la API del servicio está tratando de copia de seguridad está alojado, por ejemplo,`North-Central-US`
* `serviceName`-el nombre del servicio de administración de la API para realizar una copia de seguridad especificado en el momento de su creación
* `api-version`-Reemplazar por`2014-02-14`

En el cuerpo de la solicitud, especifique el nombre de cuenta de Azure almacenamiento de destino, tecla de acceso, el nombre del contenedor de blob y hacer copia de seguridad:

    '{  
        storageAccount : {storage account name for the backup},  
        accessKey : {access key for the account},  
        containerName : {backup container name},  
        backupName : {backup blob name}  
    }'

Establecer el valor de la `Content-Type` encabezado de solicitud para `application/json`.

Copia de seguridad es una operación larga que puede tardar varios minutos en completarse.  Si la solicitud se realizó correctamente y se inicia el proceso de copia de seguridad recibirá un `202 Accepted` código de estado de respuesta con un `Location` encabezado.  Asegúrese de 'GET' solicitudes a la dirección URL en el `Location` encabezado para averiguar el estado de la operación. Mientras la copia de seguridad está en curso seguirá recibe un código de estado '202 aceptado'. Un código de respuesta `200 OK` indicará finalización correcta de la operación de copia de seguridad.

**Nota**:

- **Contenedor** especificado en el cuerpo de solicitud **debe existir**.
* Mientras copia de seguridad está en curso **no debería intentar realizar las operaciones de administración de servicios** como SKU actualizar o degradar, el cambio de nombre de dominio, etcetera. 
* La restauración de una **copia de seguridad se garantiza solo durante 7 días** desde el momento de su creación. 
* **Datos de uso** utilizados para crear el análisis de informes **no se incluye** en la copia de seguridad. Use [API de REST de administración de Azure API][] para recuperar periódicamente los informes de análisis por motivos de seguridad.
* La frecuencia con la que realizar copias de seguridad de servicio afectará a su objetivo de punto de recuperación. Para minimizar, le recomendamos que implementar copias de seguridad periódicas, así como para realizar copias de seguridad a petición después de realizar cambios importantes en el servicio de administración de la API.
* **Cambios** realizados en la configuración del servicio (por ejemplo, API, directivas, apariencia portal de programador) durante la operación de copia de seguridad se encuentra en proceso **podrían no estar incluidos en la copia de seguridad y, por tanto, se perderán**.

## <a name="step2"> </a>Restaurar un servicio de administración de la API
Para restaurar una API de administración de servicio desde una copia de seguridad creada anteriormente realizar la solicitud HTTP siguiente:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

donde:

* `subscriptionId`-identificador de la suscripción que contiene el servicio de administración de la API está restaurando una copia de seguridad en
* `resourceGroupName`-una cadena en forma de 'Api - Default: {servicio región}' donde `service-region` identifica la región de Azure donde se hospeda el servicio de administración de la API está restaurando una copia de seguridad en, por ejemplo,`North-Central-US`
* `serviceName`-el nombre de la administración de la API de servicio que se está restaurando en especificado en el momento de su creación
* `api-version`-Reemplazar por`2014-02-14`

En el cuerpo de la solicitud, especifique la ubicación del archivo de copia de seguridad, es decir, nombre de la cuenta de almacenamiento de Azure, tecla de acceso, el nombre del contenedor de blob y hacer copia de seguridad:

    '{  
        storageAccount : {storage account name for the backup},  
        accessKey : {access key for the account},  
        containerName : {backup container name},  
        backupName : {backup blob name}  
    }'

Establecer el valor de la `Content-Type` encabezado de solicitud para `application/json`.

Restaurar es una operación larga que puede tardar hasta 30 o más minutos en completarse.  Si la solicitud se realizó correctamente y se ha iniciado el proceso de restauración recibirá un `202 Accepted` código de estado de respuesta con un `Location` encabezado.  Asegúrese de 'GET' solicitudes a la dirección URL en el `Location` encabezado para averiguar el estado de la operación. Mientras la restauración está en curso continuará recibiendo '202 aceptado' código de estado. Un código de respuesta `200 OK` indicará finalice correctamente la operación de restauración.

>[AZURE.IMPORTANT] **SKU** del servicio que se restauró en **debe coincidir con** la SKU del servicio de copia de seguridad está restaurando.
>
>**Cambios** realizados en la configuración del servicio (por ejemplo, API, directivas, apariencia portal de programador) durante la operación de restauración está en curso **se sobrescribirán**.

## <a name="next-steps"></a>Pasos siguientes
Consulte los siguientes blogs de Microsoft para dos tutoriales diferentes del proceso de copia de seguridad y restauración.

-   [Replicar las cuentas de administración de API de Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/) 
    -   Gracias a Gisela por su colaboración en este artículo.
-   [Administración de la API de Azure: Copia de seguridad y restaurar la configuración](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    -   El enfoque detallado por Stuart no coincide con la orientación oficial, pero es muy interesante.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Administración de la API de Azure API de REST]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
 
