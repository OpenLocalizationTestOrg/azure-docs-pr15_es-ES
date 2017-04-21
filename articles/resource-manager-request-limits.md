<properties
   pageTitle="Límites de solicitud de administrador de recursos Azure | Microsoft Azure"
   description="Describe cómo utilizar límite con el Administrador de recursos de Azure solicitudes cuando se han alcanzado los límites de suscripción."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="tomfitz"/>

# <a name="throttling-resource-manager-requests"></a>Limitar las solicitudes de administrador de recursos

Para cada suscripción y el inquilino, límites del Administrador de recursos solicitudes 15.000 por hora de lectura y escritura solicitudes a 1200 por hora. Si su aplicación o script llega a estos límites, debe limitar las solicitudes. En este tema se muestra cómo determinar las solicitudes restantes que tiene antes de alcanzar el límite y cómo responder cuando se ha alcanzado el límite.

Cuando haya alcanzado el límite, recibe el código de estado HTTP **demasiados 429 muchas solicitudes**.

El número de solicitudes de ámbito es la suscripción o su inquilino. Si tiene varias, aplicaciones simultáneas hacer solicitudes en la suscripción, las solicitudes de esas aplicaciones se agregan conjuntamente para determinar el número de solicitudes restantes.

Solicitudes de suscripción ámbito son los grupos de la implican pasando el identificador de la suscripción, como la recuperación de los recursos de su suscripción. Las solicitudes de inquilinos ámbito no incluya el identificador de la suscripción, como recuperar ubicaciones válidas de Azure.

## <a name="remaining-requests"></a>Solicitudes restantes

Puede determinar el número de solicitudes restantes examinando los encabezados de respuesta. Cada solicitud incluye valores para el número de restante solicitudes de lectura y escritura. La siguiente tabla describe los encabezados de respuesta, que puede examinar con esos valores:

| Encabezado de respuesta | Descripción |
| --------------- | ----------- |
| x-ms-ratelimit-Remaining-Subscription-Reads | Ámbito de suscripción lee restante |
| x-ms-ratelimit-Remaining-Subscription-Writes | Ámbito de suscripción escribe restante |
| x-ms-ratelimit-Remaining-tenant-Reads | Ámbito del inquilino lee restante |
| x-ms-ratelimit-Remaining-tenant-Writes | Ámbito del inquilino escribe restante |
| x-ms-ratelimit-Remaining-Subscription-Resource-Requests | Las solicitudes de tipo de recurso restante de ámbito de la suscripción.<br /><br />Sólo se devuelve el valor del encabezado si un servicio reemplaza el límite predeterminado. Administrador de recursos se agrega este valor en lugar de la suscripción lee o escribe. |
| x-ms-ratelimit-Remaining-Subscription-Resource-Entities-Read | Solicitudes de la colección de tipo recursos restante de ámbito de la suscripción.<br /><br />Sólo se devuelve el valor del encabezado si un servicio reemplaza el límite predeterminado. Este valor proporciona el número de solicitudes de la colección de restantes (recursos de lista). |
| x-ms-ratelimit-Remaining-tenant-Resource-Requests | Inquilino ámbito restante las solicitudes de tipo de recurso.<br /><br />Solo se agrega este encabezado para las solicitudes en el nivel del inquilino y solo si el servicio ha reemplazar el límite predeterminado. Administrador de recursos se agrega este valor en lugar del inquilino lee o escribe. |
| x-ms-ratelimit-Remaining-tenant-Resource-Entities-Read | Inquilino ámbito solicitudes de la colección de tipo recursos restante.<br /><br />Solo se agrega este encabezado para las solicitudes en el nivel del inquilino y solo si el servicio ha reemplazar el límite predeterminado. |

## <a name="retrieving-the-header-values"></a>Recuperar los valores de encabezado

Recuperar estos valores de encabezado en el código o script no es diferente a recuperar cualquier valor de encabezado. 

Por ejemplo, en **C#**, recuperar el valor de encabezado de un objeto **HttpWebResponse** denominado **respuesta** con el siguiente código:

    response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)

En **PowerShell**, puede recuperar el valor del encabezado de una operación de invocación WebRequest.

    $r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
    $r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
    
O bien, si desea ver las restantes solicitudes de depuración, puede proporcionar la **-Depurar** parámetro en el cmdlet de **PowerShell** .

    Get-AzureRmResourceGroup -Debug
    
Que devuelve una gran cantidad de información, junto con el siguiente valor de respuesta:

    ...
    DEBUG: ============================ HTTP RESPONSE ============================

    Status Code:
    OK

    Headers:
    Pragma                        : no-cache
    x-ms-ratelimit-remaining-subscription-reads: 14999
    ...

En **Azure CLI**, se recupera el valor de encabezado mediante la opción más detallada.

    azure group list -vv --json

Que devuelve una gran cantidad de información, incluido el objeto siguiente:

    ...
    silly: returnObject
    {
      "statusCode": 200,
      "header": {
        "cache-control": "no-cache",
        "pragma": "no-cache",
        "content-type": "application/json; charset=utf-8",
        "expires": "-1",
        "x-ms-ratelimit-remaining-subscription-reads": "14998",
        ...

## <a name="waiting-before-sending-next-request"></a>Espera antes de enviar la solicitud siguiente

Cuando llegue el límite de solicitud, Administrador de recursos devuelve el código de estado **429** HTTP y un **Reintento después** valor en el encabezado. Los **Intentos después** valor especifica el número de segundos que debe esperar la aplicación (o suspensión) antes de enviar la solicitud siguiente. Si envía una petición antes de que el valor de reintento transcurrido, no se procesa la solicitud y se devuelve un valor de intentos de nuevo.
