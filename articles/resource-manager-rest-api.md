<properties
   pageTitle="API de REST de administrador de recursos | Microsoft Azure"
   description="Información general sobre los ejemplos de autenticación y el uso de la API de REST de administrador de recursos"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="navale;tomfitz;"/>
   
# <a name="resource-manager-rest-apis"></a>API de REST de administrador de recursos

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [CLI de Azure](xplat-cli-azure-resource-manager.md)
- [Portal](./azure-portal/resource-group-portal.md) 
- [API DE REST](resource-manager-rest-api.md)

Detrás de cada llamada para el Administrador de recursos de Azure, detrás de cada plantilla de implementada, detrás de cada cuenta de almacenamiento configurado hay varias llamadas a la API de REST del Administrador de recursos de Azure. En este tema se dedica a esas API y cómo se puede llamar sin usar cualquier SDK en absoluto. Esto puede ser muy útil si desea que el control total de todas las solicitudes a Azure o si el SDK para su idioma preferido no está disponible o no admite las operaciones que desee realizar.

En este artículo no se pasará por cada API que se expone en Azure, pero se prefiere usar algunas como un ejemplo de cómo continuar y conectarse a ellos. Si conoce los conceptos básicos que puede continuar y leer la [Referencia de la API de REST de Azure Administrador de recursos](https://msdn.microsoft.com/library/azure/dn790568.aspx) para obtener información detallada sobre cómo usar el resto de las API.

## <a name="authentication"></a>Autenticación
Autenticación de ARM se administra por Azure Active Directory (AD). Para conectarse a cualquier API debe autenticar con Azure AD para recibir un símbolo de autenticación que se puede pasar cada solicitud. Como describimos una llamada pura directamente a las API de REST, también consideraremos que no desea autenticar con una contraseña de nombre de usuario normal donde puede solicitar nombre de usuario y contraseña e incluso otros mecanismos de autenticación que se utiliza en escenarios de autenticación de dos factores un emergente-Configuración de pantalla. Por lo tanto, vamos a crear lo que se denomina una aplicación de AD Azure y una entidad de seguridad de servicio que se usará para iniciar sesión con. Pero recuerde que Azure AD admite varios de los procedimientos de autenticación y todas ellas se pueden usar para recuperar ese token de autenticación que se necesita para las solicitudes subsiguientes API.
Siga [crear Azure AD aplicación y principio de servicio](./resource-group-create-service-principal-portal.md) para obtener instrucciones paso a paso.

### <a name="generating-an-access-token"></a>Generar un Token de acceso 
Autenticación de Azure AD se realiza llamar a Azure AD, que se encuentra en://Login.microsoftonline.com). Para autenticar necesita la siguiente información:

* Identificador de inquilinos de Azure AD (el nombre de esa Azure AD que va a usar para iniciar sesión, a menudo igual que su empresa, pero no es necesario)
* Identificador de la aplicación (tomado durante el paso de creación de la aplicación de Azure AD)
* Contraseña (que ha seleccionado al crear la aplicación de AD Azure)

En la siguiente solicitud HTTP, asegúrese de que reemplazar "ID de inquilinos de Azure AD", "ID de aplicación" y "Contraseña" con los valores correctos.

**Solicitud de HTTP genérico:**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

... will (si se realiza correctamente la autenticación) como resultado una respuesta similar a esta:

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(El access_token en la respuesta anterior se han abreviado para mejorar la legibilidad)

**Generando con fiesta de token de acceso:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Generando token de acceso con PowerShell:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

La respuesta contiene un Token de acceso, la información acerca de cuánto ese token es válido y la información acerca de qué recursos pueden utilizar ese token para.
El token de acceso que ha recibido en la llamada HTTP anterior debe pasar para todas las solicitudes a la API de ARM como un encabezado denominado "Autorización" con el valor "Portador YOUR_ACCESS_TOKEN". Observe el espacio entre "Portador" y el Token de acceso.

Como puede ver desde el resultado de HTTP anteriores, el token es válido durante un período específico de tiempo durante el cual debe almacenar en caché y volver a usar ese mismo token. Aunque es posible autenticarse en Azure AD para cada llamada a la API, sería muy poco eficiente.

## <a name="calling-arm-rest-apis"></a>API REST de BRAZO de llamada

[API de REST de Azure Administrador de recursos se incluyen aquí](https://msdn.microsoft.com/library/azure/dn790568.aspx) y está fuera del ámbito de este tutorial documentar el uso de cada. Esta documentación usará solo unas pocas API para explicar el uso básico de la API y después de que nos referimos a la documentación oficial.

### <a name="list-all-subscriptions"></a>Lista de todas las suscripciones

Una de las operaciones más sencillas que puede hacer es las suscripciones disponibles que puede tener acceso a la lista. En la siguiente solicitud puede ver cómo el Token de acceso se pasa como un encabezado.

(Reemplace YOUR_ACCESS_TOKEN con el Token de acceso real).

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

... y como resultado, obtendrá una lista de suscripciones permitido para acceder a este servicio Principal

(Se han abreviado identificadores de suscripción siguiente para mejorar la legibilidad)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>Mostrar todos los grupos de recursos en una suscripción específica

Todos los recursos disponibles con la API de ARM están anidados dentro de un grupo de recursos. Vamos a consulta ARM para grupos de recursos existentes en nuestra suscripción utilizando el debajo de la solicitud HTTP GET. Observe cómo el identificador de la suscripción se pasa como parte de la dirección URL en este momento.

(Reemplace YOUR_ACCESS_TOKEN e ID_DE_SUSCRIPCIÓN con su real Token de acceso y el identificador de suscripción)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

La respuesta que recibe dependerá de si tiene algún grupo de recursos definido y si es así, ¿cuántos.

(Se han abreviado identificadores de suscripción siguiente para mejorar la legibilidad)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Hasta ahora nos hemos solo ha consultando las API de ARM para obtener información, es hora de creamos algunos recursos en su lugar y Empecemos por la más sencilla de todos ellos, un grupo de recursos. La solicitud HTTP siguiente crea un nuevo grupo de recursos en una región o ubicación de su elección y le agrega una o más etiquetas (el ejemplo siguiente realmente sólo agrega una etiqueta).

(Reemplace YOUR_ACCESS_TOKEN, ID_DE_SUSCRIPCIÓN, RESOURCE_GROUP_NAME con su real Token de acceso, Id. de suscripción y el nombre del que desea crear el grupo de recursos)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

Si se realiza correctamente, recibirá una respuesta similar a esta

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Ha creado correctamente un grupo de recursos en Azure. ¡Felicidades!

### <a name="deploy-resources-to-a-resource-group-using-an-arm-template"></a>Implementar recursos a un grupo de recursos con una plantilla de ARM

Con BRAZO, puede implementar los recursos de uso de plantillas de ARM. Una plantilla de ARM define varios recursos y sus dependencias. Para esta sección solo consideraremos está familiarizado con las plantillas de ARM y simplemente le mostraremos cómo hacer que la llamada de API para iniciar la implementación de uno. Aquí encontrará una documentación detallada de las plantillas de ARM.

Implementación de una plantilla ARM no difiere mucho a cómo llamar a otras API. Un aspecto importante es que implementación de una plantilla puede tardar bastante mucho tiempo, dependiendo de lo que está dentro de la plantilla y sólo devolverá la llamada a la API y es hacia arriba para usted como programador para consultar el estado de la implementación de para saber cuándo se realiza la implementación.

En este ejemplo, vamos a utilizar una plantilla de ARM expuesto públicamente disponible en [GitHub](https://github.com/Azure/azure-quickstart-templates). La plantilla que se va a usar implementará una VM Linux en la región de Estados Unidos oeste. Aunque esta plantilla tendrán la plantilla está disponible en un repositorio público como GitHub, también puede seleccionar para pasar de la plantilla completa como parte de la solicitud. Tenga en cuenta que le proporcionamos los valores de parámetro como parte de la solicitud que se utilizará dentro de la plantilla utilizada.

(Reemplace ID_DE_SUSCRIPCIÓN, RESOURCE_GROUP_NAME, DEPLOYMENT_NAME, YOUR_ACCESS_TOKEN, GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME, ADMIN_USER_NAME, ADMIN_PASSWORD y DNS_NAME_FOR_PUBLIC_IP a los valores apropiados para su solicitud)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

La respuesta JSON bastante larga para esta solicitud se omiten para mejorar la legibilidad de esta documentación. La respuesta contendrá información sobre la implementación de plantilla que acaba de crear.

