<properties
    pageTitle="Metadatos de aplicaciones de la API de servicio de aplicación para la generación de detección y el código de la API | Microsoft Azure"
    description="Aprenda cómo usar aplicaciones de la API de servicio de la aplicación de Azure los metadatos de Swagger para facilitar la generación de detección y el código de la API."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="rachelap"/>

# <a name="app-service-api-apps-metadata-for-api-discovery-and-code-generation"></a>Metadatos de aplicaciones de la API de servicio de aplicación para la generación de detección y el código de la API 

Soporte para [Swagger 2.0](http://swagger.io/) API metadatos integrado en aplicaciones de API del servicio de aplicación. No es necesario que use Swagger, pero si se usa, puede aprovechar las ventajas de las características de aplicaciones de la API que facilitan la detección y consumo.   

## <a name="swagger-endpoint"></a>Extremo swagger

Puede especificar un extremo que proporciona metadatos Swagger 2.0 JSON para una aplicación de la API en una propiedad de la aplicación de la API. Puede ser el extremo en relación con la dirección URL base de la aplicación de la API o una dirección URL absoluta. Direcciones URL absolutas pueden apuntar fuera de la aplicación de la API. 

Muchos clientes de nivel inferiores (por ejemplo, generación de código de Visual Studio y flujo de PowerApps "Agregar API"), la dirección URL debe ser accesible públicamente (no protegidos por el usuario o la autenticación de servicio). Esto significa que si está usando autenticación de servicio de aplicaciones y desea exponer la definición de API de la aplicación propio, debe usar la opción de autenticación que permite el tráfico anónimo llegar a la API. Para obtener más información, vea [autenticación y la autorización para las aplicaciones de API del servicio de aplicación](app-service-api-authentication.md).

### <a name="portal-blade"></a>Módulo de portal

En el [portal de Azure](https://portal.azure.com/) la dirección URL del extremo puede ver y cambiar en el módulo de **Definición de la API** .

![](./media/app-service-api-metadata/apidefblade.png)

### <a name="azure-resource-manager-property"></a>Propiedad de administrador de recursos de Azure

También puede configurar la dirección URL de definición de API para una aplicación de la API mediante el [Explorador de recursos](https://resources.azure.com/) o [plantillas de administrador de recursos de Azure](../resource-group-authoring-templates.md) en herramientas de línea de comandos como [PowerShell de Azure](../powershell-install-configure.md) y [Azure CLI](../xplat-cli-install.md). 

En el **Explorador de recursos**, vaya a **suscripciones > {su suscripción} > resourceGroups > {su grupo de recursos} > Proveedores > Microsoft.Web > sitios > {su sitio} > Configuración > web**, y se verá la `apiDefinition` propiedad:

        "apiDefinition": {
          "url": "https://contactslistapi.azurewebsites.net/swagger/docs/v1"
        }

Para obtener un ejemplo de una plantilla de administrador de recursos de Azure que establece el `apiDefinition` propiedad, abra el [archivo azuredeploy.json en la aplicación de ejemplo de lista de tareas pendientes](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Busque la sección de la plantilla que es similar a la muestra JSON mostrado arriba.

### <a name="default-value"></a>Valor predeterminado

Cuando utiliza Visual Studio para crear una aplicación de API, el extremo de definición de la API se establece automáticamente en la dirección URL base de la aplicación API más `/swagger/docs/v1`. Esta es la dirección URL predeterminada que usa el paquete de NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) para proporcionar el servicio de metadatos de Swagger generados dinámicamente para un proyecto de ASP.NET Web API. 

## <a name="code-generation"></a>Generación de código

Una de las ventajas de integrar Swagger en aplicaciones de la API de Azure es la generación automática de código. Las clases de cliente generado que sea más fácil escribir código que llama a una aplicación de API.

Puede generar código de cliente para una aplicación de API mediante Visual Studio o desde la línea de comandos. Para obtener información acerca de cómo se generan clases de cliente en Visual Studio para un proyecto de ASP.NET Web API, vea [Introducción a aplicaciones API y ASP.NET](app-service-api-dotnet-get-started.md#codegen). Para obtener información acerca de cómo hacerlo desde la línea de comandos para todos los idiomas admitidos, vea el archivo Léame del repositorio de [Azure/autorest](https://github.com/azure/autorest) en GitHub.com.
 
## <a name="next-steps"></a>Pasos siguientes

Para obtener un tutorial paso a paso que le guiará en el proceso de creación, distribuir y consumir una aplicación de la API, vea [Introducción a la API de aplicaciones de servicio de aplicaciones de Azure](app-service-api-dotnet-get-started.md).

Si utiliza Administración de la API de Azure con la API de aplicaciones, puede usar metadatos Swagger para importar la API de administración de la API. Para obtener más información, vea [cómo importar la definición de una API con las operaciones de administración de la API de Azure](../api-management/api-management-howto-import-api.md). 
